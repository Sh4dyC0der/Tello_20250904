# Tello — Validation Codes Reference

> Documento canônico para todos os códigos de validação usados pelo Parser e consumidos pelo Drawer U11. Guarda o que é erro bloqueante, o que é aviso, a causa típica, a ação de quick-fix disponível e o JSON de exemplo que aparece em `validation.json`.

---

## Visão geral
O Parser emite um `validation.json` com duas listas principais: `errors` e `warnings`. Cada item contém: `code`, `severity` (`error`|`warning`), `message`, `nodeId`, opcional `bp` (breakpoint), `path` (localização no layout), e opcional `fix` (ação sugerida). O Drawer U11 interpreta esses itens e apresenta `Locate` e `Fix` quando aplicável.

Formato padrão (exemplo):
```json
{
  "code": "E081",
  "severity": "error",
  "message": "Offer missing required fields (title, price, primaryCTA.href)",
  "nodeId": "offer1",
  "bp": null,
  "path": ["root","children",4],
  "fix": { "action": "fill-offer-required", "params": {} }
}
```

---

## Severidade e comportamento do UI
- `error` (vermelho) → bloqueante para export (HTML/ZIP). Drawer mostra em tab Errors. Quick-fix pode ser executado; se não houver quick-fix disponível, o usuário precisa editar manualmente.
- `warning` (amarelo) → não bloqueia export. Drawer mostra em tab Warnings. Quick-fix sugerido quando aplicável.

UI rules:
- Ordenar por `severity` then `code` then `nodeId`.
- Mostrar botão `Fix` quando `fix` está presente.
- `Locate` posiciona Canvas no node e abre overlay contextual (Headings/Contrast/TabOrder) quando aplicável.

---

## Lista completa de ERROS (bloqueantes)
Abaixo a lista com `code`, `summary`, `cause`, `fix.action` (se houver), `sample params`.

### E080 — Missing required top-level fields
- **Resumo:** Falta `version`, `meta`, `tokensRef` ou `root` no layout.  
- **Causa típica:** arquivo corrompido ou template incompleto.  
- **Fix:** nenhum auto-fix genérico (requere edição). Drawer sugere criar campos básicos com `meta.name` e `tokensRef`.  
- **Severity:** error

### E081 — Offer missing required fields (title, price, primaryCTA.href)
- **Resumo:** bloco `offer` não tem `title`/`price`/`primaryCTA.href`.  
- **Causa:** seed incompleta ou CMS não populou campos.  
- **Fix:** `fill-offer-required` → params: `{ title?, price?, href?, label? }`. Preenche campos com placeholders e foca editor.  
- **Severity:** error

### E082 — FAQ item missing q/a
- **Resumo:** item FAQ sem `q` (pergunta) ou `a` (resposta).  
- **Fix:** `fill-faq-item` (alias de `fill-offer-required` behavior) → params `{ q?, a? }`. (Implementar handler similar).  
- **Severity:** error

### E083 — Hero lacks heading (h1)
- **Resumo:** hero não tem `heading` descendente marcado como `h1`.  
- **Fix:** `add-cta` no hero? No — ação correta: `add-heading` (handler opcional) ou `add-media-stub` para split. By default Drawer suggests `add-heading` with placeholder.
- **Severity:** error

### E084 — Hero lacks CTA with href
- **Resumo:** hero não contém botão com `href`.  
- **Fix:** `add-cta` → params `{ href?: '#', label?: 'Ação' }`. Drawer then revalidates.
- **Severity:** error

### E085 — Hero.split requires media
- **Resumo:** variant `hero.split` sem `media` node.  
- **Fix:** `add-media-stub` → params `{ ratio?: '16:9' }`.
- **Severity:** error

### E086 — Benefits.grid-3 requires grid with ≥3 cards
- **Resumo:** estrutura inadequada (fewer than 3 cards) or grid not present.  
- **Fix:** `add-card` or manual edit. Drawer suggests duplicar existing card or auto-inject placeholders.
- **Severity:** error

### E087 — Proofs.grid-2 requires ≥2 cards
- **Summary:** similar to E086 but for proofs.
- **Fix:** `add-card`.
- **Severity:** error

### E088 — CTA requires button with href
- **Resumo:** CTA block missing actionable button.  
- **Fix:** `add-cta`.
- **Severity:** error

### E091 — Pricing-table card missing price or CTA
- **Resumo:** card inside pricing table missing `price` or `primaryCTA.href`.  
- **Fix:** `coerce-bullets` not relevant; use `fill-offer-required` per-card or `add-cta`.
- **Severity:** error

### E120 — Multiple h1 found
- **Resumo:** mais de um h1 no documento. (A11y risk).  
- **Fix:** `demote-h1` — altera um `h1` para `h2`. Drawer mostra lista de headings e permite `demote-h1` on selected.
- **Severity:** error

### E140 — span outside 1..12
- **Resumo:** layout.span inválido (ou grid mismatch).  
- **Fix:** `set-span` → params `{ bp: 'md', span: N }`. Drawer pode suggest a clamped value.
- **Severity:** error

### E200 — Token not found
- **Resumo:** `props` references token path absent from `tokens.json`.  
- **Fix:** `map-token` (manual) or `create-token` assistant suggestion; default action: insert a placeholder token in tokens file and warn. Drawer can open tokens editor.
- **Severity:** error

### E201 — Raw HEX not allowed in props
- **Resumo:** props contain raw `#hex`. Parser blocks export. This prevents color drift.  
- **Fix:** `replace-hex-with-token` — params `{ tokenPath: 'color.brand.primary' }` (manual mapping); Drawer suggests nearest token by color similarity (future feature).
- **Severity:** error

---

## Lista de WARNINGS (não bloqueantes)

### W063 — Value outside Tailwind scale (using arbitrary class)
- **Resumo:** prop value not in scale; parser will render arbitrary class `p-[Npx]`. Warns because it reduces design consistency.  
- **Quick-fix:** `apply-scale` → map to nearest scale value or accept arbitrary.

### W090 — Placeholder injected for missing content
- **Resumo:** parser injected placeholder content for missing text/media in non-essential cards. Non-blocking. Useful for preview.
- **Quick-fix:** none or `edit-content` to fill.

### W101 — Container.max suggestion missing for hero
- **Resumo:** hero missing recommended container `max`. Drawer suggests `max: lg|xl`.
- **Fix:** `apply-container-max` → params `{ max: 'lg' }`.

### W102 — Hero split proportion off (suggested 7:5)
- **Resumo:** purely advisory.
- **Fix:** `apply-layout-suggest` → params `{ md: { spanLeft: 7, spanRight: 5 } }`.

### W103 — benefits.grid-3 missing md gap; apply default
- **Fix:** `apply-gap` → params `{ bp:'md', gap:24 }`.

### W104 — media.ratio missing; default to 16:9
- **Fix:** `add-media-ratio` → params `{ ratio:'16:9' }`.

### W105 — logos-row ratio suggestion (1:1)
- **Fix:** `apply-media-ratio`.

### W106 — bullets coerced to array
- **Fix:** `coerce-bullets` (automatic), inserts normalized array.

### W107 — price format normalized
- **Fix:** `normalize-price-format` (automatic conversion; warn user)

### W108 — FAQ fallback to <details> when fx missing
- **Fix:** `apply-fx` → params `{ fx:'accordion' }` or accept fallback.

---

## Mapping: `fix.action` → handler (quick reference)
- `demote-h1` → `applyFix(...{ action: 'demote-h1', nodeId })`
- `add-alt` → `applyFix(...{ action: 'add-alt', nodeId, params:{ alt } })`
- `apply-gap` → `applyFix(...{ action: 'apply-gap', nodeId, params:{ gap }, bp })`
- `set-span` → `applyFix(...{ action: 'set-span', nodeId, params:{ span }, bp })`
- `fill-offer-required` → `applyFix(...{ action: 'fill-offer-required', nodeId, params:{ title, price, href, label } })`
- `add-cta` → `applyFix(...{ action: 'add-cta', nodeId, params:{ href, label } })`
- `add-media-stub` → `applyFix(...{ action: 'add-media-stub', nodeId, params:{ ratio } })`
- `coerce-bullets` → `applyFix(...{ action: 'coerce-bullets', nodeId })`
- `replace-hex-with-token` → manual workflow; drawer opens tokens editor and suggests.

---

## Validation.json contract (full example)
```json
{
  "errors": [
    {
      "code": "E081",
      "severity": "error",
      "message": "Offer missing required fields (title, price, primaryCTA.href)",
      "nodeId": "offer1",
      "path": ["root","children",4],
      "fix": { "action": "fill-offer-required", "params": { "title":"Plano Inicial","price":"R$ 0","href":"#" } }
    }
  ],
  "warnings": [
    {
      "code": "W103",
      "severity": "warning",
      "message": "benefits.grid-3 sem gap em md; sugerido gap-24",
      "nodeId": "gr-b",
      "bp": "md",
      "fix": { "action": "apply-gap", "params": { "gap": 24 } }
    }
  ]
}
```

---

## UI behavior and quick-fix UX
- Clique em `Fix` aplica `applyFix()` com hot UI feedback (toasts + diffs) e revalida automatically. If fix resolved the error, it disappears from the Errors tab. If the fix only reduces severity (error→warning), update accordingly.
- For destructive fixes (e.g., demote-h1), Drawer should ask for confirmation and show `Preview` of change.
- For token-related errors (E200/E201) offer an inline `Map token` assistant that shows token palette and color similarity suggestions.

---

## Tests & automation
- Unit tests that assert each error code is produced for crafted broken layouts.
- Integration tests where `applyFix()` is called and layout revalidated — assert item removed or transformed.
- Snapshot coverage of `validation.json` for each template seed.

---

## Changelog / notes
- v0.1.1 added E081/E082 and E201 (hex ban) and placeholder behavior W090.
- v0.1.2 added variant rules (E083..E091) and mapping of fixes to handlers.

---

Keep this document as the single source of truth for all validation semantics. If you want, I can now generate a compact cheat-sheet (CSV) for devs and a printable cheat card for QA.

