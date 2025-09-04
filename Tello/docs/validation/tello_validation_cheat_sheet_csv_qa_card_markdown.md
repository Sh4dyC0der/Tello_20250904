# Tello — Validation Cheat‑Sheet (CSV + QA Card)

> Documento rápido para dev e QA. Inclui um **CSV** de referência para os códigos do Parser e um **cartão imprimível** com checklist de export.

---

## 1) Dev Cheat‑Sheet (CSV)
> Copie o bloco abaixo para um arquivo `.csv` se precisar. Separador: vírgula.

```csv
code,severity,summary,quick_fix_action,params,notes
E080,error,Missing top-level fields (version/meta/tokensRef/root),,,-
E081,error,Offer missing title/price/primaryCTA.href,fill-offer-required,"title,price,href,label",Offer panel e pricing-table cards
E082,error,FAQ item missing q/a,fill-faq-item,"q,a",Aplica a items com id/fx de FAQ
E083,error,Hero lacks h1,add-heading,"level=h1,label?",Opcional: demote outro h1 para evitar E120
E084,error,Hero lacks CTA with href,add-cta,"href,label",CTA obrigatório em hero
E085,error,Hero.split requires media,add-media-stub,"ratio=16:9",Split deve ter media
E086,error,Benefits.grid-3 requires grid ≥3 cards,add-card,amount>=3,Grid md≥3 cols + 3 cards
E087,error,Proofs.grid-2 requires ≥2 cards,add-card,amount>=2,Provas/testemunhos
E088,error,CTA requires button with href,add-cta,"href,label",CTA centrado/split
E091,error,Pricing-table card missing price/cta,fill-offer-required,"price,href",Cada card precisa price e CTA
E120,error,Multiple h1 found,demote-h1,,Escolher o h1 canônico
E140,error,span outside 1..12,set-span,"bp,span",Clampeia spans inválidos
E200,error,Token not found in tokens.json,replace-hex-with-token or map-token,"tokenPath",Abrir editor de tokens
E201,error,Raw HEX found in props,replace-hex-with-token,"tokenPath",HEX é banido em props
W063,warning,Value outside Tailwind scale (arbitrary class),apply-scale,"nearest?",Consistência de design
W090,warning,Placeholder injected for missing content,edit-content,,Cards não-essenciais
W101,warning,Hero missing container max,apply-container-max,"max=lg|xl",Heurística de UX
W102,warning,Hero split proportion off (7:5),apply-layout-suggest,"md spans",Apenas sugestão
W103,warning,Benefits.grid-3 missing md gap,apply-gap,"bp=md,gap=24",Sugestão de gap
W104,warning,Media ratio missing,add-media-ratio,"ratio=16:9",Padroniza preview
W105,warning,Logos row recommended ratio 1:1,apply-media-ratio,"ratio=1:1",Melhor alinhamento
W106,warning,Bullets coerced to array,coerce-bullets,,Normalização leve
W107,warning,Price format normalized,normalize-price-format,,Formato consistente
W108,warning,FAQ without fx uses <details>,apply-fx,"fx=accordion",Fallback aceitável
```

---

## 2) QA Card (imprimível)

### 2.1 Pré‑export: checklist mínima
- [ ] Campos básicos: `version`, `meta`, `tokensRef`, `root` (E080)
- [ ] Um único `h1` por página (E120)
- [ ] Sem HEX em `props` (E201) e todos tokens existem (E200)
- [ ] `span` sempre 1..12 em todos BPs (E140)
- [ ] Regras por seção/variante:
  - Hero: `h1` (E083) e CTA com `href` (E084); se split, exige `media` (E085)
  - Benefits grid‑3: md≥3 cols + ≥3 cards (E086, E104 implícito)
  - Proofs grid‑2: ≥2 cards (E087)
  - Offer/panel + pricing: `title/price/CTA.href` (E081/E091)
  - FAQ accordion: cada item com `q/a` (E082)
  - CTA: botão com `href` (E088)

### 2.2 Fluxo de correção no Drawer U11
1. Abra **Errors** e aplique os **Quick‑Fix** quando disponíveis.
2. Use **Locate** para focar o nó no Canvas e revisar visualmente.
3. Revalide; se restarem apenas **Warnings**, o export é permitido.

### 2.3 Overlays úteis
- **Headings**: localiza h1/h2… para resolver E120 rapidamente.
- **Tab Order**: garante navegação de teclado lógica após fixes.
- **Contrast**: heatmap auxiliar; tokens podem afetar contraste.

### 2.4 Atalhos de fixes comuns
- Demover h1 extra → `demote-h1`
- Sem CTA → `add-cta` (href obrigatório)
- Span inválido → `set-span (bp,span)`
- Pricing card incompleto → `fill-offer-required`
- Grid sem gap (benefits) → `apply-gap (bp=md,gap=24)`

### 2.5 Política de warnings (não bloqueiam)
- Placeholders (W090), gaps sugeridos (W103), ratios (W104/W105): podem ser aceitos em protótipo, mas devem ser revisados antes de entrega final.

---

## 3) Observações
- Este cheat‑sheet segue o Parser v0.1.2 e o documento **Validation Codes Reference**.
- Se novos códigos forem adicionados, atualize ambos os documentos e o Drawer.

