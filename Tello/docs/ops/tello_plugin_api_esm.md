# Tello — Plugin API (ESM)

> Documento canônico para criação de plugins do Tello. Define permissões, manifesto, ciclo de vida, hooks, contrato de comandos no Palette, sandbox, segurança e exemplos completos. Compatível com Parser v0.1.2, Drawer U11 e Export Presets.

---

## 1) Objetivo
Permitir extensões isoladas que leem/escrevem `layout`, leem `tokens`, interagem com validação/export e adicionam comandos ao **Command Palette** sem comprometer a estabilidade do host.

**Princípios**
- ESM (ECMAScript Modules) carregado por URL ou arquivo local `.esm.js`.
- Sandbox forte (iframe/Worker) + permissão explícita por plugin.
- Nenhum script global é poluído; comunicação via `postMessage`/RPC.
- Versões e contratos estáveis; breaking changes apenas em major.

---

## 2) Capacidades & Permissões
Plugins declaram exatamente o que precisam. O host concede/nega no momento da instalação.

| Permissão | Descrição | API relacionada |
|---|---|---|
| `readLayout` | Ler árvore completa e seleção atual | `host.layout.get()`, `host.selection.get()` |
| `writeLayout` | Mutar o layout (com histórico/undo) | `host.layout.update(patch)` |
| `readTokens` | Ler `tokens.json` já resolvido | `host.tokens.get()` |
| `export` | Disparar export presets | `host.export.run(preset)` |
| `validate` | Rodar validação sob demanda | `host.validate.run()` |
| `net:{origins}` | `fetch` apenas para os domínios especificados | `host.net.fetch(url)` |

> Não há `writeTokens` no MVP; edição de tokens é externa (Brand‑Form). Se necessário futuramente, será `writeTokens` com revisão manual.

---

## 3) Manifesto (`plugin.json`)
```json
{
  "id": "tello.plugin.sample",
  "name": "Sample Plugin",
  "version": "1.0.0",
  "entry": "./index.esm.js",
  "permissions": ["readLayout", "writeLayout", "validate"],
  "net": { "origins": ["https://api.example.com"] },
  "commands": [
    { "id": "normalize-spans", "title": "Normalize spans (grid)", "hotkey": "Ctrl+Alt+N" }
  ],
  "settings": [
    { "key": "spanDefault", "type": "number", "label": "Default span", "default": 12 }
  ],
  "compat": { "tello": ">=2.1.1 <3" }
}
```

**Campos**
- `id` único, em notação *reverse‑dns*.
- `entry` aponta para um ESM válido.
- `permissions` e `net.origins` são revisados na instalação.
- `commands` registram ações no **Palette**.
- `settings` definem preferências surfadas pelo host (UI padrão do host).
- `compat.tello` fixa a faixa de versões suportadas do host.

---

## 4) Ciclo de vida
1. **Install**: host lê `plugin.json`, valida compat e pede consentimento de permissões.
2. **Load**: `import(entry)` em sandbox. Chamado `register(host)` do plugin.
3. **Enable/Disable**: host preserva estado; plugin recebe `onEnable`/`onDisable` se implementados.
4. **Upgrade**: descarrega versão anterior, carrega nova; migração opcional `onMigrate(prevVersion)`.
5. **Uninstall**: remove comandos, limpa storage; `onUninstall` opcional.

---

## 5) API do Host (superfície)
O objeto `host` é passado para `register(host)` com os namespaces abaixo.

```ts
interface Host {
  version: string;
  selection: {
    get(): { nodeId: string | null };
    set(nodeId: string | null): void;
  };
  layout: {
    get(): Layout;
    update(patch: Partial<Layout>, opts?: { transactional?: boolean }): { ok: boolean };
  };
  tokens: { get(): Tokens };
  validate: { run(): ValidationResult };
  export: { run(preset: "layout.json"|"index.html"|"preview.png"|"site.zip"): Promise<ExportResult> };
  commands: {
    register(cmd: { id: string; title: string; hotkey?: string }, handler: () => void): UnregisterFn;
  };
  events: {
    on(type: HostEventType, cb: (evt: HostEvent) => void): UnsubscribeFn;
    emit(type: HostEventType, detail?: any): void; // limitado a canal de plugin
  };
  ui: {
    toast(msg: string): void;
    confirm(msg: string): Promise<boolean>;
    openDrawer(tab?: "All"|"Errors"|"Warnings"): void;
  };
  net?: { fetch(url: string, init?: RequestInit): Promise<Response> };
}
```

**Eventos** (`events.on`)
- `selection.change`, `layout.change`, `validation.change`, `export.done`, `export.blocked`.

**Undo/Redo**
- `layout.update({ ... }, { transactional:true })` cria um único commit de histórico.

---

## 6) Hooks (opcionais)
Plugins podem expor funções com nomes reservados. O host as detecta e registra.

```ts
export function register(host) { /* obrigatório */ }
export function onEnable() {}
export function onDisable() {}
export function onMigrate(prevVersion: string) {}
export function preRender(layout: Layout): Layout | void {}
export function postRender(html: string): string | void {}
export function validate(layout: Layout): Partial<ValidationResult> | void {}
```

- `preRender/postRender` permitem ajustes pontuais (não usar para grandes mutações).
- `validate` adiciona avisos/erros custom (prefixe códigos com o id do plugin: `PX001`).

---

## 7) Exemplo 1 — Normalizador de spans
**`plugin.json`**
```json
{
  "id": "tello.plugins.normalize-spans",
  "name": "Normalize Spans",
  "version": "1.0.0",
  "entry": "./index.esm.js",
  "permissions": ["readLayout", "writeLayout", "validate"],
  "commands": [{ "id": "normalize", "title": "Normalize spans (grid)" }],
  "compat": { "tello": ">=2.1.1 <3" }
}
```

**`index.esm.js`**
```js
export function register(host){
  host.commands.register({ id: "normalize", title: "Normalize spans (grid)" }, () => {
    const layout = host.layout.get();
    walk(layout.root, node => {
      if (node.layout?.md?.span && (node.layout.md.span < 1 || node.layout.md.span > 12)) {
        node.layout.md.span = Math.max(1, Math.min(12, node.layout.md.span));
      }
    });
    host.layout.update(layout, { transactional: true });
    host.validate.run();
    host.ui.toast("Spans normalizados");
  });
}
function walk(n, fn){ fn(n); (n.children||[]).forEach(c=>walk(c, fn)); }
```

---

## 8) Exemplo 2 — Export & UTM (com rede)
**Permissões**: `readLayout`, `writeLayout`, `export`, `net:{"origins":["https://utm.example"]}`

**Fluxo**: adiciona parâmetros UTM em CTAs, valida, exporta `site.zip` e POST em endpoint do cliente.

```js
export async function register(host){
  host.commands.register({ id: "export-with-utm", title: "Export with UTM & Upload" }, async () => {
    const layout = host.layout.get();
    mutateButtons(layout.root, btn => {
      if (btn.content?.href) btn.content.href += (btn.content.href.includes('?')?'&':'?') + 'utm_source=tello';
    });
    host.layout.update(layout, { transactional:true });
    const v = host.validate.run();
    if (v.errors.length) { host.ui.openDrawer("Errors"); return; }
    const file = await host.export.run("site.zip");
    await host.net.fetch("https://utm.example/upload", { method:"POST", body:file.blob });
    host.ui.toast("Export enviado");
  });
}
```

---

## 9) Sandbox & Segurança
- **Carregamento** em `iframe sandbox` (`allow-scripts` somente) ou `Worker` dedicado.
- **CSP** estrita: `default-src 'none'; img-src data:; style-src 'unsafe-inline'; connect-src {origins}`.
- `postMessage` com canal dedicado e verificação de origem.
- Sem acesso ao `window` do host; sem ler DOM do Canvas diretamente.
- Rede limitada a `net.origins`; sem cookies de 3º por padrão; `credentials: 'omit'` no `host.net.fetch`.
- Timeouts e quotas (CPU/mem). Plugin travado é encerrado e isolado.

---

## 10) Erros & Telemetria
- Toda exceção do plugin vira entrada em **Console do Plugin** + toast discreto.
- Telemetria opt‑in: `plugin_event` enviado apenas com consentimento global.
- Campos: `{ pluginId, event, duration_ms }`.

---

## 11) Publishing & Versioning
- Sem repositório central no MVP; instalação por URL/arquivo.
- Recomendações: tag semver, changelog, README com permissões justificadas.
- Compat checada via `compat.tello`. Falha bloqueia instalação.

---

## 12) Guidelines de UX
- Comandos curtos e descritivos.
- Responder com toasts curtos e não bloquear UI.
- Evitar criar modais próprios; usar confirmação do host (`host.ui.confirm`).

---

## 13) Testes (mínimo para plugins)
- Unit tests locais para funções de transformação.
- Smoke com layout de exemplo (Templates Pack) antes de publicar.
- Linters obrigatórios: `eslint`, `typescript` opcional.

---

## 14) Roadmap de API
- `writeTokens` sob revisão de segurança.
- Painel dedicado para Plugins (logs, permissões, updates).
- Hooks de `onExportStart/Done` com interceptors de arquivos.

---

Este contrato permite construir plugins úteis sem quebrar o núcleo do Tello. Alterações nesta API exigem atualização de `compat.tello` nos manifestos e nota em changelog do host.

