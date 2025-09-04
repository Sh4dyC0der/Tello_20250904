# Tello — Drawer U11 (Spec & UX)

> Documento canônico do painel de Validação do Tello. Define UI, estados, contratos de dados, atalhos, A11y, telemetria e integração com o Parser v0.1.2 e os Quick‑Fix Handlers.

---

## Objetivo
Exibir erros e avisos do `validation.json`, permitir localizar nós no Canvas e aplicar **quick‑fixes** de forma segura, previsível e audível (A11y). Export só libera quando `errors.length === 0`.

**Não‑objetivos:** ser linter genérico, editar tokens diretamente, inspeção DOM fora do contrato do layout.

---

## Layout & Comportamento
- **Container:** Drawer lateral direito; largura 420 px; resizable (min 360, max 560).
- **Abertura:** `Ctrl+Alt+Y`; foco vai para a primeira linha de erro; `Esc` fecha e retorna foco ao Canvas.
- **Seções:** Tabs `All` | `Errors` | `Warnings`.
- **Toolbar:** filtro por `code`, busca por `nodeId/text`, toggles de overlays (Headings | Tab Order | Contrast), botão **Fix All Warnings**.
- **Listagem:** linhas virtuais (virtualization ≥ 200 itens), ordenação `severity→code→nodeId`.

### Linha (item)
- **Formato:** `Chip(code) • message • nodeId • (bp?)` + botões `[Locate] [Fix] [⋯]`.
- **Locate:** seleciona nó no Canvas, centraliza, dá highlight de 2 s.
- **Fix:** executa `applyFix()` (quando disponível). Alguns fixes pedem confirmação (ex.: `demote-h1`).
- **Menu (⋯):** `Copy`, `Mute code`, `Open JSON`, `Explain`.

---

## Estados (State Machine)
`Idle → Loading(json) → Showing(list) → ApplyingFix(item) → Revalidating → Showing(updated) → (ErrorModal|SuccessToast)`

- **Loading:** spinner + contagem; timeout 5 s exibe fallback (“carregando…”).
- **ApplyingFix:** bloqueia apenas a linha; o restante permanece interativo.
- **ErrorModal:** exibe stack/causa quando `applyFix` falha.

---

## Contrato de dados (validation.json)
```json
{
  "errors": [
    {
      "code": "E081",
      "severity": "error",
      "message": "Offer missing required fields (title, price, primaryCTA.href)",
      "nodeId": "offer1",
      "bp": null,
      "path": ["root", "children", 4],
      "fix": { "action": "fill-offer-required", "params": { "title": "Plano", "price": "R$ 0", "href": "#" } }
    }
  ],
  "warnings": [
    {
      "code": "W103",
      "severity": "warning",
      "message": "benefits.grid-3 sem gap em md; sugerido gap-24",
      "nodeId": "gr-b",
      "bp": "md",
      "fix": { "action": "apply-gap", "params": { "gap": 24 }, "bp": "md" }
    }
  ]
}
```

### Campos
- `code` (string) — chave canônica (ver *Validation Codes Reference*).
- `severity` (`error|warning`).
- `message` (string curta). Evitar stack técnica; guardar detalhes em tooltip.
- `nodeId` (string) — id do nó no layout.
- `bp` (string|null) — breakpoint relacionado ao problema.
- `path` (array) — trilha no JSON para depuração.
- `fix` (obj|null) — `{ action, params?, bp? }`.

---

## Integração com Quick‑Fix Handlers
- Import: `applyFix(layout, fixRequest)` do módulo **tello-fixes**.
- Ciclo ao clicar **Fix**:
  1) Mutar `editorState.layout` in‑memory com `applyFix`.
  2) Exibir `changes[]` em console/log do Drawer (opcional).
  3) Revalidar (chamar Parser) e atualizar lista.
  4) Toast de sucesso ou modal de erro.

### Ações suportadas (v0.1.2)
`demote-h1`, `add-alt`, `apply-gap`, `set-span`, `fill-offer-required`, `add-cta`, `add-media-stub`, `coerce-bullets`.

> Para `replace-hex-with-token` e `map-token`, abrir **Tokens Editor** (fora do Drawer). Drawer apenas linka.

---

## Overlays (HUD)
- **Headings:** destaca h1..h6 e hierarquia (detecta E120). Atalho: `Ctrl+Alt+H`.
- **Tab Order:** numera foco navegável (tabIndex). Atalho: `Ctrl+Alt+T`.
- **Contrast:** heatmap baseado em tokens (estimado). Atalho: `Ctrl+Alt+C`.

Exibição fica sincronizada com **Locate**. Overlays têm painel rápido dentro do Drawer com toggles.

---

## Acessibilidade (A11y)
- **Focus trap** no Drawer; `Esc` fecha; `Enter` ativa **Fix** na linha focada; `F2` abre menu `⋯`.
- **ARIA:** `role="complementary"` para o Drawer; lista com `role="list"`, itens com `role="listitem"`.
- **Live region** (`aria-live="polite"`) para toasts pós‑fix.
- **Atalhos** exibidos em tooltip/aria‑description.

---

## Atalhos (Keymap)
- Abrir/fechar Drawer: `Ctrl+Alt+Y`
- Alternar tabs: `Ctrl+PgUp/PgDn`
- Localizar item: `L`
- Aplicar fix: `F`
- Overlays: Headings `Ctrl+Alt+H`, Tab `Ctrl+Alt+T`, Contrast `Ctrl+Alt+C`
- Fix All Warnings: `Shift+F`

> Keymap respeita **Settings → Keymap** (Default/Figma‑like/Photoshop‑like/ABNT2/Minimal).

---

## Microcopy (UX Writing)
- **Vazio (All):** “Nenhum problema por aqui. Pode exportar.”
- **Vazio (Errors):** “Sem erros bloqueantes. Que tal revisar os avisos?”
- **Linha com fix:** Botão `Fix`; tooltip “Aplicar correção sugerida”.
- **Confirmação demote‑h1:** “Mudar este h1 para h2? Isso corrige E120.”
- **Toast sucesso:** “Corrigido: {code}. Lista atualizada.”
- **Toast erro:** “Falhou ao corrigir {code}. Veja detalhes.”

---

## Telemetria (mínimo útil)
- `drawer_opened` { via, errors_count, warnings_count }
- `fix_clicked` { code, action, nodeId }
- `fix_applied` { code, action, duration_ms }
- `fix_failed` { code, action, reason }
- `locate_clicked` { code, nodeId }

Todos os eventos respeitam LGPD (sem dados pessoais, apenas métricas operacionais).

---

## Performance
- Virtualização da lista.
- Debounce 250 ms na busca.
- Revalidação assíncrona com cancel token (evitar corrida de estados).
- Renderizar diffs opcionais fora da thread principal (web worker futuro).

---

## Theming
- Usa **Tokens Contract**: cores, espaçamentos, tipografia.
- Chips de severidade: `error` → `color.semantic.danger`, `warning` → `color.semantic.warning`.

---

## API de integração
- **Parser:** `validate(layout, tokens) → validation.json`.
- **Fixes:** `applyFix(layout, fix) → { ok, changes[], layout }`.
- **Event Bus:** `drawer:open`, `drawer:close`, `drawer:locate(nodeId)`, `drawer:fix(result)`.

---

## Pseudo‑código (fluxo de Fix)
```ts
function onFix(item){
  setLineBusy(item.id, true);
  const res = applyFix(editorState.layout, { ...item.fix, nodeId: item.nodeId, bp: item.bp || undefined });
  if (!res.ok) return showError(res.changes.join("\n"));
  editorState.layout = res.layout;
  revalidate().then(updateList).then(()=>toast(`Corrigido: ${item.code}`)).finally(()=>setLineBusy(item.id,false));
}
```

---

## Testes (aceite)
- **Fix feliz:** clicar `Fix` remove item da lista (ou converte `error→warning`).
- **Fix falho:** exibe modal erro e mantém item.
- **Locate:** seleciona e centraliza nó; overlay reflete foco.
- **Atalhos:** todos funcionam e têm aria‑description.
- **Export gating:** botão Export desabilita quando `errors.length>0`.

---

## Erros e edge‑cases
- `nodeId` inexistente: mostrar `Node não encontrado` + abrir busca por id.
- `fix.action` desconhecida: explicar que o handler não está disponível nesta versão.
- Revalidação mais lenta que 2 s: manter spinner discreto na Tab ativa.

---

## Versão & Changelog
- **U11 v1.0** — Spec inicial alinhada ao Parser v0.1.2 e aos Handlers.
- Próximo: suporte a **batch fixes** com preview de diffs; palette de tokens para E200/E201.

---

Este documento completa a espinha dorsal de UI do Tello: **Schema**, **Tokens**, **Validation**, **Blocks**, **Templates** e agora **Drawer U11**. Qualquer mudança de contrato no parser deve refletir aqui imediatamente.

