# Tello — Export Presets & Packaging

> Documento canônico do sistema de exportação do Tello. Define quais artefatos são gerados, regras de **gating**, naming, histórico, estrutura do `site.zip`, e contratos com Parser/Drawer.

---

## Objetivo
Entregar artefatos prontos para revisão e envio ao cliente, minimizando retrabalho e impedindo saída inválida quando houver **erros** de validação. Export é determinístico e reprodutível.

---

## Presets oficiais (MVP)
1. **`layout.json`** — layout final (pós quick‑fix). Sempre permitido.
2. **`index.html`** — HTML único com Tailwind CDN e `<style>` de tokens. **Bloqueado** por erros.
3. **`preview.png`** — captura do Canvas (1×/2×/3×). Sempre permitido.
4. **`validation.json`** — relatório de validação. Sempre permitido.
5. **`site.zip`** — pacote com `index.html`, `layout.json`, `tokens.json` (ou `tokens.css`), `README.md`, `validation.json`, `/assets`. **Bloqueado** por erros.

> PNG e JSONs são sempre gerados; `index.html` e `site.zip` obedecem o **gating**.

---

## Gating (regras de bloqueio)
- Se `validation.errors.length > 0` → **bloquear** `index.html` e `site.zip`.
- `warnings` não bloqueiam nada.
- Códigos comuns que bloqueiam: `E080`, `E081..E091`, `E120`, `E140`, `E200`, `E201`. (ver *Validation Codes Reference*).

**Feedback de UI**
- Snackbar: “Export bloqueado. Corrija os erros no Drawer U11.”
- Link “Abrir Drawer (Ctrl+Alt+Y)”.

---

## Estrutura de arquivos por preset
### 1) `index.html`
```html
<!doctype html>
<html lang="pt-br">
<head>
  <meta charset="utf-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1"/>
  <title><!-- meta.name --></title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style id="tello-tokens">
    :root{ /* vars geradas a partir de tokens.json */ }
  </style>
  <!-- FX engine (somente se houver fx e engine=alpine) -->
  <!-- <script defer src="https://unpkg.com/alpinejs@3.x.x"></script> -->
</head>
<body class="bg-[var(--color-bg-page)] text-[var(--color-text-default)]">
  <!-- conteúdo renderizado do layout -->
</body>
</html>
```

### 2) `site.zip` (estrutura)
```
site.zip
├─ index.html
├─ layout.json
├─ tokens.json           # cópia do arquivo utilizado
├─ tokens.css            # opcional: mesmo conteúdo de <style id="tello-tokens">
├─ validation.json
├─ README.md
└─ assets/
   ├─ images/           # se houver media.src
   └─ fonts/            # opcional
```

### 3) `README.md` (modelo)
```md
# {{meta.name}}

Gerado pelo Tello {{date}}.

## Como visualizar
Abra `index.html` num navegador moderno. Não há build.

## Conteúdo
- index.html — HTML final com Tailwind CDN
- layout.json — árvore original
- tokens.json — tokens usados
- validation.json — relatório após os quick‑fixes

## Notas
- Se modificar tokens, reabra o projeto no Tello e reexporte.
```

---

## Naming & versioning
- Slug do projeto: `slug(meta.name)` → `tello-{{slug}}`.
- Versão: `layout.version` (ex.: `1.0`).
- Timestamp: `YYYYMMDD-HHmmss` (local).

**Arquivos**
- `index.html` simples.
- `preview@1x.png`, `preview@2x.png`, `preview@3x.png`.
- `site-{{slug}}-v{{version}}-{{timestamp}}.zip`.

---

## Histórico de export
Últimos 5 exports ficam logados em `exports/history.json` dentro do workspace (não é empacotado):
```json
[
  {"when":"2025-09-03T10:11:12Z","preset":"site.zip","file":"site-landing-v1-20250903-101112.zip","errors":0,"warnings":2},
  {"when":"2025-09-03T09:40:00Z","preset":"index.html","file":"index.html","errors":0,"warnings":3}
]
```

Drawer mostra o histórico num dropdown do botão **Export**.

---

## Configuração (opcional) — `export.config.json`
```json
{
  "outDir": "dist",
  "presets": ["layout.json","index.html","preview.png","site.zip"],
  "preview": { "scales": [1,2,3], "bg": "color.bg.page" },
  "zip": { "includeTokensCss": true, "includeFonts": false },
  "naming": { "slug": "landing", "version": "1.0" }
}
```

- `preview.bg` aceita **token path** (não hex).
- `zip.includeFonts` só empacota fontes se existirem assets referenciados.

---

## Assets (imagens/fonts)
- `media.content.src` relativo a `/assets/images`. O export copia mantendo estrutura.
- Se `src` ausente, permanece stub (não falha export).
- Hash de integridade opcional: renomear como `nome.hash.ext` se `--content-hash` estiver ativo (futuro).

---

## Compatibilidade offline
- Export padrão usa Tailwind CDN. Para ambientes sem rede:
  - Flag `--offline` gera `tailwind.css` mínimo necessário (via `@apply` estático).
  - `site.zip` inclui esse CSS e remove o `<script src="cdn.tailwindcss.com">`.

---

## Interação com Drawer U11
- Antes de exportar `index.html` ou `site.zip`, chamar `validate()`.
- Se erros > 0, bloquear e abrir Drawer com foco no primeiro item.
- Após export, toast: “Exportado {{preset}}” + botões `Abrir` e `Mostrar na pasta`.

---

## Telemetria (opcional)
- `export_started` { preset }
- `export_blocked` { errors }
- `export_done` { preset, duration_ms, warnings }

---

## Testes de aceite (export)
- Com erros simulados (E120/E140), `index.html` e `site.zip` não devem ser gerados.
- Sem erros: todos os presets gerados; ZIP com estrutura exata.
- `preview.png` renderiza em *worker* (UI não bloqueia).

---

## Roadmap curto
- `export preset: site-min.zip` (assets inlined, CSS crítico)
- `content-hash` para assets e tokens.css
- `diff-export` (somente arquivos alterados)

---

Este documento sela as regras de export. Qualquer alteração deve atualizar este contrato e o Parser. Próximo na fila da espinha dorsal: **Testing Plan**.

