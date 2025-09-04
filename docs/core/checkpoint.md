
Este pacote roda em **modo instrucional (GPT-only)**.
# Patch: checkpoint.v20250904

**Resumo (3 linhas)**
- Verifiquei `checkpoint_9_03.md`, `roadmap_9_03.md` e `tello-chat-bootstrap.md` e executo o BUILD-1 conforme autorizado. fileciteturn1file1L1-L7 fileciteturn1file2L48-L53 fileciteturn1file0L1-L6
- Gerei patches mínimos: bump de versão do checkpoint (2025-09-03 → 2025-09-04), entrada única no `CHANGELOG.md` e registro em `ops.log` com formato {who, action, target, when, outcome}.
- Próximo passo: aplicar os diffs no repositório local, rodar os testes de snapshot e commitar com tag `checkpoint/2025-09-04`.

---

## Patch (unified diff)

*** a/checkpoint_9_03.md
--- a/checkpoint_9_03.md
+++ b/checkpoint_9_03.md
@@
-# CHECKPOINT — 2025-09-03
+# CHECKPOINT — 2025-09-04
@@
-## Como usar este checkpoint
+## Como usar este checkpoint
@@
-- 4. Marque `checkpoint9-03.md` como referência no repositório (commit + tag `checkpoint/2025-09-03`).
++ 4. Marque `checkpoint9-03.md` como referência no repositório (commit + tag `checkpoint/2025-09-04`).
+
+## Versão canônica
+- checkpoint.v20250904 — bump aplicado 2025-09-04

---

*** a/CHANGELOG.md
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@
+# CHANGELOG
+
+- checkpoint_9_03.md — bump to checkpoint.v20250904 — 2025-09-04

---

*** a/ops.log
--- a/ops.log
+++ b/ops.log
@@
+{ "who": "GPT", "action": "bump-checkpoint", "target": "checkpoint_9_03.md -> checkpoint.v20250904", "when": "2025-09-04T02:00:00-03:00", "outcome": "patch-generated" }

---

## Notas operacionais
- Mantive cópias originais sugeridas: ao aplicar localmente, salve `checkpoint_9_03.md.orig` antes de sobrescrever. (Conforme regra "Sempre preservar original").
- Criei entradas mínimas para evitar ruído; se preferir um changelog com formato diferente (PR-linked, author-tag), altero na hora.

---

## Onde está isto
- Patch criado neste canvas como `patch_checkpoint_v20250904.md`.
