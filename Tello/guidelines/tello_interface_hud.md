# Tello — Interface & HUD — Blueprint V2.1.1 (refeito)

> Documento canônico da camada visual do Tello. Conecta UI, HUD e App‑Shell ao **Schema (layout.json)**, **Tokens**, **Validation/Drawer U11**, **FX Engine**, **Export Presets**, **Templates Pack**, **Plugin API** e **Testing Plan**. Serve como contrato de interação e referência para implementação.

---

## 1) Escopo & Objetivos
- **Editar por blocos** com grid responsivo (base/md/lg; opt‑in xl/2xl), usando **tokens** e **contratos** do layout.
- **HUD** para edição rápida de `layout.span/start/order` por breakpoint.
- **Validação viva** via Drawer U11; export bloqueado com erros.
- **Baixa fricção**: Preview e Export sem build; `preview.png` em worker.

---

## 2) App‑Shell (mapa funcional)
- **TopBar**: Undo/Redo • Chips de Breakpoint (+ EDIT BP) • Device preset + Reset • Zoom • Validation (abre Drawer U11) • Export • Palette (Ctrl/⌘+K) • Plugins • Settings.
- **Left Sidebar**: Biblioteca (Blocks/Templates) com busca, categorias e inserção por click/drag; auto‑wrap `section ▸ container` quando necessário.
- **Canvas**: área editável com **Grid** (4/8/12 col), **Baseline** 4px, **Réguas/Guias**, **Safe‑Area**, **Overlays** (Headings/Tab/Contrast).
- **Right Sidebar**: Abas **Props**, **FX**, **ARIA**, **Tokens (read‑only)**.
- **Status Bar**: caminho do nó, BP ativo, snap, autosave, mini‑validação.

> A UI é apenas front de contratos: tudo que altera estilo escreve em `props{bp}`, tudo que altera posicionamento escreve em `layout{bp}`.

---

## 3) Seleção, Grid e Snapping
- **Handles**: 8 laterais + centro para mover; shift para manter eixo; alt para duplicar.
- **Snap**: grid, guias, centros, espaçamentos pareados; prioridade configurável (Settings ▸ Canvas).
- **Resize**: respeita `layout.span` e `grid.props.cols` do pai; clamp 1..12.
- **Drag com Alt**: troca a posição com o irmão (swap) sem quebrar ordem.

---

## 4) HUD (Q) — edição direta de layout
- **Atalho**: `Q` para mostrar/ocultar; sempre sincronizado com seleção.
- **Chips**: `span`, `start`, `order`, `BP`, `snap`.
- **Ações rápidas**:
  - `colSpan −/+` via `[ ]` (ABNT2 alias: `Ctrl+Alt+,` / `Ctrl+Alt+.`).
  - Definir `start` por setinha/numérico.
  - Mudar BP inline (reflete nas Chips da TopBar).
- **Persistência**: cada ajuste grava `layout.{bp}` no node selecionado (Undo/Redo transacional).

---

## 5) Sidebars em detalhe
### 5.1 Left — Biblioteca
- **Blocks (MVP)**: `section, container, grid, media, heading, paragraph, button, card, form, input, footer`.
- **Templates**: seeds oficiais (Canonical, Split, Benefits, Proofs, Pricing, FAQ, CTA, Long‑form). Inserção para página inteira ou seção.
- **Heurísticas**: se inserir `grid` num `section` vazio, cria `container` automaticamente.

### 5.2 Right — Props/FX/ARIA/Tokens
- **Props** por BP com preview imediato; valores vinculados ao **Tokens Contract**. Hex em props é proibido.
- **FX**: liga `accordion`, `modal`, `reveal`, etc., com props simples; mostra alerta se engine exigir Alpine.
- **ARIA**: rótulos essenciais, `role` de região e dicas de foco.
- **Tokens**: painel read‑only com busca; clique copia caminho do token.

---

## 6) TopBar (comportamentos)
- **Undo/Redo**: transações coesas por ação; Redo limpa após novo commit.
- **Chips de BP**: base | md | lg (xl/2xl opt‑in). Clique alterna o BP ativo e guia HUD/Canvas/Props.
- **Device preset**: Mobile/Tablet/Desktop; reflete apenas viewport do Canvas.
- **Zoom**: 50–200%; `Ctrl+1/0` para fit/reset.
- **Validation**: abre Drawer U11 (Ctrl+Alt+Y). Ícone exibe contagem de erros/avisos.
- **Export**: abre modal de presets; `Ctrl+Shift+E` (abrir) / `Ctrl+Alt+E` (último preset). Respeita **gating**.
- **Palette**: busca de comandos, blocos e templates (Ctrl/⌘+K). Inclui comandos de plugins.
- **Plugins**: gerencia instalação por URL/arquivo `.esm.js`, permissões e compat.
- **Settings**: Keymap, Canvas, TokensRef, Engine (vanilla|alpine|noscript), General.

---

## 7) Modos de renderização
- **Normal**: render final.
- **Wireframe** (`Ctrl+Alt+W`): remove cores/shadows, exibe caixas e hierarquia; útil para spans.
- **Low‑Power** (`Alt+Shift+P`): suspende imagens e FX; ideal para máquinas fracas.

---

## 8) Overlays (Canvas)
- **Headings**: destaca `h1..h6`; integra E120 (múltiplos h1) com botão `Fix` no Drawer.
- **Tab Order**: numera elementos focáveis; denuncia ordem ilógica.
- **Contrast**: heatmap estimado com base em tokens (informativo).

Atalhos: `Ctrl+Alt+H` (Headings), `Ctrl+Alt+T` (Tab), `Ctrl+Alt+C` (Contrast).

---

## 9) Drawer U11 (ponte com a UI)
- Abre à direita; listas **All/Errors/Warnings**. Cada item tem `Locate` (foca Canvas) e `Fix` (executa quick‑fix). Export é desbloqueado só quando `errors = 0`.
- Quick‑fixes relevantes à UI: `demote-h1`, `set-span`, `apply-gap`, `add-cta`, `fill-offer-required`, `add-media-stub`, `coerce-bullets`.

---

## 10) Acessibilidade
- **Focus trap** em diálogos/Drawer; `Esc` fecha; toasts em `aria-live`.
- **1 h1** por página; `media.alt` significativo; tooltips associadas por `aria-describedby`.
- **Atalhos** documentados nas tooltips; keymap configurável.

---

## 11) Performance
- Pré‑visualização e **preview.png** renderizadas em worker quando possível.
- Virtualização em listas de Templates/Validation.
- Evitar layout thrashing: animar via classes utilitárias.

---

## 12) Integrações de contrato (matriz)
- **Schema (layout.json)**: UI grava/edita `props{bp}` e `layout{bp}`; `role/variant` ajudam templates e validação.
- **Tokens**: obrigatórios; UI só exibe caminhos; alterações vêm do Brand‑Form.
- **Validation**: erros/avisos acionam Drawer e overlays; export bloqueado por `E*`.
- **FX**: UI liga efeitos via `fx` no node; avisa quando `engine = alpine` for necessário.
- **Export**: presets `layout.json`, `index.html`, `preview.png`, `validation.json`, `site.zip`.
- **Plugins**: comandos aparecem no Palette; respeitam permissões.
- **Testing**: UI expõe IDs estáveis para E2E (Playwright) e Axe.

---

## 13) Telemetria mínima
- `ui_open` (drawer/overlays/palette), `fix_clicked`/`fix_applied`, `export_started/done/blocked`.
- Sem dados pessoais; apenas métricas operacionais.

---

## 14) Checklists (operacional)
**Antes do export**
- [ ] 1 único `h1`
- [ ] Sem HEX em props; tokens válidos
- [ ] Spans válidos e gaps setados nos grids principais
- [ ] Hero com CTA; split com `media`
- [ ] Offer/Pricing/FAQ com mínimos obrigatórios

**Debug rápido da UI**
- [ ] Wireframe habilitado para ver spans
- [ ] Drawer sem `E*`
- [ ] Overlays Headings/Tab revisadas

---

## 15) Roadmap curto (UI/HUD)
- Preview de **diff** ao aplicar quick‑fixes
- Batch fixes em `Warnings`
- Editor visual de tokens (read‑only hoje) com **map‑token** assistido

---

Este blueprint fecha a ponta **Interface & HUD** alinhada a todos os contratos do Tello. Alterações de Schema/Validation/Export exigem atualização deste documento e da UI correspondente.

