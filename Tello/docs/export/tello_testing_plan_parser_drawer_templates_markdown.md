# Tello — Testing Plan (Parser • Drawer • Templates)

> Plano de testes oficial do Tello. Abrange Parser v0.1.2, Drawer U11, FX Engine, Export e Templates Seeds. Este documento define escopo, matriz de cobertura, fixtures, ferramentas, como rodar local/CI e critérios de aceite para releases.

---

## 1) Objetivos
- Detectar regressões cedo e bloquear export/merge quando violarem contratos.
- Garantir previsibilidade: HTML estável, validações corretas, quick‑fixes funcionais.
- Medir performance (tempo de parse/render/validar), A11y mínima e fidelidade dos templates.

---

## 2) Matriz de cobertura

| Componente | Unit | Snapshot | Integração | E2E/UI | A11y | Perf/Stress |
|---|---|---|---|---|---|---|
| Parser (classesFromProps, renderNode, runValidation) | ✅ | ✅ | ✅ | — | — | ✅ |
| Validation Rules (E*/W*) | ✅ | ✅ (validation.json) | ✅ | — | — | — |
| Quick‑Fix Handlers (applyFix) | ✅ | — | ✅ (revalida) | — | — | — |
| Drawer U11 (lista, locate, fix) | — | — | ✅ (com parser mock) | ✅ (Playwright) | ✅ (axe) | — |
| FX Engine (vanilla/alpine bindings) | ✅ | — | ✅ (smoke) | ✅ (interação) | ✅ | — |
| Export (gating, presets, zip) | — | — | ✅ | ✅ (smoke de arquivos) | — | ✅ (tempo) |
| Templates Pack (8) | — | ✅ (HTML e validation.json) | ✅ | — | — | — |
| Tokens Contract | ✅ (shape) | — | ✅ (flatten → :root) | — | Contraste básico | — |

Browsers-alvo para UI/E2E: `Chromium latest`, `Firefox latest`, `WebKit latest` (CI usa Playwright). Mobile viewports simuladas: 390×844, 768×1024, 1280×800.

---

## 3) Estrutura de pastas
```
/tests
  /fixtures
    tokens.valid.json
    layout.minimal.json
    layout.hero.centered.json
    layout.hero.split.missing-media.json   # E085
    layout.offer.missing-fields.json       # E081
    layout.faq.missing-qa.json             # E082
    layout.multiple-h1.json                # E120
    layout.span.invalid.json               # E140
    layout.hex.in.props.json               # E201
    layout.tokens.notfound.json            # E200
  /parser
    classesFromProps.spec.ts
    renderNode.spec.ts
    runValidation.spec.ts
    flattenTokens.spec.ts
    quickfix.applyFix.spec.ts
  /validation
    e_codes.snapshot.spec.ts               # snapshots de validation.json
  /drawer
    drawer.listing.spec.ts
    drawer.fix.flow.spec.ts
    drawer.a11y.spec.ts
  /fx
    accordion.spec.ts
    modal.spec.ts
    tooltip.spec.ts
  /export
    export.gating.spec.ts
    export.zip.structure.spec.ts
  /templates
    seeds.snapshot.spec.ts
  /perf
    parser.stress.spec.ts
/playwright
  drawer.e2e.spec.ts
  export.e2e.spec.ts
```

---

## 4) Ferramentas
- **Unit/Integration**: Vitest ou Jest (Node 18+)
- **E2E/UI**: Playwright
- **A11y**: axe-core (via Playwright), Lighthouse CI opcional
- **Zip**: `adm-zip` ou nativo Node
- **Coverage**: `c8`/`istanbul` (alvo ≥ 85% statements nos módulos core)

---

## 5) Como rodar (local)
```bash
pnpm i
pnpm test              # unit + integration
pnpm test:ui           # playwright headless
pnpm test:ui:headed    # playwright com UI
pnpm test:perf         # stress parser
pnpm test:update       # atualiza snapshots conscientemente
```

CI (GitHub Actions ou equivalente) executa `test`, depois `test:ui`, depois `test:perf` com thresholds.

---

## 6) Fixtures essenciais
- `tokens.valid.json` — contrato completo (cores, espaço, radius, shadows, container, typography).
- Seeds válidos (herói centered/split, benefits grid‑3, proofs grid‑2, pricing, faq, cta, landing canonical, landing split).
- Casos de erro por código (E080/E081/E082/E083/E084/E085/E086/E087/E088/E091/E120/E140/E200/E201). Cada um com descrição e motivo.

---

## 7) Casos de teste (exemplos)

### 7.1 Parser: validação + render
```ts
import { validate, render } from "../src/tello-parser";
import layout from "../tests/fixtures/layout.offer.missing-fields.json";
import tokens from "../tests/fixtures/tokens.valid.json";

it("bloqueia export quando oferta está incompleta (E081)", () => {
  const v = validate(layout, tokens);
  expect(v.errors.some(e => e.code === "E081")).toBe(true);
});

it("gera HTML estável para hero.centered", () => {
  const html = render(require("../tests/fixtures/layout.hero.centered.json"), tokens);
  expect(html).toMatchSnapshot();
});
```

### 7.2 Quick‑fix → revalidação
```ts
import { applyFix } from "../src/tello-fixes";
import { validate } from "../src/tello-parser";

it("preenche campos obrigatórios da oferta e zera E081", () => {
  let layout = require("../tests/fixtures/layout.offer.missing-fields.json");
  let v = validate(layout, tokens);
  const err = v.errors.find(e => e.code === "E081");
  layout = applyFix(layout, { action: "fill-offer-required", nodeId: err.nodeId, params: { title: "Plano", price: "R$ 0", href: "#" } }).layout;
  v = validate(layout, tokens);
  expect(v.errors.some(e => e.code === "E081")).toBe(false);
});
```

### 7.3 Drawer U11 (E2E)
- Carrega `validation.json` com 5 erros e 3 avisos
- Filtra por `Errors`
- Clica `Fix` em `E120` → confirma `demote-h1` → lista baixa 1 item
- `Locate` foca nó correto (inspeciona atributo `data-selected` no Canvas)
- Axe passa sem violações críticas (role/aria, foco)

### 7.4 Export (gating)
- Quando `errors > 0`, botão `Export` desabilitado; tentativa manual retorna snackbar de bloqueio
- Com `errors = 0`, gera `index.html` e `site.zip`; ZIP contém `index.html`, `layout.json`, `tokens.json`, `validation.json`, `README.md`

### 7.5 FX Engine
- `accordion` funciona sem Alpine (detalhes abre/fecha, `aria-expanded` alterna)
- `modal` respeita `Esc`/trap de foco (com Alpine)

### 7.6 Perf/Stress
- 1k nós em árvore com 5 níveis: `validate+render` ≤ 120 ms em Node 18 numa máquina padrão CI
- 100 exports consecutivos não deve vazar memória (heap estável)

---

## 8) Perf Budgets
- `validate(layout)` ≤ 10 ms para 200 nós
- `render(layout)` ≤ 30 ms para 200 nós
- `export(site.zip)` ≤ 150 ms (sem assets externos)

Falhas nos budgets marcam o build como amarelo; 20% acima por 2 builds consecutivos converte para vermelho (falha).

---

## 9) A11y mínimos
- Uma única tag `<h1>` por página (checado por E120 + auditoria DOM em E2E)
- `dialog` com `aria-modal`, foco inicial e retorno de foco
- `tooltip` com `role=tooltip` e tecla `Esc`

---

## 10) Política de snapshots
- Snapshot só para HTML final e `validation.json` dos seeds.
- Atualização requer mensagem de commit com `chore(snapshot): motivo claro` e link para PR.

---

## 11) Critérios de aceite para release
- ✅ 100% dos testes unit/integration passando
- ✅ E2E estável nos 3 navegadores
- ✅ Cobertura ≥ 85% nas pastas `src/parser` e `src/fixes`
- ✅ Sem erros A11y críticos
- ✅ Perf budgets atendidos
- ✅ Templates seeds exportando sem erros (apenas warnings toleráveis)

---

## 12) Bug triage & SLO
- P0 (bloqueia export): corrigir em 24 h
- P1 (quebra UX do Drawer): 72 h
- P2 (warning falso positivo): próximo ciclo
- P3 (aperfeiçoamento de mensagens): backlog

---

## 13) Roadmap de QA
- Diffs visuais automatizados (Playwright trace + screenshot comparar) — opcional
- Lighthouse CI em `index.html` (alvo A11y ≥ 95)
- Testes de migração de schema/tokens

---

## 14) Anexos
- Scripts utilitários: `scripts/make-zip.ts`, `scripts/render-preview.ts`
- Template de relatório de testes para releases (`/docs/release-test-report.md`)

---

Este plano é o contrato de qualidade do Tello. Alterações de Parser/Drawer/FX/Export requerem atualização dos casos e fixtures aqui definidos.

