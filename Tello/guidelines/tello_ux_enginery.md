# Tello — UX Enginery — Blueprint V2.1.1 (refeito)

> Documento canônico de engenharia de experiência para o Tello. Conecta fluxo do usuário, heurísticas, prevenção de erro, recuperação e padrões operacionais ao **Layout Schema**, **Tokens**, **Validation/Drawer U11**, **FX**, **Export**, **Templates**, **Plugin API**, **Testing Plan** e **UI Microcopy**. Otimizado para um único operador (você) com baixo atrito e previsibilidade.

---

## 1) Objetivos de UX
- **Velocidade com segurança**: editar rápido sem abrir margem para saída inválida (gating por erros E*).
- **Determinismo**: mesma entrada (layout+tokens) sempre gera o mesmo HTML.
- **Carga cognitiva baixa**: comandos estáveis, HUD direto, Palette como “busca universal”.
- **Recuperação garantida**: Undo/Redo transacional, autosave, histórico curto, seeds versionados.

KPIs: TTFP ≤ 4h, exports bloqueados por projeto < 1 após primeira rodada, zero E* no momento do export final.

---

## 2) Fluxos canônicos (ponta‑a‑ponta)
1) **Início**
- Selecionar seed (Templates Pack) → definir `meta.name` → apontar `tokensRef`.
- Checar `Settings ▸ Engine` conforme FX necessários.

2) **Montagem**
- Comandos principais pela **Palette** (`Ctrl/⌘+K`): *Insert block*, *Insert template section*, *Normalize spans*, *Apply gap*, *Add CTA*.
- HUD (`Q`) para colSpan/start/order. `[ ]` ajustam spans (ABNT2 alias: `Ctrl+Alt+,/.`).

3) **Validação ativa**
- Abrir **Drawer U11** (`Ctrl+Alt+Y`). Resolver **E*** primeiro via quick‑fix, depois revisar **W***.
- Overlays para contexto: Headings (E120), Tab Order, Contrast.

4) **Preview e Export**
- Testar `index.html` local (CDN Tailwind) e `preview.png` para revisão rápida.
- Export final como `site.zip` quando `errors=0`.

5) **Iteração**
- Ajustes de conteúdo e tokens por rodada, mantendo spans e gaps estáveis.
- Se tokens mudarem, revalidar contraste e CTA legibilidade.

---

## 3) Heurísticas de interação
- **HUD primeiro** para layout; **Right Sidebar** apenas para props.
- **Palette** para tudo que tem nome; não caçar botões.
- **Wireframe** para investigar grid; **Low‑Power** quando a máquina sofrer.
- **Um h1 por página**; CTA sempre com `href`; grids críticos com `gap` no `md`.
- **Tokens sempre**: nunca hex em props.

---

## 4) Prevenção de erro (antes do Drawer)
- **Seeds guardrails**: exemplos já respeitam spans 1..12 e gaps mínimos.
- **Normalização automática**: spans fora do range são clampeados no ato (HUD/Plugins). Gera warning somente se insistente.
- **Placeholders conscientes**: quando conteúdo faltar em cards não essenciais, inserir `W090` e seguir. Evitar bloquear o flow criativo.
- **FX consciente**: se `fx` exigir Alpine e engine=vanilla, exibir hint não intrusivo.

---

## 5) Tratativa de erros (triagem no Drawer)
**Ordem sugerida**:
1. **E080** (estrutura básica) → layout inválido nem abre sprint.
2. **E120** (h1 único) → resolve overlay Headings.
3. **E140** (span) → HUD/`set-span`.
4. **E081/E091/E084/E088** (oferta/pricing/cta) → conteúdo mínimo e ação.
5. **E085** (hero split) → `add-media-stub`.
6. **E200/E201** (tokens/hex) → corrigir no Tokens/props.

Depois, tratar **W101/103/104/105** para acabamento.

---

## 6) Recuperação & estado
- **Undo/Redo transacional** por ação; Redo limpa ao novo commit.
- **Autosave** periódico (30 s) e manual ao exportar.
- **Histórico de export** (últimos 5) visível no botão Export.
- **Seeds versionados** e snapshots de HTML para comparar mudanças regressivas.

---

## 7) Acessibilidade operacional
- **Atalhos**: documentados nas tooltips; respeitam Keymap (Default/Figma/Photoshop/ABNT2/Minimal).
- **Focus trap** em Drawer/Modais; `Esc` fecha e restaura foco.
- **Overlays** integrados para evitar caçar problemas invisíveis (Headings/Tab/Contrast).

---

## 8) Performance percebida
- **Virtualização** na lista do Drawer e biblioteca de Templates.
- **Preview em worker** para não travar o editor.
- **Render por classes** (Tailwind) sem recalcular estilos pesados.

Alvos: `validate(layout)` ≤ 10 ms/200 nós; `render(layout)` ≤ 30 ms/200 nós.

---

## 9) Debug & inspeção
- **Console canalizado** do Drawer com eventos `fix_clicked/applied/failed`.
- **Locate** sempre sincroniza Canvas e HUD para contexto imediato.
- **IDs estáveis** nos nós para E2E (Playwright) e diff de snapshots.

---

## 10) Colaboração com GPT (operador único)
- **Brief claro** nos prompts: objetivo da seção + tokens relevantes + restrições.
- **Pedir JSON minimamente viável** por seção (page‑by‑page) quando em dúvida.
- **Checklist** no fim da resposta do GPT: campos obrigatórios preenchidos? spans 1..12? CTA com `href`?
- **Reforçar contratos**: colar trechos do *Layout Schema* quando gerar novos blocos.

---

## 11) Edge‑cases & decisões
- **Tokens incompletos**: aceitar placeholder com **W090** para seguir; bloquear export só se `E200/E201` presentes.
- **Sem FX permitido**: `noscript` coopera com `<details>`/`<dialog>` quando possível.
- **Offline**: usar preset offline no export (Tailwind embutido) quando necessário.
- **ABNT2**: alias de `[ ]` como `Ctrl+Alt+,/.` mantém fluxo no HUD.

---

## 12) Quality loops (antes de export)
- **Checklist**: h1 único, CTA com href, spans válidos, gaps em md, tokens ok, W* aceitáveis.
- **QA Card**: usar o cartão de QA do Validation Cheat‑Sheet para tiro curto.
- **Testing Plan**: rodar seeds snapshots ao mexer em Templates, Parser ou Validation.

---

## 13) Integrações críticas
- **Tokens Contract**: fonte única; mudanças exigem revalidação de contraste.
- **FX Inventory**: só injetar Alpine quando houver `fx` que precisa JS.
- **Export Presets**: `index.html`/`site.zip` respeitam **gating**. JSON/PNG sempre liberados.
- **Plugin API**: comandos utilitários (normalize spans, export UTM). Permissões explícitas.

---

## 14) Roadmap de UX
- **Preview de diffs** ao aplicar quick‑fixes.
- **Fix All Warnings** com seleção granular.
- **Palette de tokens** para mapear cores por similaridade (apoio aos casos E200/E201).

---

## 15) Critérios de aceite UX (release)
- Fluxo p2p executável sem docs: seed → HUD → Drawer → Export sem travas.
- Zero **E*** em export final; warnings remanescentes documentados.
- Tempo de aprendizado para novo seed < 30 min.

---

Este blueprint fecha o comportamento, prevenção de erros e recuperação do operador dentro do Tello. Toda mudança em Schema/Validation/Export/FX deve refletir aqui para manter coerência operacional.

