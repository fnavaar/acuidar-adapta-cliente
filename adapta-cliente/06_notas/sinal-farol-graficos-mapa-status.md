# Sinal — Farol com gráficos, status de atividade e mapa de acompanhamento (2026-10-07 12:36)

**Pedido do champion (12:36):** "eu quero que o farol das unidades tenha graficos e mostre STATUS DE ATIVIDADE DAS UNIDADES e MAPA DE ACOMPANHAMENTO que vai mostrar a relação com as reuniões, Unidades em dia com as reuniões, Unidades que não retornam as tentativas, Unidades próximo a ficar em atraso com as reuniões, Unidades com reunião programada"

## Interpretação (validada no relatório de análise)

- O champion quer REVERTER (parcialmente) a FA-6: o mapa de acompanhamento mensal volta ao farol —
  com as 4 categorias aprovadas na FA-1 (em_dia, nao_retorna, proximo_atraso, programada) —
  ALÉM do semáforo PECAF/PEDHE que continua (aprovado e com carga 2026 rodada).
- "tenha graficos" = distribuição visual (gráficos) das contagens: mapa de acompanhamento
  (4 categorias) + status de atividade (ativa/treinada/suspensa/fechada) — como a FA-1 tinha
  (cards + gráfico de distribuição).
- STATUS DE ATIVIDADE DAS UNIDADES = campo `status_atividade` da `unidades_info` (FA-1),
  com gráfico de distribuição.
- MAPA DE ACOMPANHAMENTO = relação com as reuniões: em dia / não retornam / próximo a atraso /
  programada — classificação mensal da FA-1 (cadência aprovada).

## Contexto da reversão

- FA-6 (10:37) removeu o mapa mensal do farol por pedido do champion ("as reuniões não vai
  influenciar mais no farol") — farol ficou unicamente PECAF/PEDHE.
- FA-1 (12:52) tinha o mapa completo: cards por categoria + gráfico de distribuição do status
  de atividade + tabela ordenada por prioridade. Foi aprovado ("ok pode prosseguir").
- Agora (12:36) o champion pede os DOIS no farol: semáforo PECAF/PEDHE + mapa de acompanhamento
  com gráficos + status de atividade.

## Decisões técnicas (resolvidas na implementação FA-7)

1. Hook do farol volta a consultar ocorrências/agenda (como na FA-1) — sem tocar nas regras
   aprovadas (cadência mensal, nao_retorna vence, RN-1-14).
2. Tela: 2 gráficos donut (distribuição do mapa + status de atividade) + tabela com colunas
   do semáforo E do mapa.
3. Pele NEXUS mantida (FA-5 aprovada).
4. Painel de cobertura (SPEC-1-003) inalterado — a visão mensal duplica nele e no farol
   (o champion pediu explicitamente o mapa no farol).
