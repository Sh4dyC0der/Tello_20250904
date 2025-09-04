# Tello — Release & Versioning Policy

> Política oficial de versionamento, empacotamento e release do Tello: núcleo (Schema/Parser/Drawer), Templates, Tokens e Plugins. Este documento evita “telefone sem fio” na evolução do projeto e define como sincronizar contratos sem quebrar entregas.

---

## 1) Objetivos
- Garantir compatibilidade entre **Schema**, **Parser**, **Drawer**, **FX**, **Templates** e **Tokens**.
- Permitir evolução rápida com **mínimo de quebras** e trilha de migração clara.
- Padronizar nomes de releases e artefatos (ZIPs, seeds, docs).

---

## 2) SemVer aplicada (MAJOR.MINOR.PATCH)
- **MAJOR**: mudanças quebrando contrato (ex.: alterar shape do `layout.json`, remover prop). Exige migração e bump de `compat` em plugins.
- **MINOR**: novas features compatíveis (novos blocos/props opcionais, novos warnings E/W, FX opcional). Pode exigir mudanças em docs, nunca quebra seed existente.
- **PATCH**: correções de bug, mensagens de validação, microcopy, performance sem impactar contratos.

Exemplos: `2.1.1` (major 2 do Tello, minor 1, patch 1).

---

## 3) Version Matrix (contrato cruzado)
| Módulo | Campo de versão | Exemplo | Compat base |
|---|---|---|---|
| **Core** (Editor) | `app.version` | `2.1.1` | n/a |
| **Schema** (layout.json) | `layout.version` | `1.0` | App `>=2.1.0` |
| **Parser** | `parser.version` | `0.1.2` | Schema `>=1.0 <2` |
| **Tokens** | `tokens.version` | `1.0` | Parser `>=0.1.0` |
| **Drawer U11** | `u11.version` | `1.0` | Parser `>=0.1.2` |
| **Templates** | `seed.version` | `1.0` | Schema `>=1.0` |
| **Plugins** | `plugin.compat.tello` | `>=2.1.1 <3` | App `2.x` |

> Toda release deve atualizar esta matriz, mesmo que só mude PATCH.

---

## 4) Regras de compatibilidade
- O **Parser** deve aceitar `layout.version` dentro de um intervalo e expor `adapters` de migração menores (ex.: `0.9 → 1.0`).
- O **Drawer** é compatível por `parser.version` declarado e pela lista de códigos E/W publicados.
- **Templates** usam somente campos do Schema estável; se um campo for deprecated, manter por 2 MINORs antes de remover em MAJOR seguinte.
- **Plugins** devem declarar `compat.tello`; o host recusa instalar quando fora da faixa.

---

## 5) Processo de release
1. **Branch**: `release/vX.Y.Z` a partir da *main* estável.
2. **Freeze**: só commits de `fix/` e `docs/` entram após RC.
3. **RC**: publicar `X.Y.Z-rc.N` com changelog.
4. **QA**: rodar **Testing Plan** completo; gerar `release-test-report.md`.
5. **Tag**: `vX.Y.Z` + changelog.
6. **Artifacts**: publicar `site-<slug>-v<version>-<timestamp>.zip` e docs correlatos.
7. **Post-release**: abrir `next` com versão bump (ex.: `2.1.2-dev`).

---

## 6) Changelogs (formato)
```
## [2.1.1] — 2025-09-03
### Added
- Drawer U11: Fix All Warnings;
- Parser: E085/E086/E087/E091 (variants), prefixes responsivos.

### Changed
- Tokens: adicionada `color.brand.inverse`.

### Fixed
- Export: bloqueio não-intermitente quando Warnings somente.

### Deprecated
- `paragraph.props.base.maxWidth` (use em container principal). Remover em 3.0.
```

---

## 7) Deprecações e migrações
- Qualquer deprecação entra com etiqueta `Deprecated` no changelog + aviso W* opcional.
- Migrações automáticas devem vir com script `migrate-layout.js` e/ou `tokens.migrate.js`.
- Janela de convivência: manter ambos por **2 MINORs**; remover no próximo **MAJOR**.

---

## 8) Names & paths padronizados
- Seeds: `/seeds/<family>/<name>-vMAJOR.MINOR.json`
- Docs: `/docs/<area>/<name>.md` (os que você já possui no canvas)
- Exports: `/exports/<slug>/site-<slug>-vX.Y.Z-<timestamp>.zip`
- Test fixtures: `/tests/fixtures/*.json`

---

## 9) Política para Templates
- Cada template tem seu próprio `seed.version` e changelog curto.
- Alterações que quebrem export devem vir acompanhadas de atualização de snapshots.
- Templates oficiais: manter *LTS* por 1 MAJOR.

---

## 10) Política para Tokens
- Renomear tokens somente com `aliases` e script de migração.
- Mudanças de paleta que afetem contraste exigem rodada de QA com Axe/Lighthouse.

---

## 11) Política para Plugins
- `compat.tello` obrigatório; denylist para plugins que quebrem políticas.
- Atualizações de permissões exigem reconfirmação do usuário.

---

## 12) Checklists de release
**Pré‑release**
- [ ] Tests OK (conforme Testing Plan)
- [ ] Changelog escrito e revisado
- [ ] Matriz de versões atualizada
- [ ] Scripts de migração testados
- [ ] Docs no canvas atualizados

**Pós‑release**
- [ ] Tag + artifacts publicados
- [ ] Anúncio interno (resumo de breaking changes)
- [ ] Abertura de branch `next` com bump dev

---

## 13) Exemplos de políticas por tipo de mudança
- **Adicionar bloco novo** (minor): atualizar Blocks Library, Templates afetados, snapshots e docs.
- **Mudar shape de `props`** (major): versão do Schema major, adapter no Parser, migração e changelog com guia.
- **Novo warning** (minor): atualizar Validation Codes e Drawer (microcopy) — sem gating.

---

## 14) Roadmap de governança
- Automação para gerar notas de release a partir de PRs rotulados.
- Bot para verificar `compat.tello` de plugins instalados após upgrade do App.

---

Tudo isso elimina a loteria de versões quebradas. Qualquer mudança de contrato agora tem ritual claro e previsível.

