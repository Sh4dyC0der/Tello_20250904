# Tello — Schema completo do `layout.json`

> Documento canônico. Serve como contrato entre editor, parser e quaisquer integrações (Brand‑Form, Notion, plugins). Salve este Markdown como referência única do schema do Tello.

---

## Visão geral
O `layout.json` descreve a árvore de componentes que o editor Tello manipula e que o Parser converte em HTML + Tailwind. É um objeto versionado com contratos claros para props (por breakpoint), layout (span/start/order), conteúdo e efeitos (fx).

**Regras chave**
- Versão do schema obrigatória (`version`), sempre semântica (ex.: `2.1.1`).
- `meta` obrigatório com pelo menos `name` e `createdAt` (ISO date preferível).
- `tokensRef` obrigatório apontando para o `tokens.json` que alimenta as CSS vars.
- `root` obrigatório — nó do tipo `section` que pode conter toda a página ou uma seção isolada.
- IDs ASCII, sem espaços; use `kebab-case` (ex.: `hero-main`, `offer-1`).

---

## Estrutura principal (alta-nível)
```json
{
  "version": "1.0",
  "meta": { "name": "Demo", "createdAt": "2025-09-03" },
  "tokensRef": "./tokens.json",
  "root": { /* node */ }
}
```

### Nó (`TelloNode`) — campos principais
- `id` (string) — obrigatório dentro do node; único por documento. ASCII, kebab-case.
- `type` (string) — obrigatório; valores permitidos (MVP): `section`, `container`, `grid`, `media`, `heading`, `paragraph`, `button`, `card`, `form`, `input`, `footer`.
- `name` (string) — opcional; label humano, usado no Canvas.
- `role` (string) — opcional; semantic role (ex.: `hero`, `benefits`, `offer`, `faq`, `cta`). Facilita validações e presets.
- `variant` (string) — opcional; ex.: `centered`, `split`, `grid-3`, `media-left`.
- `props` (object) — opcional; props por breakpoint. Veja seção Props.
- `layout` (object) — opcional; `span/start/order` por breakpoint. Veja seção Layout.
- `content` (any) — opcional; texto simples, object (e.g., for `button` { label, href }), arrays, etc.
- `fx` (array[string]) — opcional; lista de efeitos declarativos: `toggle`, `accordion`, `modal`, `tooltip`, `revealOnScroll`, etc.
- `children` (array[TelloNode]) — opcional; nó filho(s).

---

## Props (contrato por breakpoint)
Props aparecem agrupadas por breakpoint: `base`, `md`, `lg`, `xl`, `2xl`.

Exemplo:
```json
"props": {
  "base": { "pad": 24, "bg": "color.bg.page", "align": "center" },
  "md": { "pad": "md" }
}
```

Principais propriedades (MVP):
- `pad`: number (px) | enum {xs,sm,md,lg,xl}
- `bg`: token path (ex.: `color.bg.page`) **NÃO** aceitar hex (#...) — parser emite E201
- `tone`: semantic text tone (ex.: `dark`, `light`, `muted`) — mapeado para tokens de texto
- `max`: container max (ex.: `lg`, `xl`) — mapeia para `max-w-*`
- `align`: `start` | `center` | `end`
- `cols` (para `grid`): integer 1..12
- `gap` (para `grid`): number (px) | enum small/medium/large
- `radius`: number (px) | enum small/medium
- `elev`: integer (0..3)
- `ratio` (para `media`): string `16:9` | `1:1` etc.
- `fit` (para `media`): `cover` | `contain` | `fill`

**Regras**:
- Valores que representam tokens devem sempre vir como caminhos (ex.: `color.brand.primary`).
- Quando for necessário um valor arbitrário, o prop pode aceitar string que o parser mapeia para classes arbitrárias (`p-[24px]`). Isso gera `W063` se não estiver na escala padrão.

---

## Layout (span/start/order) — por breakpoint
`layout` descreve a ocupação do nó dentro de grids parent.

Exemplo:
```json
"layout": { "base": { "span": 12 }, "md": { "span": 6, "start": 1 } }
```

- `span`: integer 1..12 — **E140** se fora do range.
- `start`: integer 1..12 — optional
- `order`: integer — optional

Regras:
- `span` deve ser coerente com `grid.props.cols` do ancestor grid. Se houver mismatch, validação sinaliza E140.
- Não usar spans fracionados; usar integer clamped 1..12.

---

## Content — formatos por bloco
- `heading` → `content` pode ser string. `props.base.level` define `h1..h6`. Múltiplos `h1` no documento geram `E120`.
- `paragraph` → `content` string
- `button` → `content` object `{ "label": "text", "href": "#link" }` (href obrigatório para CTAs — E088)
- `card` → `content` string or object `{ title, text, price, bullets:[], primaryCTA:{href,label} }` (offer cards exigem `title`+`price`+`primaryCTA.href`)
- `media` → `content` object `{ alt: string, src?: string }` ou só `alt` quando assets externos são gerenciados fora
- `form/input` → `content` or props describing fields (fora do MVP detalhar)

---

## FX (efeitos declarativos)
- Lista suportada (MVP): `toggle`, `accordion`, `modal`, `tooltip`, `sticky`, `disclosure`, `revealOnScroll`, `transition`, `collapse`.
- `fx` é um array de strings no node. Se qualquer `fx` estiver presente no documento, o Parser injeta um loader condicional (ex.: Alpine CDN). Caso contrário, nada é carregado.
- Sempre preferir fallback CSS/HTML (`<details>`/`<summary>`) quando possível.

---

## Identification heuristics (role/variant)
Para templates automáticos e validações, prefira preencher `role` e `variant`. Se ausente, o editor e parser usam heurísticas:
- `inferRoleFromId` / `inferVariantFromId` por palavras-chave em `id` ou `name` (ex.: `hero`, `benefit`, `grid-3`, `split`).

Recomendo: sempre especifique `role` e `variant` nos seeds/templates.

---

## Exemplos mínimos e completos
### Exemplo mínimo válido
```json
{
  "version":"1.0",
  "meta":{ "name":"Minimal", "createdAt":"2025-09-03" },
  "tokensRef":"./tokens.json",
  "root":{
    "type":"section",
    "id":"root",
    "children":[
      { "type":"heading","id":"h1","props":{"base":{"level":"h1"}},"content":"Olá" }
    ]
  }
}
```

### Exemplo completo (hero + benefits)
> Use os templates seeds do pack para exemplos práticos (já gerados no pack). Não repito aqui para evitar poluição de leitura.

---

## Versionamento do schema
- Inclua `version` no topo do arquivo. Ao alterar o schema, incremente semântica (major.minor.patch) e documente migrações.
- Parser deve suportar um adaptador de versão: `if (layout.version !== SUPPORTED) -> use adapter to upgrade or fail with E0xx`.

---

## Validação (contrato)
O parser executa validações que retornam `validation.json` com estrutura padrão:
```json
{
  "errors":[ { "code":"E120","message":"Multiple h1", "nodeId":"h1-hero", "fix":{...} } ],
  "warnings":[ ... ]
}
```

Códigos usados no schema (resumo):
- E080, E120, E140, E200, E201, E081..E091, W063, W090, W101..W108

---

## Boas práticas e recomendações
- Produza seeds de templates com `role` e `variant` declarados.
- Use `tokensRef` e mantenha tokens sincronizados com Brand‑Form/Notion.
- IDs legíveis e curtos, sem caracteres especiais.
- Mantenha `content` simples; sempre normalize listas (bullets) no momento de salvar.
- Versione seeds (`landing-v1`, `landing-v1.1`) para rollback.

---

## Migrações e compatibilidade
- Ao introduzir novos campos, torne-os opcionais por 2 versões antes de torná‑los obrigatórios.
- Forneça um script `migrate-layout.js` que converta seeds antigos para o novo schema.

---

## Check-list de QA para um `layout.json` antes do export
- [ ] `version`, `meta`, `tokensRef`, `root` presentes
- [ ] IDs ASCII e únicos
- [ ] Nenhum HEX em `props`
- [ ] `h1` apenas uma vez por página
- [ ] `span` entre 1..12 para todos os nodes com layout
- [ ] Required por variant presentes (offer, faq, hero, benefits, proofs, cta)
- [ ] `tokensRef` aponta para arquivo válido

---

Documento criado. Este `layout.json` schema pode ser salvo como contrato. Próximo item recomendado: **Tokens Contract** (estrutura, naming conventions, exemplos e versões) — pronto para eu gerar quando quiser.

