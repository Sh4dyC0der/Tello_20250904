# Tello — Tokens Contract (`tokens.json`)

> Documento canônico para o contrato de tokens entre Brand‑Form (Notion), editor Tello e Parser. Se o `layout.json` é a receita, os `tokens.json` são os ingredientes. Guarde este arquivo como referência única para evitar drift, hexões soltos e decisões estéticas aleatórias.

---

## Objetivo
Padronizar a estrutura, nomenclatura e utilização de tokens (cores, espaçamentos, tipografia, sombras, radii, containers) que o Tello consome. O Parser flattenará esses tokens para CSS variables `--...` e o HTML usará classes Tailwind arbitrárias (ex.: `bg-[var(--color-brand-primary)]`).

Regras principais:
- `tokens.json` é a fonte de verdade das variáveis visuais. Atualize aqui e torne a edição por UI/Notion um processo controlado.
- Tokens devem usar paths sem espaços e com kebab-case (dots separam níveis): `color.brand.primary`.
- Valores finais (cores) podem ser hex/rgba/hsl. O parser aceita tokens com valores CSS válidos.
- Não coloque valores hex aleatórios nos `props` do `layout.json`. Referencie tokens aqui.

---

## Estrutura proposta (top-level)
```json
{
  "version": "1.0",
  "meta": { "source": "Brand-Form", "updatedAt": "2025-09-03" },
  "color": { ... },
  "space": { ... },
  "radius": { ... },
  "shadow": { ... },
  "container": { ... },
  "typography": { ... },
  "misc": { ... }
}
```

Cada categoria tem convenções e exemplos abaixo.

---

## 1) Color (naming e escalas)
**Estrutura** sugerida:
```json
"color": {
  "bg": { "page": "#0A0A0A", "surface": "#111827" },
  "text": { "default": "#E5E7EB", "muted": "#9CA3AF" },
  "brand": { "primary": "#22D3EE", "accent": "#A78BFA", "inverse": "#08132a" },
  "semantic": { "success": "#10b981", "warning": "#f59e0b", "danger": "#ef4444" }
}
```

### Convenções
- Níveis semânticos (bg/text/brand/semantic) ajudam a manter portabilidade entre temas.
- Use `bg.page` para fundo geral, `bg.surface` para cards/panels.
- `text.default` e `text.muted` para hierarquia tipográfica.
- `brand.*` sempre contém cores da identidade; evite usar `brand.primary-600` no layout — construa tokens adequados.

### Dark / Light
- Preferir manter dois arquivos (ou um objeto com `modes`):
  - `tokens.json` + `tokens.dark.json` ou
  - `color.modes.light` / `color.modes.dark` dentro do mesmo arquivo.
- O parser suporta `tokensRef` apontando para o arquivo correto; se você quiser toggle dinâmico, estabeleça `tokens.dark.json` separado.

---

## 2) Space (spacing scale)
**Estrutura**:
```json
"space": { "xs": 4, "sm": 8, "md": 16, "lg": 24, "xl": 32, "xxl": 48 }
```

### Convenções
- Valores em pixels (números). O Parser transforma em `--space-md: 16px`.
- Preferir um sistema 4px base (grid baseline 4px) para consistência.
- Use palavras curtas (`xs`, `sm`, `md`) para mapear direto para classes utilitárias.

---

## 3) Radius (corner radii)
```json
"radius": { "sm": 4, "md": 8, "lg": 12 }
```
- Números em px. Mapear para `--radius-md: 8px`.

---

## 4) Shadow
```json
"shadow": {
  "0": "0 0 #0000",
  "1": "0 1px 2px 0 rgb(0 0 0 / 0.05)",
  "2": "0 4px 6px -1px rgb(0 0 0 / 0.1)",
  "3": "0 10px 15px -3px rgb(0 0 0 / 0.15)"
}
```
- Strings CSS prontas; o parser injeta direto em `:root`.

---

## 5) Container
```json
"container": { "max": { "base": 640, "md": 768, "lg": 1024, "xl": 1280 } }
```
- Valores em px para `max-w-*` map ou para `max-width` em CSS variables.

---

## 6) Typography
```json
"typography": {
  "fontFamily": { "sans": "Inter, ui-sans-serif, system-ui" },
  "scale": { "sm": 14, "md": 16, "lg": 18, "h1": 36, "h2": 28 }
}
```
- Font sizes em px.
- Parser mapeia `h1` → `text-[var(--typography-h1)]` ou para classes utilitárias quando possível.

---

## 7) Misc / Tokens adicionais
Use `misc` para tokens menos comuns: `z-index`, `transitions`, `ratio-presets`, `iconsize` etc.

```json
"misc": { "z": { "overlay": 1000 }, "ratio": { "hero": "16:9" } }
```

---

## 8) Flattening → CSS vars (regras do Parser)
O Parser aplaina o objeto e gera `:root` com nomes hyphenados:
- `color.brand.primary` → `--color-brand-primary: #22D3EE;`
- `space.md` → `--space-md: 16px;`
- `typography.scale.h1` → `--typography-scale-h1: 36px;`

### Regras de nomenclatura
- Pontos → hífen; tudo em lower-case; remover caracteres não alfanuméricos.
- Prefixo `--` obrigatório.
- Valores numéricos (space, radius) são postfixed com `px`.

---

## 9) Versioning e Migrations
- `tokens.json` deve ter `version` top-level.
- Ao mudar chaves, gere um `tokens.migrate.js` que converta nomes antigos para os novos.
- Evite renomear tokens sem criar aliases; o Parser pode suportar `aliases` map para retrocompatibilidade.

Exemplo:
```json
"aliases": { "brandPrimary": "color.brand.primary" }
```

---

## 10) Validations & QA
Validações que o Parser aplica:
- Valores de cor válidos (hex/rgba/hsl). Caso inválido → `T001` (token malformed).
- Espaçamento/radius números positivos → `T002`.
- Tipografia em px positivos → `T003`.
- Duplicate keys → `T004`.

**Check-list antes de publicar tokens**
- [ ] `version` e `meta` preenchidos
- [ ] Todas as cores principais definidas (`bg.page`, `bg.surface`, `text.default`, `brand.primary`)
- [ ] Espaçamentos consistentes (multiples of 4)
- [ ] Radii e shadows definidos
- [ ] Container max para breakpoints importantes

---

## 11) Integração com Brand‑Form (Notion)
- Mantenha um workflow: Designer atualiza Brand‑Form → exporta tokens.json → validações automáticas → commit na branch tokens/
- Se o Brand‑Form é uma fonte do Notion, automatize export via script que escreve `tokens.example.json` no repo.
- Evite edição manual do `layout.json` para ajustar cores. Sempre ajuste tokens.

---

## 12) Best practices e anti‑drift
- Nunca referencie hex direto em `layout.json` props; sempre use `color.*` tokens.
- Prefira tokens semânticos (`bg.surface`) em vez de `brand.primary` diretamente no layout (permite swap de paleta sem mexer em layouts).
- Mantenha token names estáveis; renomear gera debts.
- Adote review gate para qualquer mudança de token (pull request + checks).

---

## 13) Exemplos práticos
### tokens.example.json (simples)
```json
{
  "version": "1.0",
  "meta": { "source": "example", "updatedAt": "2025-09-03" },
  "color": {
    "bg": { "page": "#0A0A0A", "surface": "#111827" },
    "text": { "default": "#E5E7EB", "muted": "#9CA3AF" },
    "brand": { "primary": "#22D3EE", "accent": "#A78BFA" },
    "semantic": { "success": "#10b981", "warning": "#f59e0b", "danger": "#ef4444" }
  },
  "space": { "xs": 4, "sm": 8, "md": 16, "lg": 24, "xl": 32 },
  "radius": { "sm": 4, "md": 8, "lg": 12 },
  "shadow": { "0": "0 0 #0000", "1": "0 1px 2px 0 rgb(0 0 0 / 0.05)", "2": "0 4px 6px -1px rgb(0 0 0 / 0.1)" },
  "container": { "max": { "base": 640, "md": 768, "lg": 1024 } },
  "typography": { "fontFamily": { "sans": "Inter, ui-sans-serif, system-ui" }, "scale": { "sm": 14, "md": 16, "lg": 18, "h1": 36 } }
}
```

---

## 14) Automação recomendada
- `validate-tokens.js` — script que roda antes do commit e valida keys/values.
- `generate-css-vars.js` — para debug local, cria um `tokens.css` com `:root{}`.
- `sync-notion-to-repo` — webhook/automation que salva export automático do Brand‑Form.

---

## 15) Erros e códigos relacionados (referência rápida)
- `T001` — token malformed (cor inválida)
- `T002` — spacing/radius invalid
- `T003` — typography invalid
- `T004` — duplicate key
- `E200` — token referenced in layout not found (gating on export)

---

## 16) FAQ breve
**Posso ter tokens dinâmicos por cliente?**
- Sim. Use per-customer `tokens.{client}.json` e compile no build/preview.

**Posso usar porcentagens nas tokens de espaço?**
- Evite. Use px para previsibilidade; apenas `misc` pode conter strings css.

---

## Conclusão
Este contrato evita desordem e drift entre branding e entrega técnica. Atualize o `version` aqui sempre que alterar a shape do arquivo. Próximo documento recomendado: **Validation Codes Reference**. Eu gero quando você pedir.

