# Estado atual — Adapta Cliente

- task_id: FA-7 (farol com gráficos + status de atividade + mapa de acompanhamento)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 12:36 (farol com gráficos, STATUS DE ATIVIDADE e MAPA DE ACOMPANHAMENTO); sinal em `06_notas/sinal-farol-graficos-mapa-status.md` + análise em `06_notas/analise-fa7-farol-graficos-mapa.md`
- etapa: aguardando_teste_humano (FA-7 implementada e provada — Skip v0.0.77-80 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T12:40-03:00 — "Pode implementar"
- teste_humano: pendente (FA-7 — farol com gráficos + mapa + status; LT-2-T01 aprovado 12:30; CARGA-2026 aprovada 11:58; FA-5/FA-6 aprovados 10:51)
- verificacao_automatica: passou — Skip v0.0.77-80 QA ✓. 12 provas: hook com mapa (acuidar: em_dia 3/em_atraso 169; donahelp: em_dia 2/em_atraso 52); semáforo INTACTO por regressão (verde 32/vermelho 140 acuidar; verde 9/vermelho 45 donahelp — contagens iguais); painel de cobertura INTACTO (172 unidades, 169 sem registro); RLS 403 (consultora donahelp→farol acuidar); 401 sem auth; classificação provada com fixtures: ocorrência futura 2026-10-20 → programada (bug pego pela prova: fato futuro contava como registro do mês → corrigido data_fato <= hoje v0.0.78); 3 tentativas → nao_retorna vence (unidade 253); status_atividade contagem (ativa 1 com fixture); hoje dia 7 (< 20) → sem registro = em_atraso (regra proximo_atraso a partir do dia 20); fixtures limpas (migration 0030; unidades_info 0); gráficos donut + cards clicáveis + coluna Situação provados no navegador (artifacts/fa7-*.png) — filtro "Em dia" filtra a tabela para as 3 unidades em dia
- aprendizado: pendente (FA-7 — sinal: fato futuro do mesmo mês não é registro do mês, é programada; a regra FA-1 foi reforçada pela prova); anteriores capturados: AP-2026-10-07-1215 + AP-2026-10-07-1240 (LT-2-T01); AP-2026-10-06-1258/1310/1710; FA-5/FA-6 sem sinal
- ultima_acao: FA-7 implementada — hook do farol volta a classificar o mapa mensal (regras FA-1: nao_retorna vence; registro do mês = fato ocorrido; programada = reunião futura; proximo_atraso a partir do dia 20) + tela com 2 gráficos donut (mapa + status), cards clicáveis que filtram, coluna Situação (mês) e filtro de situação
- proxima_acao: teste humano do champion (farol com gráficos + mapa + status; clicar nos cards do mapa filtra a tabela; semáforo intacto)
- atualizado_em: 2026-10-07T13:05:00-03:00

## Recorte FAROL-1 (concluído) + FA-7

- **FA-1..FA-6 ✅ CONCLUÍDAS:** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado ("Testei e funcionou" 12:30)
- **FA-7 (aguardando_teste_humano, v0.0.77-80):** gráficos donut (mapa + status) + mapa de acompanhamento mensal (regras FA-1) + coluna Situação na tabela + filtro de situação — reversão parcial da FA-6; semáforo e painel intactos

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07) — REVERTIDA PARCIALMENTE pela FA-7 (12:36): o mapa volta ALÉM do semáforo.
- FA-7: regras do mapa = as aprovadas na FA-1 (nenhuma regra nova); registro do mês = fato OCORRIDO (data_fato <= hoje) — fato futuro é programada; painel de cobertura continua com a visão mensal detalhada.
- LT-2-T01: registro NUNCA depende do Google; falha → nao_sincronizada + erro + retry por botão; duração fixa 1h; quem sincroniza = criador + gestor/admin.

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
