# Tello — UI Microcopy Guidelines

> Guia canônico de microcopy para o Tello. Serve para padronizar textos de botões, tooltips, toasts, modais, mensagens de erro/aviso e labels de A11y. Português‑BR como padrão. Mantém coerência entre Editor, Drawer U11, Export e Plugins.

---

## 1) Princípios de estilo
- **Clareza acima de tudo:** frases curtas, verbos no imperativo (“Corrigir”, “Exportar”).
- **Contexto direto:** diga o que aconteceu e o que fazer em seguida.
- **Sem jargão desnecessário:** use termos técnicos só quando ajudam.
- **Consistência de termos:** use sempre os mesmos nomes para áreas e ações (ver Glossário).
- **Acessibilidade:** mensagens audíveis em `aria-live` e foco previsível após ações.
- **Localidade:** PT‑BR padrão; placeholders em minúsculas e entre `{chaves}`.

Tone: profissional, pragmático, amistoso sem ser coloquial. Evitar humor no erro.

---

## 2) Glossário (termos oficiais)
- **Drawer U11**: painel de validação
- **Export**: ação de gerar artefatos
- **Templates**: modelos/“seeds”
- **Tokens**: `tokens.json`
- **Layout**: `layout.json`
- **Quick‑fix**: correção sugerida
- **Overlays**: Headings, Tab Order, Contrast
- **Engine**: `vanilla`, `alpine`, `noscript`

---

## 3) Botões (labels padrão)
- Primários: `Exportar`, `Aplicar`, `Corrigir`, `Salvar`, `Gerar`.
- Secundários: `Cancelar`, `Fechar`, `Ignorar`, `Rever`, `Abrir Drawer`.
- Destrutivos (confirmar): `Confirmar`, `Remover`, `Substituir`.

**Regras**: verbo no infinitivo/imperativo, 1–2 palavras, evitar advérbios.

---

## 4) Tooltips & Help
- Curto (≤ 8 palavras) e específico: `Aplicar correção sugerida`, `Localizar no Canvas`.
- Não repetir o rótulo do botão; adicione o “para quê”.
- Atalhos: `Exportar (Ctrl+Alt+E)`.

---

## 5) Toasters (feedback rápido)
Formato: **Ação + resultado**. Evitar “Sucesso!” genérico.

- **Sucesso**
  - `Exportado: {preset}.`
  - `Corrigido: {code}. Lista atualizada.`
  - `Plugins atualizados.`
- **Informativo**
  - `Sem erros. Pode exportar.`
  - `Avisos não bloqueiam export.`
- **Erro**
  - `Export bloqueado. Corrija erros no Drawer.`
  - `Falha ao corrigir {code}. Veja detalhes.`

---

## 6) Modais (confirmação)
Estrutura: **Título** + **Resumo objetivo** + **Consequência** + **Ações**.

- **Demover h1**
  - **Título:** `Mudar h1 para h2?`
  - **Texto:** `Isso corrige E120 (múltiplos h1).`
  - **Ações:** `Confirmar` | `Cancelar`
- **Substituir tokens**
  - **Título:** `Substituir tokens ativos?`
  - **Texto:** `Isso reatualiza estilos e pode afetar contraste.`
  - **Ações:** `Substituir` | `Cancelar`

---

## 7) Estados vazios
- Drawer (All): `Nenhum problema por aqui. Pode exportar.`
- Drawer (Errors): `Sem erros bloqueantes. Revise os avisos antes de exportar.`
- Templates: `Nenhum template encontrado.`
- Export: `Nenhum histórico de export ainda.`

---

## 8) Mensagens do Drawer U11
- Linha com fix: `Aplicar correção sugerida` (tooltip de **Fix**)
- Locate: `Localizar no Canvas`
- Filtros: `Todos`, `Erros`, `Avisos`
- Overlays: `Headings`, `Tab Order`, `Contraste`
- Barra de busca: `Buscar por código, id ou texto…`

**Vazio com link:** `Export bloqueado. Abra o Drawer (Ctrl+Alt+Y).`

---

## 9) Mensagens do Export
- Botão: `Exportar`
- Dropdown: `index.html`, `layout.json`, `preview.png`, `site.zip`
- Bloqueio: `Export bloqueado por erros de validação.`
- Offline: `Modo offline: Tailwind embutido.`
- Histórico: `Últimos exports`

---

## 10) Validação (microcopy por código)
> Os textos abaixo devem refletir exatamente os códigos do **Validation Codes Reference**.

- **E080**: `Campos obrigatórios ausentes (version/meta/tokensRef/root).`
- **E081**: `Oferta incompleta (title/price/CTA).`
- **E082**: `Item de FAQ sem pergunta/resposta.`
- **E083**: `Hero sem h1.`
- **E084**: `Hero sem CTA com link.`
- **E085**: `Hero (split) sem mídia.`
- **E086**: `Benefits (grid‑3) exige ≥ 3 cards.`
- **E087**: `Proofs (grid‑2) exige ≥ 2 cards.`
- **E088**: `CTA sem botão com link.`
- **E091**: `Pricing card sem preço ou CTA.`
- **E120**: `Mais de um h1 na página.`
- **E140**: `Span inválido (use 1..12).`
- **E200**: `Token não encontrado em tokens.json.`
- **E201**: `HEX cru não permitido em props.`

- **W063**: `Valor fora da escala Tailwind (classe arbitrária aplicada).`
- **W090**: `Placeholder aplicado.`
- **W101**: `Hero sem container max sugerido.`
- **W102**: `Proporção do split sugerida 7:5.`
- **W103**: `Defina gap em md (sugerido 24).`
- **W104**: `Defina ratio para mídia (ex.: 16:9).`
- **W105**: `Recomendado ratio 1:1 para logos.`
- **W106**: `Bullets normalizados.`
- **W107**: `Preço normalizado.`
- **W108**: `FAQ usa fallback <details> sem FX.`

**Padrão de frase:** substantivo primeiro, sem verbo desnecessário. Curto.

---

## 11) Acessibilidade (microcopy e labels)
- Modais: `role="dialog"`, `aria-modal="true"`, título via `aria-labelledby`.
- Botões fechar: `aria-label="Fechar"`.
- Tooltips: `role="tooltip"`; associe com `aria-describedby`.
- Feedback: `aria-live="polite"` para toasts.
- Media sem descrição: `alt=""` apenas se puramente decorativa; caso contrário, descritivo curto.

---

## 12) Placeholders
- Busca: `Buscar por código, id ou texto…`
- CTA href: `https://exemplo.com/acao`
- Preço: `R$ 0`
- FAQ: `Pergunta frequente…` / `Resposta sucinta…`

**Regra**: placeholder indica formato, não conteúdo final de marketing.

---

## 13) Erros técnicos (falhas de operação)
- **Network**: `Sem conexão. Tente novamente.`
- **Timeout**: `Demorou mais que o esperado. Verifique e tente novamente.`
- **Parser**: `Falha ao validar. Veja detalhes no console.`
- **Export**: `Não foi possível gerar {preset}.`

Evitar códigos HTTP crus na UI; logue detalhes no console.

---

## 14) Strings parametrizadas (i18n keys)
```json
{
  "drawer.empty.all": "Nenhum problema por aqui. Pode exportar.",
  "drawer.empty.errors": "Sem erros bloqueantes. Revise os avisos.",
  "drawer.fix.toast": "Corrigido: {code}. Lista atualizada.",
  "export.blocked": "Export bloqueado por erros de validação.",
  "export.done": "Exportado: {preset}.",
  "overlay.headings": "Headings",
  "overlay.tab": "Tab Order",
  "overlay.contrast": "Contraste"
}
```

**Padrão de chaves**: `area.subarea.evento`.

---

## 15) Do / Don’t
**Do**
- `Corrigido: E120. Lista atualizada.`
- `Defina ratio para mídia (ex.: 16:9).`
- `Export bloqueado. Abra o Drawer.`

**Don’t**
- `Sucesso total absoluto!` (vago)
- `Span inválido (coloque um número melhor).` (julgamento)
- `Ações executadas com maestria!` (pomposo)

---

## 16) QA de microcopy
- Revise capitalização: só primeira palavra em maiúscula, exceto nomes próprios.
- Números: use algarismos (1..12) quando o usuário precisa comparar rapidamente.
- Unidades: `px` apenas em documentação; UI evita unidades quando possível.

---

## 17) Processo de atualização
- Mudança em códigos E/W → atualizar aqui e no **Validation Codes Reference**.
- Nova ação no Drawer/Export → adicionar label, tooltip e toast correspondente.
- Revisão trimestral do guia; changelog curto embutido no topo do arquivo.

---

## 18) Changelog (deste documento)
- v1.0 — Versão inicial alinhada ao Parser v0.1.2, Drawer U11 e Export Presets.

---

Este guia fecha a padronização textual do Tello. Se a UI falar sempre do mesmo jeito, o usuário entende mais rápido e erra menos.

