# Tello — Macro Strategy (USP • Go‑to‑Market)

> Documento canônico de mercadologia e operação de oferta. Alinha proposta de valor, ICP, arquitetura de ofertas (A/B/C), funis, precificação, SLAs, KPIs e roadmap para tornar o Tello indispensável para diagramação de frameworks no Tailwind.

---

## 1) Executive Summary
- **O que vendemos**: “Branding expresso competitivo de autoridade para white‑label” com camadas combináveis: **A) Branding**, **B) Landing (Tello)**, **C) Media Pack**.
- **Como entregamos**: Brand‑Form (Notion) coleta tokens → Tello (editor por blocos + validação) gera `index.html` + `layout.json` → Export conforme contrato.
- **USP**: rapidez com **escopo rígido** e **qualidade previsível** via validação automática. Cliente sabe exatamente o que compra; você não perde tempo com refação infinita.
- **Promessa operacional**: **48–72h** para uma landing canônica a partir de tokens aprovados e conteúdo fornecido (janela ajustável por pacote).

---

## 2) Posicionamento & USP
- **Categoria**: Ferramenta/serviço de *landing kits* white‑label em Tailwind para agências, freelancers e micro‑SaaS.
- **Diferenciais concretos**
  1) **Gating técnico**: export bloqueado com erros (A11y, spans, CTA, tokens). Confiança no output.
  2) **Tokens‑first**: a estética vem de tokens; nada de “hex aleatório”. Escalável a múltiplas marcas.
  3) **Tempo‑para‑preview baixo**: hero + 2 seções em minutos, preview.png em worker.
  4) **Playbook fix/redo**: quick‑fixes com histórico; rollback não traumático.

**Prova social futura**: demos interativas com seeds oficiais + comparativo “antes/depois” (tokens diferentes, mesmo layout).

---

## 3) ICP (perfil ideal de cliente)
- **Primário**: agências e estúdios que entregam sites estáticos simples e precisam padronizar, white‑label.
- **Secundário**: solopreneurs, consultores e micro‑SaaS com urgência e orçamento controlado.
- **Critérios de fit**
  - Tem logomarca e paleta definidos ou dispostos a usar o Brand‑Form.
  - Precisa de **landing de conversão** com call‑to‑action claro.
  - Tolerância a **escopo fechado** com opções de upsell.

---

## 4) Arquitetura de Ofertas (Value Ladder)
- **A) Branding** (base para tokens)
  - Entregas: mini‑guia de marca, paleta, tipografia, ícones essenciais, `tokens.json`.
  - Upsells: logotipos extras, guidelines estendidos.
- **B) Landing com Tello** (núcleo)
  - Entregas: `index.html`, `layout.json`, `validation.json`, `preview.png`. Opção `site.zip` completo.
  - Variações: **Canonical**, **Split + Pricing**, **Long‑form + FAQ**, **SaaS + Logos**.
- **C) Media Pack**
  - Entregas: banners, thumbnails, post templates (SVG/PNG), snippets testes A/B.

**Add‑ons**: copy assistida, analytics básico (GA, Plausible), formulário de captura (embed), integração simples de e‑mail (Mailchimp/ConvertKit via link).

---

## 5) Precificação (âncoras e estratégia)
- **Âncoras**: 3 pacotes previsíveis com upsell claro; comunicar **escopo fechado** e SLA.
  - **Start** (rápido): 1 página canonical, 1 rodada de ajustes, SLA 72h.
  - **Pro** (vitrine): canonical + seção extra (Pricing/FAQ), 2 rodadas, SLA 72h.
  - **Authority** (completo): split + pricing + proofs + FAQ, 2–3 rodadas, SLA 48–72h.
- **Regras**
  - Ajustes de texto e imagens dentro do escopo: incluso.
  - Mudanças estruturais fora do seed: cobrado por módulo.
  - Taxa de urgência (<48h) e fim de semana: multiplicador.

---

## 6) Funil (aquisição → entrega)
1. **Entrada**: lead chega por portfólio/demo, indicação, conteúdo técnico (Twitter/Dev.to), ou parceria white‑label.
2. **Qualificação** (checklist curto)
   - Objetivo da página em 1 frase? CTA? Prazo? Conteúdo pronto? Tokens disponíveis?
3. **Proposta**: selecionar pacote A/B/C + add‑ons. Deadline, SLA, número de rodadas, direitos de uso.
4. **Kickoff**: Brand‑Form preenchido; conteúdo mínimo coletado; seed escolhido.
5. **Produção**: montar layout com seeds; rodar Drawer; enviar `preview.png`.
6. **Ajustes**: rodadas definidas no pacote; todas via comentários objetivos.
7. **Entrega**: `site.zip` e documentação curta.
8. **Upsell**: media pack, variações de campanha, “sazonal” ou multilíngue.

---

## 7) SLA, Política de ajustes e garantias
- **SLA**: contagem começa após Brand‑Form + conteúdo mínimo.
- **Rodadas**: conforme pacote; alterações adicionais são módulos (menu de preços claro).
- **Garantia técnica**: saída sem erros de validação; warnings toleráveis acordados previamente.
- **Rollback**: seeds versionados; snapshots de HTML para auditoria.

---

## 8) Mensagens (copy base por canal)
- **Site/Portfolio**: “Tokens entram, landing sai. White‑label, em Tailwind, com validação automática. Sem drama.”
- **Pitch curto**: “Landing pronta em 48–72h, com seu branding e sem retrabalho. Preço fechado, escopo claro.”
- **Parcerias**: “Você vende; eu entrego. Seus clientes, sua marca; meu motor por trás.”

> Microcopy de produto/UI vai em documento próprio de *UI Microcopy Guidelines*.

---

## 9) Canais de aquisição (
**baixa fricção primeiro**)
- **Demos públicas**: seeds rodando com 2–3 paletas diferentes.
- **Conteúdo técnico**: artigos “Tello Method” (tokens→Tailwind), snippets no GitHub Gist.
- **Parcerias**: agências que terceirizam landing estática.
- **Comunidades**: Tailwind Discord, r/web_design, r/Entrepreneur (sem spam, com valor real).
- **Outbound leve**: micro‑SaaS e newsletters que precisam de páginas de campanha.

---

## 10) KPIs e dashboards
- **Qualidade**: taxa de exports bloqueados (errors > 0) por projeto; média < 1.0 após primeira rodada.
- **Velocidade**: TTFP (time‑to‑first‑preview) ≤ 4h; TTE (time‑to‑export) ≤ 48–72h.
- **Reuso**: % de seções vindas de templates oficiais > 70%.
- **Satisfação**: NPS simples pós‑entrega; taxa de refação extra < 10%.
- **Aquisição**: conversão de lead qualificado → fechado ≥ 40% (white‑label partners) / ≥ 20% (direto).

---

## 11) Riscos & mitigação
- **Scope creep**: contrato de escopo fechado por pacote; mudanças viram módulos; aprovar antes.
- **Custos de revisão**: limite de rodadas; comentários estruturados (per seção).
- **Atrasos do cliente**: SLA suspenso sem Brand‑Form/conteúdo; clusterizar lembretes automáticos.
- **Qualidade visual**: tokens fracos → resultado fraco. Oferecer ajuste mínimo de tokens como add‑on.
- **Dependência de CDN**: preset offline no export.

---

## 12) Roadmap para “indispensável em Tailwind”
- **Curto**: publicar Templates Pack oficial; CLI simples (`validate`, `export`).
- **Médio**: galeria pública de seeds; “Open Template Standard” (JSON) com validações.
- **Plugins**: marketplace mínimo com 3–5 plugins úteis (normalize spans, UTM exporter, l10n strings).
- **Integrações**: import de Figma tokens; Brand‑Form → tokens.json automático.
- **Educação**: guia “Tailwind por tokens” com exemplos reais.

---

## 13) Operação (LauncherOps) — fluxo direto e inverso
- **Direto**: Brief → Tokens → Layout Seed → Validação → Export → Entrega.
- **Inverso**: Problema do cliente → mapear no `validation.json` ou tokens → corrige na origem → reexport.
- **Versionamento**: seeds `vMAJOR.MINOR`; scripts de migração; histórico de export por projeto.

---

## 14) Playbook de Descoberta (discovery)
Perguntas obrigatórias (5 minutos):
1. Objetivo da landing e CTA?
2. Oferta principal (preço/benefícios) existe? Precisará de Pricing/FAQ?
3. Há prova social (logos, depoimentos)?
4. Tokens/branding prontos? Se não, pacote A primeiro.
5. Prazo real e restrições?

Saída: pacote recomendado + cronograma e entregáveis.

---

## 15) Materiais de venda (entregar junto da proposta)
- 1 preview do seed com paleta do cliente (mock rápido).
- Lista do que está incluso em cada pacote e o que não está.
- Linha do tempo com check‑ins (ex.: D0 kickoff, D1 preview, D2 ajustes, D3 entrega).

---

## 16) Política de conteúdo
- Cliente fornece textos e imagens; correções leves incluídas. Copy do zero é add‑on.
- Direitos: o HTML final é do cliente; templates permanecem seus (licença de uso).

---

## 17) Próximos passos (ação)
1. Publicar 3 demos online (Canonical, Split+Pricing, Long‑form+FAQ) com 2 paletas cada.
2. Escrever 2 posts de processo (tokens→landing, validação/gating na prática).
3. Criar kit de proposta em Markdown com tabelas de pacotes e SLAs.
4. Montar lista de 20 parceiros potenciais e abordagem de piloto.

---

Este documento é o norte de mercado. Ajuste os thresholds, pacotes e canais conforme a realidade das conversões e do seu tempo. O Tello sustenta a promessa: rápido, previsível, white‑label, sem telefone sem fio.

