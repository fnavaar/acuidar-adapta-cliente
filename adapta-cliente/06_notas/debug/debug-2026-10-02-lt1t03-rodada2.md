# Debug 2026-10-02 (2ª rodada) — LT-1-T03: botão ainda falhou no champion + trilha "esquisita"

## Sintomas relatados pelo champion (2º teste, v0.0.20)
1. Botão "Aprovar exceção" **continua sem funcionar** no navegador dele.
2. **Trilha de decisão "esquisita"** — o print mostra entradas de teste meu gravadas no registro
   ("teste find", "teste volta", "recolocar na fila", "APROVADO via fetch direto do browser").
   Causa: poluição dos MEUS testes automatizados em registros reais de fixture — não é bug do
   código; é sujeira de dados. Corrigido limpando o campo motivo dos registros de fixture e
   criando registros limpos para teste.

## Diagnóstico do botão (cadeia causal)
- Prova no navegador automatizado (mesmo preview, v0.0.20): aprovação pela UI **funciona**
  (diálogo fecha, registro vira confirmado, trilha gravada com "Exceção APROVADA: ...").
- O bundle do preview tem o código correto (`fetch(pb.baseUrl + "/backend/v1/ocorrencias/transicao")`
  com URL absoluta `https://adapta-cliente-c2bc2.shrd00.internal.goskip.dev`).
- Reprodução por API do cenário do champion (login gestor → transição) também funciona.
- **Hipótese principal: cache do navegador** — o champion testou com o bundle antigo (v0.0.18/19,
  que chamava `pb.send` → 404 silencioso). O print mostra "Atualizada: 2026-10-02 15:12", horário
  compatível com o bundle anterior ao fix.
- Hipótese secundária descartada: rota/endpoint (todas as vias provadas funcionam na v0.0.20).

## Ações desta rodada
1. Limpada a trilha dos registros de fixture (motivo substituído por texto de fixture).
2. Criados 2 registros LIMPOS em aguardando_aprovacao para o reteste:
   - `aspk6i9bz5pd7ll` — "acompanhamento - acuidar abc" (unidade 101)
   - `t7nfareuzlwkwr7` — "Acompanhamento — Acuidar JP Centro (teste exceção)" (unidade 2)
3. Prova de navegador repetida na v0.0.20: SUCESSO (aprovação pela UI confirmada por API).

## Gate atual
aguardando_teste_humano — champion deve **recarregar com cache limpo (Ctrl+Shift+R)** e refazer o
teste nos registros limpos. Se ainda falhar com cache limpo, capturar o erro do Console do
navegador (F12 → Console) — não vou adivinhar além disso.