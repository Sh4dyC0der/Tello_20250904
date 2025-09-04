# Tello — Macro Strategy — Blueprint V2.1.1 (refeito)

> Guia canônico de estratégia e operação comercial do Tello. Conecta oferta (A/B/C), posicionamento, ICP, funis, precificação, SLAs, KPIs e riscos aos contratos técnicos: **Tokens Contract**, **Layout Schema**, **Templates Pack**, **Validation/Drawer U11**, **FX Inventory**, **Export Presets**, **Plugin API**, **Testing Plan** e **Release & Versioning Policy**.

---

## 1) Executive Summary
- **Proposta**: “Branding expresso competitivo de autoridade (white‑label) + landing em Tailwind com validação automática.”
- **Como**: tokens do Brand‑Form → layout com Templates → validação (E*/W*) → export `index.html`/`site.zip`. Zero build.
- **Promessa**: preview em ~4h; entrega em **48–72h** após tokens+conteúdo mínimo.
- **Diferencial**: **gating técnico** (sem HTML inválido), **tokens‑first**, **seeds versionados**.

---

## 2) Posicionamento & Narrativa
- **Categoria**: “Landing kits operacionais em Tailwind” para agências e solo makers que querem padronizar e escalar.
- **USP técnica**
  1) **Validação com quick‑fix** → reduz refação e ambiguidade.
  2) **Tokens contra HEX** → consistência visual e portabilidade multi‑marca.
  3) **Export determinístico** → previsibilidade de resultado.
  4) **Tempo‑para‑preview curto** → acelera decisão do cliente.

**Prova**: demos com 2–3 paletas em seeds oficiais, `validation.json` zero E*.

---

## 3) ICP (Ideal Customer Profile)
- **Primário**: estúdios/agências white‑label com pipeline repetível de landings.
- **Secundário**: micro‑SaaS, consultores e creators com ofertas claras e urgentes.
- **Critérios de fit**: tem branding ou aceita Brand‑Form; foco em conversão; escopo fechado; tolerância a templates customizáveis.

---

## 4) Arquitetura de Ofertas (Value Ladder)
- **A) Branding**
  - Entregas: mini‑guia + `tokens.json` (conforme **Tokens Contract**).
  - Saídas opcionais: variantes de logo, guidelines rápidas.
- **B) Landing com Tello (núcleo)**
  - Entregas: `index.html`, `layout.json`, `validation.json`, `preview.png`, `site.zip`.
  - Variações: **Canonical**, **Split + Pricing**, **Long‑form + FAQ**, **SaaS + Logos** (ver **Templates Pack**).
  - Garantia técnica: export **bloqueia** com E* (E080/E081…E201).
- **C) Media Pack**
  - Entregas: banners, covers, posts; paleta derivada de tokens.

**Add‑ons**: copy assistida, analytics (GA/Plausible), formulário (embed), internacionalização leve, integrações simples via **Plugin API**.

---

## 5) Precificação & SLAs
- **Pacotes**
  - **Start** — 1 página canonical, 1 rodada, SLA 72h.
  - **Pro** — canonical + 1 seção (Pricing/FAQ/Logos), 2 rodadas, SLA 72h.
  - **Authority** — split+pricing+proofs+faq, 2–3 rodadas, SLA 48–72h.
- **Regras**
  - Mudança estrutural fora do seed → preço por módulo adicional.
  - Urgência (<48h) e fim de semana → multiplicador.
  - Tokens/copy revisados pelo cliente; nós aplicamos.

---

## 6) Funil operacional (A → Z)
1. **Aquisição**: demo pública, conteúdo técnico, parceria white‑label.
2. **Qualificação rápida** (5 itens): objetivo da landing, CTA, prazo, conteúdo mínimo, tokens.
3. **Proposta**: escolher pacote + add‑ons + cronograma; checklist de escopo.
4. **Kickoff**: Brand‑Form preenchido, seed selecionado.
5. **Produção**: montar layout, HUD para spans/start/order, validação no Drawer.
6. **Preview**: enviar `preview.png` e/ou `index.html`.
7. **Ajustes**: rodadas acordadas, difs claros.
8. **Entrega**: `site.zip` + README.
9. **Upsell**: Media Pack, variações de campanha, idiomas.

Integrações com docs: **Onboarding/Quickstart** detalha atalhos e pitfalls; **Export Presets** dita artefatos e naming; **UI Microcopy** padroniza mensagens.

---

## 7) KPIs (mínimos de operação)
- **Qualidade**: média de E* por projeto após 1ª rodada ≤ 1.0; export final com 0 E*.
- **Velocidade**: TTFP ≤ 4h; TTE ≤ 48–72h.
- **Reuso**: ≥ 70% das seções vindas de Templates oficiais.
- **Satisfação**: NPS simples e taxa de refação extra < 10%.
- **Aquisição**: conversão de lead qualificado → fechado ≥ 40% (parcerias) / ≥ 20% (orgânico).

Dashboards usam telemetria leve: `export_done`, `export_blocked`, `fix_applied`.

---

## 8) Riscos & Mitigação
- **Scope creep** → contrato de escopo fechado; mudanças viram módulos com preço.
- **Tokens ruins** → oferecer ajuste mínimo; validar contraste com overlays.
- **Dependência de CDN** → preset offline no export.
- **Atrasos de insumo** → SLA só começa após Brand‑Form+conteúdo; lembretes automatizados.
- **Fragmentação de versões** → seguir **Release & Versioning Policy**; seeds versionados.

---

## 9) Go‑to‑Market (passos práticos)
- **Demos**: 3 seeds online com 2 paletas; código aberto dos layouts.
- **Conteúdo**: 2 posts (“tokens→Tailwind”, “gating de export na prática”).
- **Parcerias**: lista de 20 estúdios para piloto; pitch com `site.zip` exemplo.
- **Case rápido**: refazer landing conhecida usando tokens diferentes; publicar antes/depois.

---

## 10) Operação & Playbooks
- **Direto**: Brief → Tokens → Layout Seed → Drawer → Export → Entrega.
- **Inverso**: Encontrou erro na entrega? Diagnosticar pelo `validation.json` e corrigir no layout/tokens → reexport.
- **Suporte**: checklists de QA e *cheat‑sheet* dos códigos para acelerar.

---

## 11) Escalonamento controlado
- **Templates**: manter catálogo enxuto e de alta qualidade; adicionar variações por cluster (Hero/Benefits/Proofs/Pricing/FAQ/CTA/Footer).
- **Plugins**: 3–5 utilitários de alto impacto (normalize spans, UTM exporter, l10n strings).
- **Educação**: guia “Tailwind por tokens”; vídeo curto *speed‑run*.

---

## 12) Política de conteúdo & direitos
- Cliente fornece textos/imagens; correções leves inclusas; copy do zero é add‑on.
- HTML final é do cliente; **templates** permanecem seus (licença de uso).

---

## 13) Checklists comerciais
**Antes da proposta**
- [ ] ICP bate com o lead
- [ ] Tokens ou Brand‑Form combinados
- [ ] Prazos e rodadas claras
- [ ] Seed iniciante sugerido

**Antes da entrega**
- [ ] `site.zip` com `index.html` + `layout.json` + `tokens.json` + `validation.json` + README
- [ ] `validation.json` sem E*
- [ ] `preview.png` anexado

---

## 14) Roadmap estratégico (12 meses)
- Q1: catálogo oficial de templates + CLI mínima (validate/export)
- Q2: galeria pública e marketplace de plugins mínimo
- Q3: integração de tokens do Figma/Notion; estudos de contraste automatizados
- Q4: programa de parceiros white‑label; estudo de preços baseado em dados

---

Este blueprint amarra a frente de mercado ao núcleo técnico do Tello. Se o contrato mudar em **Schema/Validation/Export**, revise este documento e o de **Release & Versioning Policy** para evitar divergência entre promessa e execução.

