# AP-2026-10-07-1300 — Fato futuro do mesmo mês é programada, não registro

**Origem:** FA-7 (prova do mapa, v0.0.78) · **Data:** 2026-10-07

- **Sinal:** fixture com ocorrência futura no MESMO mês (unidade 340, data_fato 2026-10-20) fez a
  unidade virar `em_dia` (reg_mes=1) em vez de `programada` — o filtro do mês corrente
  (data_fato >= primeiro dia) capturava fatos futuros.
- **Regra FA-1 reforçada pela prova:** "registro no mês corrente" = fato JÁ OCORRIDO
  (data_fato <= hoje). Reunião futura do mesmo mês é `programada` (categoria própria), nunca
  registro. Correção: fim do filtro = amanhã 00:00 (data_fato < amanhã).
- **Regra reutilizável:** janelas de "registro no período" devem terminar no PRESENTE
  (data_fato < amanhã), não no fim do período — ocorrências futuras são planejamento, não
  histórico.
- **Confiança:** alta — bug reproduzido com fixture, correção provada (unidade 340 → programada).
