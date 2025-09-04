# Tello — Onboarding & Quickstart Guide

> Guia de entrada rápida e operação diária do Tello. Este documento costura **Schema**, **Tokens**, **Blocks**, **Validation**, **Templates**, **FX**, **Export** e **Testing** em um fluxo operacional único. Use como checklist para iniciar e entregar uma landing sem fricção.

---

## 1) Pré‑requisitos
- **Arquivos**: `tokens.json` (Brand‑Form) e um `layout.json` (pode partir dos templates seeds).
- **Parser**: `node tello-parser.js <layout.json> <tokens.json>` (v0.1.2+).
- **Navegador** moderno (Tailwind CDN) ou modo **offline** via preset de export.
- **Decisão de engine**: `vanilla` (padrão), `alpine` (se usar FX que exige JS), ou `noscript` (somente fallbacks).

### Hotkeys essenciais
- **Palette** `Ctrl/⌘+K`
- **Drawer U11** `Ctrl+Alt+Y`
- **Wireframe** `Ctrl+Alt+W`
- **Undo/Redo** `Ctrl+Z` / `Ctrl+Y` (`Ctrl+Shift+Z`)
- **HUD chips** `Q`
- **colSpan −/+** `[ ]`  (ABNT2: `Ctrl+Alt+,` / `Ctrl+Alt+.`)

---

## 2) Fluxo canônico (Brand‑Form → Tello → Export)

### Passo 1 — Tokens (Brand‑Form)
1. Gere `tokens.json` conforme **Tokens Contract** (cores, espaços, radius, shadows, container, tipografia).
2. Verifique com `validate-tokens.js` (quando disponível) ou checklist do contrato.
3. Salve junto ao projeto e aponte com `tokensRef` no `layout.json`.

### Passo 2 — Layout (Templates + Blocks)
1. Escolha um seed do **Templates Pack** (ex.: `landing-canonical.json`).
2. Ajuste `meta.name`, `role/variant` onde couber, e preencha conteúdo mínimo:
   - Hero: `h1`, CTA com `href`.
   - Benefits: ≥3 cards.
   - Proofs: ≥2 cards.
   - Offer/Pricing: `title`, `price`, `primaryCTA.href`.
   - FAQ: `q/a` por item.
3. Se precisar de seções extras, use a **Blocks Library** e mantenha spans válidos 1..12.

### Passo 3 — FX (opcional)
- Para FAQ, adicione `fx: ["accordion"]`.
- Para Modais/Reveal, defina `engine: "alpine"` nas **Settings** antes do export.

### Passo 4 — Validação (Drawer U11)
1. Abra **Drawer U11** (`Ctrl+Alt+Y`).
2. Corrija **Errors** com Quick‑Fix (`Fix`):
   - E120 (múltiplos h1) → `demote-h1`.
   - E140 (span inválido) → `set-span`.
   - E081/E091 (offer/pricing) → `fill-offer-required`.
   - E084/E088 (cta sem href) → `add-cta`.
   - E085 (hero split sem media) → `add-media-stub`.
   - E200/E201 (tokens/hex) → mapear token (fora do Drawer).
3. Warnings não bloqueiam; priorize `W103` (gap md) e `W104/W105` (ratio media).

### Passo 5 — Export
- Quando `errors = 0`, gere:
  - `index.html`, `layout.json`, `validation.json`, `preview.png`, ou `site.zip` (estrutura contratada).
- Naming: `site-<slug>-v<version>-<timestamp>.zip`.
- Histórico: últimos 5 exports disponíveis no botão Export.

### Passo 6 — Entrega
- Envie `site.zip` ao cliente ou abra `index.html` para revisão.
- Mudou tokens? Reimporte no Tello e reexporte.

---

## 3) “Speed‑Run” (10 minutos)
1. Abrir Template `landing-split-pricing.json`.
2. Setar `tokensRef` para seu `tokens.json` (Brand‑Form).
3. Preencher hero (h1, CTA href), features (textos), proofs (2 logos), pricing (prices + ctas).
4. Drawer U11: aplicar fixes até `errors = 0`.
5. Exportar `index.html` + `preview.png` para validação rápida.

---

## 4) “First Landing” (roteiro guiado)
- **Grid**: base 1 col, `md` 12 col, gaps 16/24 (ver **Blocks Library**).
- **Spans**: hero split 7/5; CTA split 8/4.
- **Containers**: hero centered `max: lg|xl` (W101 se ausente).
- **Tokens**: não use HEX em props; sempre `bg: "color.bg.page"` etc.
- **A11y**: 1 h1 por página; `media.alt` significativo; `dialog` com `aria-modal`.

---

## 5) Pitfalls comuns → soluções
- **Erro** E201: HEX em props. Solução: crie um token e mapeie.
- **Erro** E200: token ausente. Solução: alinhar `tokens.json` ou corrigir path.
- **Erro** E085: hero split sem imagem. Solução: `add-media-stub`.
- **Erro** E120: múltiplos h1. Solução: `demote-h1` no Drawer.
- **Erro** E140: span fora 1..12. Solução: `set-span` (ou normalize via plugin de spans).
- **Warning** W103: grid sem gap em `md`. Solução: `apply-gap` 24.

---

## 6) Troubleshooting
- **Export bloqueado**: sempre abra Drawer; clique no primeiro erro e aplique fix.
- **Pré‑visualização lenta**: ative **Low‑Power** (Alt+Shift+P) e o **Wireframe** para editar sem imagens.
- **FX não funciona**: confira `Settings → Engine` e se o efeito exige Alpine; caso `noscript`, espere apenas fallbacks.
- **Tokens trocados**: confirme `tokensRef` e versão do arquivo; rode o flatten e compare `:root`.

---

## 7) Estrutura de projeto sugerida
```
/tello
  /seeds           # templates base versionados
  /tokens          # tokens por cliente (ex.: acme.tokens.json)
  /exports         # histórico (gera-se automático)
  layout.json      # atual em edição
  tokens.json      # link para tokens ativos
  tello-parser.js  # parser local
```

- Versione seeds com sufixos (`landing-v1.json`, `landing-v1.1.json`).
- Commits: `feat: add benefits grid-3`, `fix: E120 demote-h1`, `chore: tokens v1.2`.

---

## 8) CLI / Scripts úteis
```json
{
  "scripts": {
    "validate": "node tello-parser.js layout.json tokens.json --validate-only",
    "export:html": "node tello-parser.js layout.json tokens.json --preset html",
    "export:zip": "node tello-parser.js layout.json tokens.json --preset zip",
    "test:seeds": "pnpm vitest run -t 'templates'"
  }
}
```

---

## 9) Checklists
**Antes do export**
- [ ] `version/meta/tokensRef/root` presentes
- [ ] 1 único `h1`
- [ ] Sem HEX; tokens válidos
- [ ] Spans válidos; gaps definidos em grids críticos
- [ ] Offer/Pricing/FAQ com mínimos obrigatórios

**Entrega**
- [ ] `site.zip` contém `index.html`, `layout.json`, `tokens.json`, `validation.json`, `README.md`
- [ ] `preview.png` anexado na revisão

---

## 10) Referências cruzadas
- **Layout Schema** — contrato dos nós
- **Tokens Contract** — fonte de verdade visual
- **Blocks Library** — props e exemplos por bloco
- **Validation Codes** — semântica dos erros/avisos
- **FX Inventory & Engine** — efeitos declarativos
- **Export Presets & Packaging** — artefatos e gating
- **Testing Plan** — garantia de qualidade

---

Este guia é o “manual de operação” mínimo e suficiente para rodar o Tello sem perder tempo. Atualize conforme seus hábitos e fixe como primeira leitura ao abrir um novo projeto.

