# Tello — Pack de Templates (8) — Markdown

> Conteúdo: 8 templates JSON compatíveis com schema v2.1.1 e com as regras de validação implementadas (E08x/E09x/E14x/E20x etc). Copie/cole cada bloco em um arquivo `.json` com os nomes indicados. Não há downloads aqui — apenas um blueprint limpo e pronto para colar.

---

## Instruções Rápidas

1. Copie o bloco JSON de cada seção e salve como o nome indicado (por exemplo `landing-canonical.json`).
2. Valide com o parser (v0.1.2+) que retorna `validation.json`.
3. Corrija quick-fixes via Drawer U11 (ou aplique handlers de fix programaticamente).
4. Quando `validation.json.errors` estiver vazio, gere `index.html` com o parser.

---

## Índice de templates

1. `landing-canonical.json` — página completa (hero, benefits, proofs, offer, faq, cta)
2. `landing-split-pricing.json` — hero split + features media-left + logos + pricing table + cta split
3. `section-hero-centered.json` — seção Hero centered
4. `section-hero-split.json` — seção Hero split (conteúdo + media)
5. `section-benefits-grid3.json` — seção Benefits em grid 3
6. `section-features-media-left.json` — features com mídia à esquerda
7. `section-proofs-logos-row.json` — logos row (provas sociais)
8. `section-faq-accordion.json` — FAQ com accordion (fx)

---

> **Nota:** Cada template já respeita as regras: sem HEX cru em props; CTAs com `href` onde obrigatório; spans coerentes; tokens via `tokensRef`.

---

# 1) `landing-canonical.json`

```json
{
  "version": "1.0",
  "meta": { "name": "Landing — Canonical", "createdAt": "2025-09-03" },
  "tokensRef": "./tokens.example.json",
  "root": {
    "type": "section",
    "id": "page-root",
    "props": { "base": { "pad": 24 } },
    "children": [
      {
        "type": "section",
        "id": "hero-centered",
        "name": "Hero",
        "props": { "base": { "pad": "xl", "bg": "color.bg.page" } },
        "children": [
          { "type": "container", "id": "ctr-hero", "props": { "base": { "max": "lg", "align": "center" } }, "children": [
            { "type": "heading", "id": "h1", "props": { "base": { "level": "h1" } }, "content": "Título de impacto" },
            { "type": "paragraph", "id": "h-sub", "props": { "base": { "tone": "muted", "maxWidth": "md" } }, "content": "Subtexto direto e curto." },
            { "type": "button", "id": "h-cta", "props": { "base": { "variant": "primary", "size": "lg" } }, "content": { "label": "Começar", "href": "#offer" } }
          ] }
        ]
      },
      {
        "type": "section",
        "id": "benefits-grid3",
        "name": "Benefits",
        "props": { "base": { "pad": 24 } },
        "children": [
          { "type": "container", "id": "ctr-b", "children": [
            { "type": "grid", "id": "gr-b", "props": { "base": { "cols": 1, "gap": 16 }, "md": { "cols": 3, "gap": 24 } }, "children": [
              { "type": "card", "id": "b1", "props": { "base": { "pad": 16 } }, "content": "Velocidade" },
              { "type": "card", "id": "b2", "props": { "base": { "pad": 16 } }, "content": "Consistência" },
              { "type": "card", "id": "b3", "props": { "base": { "pad": 16 } }, "content": "A11y de fábrica" }
            ] }
          ] }
        ]
      },
      {
        "type": "section",
        "id": "proofs-grid2",
        "name": "Proofs",
        "props": { "base": { "pad": 24 } },
        "children": [
          { "type": "container", "id": "ctr-pr", "children": [
            { "type": "grid", "id": "gr-pr", "props": { "base": { "cols": 1, "gap": 16 }, "md": { "cols": 2, "gap": 24 } }, "children": [
              { "type": "card", "id": "pr1", "content": "“Melhor decisão do trimestre.” — Cliente A" },
              { "type": "card", "id": "pr2", "content": "“Entrega sem retrabalho.” — Cliente B" }
            ] }
          ] }
        ]
      },
      {
        "type": "section",
        "id": "offer-panel",
        "name": "Offer",
        "props": { "base": { "pad": 24 } },
        "children": [
          { "type": "container", "id": "ctr-offer", "children": [
            { "type": "card", "id": "offer1", "props": { "base": { "pad": 16, "radius": 12 } }, "content": {
              "title": "Plano Inicial",
              "price": "R$ 499",
              "bullets": ["Setup tokens", "Landing 1 seção hero + 4 blocos", "Export HTML pronto"],
              "primaryCTA": { "href": "#contact", "label": "Contratar" }
            } }
          ] }
        ]
      },
      {
        "type": "section",
        "id": "faq-accordion",
        "name": "FAQ",
        "props": { "base": { "pad": 24 } },
        "children": [
          { "type": "container", "id": "ctr-faq", "children": [
            { "type": "card", "id": "faq1", "fx": ["accordion"], "content": { "q": "Prazo?", "a": "Entrega em 48–72h salvo exceções." } },
            { "type": "card", "id": "faq2", "fx": ["accordion"], "content": { "q": "Revisões?", "a": "1 round incluído no MVP." } }
          ] }
        ]
      },
      {
        "type": "section",
        "id": "cta-centered",
        "name": "CTA",
        "props": { "base": { "pad": 24 } },
        "children": [
          { "type": "container", "id": "ctr-cta", "props": { "base": { "align": "center" } }, "children": [
            { "type": "button", "id": "cta-final", "props": { "base": { "variant": "primary", "size": "lg" } }, "content": { "label": "Comece agora", "href": "#contact" } }
          ] }
        ]
      }
    ]
  }
}
```

---

# 2) `landing-split-pricing.json`

```json
{
  "version": "1.0",
  "meta": { "name": "Landing — Split + Pricing", "createdAt": "2025-09-03" },
  "tokensRef": "./tokens.example.json",
  "root": {
    "type": "section",
    "id": "page-root",
    "props": { "base": { "pad": 24 } },
    "children": [
      {
        "type": "section",
        "id": "hero-split",
        "name": "Hero",
        "props": { "base": { "pad": "xl" } },
        "children": [
          { "type": "container", "id": "ctr-hero", "children": [
            { "type": "grid", "id": "gr-hero", "props": { "base": { "cols": 1 }, "md": { "cols": 12, "gap": 24 } }, "children": [
              { "type": "container", "id": "hero-content", "layout": { "md": { "span": 7 } }, "children": [
                { "type": "heading", "id": "h1", "props": { "base": { "level": "h1" } }, "content": "Crie páginas mais rápido" },
                { "type": "paragraph", "id": "h-sub", "props": { "base": { "tone": "muted" } }, "content": "Blocos, tokens e validação de fábrica." },
                { "type": "button", "id": "h-cta", "props": { "base": { "variant": "primary", "size": "lg" } }, "content": { "label": "Experimentar", "href": "#pricing" } }
              ] },
              { "type": "media", "id": "hero-media", "props": { "base": { "ratio": "16:9", "fit": "cover" } }, "layout": { "md": { "span": 5 } }, "content": { "alt": "Screenshot" } }
            ] }
          ] }
        ]
      },

      {
        "type": "section",
        "id": "features-media-left",
        "name": "Features",
        "props": { "base": { "pad": 24 } },
        "children": [
          { "type": "container", "id": "ctr-feat", "children": [
            { "type": "grid", "id": "feat1", "props": { "base": { "cols": 1, "gap": 16 }, "md": { "cols": 12, "gap": 24 } }, "children": [
              { "type": "media", "id": "f1m", "props": { "base": { "ratio": "16:9", "fit": "cover" } }, "layout": { "md": { "span": 4 } }, "content": { "alt": "Feature 1" } },
              { "type": "card", "id": "f1c", "layout": { "md": { "span": 8 } }, "content": "Editor por blocos com snap 4 px" }
            ] },
            { "type": "grid", "id": "feat2", "props": { "base": { "cols": 1, "gap": 16 }, "md": { "cols": 12, "gap": 24 } }, "children": [
              { "type": "media", "id": "f2m", "props": { "base": { "ratio": "16:9", "fit": "cover" } }, "layout": { "md": { "span": 4 } }, "content": { "alt": "Feature 2" } },
              { "type": "card", "id": "f2c", "layout": { "md": { "span": 8 } }, "content": "Exporta HTML + Tailwind + tokens" }
            ] },
            { "type": "grid", "id": "feat3", "props": { "base": { "cols": 1, "gap": 16 }, "md": { "cols": 12, "gap": 24 } }, "children": [
              { "type": "media", "id": "f3m", "props": { "base": { "ratio": "16:9", "fit": "cover" } }, "layout": { "md": { "span": 4 } }, "content": { "alt": "Feature 3" } },
              { "type": "card", "id": "f3c", "layout": { "md": { "span": 8 } }, "content": "Validação com quick-fixes" }
            ] }
          ] }
        ]
      },

      {
        "type": "section",
        "id": "proofs-logos-row",
        "name": "Proofs",
        "props": { "base": { "pad": 24 } },
        "children": [
          { "type": "container", "id": "ctr-logos", "children": [
            { "type": "grid", "id": "gr-logos", "props": { "base": { "cols": 2, "gap": 16 }, "md": { "cols": 6, "gap": 24 } }, "children": [
              { "type": "media", "id": "lg1", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 1" } },
              { "type": "media", "id": "lg2", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 2" } },
              { "type": "media", "id": "lg3", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 3" } },
              { "type": "media", "id": "lg4", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 4" } },
              { "type": "media", "id": "lg5", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 5" } },
              { "type": "media", "id": "lg6", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 6" } }
            ] }
          ] }
        ]
      },

      {
        "type": "section",
        "id": "offer-pricing-table",
        "name": "Offer",
        "props": { "base": { "pad": 24 } },
        "children": [
          { "type": "container", "id": "ctr-price", "children": [
            { "type": "grid", "id": "gr-price", "props": { "base": { "cols": 1, "gap": 16 }, "md": { "cols": 3, "gap": 24 } }, "children": [
              { "type": "card", "id": "p1", "props": { "base": { "pad": 16 } }, "content": { "title": "Start", "price": "R$ 499", "bullets": ["Hero + 4 blocos"], "primaryCTA": { "href": "#buy", "label": "Escolher" } } },
              { "type": "card", "id": "p2", "props": { "base": { "pad": 16 } }, "content": { "title": "Pro", "price": "R$ 899", "bullets": ["+ Seções extras"], "primaryCTA": { "href": "#buy", "label": "Escolher" } } },
              { "type": "card", "id": "p3", "props": { "base": { "pad": 16 } }, "content": { "title": "Max", "price": "R$ 1299", "bullets": ["Tudo do Pro + templates"], "primaryCTA": { "href": "#buy", "label": "Escolher" } } }
            ] }
          ] }
        ]
      },

      {
        "type": "section",
        "id": "cta-split",
        "name": "CTA",
        "props": { "base": { "pad": 24 } },
        "children": [
          { "type": "container", "id": "ctr-cta", "children": [
            { "type": "grid", "id": "gr-cta", "props": { "base": { "cols": 1 }, "md": { "cols": 12, "gap": 24 } }, "children": [
              { "type": "paragraph", "id": "cta-text", "layout": { "md": { "span": 8 } }, "content": "Pronto para avançar? Feche agora." },
              { "type": "button", "id": "cta-btn", "layout": { "md": { "span": 4 } }, "props": { "base": { "variant": "primary", "size": "lg" } }, "content": { "label": "Contratar", "href": "#contact" } }
            ] }
          ] }
        ]
      }
    ]
  }
}
```

---

# 3) `section-hero-centered.json`

```json
{
  "version": "1.0",
  "meta": { "name": "Section — Hero Centered", "createdAt": "2025-09-03" },
  "tokensRef": "./tokens.example.json",
  "root": {
    "type": "section",
    "id": "hero-centered",
    "props": { "base": { "pad": "xl", "bg": "color.bg.page" } },
    "children": [
      { "type": "container", "id": "ctr-hero", "props": { "base": { "max": "lg", "align": "center" } }, "children": [
        { "type": "heading", "id": "h1", "props": { "base": { "level": "h1" } }, "content": "Título direto" },
        { "type": "paragraph", "id": "p", "props": { "base": { "tone": "muted", "maxWidth": "md" } }, "content": "Subheadline." },
        { "type": "button", "id": "cta", "props": { "base": { "variant": "primary", "size": "lg" } }, "content": { "label": "Começar", "href": "#contact" } }
      ] }
    ]
  }
}
```

---

# 4) `section-hero-split.json`

```json
{
  "version": "1.0",
  "meta": { "name": "Section — Hero Split", "createdAt": "2025-09-03" },
  "tokensRef": "./tokens.example.json",
  "root": {
    "type": "section",
    "id": "hero-split",
    "props": { "base": { "pad": "xl" } },
    "children": [
      { "type": "container", "id": "ctr-hero", "children": [
        { "type": "grid", "id": "gr-hero", "props": { "base": { "cols": 1 }, "md": { "cols": 12, "gap": 24 } }, "children": [
          { "type": "container", "id": "hero-content", "layout": { "md": { "span": 7 } }, "children": [
            { "type": "heading", "id": "h1", "props": { "base": { "level": "h1" } }, "content": "Título forte" },
            { "type": "paragraph", "id": "p", "props": { "base": { "tone": "muted" } }, "content": "Texto breve e objetivo." },
            { "type": "button", "id": "cta", "props": { "base": { "variant": "primary", "size": "lg" } }, "content": { "label": "Assinar", "href": "#pricing" } }
          ] },
          { "type": "media", "id": "hero-media", "layout": { "md": { "span": 5 } }, "props": { "base": { "ratio": "16:9", "fit": "cover" } }, "content": { "alt": "Imagem" } }
        ] }
      ] }
    ]
  }
}
```

---

# 5) `section-benefits-grid3.json`

```json
{
  "version": "1.0",
  "meta": { "name": "Section — Benefits Grid 3", "createdAt": "2025-09-03" },
  "tokensRef": "./tokens.example.json",
  "root": {
    "type": "section",
    "id": "benefits-grid3",
    "props": { "base": { "pad": 24 } },
    "children": [
      { "type": "container", "id": "ctr-b", "children": [
        { "type": "grid", "id": "gr-b", "props": { "base": { "cols": 1, "gap": 16 }, "md": { "cols": 3, "gap": 24 } }, "children": [
          { "type": "card", "id": "b1", "props": { "base": { "pad": 16 } }, "content": "Velocidade" },
          { "type": "card", "id": "b2", "props": { "base": { "pad": 16 } }, "content": "Consistência" },
          { "type": "card", "id": "b3", "props": { "base": { "pad": 16 } }, "content": "Export pronto" }
        ] }
      ] }
    ]
  }
}
```

---

# 6) `section-features-media-left.json`

```json
{
  "version": "1.0",
  "meta": { "name": "Section — Features Media Left", "createdAt": "2025-09-03" },
  "tokensRef": "./tokens.example.json",
  "root": {
    "type": "section",
    "id": "features-media-left",
    "props": { "base": { "pad": 24 } },
    "children": [
      { "type": "container", "id": "ctr-feat", "children": [
        { "type": "grid", "id": "f1", "props": { "base": { "cols": 1, "gap": 16 }, "md": { "cols": 12, "gap": 24 } }, "children": [
          { "type": "media", "id": "f1m", "layout": { "md": { "span": 4 } }, "props": { "base": { "ratio": "16:9", "fit": "cover" } }, "content": { "alt": "Imagem 1" } },
          { "type": "card", "id": "f1c", "layout": { "md": { "span": 8 } }, "content": "Blocos reutilizáveis" }
        ] },
        { "type": "grid", "id": "f2", "props": { "base": { "cols": 1, "gap": 16 }, "md": { "cols": 12, "gap": 24 } }, "children": [
          { "type": "media", "id": "f2m", "layout": { "md": { "span": 4 } }, "props": { "base": { "ratio": "16:9", "fit": "cover" } }, "content": { "alt": "Imagem 2" } },
          { "type": "card", "id": "f2c", "layout": { "md": { "span": 8 } }, "content": "Tokens → CSS vars" }
        ] }
      ] }
    ]
  }
}
```

---

# 7) `section-proofs-logos-row.json`

```json
{
  "version": "1.0",
  "meta": { "name": "Section — Proofs Logos Row", "createdAt": "2025-09-03" },
  "tokensRef": "./tokens.example.json",
  "root": {
    "type": "section",
    "id": "proofs-logos-row",
    "props": { "base": { "pad": 24 } },
    "children": [
      { "type": "container", "id": "ctr-logos", "children": [
        { "type": "grid", "id": "gr-logos", "props": { "base": { "cols": 2, "gap": 16 }, "md": { "cols": 6, "gap": 24 } }, "children": [
          { "type": "media", "id": "lg1", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 1" } },
          { "type": "media", "id": "lg2", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 2" } },
          { "type": "media", "id": "lg3", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 3" } },
          { "type": "media", "id": "lg4", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 4" } },
          { "type": "media", "id": "lg5", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 5" } },
          { "type": "media", "id": "lg6", "props": { "base": { "ratio": "1:1", "fit": "contain" } }, "content": { "alt": "Logo 6" } }
        ] }
      ] }
    ]
  }
}
```

---

# 8) `section-faq-accordion.json`

```json
{
  "version": "1.0",
  "meta": { "name": "Section — FAQ Accordion", "createdAt": "2025-09-03" },
  "tokensRef": "./tokens.example.json",
  "root": {
    "type": "section",
    "id": "faq-accordion",
    "props": { "base": { "pad": 24 } },
    "children": [
      { "type": "container", "id": "ctr-faq", "children": [
        { "type": "paragraph", "id": "faq-title", "content": "Dúvidas frequentes" },
        { "type": "card", "id": "faq1", "fx": ["accordion"], "content": { "q": "Como funciona?", "a": "Monte blocos, valide e exporte HTML." } },
        { "type": "card", "id": "faq2", "fx": ["accordion"], "content": { "q": "Posso editar tokens?", "a": "Tokens vêm do Brand-Form (Notion)." } },
        { "type": "card", "id": "faq3", "fx": ["accordion"], "content": { "q": "Entrega?", "a": "HTML único com Tailwind + vars." } }
      ] }
    ]
  }
}
```

---

## Próximos passos sugeridos (sem execução automática)

- Rodar stress-tests lógicos contra o validador (spans inválidos, tokens faltando, hex em props, h1 duplicado, oferta sem price). O validador vai gerar `validation.json` com códigos E/W.
- Se `validation.json.errors` não estiver vazio, aplicar quick-fixes via handlers já preparados.
- Iterar variações menores (trocar `hero` variantes, ajustar `gap`, trocar `max` do container) e manter versionamento de seeds (ex.: `landing-v1`, `landing-v1.1`).

---

Feito. Está tudo aqui: 8 templates completos, prontos para uso com o parser e as regras do Tello. Se quer que eu rode os stress-tests agora, eu executo a suíte lógica e te dou o relatório — sem baixar nada até o pacote estiver 100% limpo.

