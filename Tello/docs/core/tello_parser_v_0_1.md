# Tello — Parser v0.1.2 — Markdown

> Documento técnico: especificação, uso e código do Parser v0.1.x (JSON → HTML + validação). Este arquivo serve como a referência canônica do parser para o projeto Tello — copie/cole em um repositório, ou mantenha como contrato.

---

## Sumário
- Visão geral
- Arquitetura e arquivos gerados
- package.json (exemplo)
- tello-parser.js (código v0.1.2) — explicação e pontos de extensão
- Pipeline de render
- Flatten de tokens e CSS vars
- Mapping essencial (tabela rápida)
- Validações (códigos E/W) e regras por variante do framework
- Quick-fix handlers (contrato com Drawer U11)
- Integração com UI (fluxo Drawer/Fix/Locate)
- Debugging, testes e recomendações de evolução
- Changelog curto

---

## Visão geral
O Parser transforma `layout.json` + `tokens.json` em um artefato exportável (`index.html`) e em relatórios de validação (`validation.json`). Deve ser determinístico, bloqueador para erros críticos e oferecer quick-fixes programáticos para usar no editor.

Objetivos principais:
- Garantir que a saída HTML seja semântica e previsível;
- Proteger a exportação com regras de gating (E*/W*);
- Não injetar scripts/extras desnecessários (Alpine só se `fx` presente);
- Produzir artefatos soltos (`index.html`, `layout.json`, `validation.json`, `README.md` se desejado).

---

## Arquitetura e arquivos gerados
**Entrada**: `layout.json`, `tokens.json` (paths relativos ok)

**Saída** (pasta `dist/` por padrão):
- `index.html` — HTML único (Tailwind CDN + `<style>` tokens)
- `validation.json` — lista de errors/warnings com `nodeId`, `code`, `fix` (opcional)
- `layout.json` — cópia do layout processado (pode ser mutado por quick-fixes)

---

## package.json (exemplo mínimo)
```json
{
  "name": "tello-parser",
  "version": "0.1.2",
  "type": "module",
  "scripts": {
    "build": "node tello-parser.js layout.example.json tokens.example.json"
  },
  "dependencies": {
    "ajv": "^8.12.0",
    "prettier": "^2.8.0"
  }
}
```

---

## tello-parser.js (v0.1.2)
**Obs:** O arquivo completo com código-fonte está versionado como `tello-parser.js`. Ele contém:
- Validação básica de schema com AJV
- Flatten de tokens e geração de `:root{ --... }`
- Validações globais (E080, E120, E140, E200, E201)
- Regras por variante/framework (E081..E091 etc.)
- Render responsivo com prefixos `md:`/`lg:` aplicados nas classes
- Injeção condicional de Alpine quando `fx` está presente
- Produção de `validation.json` com estruturas de `fix` sugeridas

> **Pontos de extensão já documentados no código:** `classesFromProps`, `renderNode`, `runValidation`. Esses são os pontos onde adicionar novos mapeamentos de prop → classes e/ou novas validações.

---

## Pipeline de render (resumido)
1. Ler `layout.json` e `tokens.json`.
2. Validar `layout.json` contra um schema mínimo (campos obrigatórios: `version`, `meta`, `tokensRef`, `root`).
3. Flatten tokens → map `token.path` → CSS var `--token-path`.
4. Rodar `runValidation()` — colecionar `errors` e `warnings`.
   - Se `errors.length > 0` → escrever `validation.json` e abortar (gating).
5. Montar HTML: head (Tailwind CDN, `<style>` tokens, Alpine se `fx`), corpo (`renderNode(layout.root)`).
6. Format/Prettify e escrever `index.html`.

---

## Flatten de tokens e CSS vars
- `color.bg.page` → `--color-bg-page`
- `space.md` → `--space-md`

Uso na saída HTML: `bg-[var(--color-bg-page)]`, `text-[var(--color-text-default)]`.

**Regra importante:** propriedades não aceitam valores hex puros em `props` (erro `E201`) — o projeto deve referenciar tokens via paths.

---

## Mapping essencial (tabela rápida)
- `pad: xs/sm/md/lg/xl` → `p-1`, `p-2`, `p-4`, `p-6`, `p-8` ; numérico → `p-[Npx]`
- `radius: N` → `rounded-[Npx]` or scale `rounded-md`
- `elev: 0/1/2/3` → `'' / shadow-sm / shadow / shadow-lg`
- `maxWidth: sm/md/lg/xl` → `max-w-sm` etc.
- `align: start/center/end` → `text-left/center/right`
- `cols/gap` (grid) → `grid grid-cols-{n} md:grid-cols-{n}` etc.
- `layout.span` → `col-span-{N}` (1..12)
- `tokens` → `bg-[var(--...)]` / `text-[var(--...)]`

---

## Validações (códigos) — resumo operacional
**Erros bloqueantes (exigem correção antes da export):**
- `E080` — Faltam campos básicos: `version/meta/tokensRef/root`
- `E120` — Múltiplos `h1` detectados
- `E140` — `span` fora do intervalo 1..12 em qualquer breakpoint
- `E200` — Token referenciado não existe
- `E201` — Propriedade com HEX puro detectada (banido)
- `E081` — Offer sem `title`/`price`/`primaryCTA.href`
- `E082` — FAQ item sem `q`/`a`
- `E083` — Hero falta `h1`
- `E084` — Hero falta CTA com href
- `E085` — Hero.split falta `media`
- `E086` — Benefits.grid-3 sem grid/sem ≥3 cards
- `E087` — Proofs.grid-2 sem ≥2 cards
- `E088` — CTA sem botão com href
- `E091` — Pricing-table card sem price/cta

**Warnings (não bloqueiam):**
- `W063` — Valor fora da escala Tailwind (usar classe arbitrária)
- `W090` — Placeholder injetado para conteúdo faltante (non-blocking)
- `W101-W108` — heurísticas de UX (ex.: container.max ausente, gap sugerido)

Cada item em `validation.json` inclui `fix` com `action` sugerida quando aplicável.

---

## Quick-fix handlers (contrato para a UI)
A interface Drawer U11 consome `validation.json` e expõe ações de Quick-fix. Os handlers disponíveis (contrato) são:
- `demote-h1` — transforma `h1` → `h2` no nó alvo
- `add-alt` — adiciona `alt` a um `media`
- `apply-gap` — define `props.{bp}.gap`
- `set-span` — força `layout.{bp}.span` para um valor válido [1..12]
- `fill-offer-required` — cria `title/price/primaryCTA.href` vazios
- `add-cta` — injeta um `button` com `href` placeholder
- `add-media-stub` — injeta `media` com `ratio:16:9`
- `coerce-bullets` — normaliza `bullets` string → array

**Fluxo**: Drawer chama `applyFix(layout, fix)`, substitui estado, revalida e atualiza lista.

---

## Integração com UI (contrato Drawer U11)
- Input: `validation.json` (array `errors[]`, `warnings[]`), cada item pode ter `fix` object
- Drawer UI: Tabs All / Errors / Warnings; list rows with `Locate` and `Fix`
- `Locate` seleciona o nó no Canvas e aplica overlays
- `Fix` chama handler (backend ou client) que muta o `layout` em memória e reexecuta validação

Exemplo de `validation.json` entry:
```json
{
  "code":"E081",
  "severity":"error",
  "message":"Offer missing required fields",
  "nodeId":"offer1",
  "path":["root","children",5],
  "fix":{ "action":"fill-offer-required", "params":{} }
}
```

---

## Debugging, testes e recomendações
- Adicionar `--debug` para imprimir mapping por nó (JSON)
- Criar unit tests para `classesFromProps`, `layoutClassesByBp`, `renderNode`
- Snapshot tests para HTML gerado de templates oficiais
- Testes de validação cobrindo cada E*/W*
- Automatizar smoke tests que gerem `index.html` e rodem Lighthouse A11y (meta ≥95)

---

## Pontos de atenção / extensões futuras
- Separar renderer para um módulo (render/html.js) para facilitar testes e SSR
- Gerar source maps de layout → HTML para depuração
- Export presets (index.html vs site.zip com assets) e histórico de exports
- Plugin API: hooks para `preRender`, `postRender`, `validate` (permite extensão por plugins)

---

## Changelog (curto)
- v0.1.0 — POC básico (render base + flatten tokens)
- v0.1.1 — E081/E082, HEX banido (E201), placeholder W090, Alpine condicional
- v0.1.2 — Responsividade real (`md:`/`lg:` prefixes), regras variantes (hero/benefits/proofs/offer/faq/cta), quick-fix contract

---

## Como usar (rápido)
1. Salve `layout.json` e `tokens.json` no mesmo diretório do parser
2. `npm install`
3. `node tello-parser.js layout.json tokens.json`
4. Veja `dist/validation.json` e trate quick-fixes até `errors` = []
5. Abra `dist/index.html`

---

## Contato do documento
Este arquivo é a fonte de verdade do Parser. Para alterações de schema, atualize também o CHANGELOG e os testes de snapshot.





