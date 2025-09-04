# Tello — Blocks Library (MVP U1–U12)

> Documento canônico dos blocos do Tello: tipos, props por breakpoint, conteúdo, filhos permitidos, mapeamento para Tailwind e validações. Compatível com Parser v0.1.2, Tokens Contract e Validation Codes Reference.

---

## Índice
- Regras gerais
- Tipos de bloco (MVP)
  - section
  - container
  - grid
  - media
  - heading
  - paragraph
  - button
  - card
  - form
  - input
  - footer
- Convenções de responsividade
- Regras de validação por bloco (E/W)
- Exemplos rápidos (snippets JSON)

---

## Regras gerais
- **IDs**: ASCII, kebab-case, únicos por documento. Ex.: `hero-main`, `offer-1`.
- **Breakpoints**: `base`, `md`, `lg`, `xl`, `2xl`.
- **Props**: agrupadas por breakpoint. Ex.: `props.base.pad`, `props.md.cols`.
- **Layout**: `layout.{bp}.{span|start|order}`. `span` 1..12 (**E140** se inválido).
- **Tokens**: só via paths (`color.*`, `space.*`). HEX em props é proibido (**E201**).
- **FX**: array de strings; Alpine só é injetado se houver `fx` em algum nó.
- **A11y**: 1 h1 por página (**E120**). `media` requer `alt` significativo (warning se vazio; quick-fix `add-alt`).

---

## Tipos de bloco

### 1) `section`
- **Uso**: agrupador semântico maior; raiz ou subdivisões da página.
- **Filhos permitidos**: quaisquer blocos (geralmente `container`).
- **Props**: `pad`, `bg`, `tone`, `radius`, `elev`.
- **Layout**: opcional; quando dentro de `grid`.
- **Map (Tailwind)**: `pad` → `p-*`; `bg` → `bg-[var(--...)]`; `tone` → `text-[var(--...)]`.
- **Validações**: nenhuma específica; segue regras de variantes quando `role/variant` preenchidos (ex.: `hero.*`).

### 2) `container`
- **Uso**: restrição de largura e alinhamento.
- **Props**: `max` (`sm|md|lg|xl`), `align` (`start|center|end`), `pad` opcional.
- **Filhos**: qualquer.
- **Map**: `max` → `max-w-*` + `mx-auto`; `align` → `text-left|center|right`.
- **Validações**: `hero.centered` sugere `max: lg|xl` (**W101**).

### 3) `grid`
- **Uso**: layout em colunas.
- **Props**: `cols` (1..12), `gap` (px ou escala), opcionais por BP.
- **Filhos**: qualquer; filhos usam `layout.{bp}.span|start|order`.
- **Map**: `grid grid-cols-{n}` + `md:grid-cols-{n}`; `gap-*` com prefixos.
- **Validações**: checagens por variante (benefits/proofs/pricing), `gap` sugerido em md (**W103**), spans fora do range (**E140**).

### 4) `media`
- **Uso**: imagem, ilustração, mock de vídeo.
- **Props**: `ratio` (`16:9`, `1:1`), `fit` (`cover|contain|fill`), `radius` opcional.
- **Content**: `{ alt: string, src?: string }`.
- **Map**: container `figure` + utilitárias de aspecto (via classes custom/arbitrárias quando necessário).
- **Validações**: `alt` recomendado (quick-fix `add-alt`); `ratio` sugerido em features/logos (**W104/W105**); `hero.split` exige `media` (**E085**).

### 5) `heading`
- **Uso**: títulos `h1..h6`.
- **Props**: `level` (`h1..h6`), `tone`, `align`, `maxWidth`.
- **Content**: string.
- **Map**: tag HTML nativa (`<h1>` etc.) + utilitárias.
- **Validações**: apenas 1 `h1` por documento (**E120**). Quick-fix `demote-h1`.

### 6) `paragraph`
- **Uso**: textos longos/curtos.
- **Props**: `tone`, `align`, `maxWidth`.
- **Content**: string.
- **Map**: `<p>` + utilitárias.

### 7) `button`
- **Uso**: CTA/link.
- **Props**: `variant` (`primary|secondary|ghost`), `size` (`sm|md|lg`), `full?` (bool).
- **Content**: `{ label: string, href: string }`.
- **Map**: `<a>` com classes; se `full` → `w-full`.
- **Validações**: CTA exige `href` em heróis/cta/offer/pricing (**E084/E088/E091**). Quick-fix `add-cta`.

### 8) `card`
- **Uso**: unidade de conteúdo (feature, benefit, proof, pricing, faq item).
- **Props**: `pad`, `radius`, `elev`, `tone`.
- **Content**: string ou objeto (ex.: pricing `{ title, price, bullets[], primaryCTA{href,label} }`, faq `{ q, a }`).
- **Map**: `<article>` com padding, radius, shadow.
- **Validações**:
  - `offer.panel` precisa `title/price/primaryCTA.href` (**E081**).
  - `pricing-table` cada card precisa `price`+CTA (**E091**).
  - `faq.accordion` cada card precisa `q/a` (**E082**).
  - Conteúdo vazio vira placeholder (**W090**).

### 9) `form` (MVP mínimo)
- **Uso**: agrupador de inputs simples.
- **Props**: `layout` (grid 1..2 cols), `gap`.
- **Content**: opcional.
- **Filhos**: `input`.
- **Map**: `<form>` + `grid` (quando `layout` usar cols/gap via props).
- **Validações**: nenhuma bloqueante no MVP; foco em A11y no futuro (labels, required, etc.).

### 10) `input` (MVP mínimo)
- **Uso**: campos de formulário (apenas estrutura, sem lógica complexa).
- **Props**: `type` (`text|email|tel|checkbox`), `size` (`sm|md|lg`), `full?`.
- **Content**: `{ label?: string, placeholder?: string }`.
- **Map**: `<input>` + utilitárias; label opcional como `<label>` adjacente.

### 11) `footer`
- **Uso**: rodapé básico.
- **Props**: `pad`, `bg`, `tone`.
- **Filhos**: livre (links, logos, social).
- **Map**: `<footer>`.

---

## Convenções de responsividade
- Para cada prop de estilo: aplique prefixos por BP quando definidos. Ex.: `props.md.pad → md:p-*`.
- Para grid: `cols/gap` por BP. Filhos aplicam `layout.{bp}.span|start|order` → `md:col-span-*` etc.
- O Parser v0.1.2 já suporta mapping com `BP_PREFIX`.

---

## Regras de validação por bloco (resumo)
- **heading**: único `h1` (**E120**) → `demote-h1`.
- **grid**: spans 1..12 (**E140**) → `set-span`.
- **button**: `href` obrigatório em contextos de CTA/hero/offer (**E084/E088/E091**) → `add-cta`.
- **media**: `alt` sugerido (**add-alt**), `ratio` recomendado em certos padrões (**W104/W105**).
- **card**: placeholders para texto faltante (**W090**); estruturas específicas em offer/pricing/faq (**E081/E091/E082**).

---

## Exemplos rápidos

### Section + Container + Heading + Button (Hero Centered)
```json
{
  "type":"section","id":"hero","props":{"base":{"pad":"xl","bg":"color.bg.page"}},
  "children":[
    {"type":"container","id":"ctr","props":{"base":{"max":"lg","align":"center"}},
      "children":[
        {"type":"heading","id":"h1","props":{"base":{"level":"h1"}},"content":"Título"},
        {"type":"paragraph","id":"sub","props":{"base":{"tone":"muted","maxWidth":"md"}},"content":"Texto."},
        {"type":"button","id":"cta","props":{"base":{"variant":"primary","size":"lg"}},"content":{"label":"Começar","href":"#"}}
      ]
    }
  ]
}
```

### Grid com 3 cards (Benefits Grid‑3)
```json
{
  "type":"section","id":"benefits","props":{"base":{"pad":24}},
  "children":[
    {"type":"container","id":"ctr","children":[
      {"type":"grid","id":"gr","props":{"base":{"cols":1,"gap":16},"md":{"cols":3,"gap":24}},
        "children":[
          {"type":"card","id":"b1","props":{"base":{"pad":16}},"content":"Velocidade"},
          {"type":"card","id":"b2","props":{"base":{"pad":16}},"content":"Consistência"},
          {"type":"card","id":"b3","props":{"base":{"pad":16}},"content":"A11y de fábrica"}
        ]
      }
    ]}
  ]
}
```

### Hero Split (conteúdo + media)
```json
{
  "type":"section","id":"hero-split","props":{"base":{"pad":"xl"}},
  "children":[
    {"type":"container","id":"ctr","children":[
      {"type":"grid","id":"gr","props":{"base":{"cols":1},"md":{"cols":12,"gap":24}},
        "children":[
          {"type":"container","id":"content","layout":{"md":{"span":7}},
            "children":[
              {"type":"heading","id":"h1","props":{"base":{"level":"h1"}},"content":"Crie páginas mais rápido"},
              {"type":"paragraph","id":"p","props":{"base":{"tone":"muted"}},"content":"Blocos, tokens, validação"},
              {"type":"button","id":"cta","props":{"base":{"variant":"primary","size":"lg"}},"content":{"label":"Experimentar","href":"#pricing"}}
            ]
          },
          {"type":"media","id":"mock","layout":{"md":{"span":5}},"props":{"base":{"ratio":"16:9","fit":"cover"}},"content":{"alt":"Screenshot"}}
        ]
      }
    ]}
  ]
}
```

---

## Observações finais
- Este documento completa o trio canônico com **Layout Schema**, **Tokens Contract** e **Validation Codes**.
- Qualquer novo bloco deve seguir este formato: props por BP, layout claro, conteúdo tipado, regras de validação e snippet de exemplo.

