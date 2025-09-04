# Tello — FX Inventory & Engine

> Documento canônico do subsistema de efeitos declarativos do Tello (FX). Compatível com Parser v0.1.2, Drawer U11 e Tokens Contract.

---

## Objetivo
Permitir enriquecer seções com comportamentos padrões (accordion, modal, tooltip etc.) sem código imperativo manual no layout. A **definição é declarativa** no `layout.json` e o Parser injeta apenas o que é necessário.

**Princípios**
- Sem dependências quando nenhum `fx` é usado.
- Fallback HTML/CSS quando couber (`<details>`/`<summary>`).
- A11y e teclado padronizados; foco nunca se perde.
- Configuração por nó, com herança simples e sobreposição por breakpoint quando aplicável.

---

## Inventário de FX (MVP)
| FX | Finalidade | Fallback | Dep. | A11y principal |
|---|---|---|---|---|
| `accordion` | Expandir/contrair lista de itens (FAQ) | `<details>` | opcional | `aria-expanded`, `aria-controls`, foco em título |
| `modal` | Diálogo sobreposto | `<dialog>` se suportado | opcional | `role="dialog"`, `aria-modal="true"`, trap de foco, `Esc` fecha |
| `tooltip` | Dica em foco/hover | título/`aria-describedby` | nenhuma | não bloquear leitura; `Esc` oculta |
| `sticky` | Header/CTA fixo ao rolar | CSS `position: sticky` | nenhuma | semântico intacto |
| `disclosure` | Mostrar/ocultar bloco único | `<details>` | nenhuma | `aria-expanded` |
| `revealOnScroll` | Anima entrada ao entrar na viewport | sem | nenhuma | respeitar `prefers-reduced-motion` |
| `transition` | Transições utilitárias (opacity/transform) | sem | nenhuma | respeitar `reduced-motion` |
| `collapse` | Expandir/colapsar altura | `max-height` CSS | opcional | `aria-expanded`, conteúdo acessível ao foco |

> **Dep. opcional** = Alpine só carrega se `engine: "alpine"` e houver pelo menos um efeito que precise JavaScript além do fallback.

---

## Modelo de dados (layout.json)
Há duas formas válidas; o Parser normaliza para a forma canônica (array de objetos):

**Atalho (array de strings)**
```json
{"fx": ["accordion"]}
```

**Canônico (array de objetos)**
```json
{
  "fx": [
    { "type": "accordion", "props": { "multiple": false, "persistOpen": true } }
  ]
}
```

### `fx[].props` por efeito
- `accordion`: `{ multiple?: boolean, persistOpen?: boolean, defaultOpenIds?: string[] }`
- `modal`: `{ closeOnEsc?: boolean, closeOnBackdrop?: boolean, initialFocusId?: string }`
- `tooltip`: `{ placement?: "top"|"right"|"bottom"|"left", delay?: number }`
- `sticky`: `{ top?: number }`
- `disclosure`: `{ defaultOpen?: boolean }`
- `revealOnScroll`: `{ threshold?: number (0..1), once?: boolean }`
- `transition`: `{ enter?: string, enterFrom?: string, enterTo?: string, leave?: string, leaveFrom?: string, leaveTo?: string }`
- `collapse`: `{ duration?: number, easing?: string, defaultOpen?: boolean }`

> Breakpoints: quando fizer sentido, valores numéricos podem variar por BP dentro de `props`, ex.: `{ top: { "base": 0, "md": 8 } }`.

---

## Injeção de engine
- `Settings → Engine`: `vanilla` (default) | `alpine` | `noscript`.
- Parser injeta Alpine **apenas** se `engine === "alpine"` **e** existir pelo menos um `fx` que exija JS. CDN sugerido:
```html
<script defer src="https://unpkg.com/alpinejs@3.x.x" crossorigin="anonymous"></script>
```
- `noscript`: força fallback (ex.: `<details>`/`<summary>`, `<dialog>` puro). Export continua válido.

---

## Mapeamento HTML (por FX)
### 1) Accordion
**Estrutura exportada (vanilla/fallback)**
```html
<div data-fx="accordion" data-fx-multiple="false">
  <details id="faq1" open>
    <summary id="faq1-sum" aria-controls="faq1-panel">Pergunta</summary>
    <div id="faq1-panel" role="region" aria-labelledby="faq1-sum">Resposta…</div>
  </details>
  <!-- repetir itens -->
</div>
```
**Validação relacionada**: `E082` (q/a obrigatórios), `W108` (fallback `<details>` sem JS).

### 2) Modal
**Estrutura**
```html
<div data-fx="modal" aria-hidden="true">
  <div class="fixed inset-0" data-fx-backdrop></div>
  <div role="dialog" aria-modal="true" aria-labelledby="modal-title" tabindex="-1">
    <h2 id="modal-title">Título</h2>
    <button type="button" data-fx-close aria-label="Fechar">×</button>
    <div>Conteúdo</div>
  </div>
</div>
```
**Regras**
- Trap de foco, `Esc` fecha, restaura foco anterior.
- Backdrop tem `inert` aplicado ao resto da página quando aberto.

### 3) Tooltip
**Estrutura mínima**
```html
<button id="btn1" aria-describedby="tip1">Ação</button>
<div id="tip1" role="tooltip" hidden data-fx="tooltip">Dica…</div>
```
- Mostra em foco/hover; `Esc` oculta; nunca substituir `title` crucial.

### 4) Sticky
```html
<div data-fx="sticky" style="position:sticky; top: var(--space-sm, 8px)">…</div>
```
- Sem JS; apenas respeita offsets por BP via classes utilitárias.

### 5) Disclosure
```html
<details data-fx="disclosure" open>
  <summary aria-controls="disc1">Título</summary>
  <div id="disc1">Conteúdo</div>
</details>
```

### 6) revealOnScroll
```html
<div data-fx="reveal" class="opacity-0 will-change-transform">…</div>
```
- Usa `IntersectionObserver`; se `prefers-reduced-motion`, mostra sem animação.

### 7) transition
- Apenas classes declarativas aplicadas a estados; útil com `reveal`/`collapse`.

### 8) collapse
```html
<div data-fx="collapse" hidden style="max-height:0; overflow:hidden">…</div>
```
- Anima `max-height`; acessível via `aria-expanded` no trigger.

---

## Data-attributes (contrato)
- `data-fx="{type}"`
- Props simples viram `data-fx-*` em kebab-case: `data-fx-multiple="true"`, `data-fx-top="8"`.
- Elementos auxiliares: `[data-fx-close]`, `[data-fx-backdrop]`, `[data-fx-trigger]`, `[data-fx-panel]`.

---

## Eventos custom (dom)
- `fx:init` — quando o nó inicializa.
- `fx:open` / `fx:close` / `fx:toggle` — quando o estado muda.
- `fx:error` — quando props inválidas impedem inicialização.
`event.detail = { nodeId, type, props }`.

---

## A11y e teclado por FX
- **Accordion/Disclosure**: `Enter`/`Space` alterna; `Home/End` navega entre cabeçalhos; foco não salta.
- **Modal**: `Esc` fecha; trap de foco; restaurar foco; `Tab/Shift+Tab` ciclam.
- **Tooltip**: exigir elemento focável; `Esc` fecha; delays curtos (150–250 ms).
- **Reveal/Transition/Collapse**: respeitar `prefers-reduced-motion`.

---

## Validações específicas (opcionais)
> Desligadas por padrão; habilitar por flag do Parser se desejar endurecer.
- `E300` — Modal sem controle de fechamento (`data-fx-close` ausente).
- `E301` — Tooltip sem `aria-describedby` coerente.
- `E302` — `fx.type` desconhecido.
- `E303` — Propriedade inválida para o `fx` indicado.
- `W109` — Accordion sem `summary` semanticamente correto.
- `W110` — Sticky sem `top` definido em páginas com header.
- `W111` — Reveal desativado por `prefers-reduced-motion` (informativo).

---

## Engine bindings
### Vanilla (sugestão de inicializador)
```js
function initFX(root=document){
  root.querySelectorAll('[data-fx]')
    .forEach(el => { const type = el.dataset.fx; try { FX[type]?.(el); el.dispatchEvent(new CustomEvent('fx:init',{detail:{type}})); } catch(e){ el.dispatchEvent(new CustomEvent('fx:error',{detail:{type, error:e}})); } });
}
const FX = {
  accordion(el){ /* usar <details> nativo; nada a fazer */ },
  modal(el){ /* abrir/fechar via data attr + trap de foco */ },
  tooltip(el){ /* toggle hidden e aria-live discreto */ },
  sticky(el){ /* CSS only */ },
  disclosure(el){ /* <details> nativo */ },
  reveal(el){ /* IO + add classes */ },
  transition(el){ /* utilitárias */ },
  collapse(el){ /* animar max-height */ }
};
window.addEventListener('DOMContentLoaded',()=>initFX());
```

### Alpine (binding breve)
```html
<div x-data="{open:false}" x-on:keydown.escape="open=false" x-bind:aria-expanded="open" x-show="open" x-transition>…</div>
```
- O Parser apenas injeta o script Alpine; bindings são gerados pelas diretivas conforme o `fx`.

---

## Performance
- Não tocar no layout quando o efeito não estiver na viewport.
- Evitar medição síncrona de layout; animar via CSS sempre que possível.
- `IntersectionObserver` para `revealOnScroll` com `rootMargin:"0px 0px -10% 0px"`.

---

## Segurança
- Nenhum `eval` ou `Function` dinâmico.
- Sem requisições de rede pela engine FX.
- Plugins que estendam FX passam pela Plugin API (permissões explícitas).

---

## Exemplos de uso (layout.json)
### FAQ Accordion (mínimo)
```json
{
  "type":"section","id":"faq","children":[
    {"type":"card","id":"faq1","fx":["accordion"],"content":{"q":"Prazo?","a":"48–72h"}}
  ]
}
```

### Modal com CTA
```json
{
  "type":"button","id":"open-modal","content":{"label":"Abrir","href":"#"},
  "fx":[{"type":"modal","props":{"closeOnEsc":true,"closeOnBackdrop":true}}]
}
```

---

## Integração com Drawer U11
- Erros/avisos relacionados a FX aparecem em `validation.json` com `code` da família `E3xx/W1xx` (quando a flag de FX estiver ativa).
- Quick‑fixes típicos: `add-media-stub` (hero split), `apply-fx` (adicionar `accordion` a FAQ), `add-alt`.

---

## Changelog
- v1.0 — Inventário inicial, props por efeito, data‑attributes, eventos, A11y, vanilla/alpine bindings.

---

Este documento completa a parte de FX no ecossistema Tello. Qualquer adição de efeito deve seguir: definição de `props`, fallback, A11y, data‑attributes, eventos e (opcional) validação com quick‑fix correspondente.

