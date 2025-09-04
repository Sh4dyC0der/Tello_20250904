# Tello — Tracking Plan — Markdown

> Documento canônico de telemetria e métricas do Tello. Conecta eventos mínimos (UI, Drawer, Export, Fixes, Plugins) às necessidades de QA e Macro Strategy. Baseado nos goals de 03/09. Não é crítica externa, mas consolidação dos próximos passos.

---

## 1) Objetivo
- Formalizar **dicionário de eventos**.
- Garantir **consistência de schema** (nome, payload, owner).
- Atender objetivos de UX Enginery (recuperação, debug, previsibilidade) e Macro Strategy (KPIs de aquisição, velocidade, qualidade).

---

## 2) Escopo
Eventos instrumentados no editor (App‑Shell, HUD, Drawer, Export, Plugins). Sem coleta de PII. Payload mínimo para depuração e métricas.

---

## 3) Padrões de nomenclatura
- **snake_case** para nomes de eventos.
- Prefixo de área: `ui_`, `drawer_`, `export_`, `fix_`, `plugin_`.
- Campos obrigatórios: `timestamp`, `user_id` (hash/anon), `session_id`.

---

## 4) Eventos mínimos

### 4.1 UI & HUD
- **`ui_open`**
  - **Quando**: abrir Palette, Settings, Overlays.
  - **Payload**: `{ area: "palette|settings|overlay_headings|overlay_tab|overlay_contrast" }`
  - **Owner**: Frontend

- **`ui_action`**
  - **Quando**: clique em botões chave (Undo, Redo, Zoom, Device preset).
  - **Payload**: `{ action: "undo|redo|zoom_in|zoom_out|device_switch" }`
  - **Owner**: Frontend

### 4.2 Drawer U11
- **`drawer_opened`**
  - **Quando**: abrir Drawer.
  - **Payload**: `{ via: "icon|hotkey", errors_count: number, warnings_count: number }`
  - **Owner**: UX

- **`fix_clicked`**
  - **Quando**: usuário clica em botão Fix.
  - **Payload**: `{ code: "E120", nodeId: "h1-main" }`
  - **Owner**: UX

- **`fix_applied`**
  - **Quando**: quick‑fix executado com sucesso.
  - **Payload**: `{ code: "E120", action: "demote-h1", duration_ms: 45 }`
  - **Owner**: Parser/QA

- **`fix_failed`**
  - **Quando**: quick‑fix falha.
  - **Payload**: `{ code: "E081", action: "fill-offer-required", reason: "missing params" }`
  - **Owner**: Parser/QA

### 4.3 Export
- **`export_started`**
  - **Quando**: clique em Export (qualquer preset).
  - **Payload**: `{ preset: "site.zip|index.html|preview.png", errors: n, warnings: n }`
  - **Owner**: Ops

- **`export_blocked`**
  - **Quando**: export bloqueado por erros.
  - **Payload**: `{ errors: n, blocking_codes: ["E081","E120"] }`
  - **Owner**: QA

- **`export_done`**
  - **Quando**: export finalizado.
  - **Payload**: `{ preset: "site.zip", duration_ms: 210, warnings: n }`
  - **Owner**: Ops

### 4.4 Plugins
- **`plugin_loaded`**
  - **Quando**: plugin aceito e registrado.
  - **Payload**: `{ pluginId: "tello.plugins.normalize-spans", version: "1.0.0" }`
  - **Owner**: Platform

- **`plugin_event`**
  - **Quando**: plugin emite evento próprio.
  - **Payload**: `{ pluginId, event, duration_ms }`
  - **Owner**: Plugin developer

---

## 5) Estrutura do schema (JSON)
```json
{
  "event": "export_done",
  "timestamp": "2025-09-03T14:21:00Z",
  "user_id": "anon-abc123",
  "session_id": "sess-xyz456",
  "payload": {
    "preset": "site.zip",
    "duration_ms": 210,
    "warnings": 1
  },
  "owner": "Ops"
}
```

---

## 6) Qualidade de dados (KPIs)
- **Cobertura**: ≥ 90% das ações críticas com evento registrado.
- **Consistência**: 0 eventos com schema inválido por release.
- **Latência**: eventos entregues ≤ 5s.
- **Cardinalidade controlada**: campos como `nodeId` devem ser curtos/normalizados.

---

## 7) Governança
- **Owner central**: Ops (garante schema e destino).
- **Review**: mudanças de schema exigem atualização neste doc e no Testing Plan.
- **Versão**: bumpar `tracking.version` a cada alteração.
- **Compat**: eventos deprecatados mantidos por 1 MINOR com aviso.

---

## 8) Próximos passos (direto dos goals)
- Publicar este Tracking Plan como doc oficial.
- Integrar no **Testing Plan**: smoke para `fix_applied`, `export_blocked`, `export_done`.
- Mapear eventos → KPIs do Macro Strategy (TTFP, TTE, % seeds reusados, NPS proxy).
- Gerar painel inicial simples (Count by event, errors vs. warnings).

---

## 9) Roadmap futuro
- Adicionar eventos de perf (parse/render ms, export ms) → cruzar com budgets.
- Eventos A11y (headings overlay usado, tab order revisado).
- Tracking anônimo de templates mais usados (sem PII).

---

**Versão:** tracking.v1.0 — alinhado aos goals de 03/09. Alterações futuras exigem bump de versão e sync com Testing Plan.
