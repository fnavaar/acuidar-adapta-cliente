# FA-7 — Farol com gráficos + status de atividade + mapa de acompanhamento (análise 2026-10-07 12:40)

**Pedido do champion (12:36):** farol com gráficos, STATUS DE ATIVIDADE DAS UNIDADES e MAPA DE
ACOMPANHAMENTO (em dia, não retornam, próximo a atraso, programada) — reversão parcial da FA-6
com acréscimo de gráficos.

## Estado atual verificado

- Hook `farol_unidades.js` (v0.0.70): só semáforo PECAF/PEDHE; unidades_info (status_atividade,
  tentativas_sem_retorno) e avaliações do ano JÁ consultados. O mapa mensal foi removido na FA-6.
- Tela `Farol.tsx`: cards clicáveis do semáforo + tabela (Unidade/Semáforo/Avaliação/Status) +
  detalhe da unidade (avaliação/status/ocorrências). Pele NEXUS (FA-5 aprovada).
- Painel de cobertura (`painel_cobertura.js`): visão mensal por ocorrências — INTACTO (SPEC-1-003).
- recharts ^3.8.1 JÁ está no package.json (sem instalar nada).
- Regras aprovadas da FA-1 (changelog 2026-10-06, mapa provado e aprovado "ok pode prosseguir"):
  em_dia = registro no mês corrente ou programada · proximo_atraso = sem registro no mês corrente
  a partir do dia 20 · em_atraso = sem registro no mês corrente/mês anterior vazio ·
  programada = reunião futura na agenda · nao_retorna = 3+ tentativas sem retorno, vence as demais.

## Plano (5 passos) — EXECUTADO (v0.0.77-80)

1. **Hook:** restaurar a consulta de ocorrências do mês corrente + próximo mês (programadas) e a
   classificação mensal aprovada da FA-1 — sem tocar nas regras do semáforo (intactas) nem no
   painel de cobertura. Resposta ganha: mapa_contagem (em_dia/nao_retorna/proximo_atraso/em_atraso/
   programada) + linhas[].classificacao/registro_no_mes/proxima_programada/tentativas.
2. **Tela — gráficos:** 2 gráficos de distribuição (recharts PieChart donut): MAPA DE
   ACOMPANHAMENTO (5 categorias) + STATUS DE ATIVIDADE (ativa/treinada/suspensa/fechada/sem_status).
   Cards clicáveis do mapa que filtram a tabela (padrão NEXUS já aprovado nos cards do semáforo).
3. **Tela — tabela:** coluna "Situação" (badge da classificação mensal) + coluna "No mês"
   (registros do mês) ao lado do semáforo; ordenação por prioridade (nao_retorna → em_atraso →
   proximo_atraso → programada → em_dia).
4. **Filtros:** filtro do mapa (select) somável ao filtro do semáforo existente.
5. **Provas:** RLS 403/401; classificação mensal provada com fixtures dedicadas (limpas depois —
   migration nova); semáforo intacto (regressão: 61 avaliações, contagens iguais); painel de
   cobertura intacto; gráficos renderizados no navegador (screenshot).

## Decisões embutidas (champion pode vetar)

- O mapa volta ao farol ALÉM do semáforo (não substitui) — os dois convivem na mesma tela.
- Regras do mapa = as aprovadas na FA-1 (nenhuma regra nova).
- Painel de cobertura continua com a visão mensal detalhada (duplicação de propósito aceita —
  o champion pediu explicitamente o mapa no farol).
- Gráficos = donut charts (recharts) no estilo da pele NEXUS.

## Riscos

- Regressão no semáforo: mitigada por prova de contagens iguais antes/depois (61 avaliações).
- Performance: hook farol passa a consultar ocorrências do mês (banco local, tempo real — mesma
  consulta do painel; carga baixa).
- JSVM: lógica inline no callback (AP-2026-10-07-1215); sem toLocaleString (AP-2026-10-06-1710).

## Teste humano previsto

Farol com gráficos + mapa + status visíveis; clicar num card do mapa filtra a tabela;
semáforo continua funcionando; detalhe da unidade intacto.
