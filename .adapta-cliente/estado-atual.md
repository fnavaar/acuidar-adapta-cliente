# Estado atual — Adapta Cliente

- task_id: FA-10 (agenda em CALENDÁRIO mensal com quadrados — substitui a lista por dia)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 13:20, reafirmado 13:42 ("agenda em formato de calendário real com os dias certinhos — quadrados, não lista; consultora e admin"); análise em `06_notas/analise-fa10-calendario.md`
- etapa: aguardando_teste_humano (FA-10 implementada e provada — Skip v0.0.89-91 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T13:43-03:00 — "Pode implementar"
- teste_humano: pendente (FA-10 — grade mensal com quadrados; consultora vê a sua empresa; admin as duas; clique no dia abre o detalhe)
- verificacao_automatica: passou — Skip v0.0.89-91 QA ✓. Provas: hook agenda/mes (acuidar outubro: 11 reuniões em 6 dias, contagens 4 agendadas/7 feitas; donahelp: 16 reuniões); RLS 403 (consultora donahelp→acuidar); 401 sem auth; mês 13 rejeitado; consultora vê a agenda da SUA empresa (16 reuniões); fixtures futuro (20/10) e passado (01/10) provadas na grade; tela provada no navegador (artifacts/fa10-calendario.png — grade com quadrados, etiquetas coloridas por estado, hoje destacado, contagens do mês); detalhe do dia provado (agenda/dia 02/10: 5 feitas com unidade e quem registrou); fixtures limpas (migration 0033, banco final 27 reais); regressão: agenda/dia, farol e painel ok
- aprendizado: pendente (FA-10); anteriores capturados: AP-2026-10-07-1325; AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-10 implementada — hook GET /backend/v1/agenda/mes (reuniões do mês agrupadas por dia, RLS por empresa) + tela Agenda reescrita como CALENDÁRIO mensal (grade 7 colunas domingo-sábado, etiquetas coloridas por estado: confirmado verde/pendente âmbar/cancelado vermelho, hoje destacado, navegação ← → e botão Hoje, clique no dia abre o detalhe com Agendadas/Feitas e quem registrou)
- proxima_acao: teste humano do champion (aba Agenda: grade mensal, navegar entre meses, clicar num dia com reuniões, consultora vê só a sua empresa, admin as duas)
- atualizado_em: 2026-10-07T13:50:00-03:00

## Recorte FAROL-1 (concluído) + LT-2 + FA-8/9/10

- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70) + gráficos donut e mapa de acompanhamento de volta (v0.0.77-80, aprovado 12:55)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado ("Testei e funcionou" 12:30)
- **LT-2-T02 ✅ CONCLUÍDA (v0.0.81-83):** sincronização das agendas ao logar — Layout dispara a importação por empresa autorizada (fire-and-forget, silencioso); disparo provado por rede; teste humano aprovado ("Testei e funcionou" 13:03)
- **FA-8 ✅ CONCLUÍDA (v0.0.84-86):** tela Agenda (reuniões agendadas + feitas do dia) substitui "Importar da Agenda" — importação automática no login permanece; aprovada pelo champion ("Testei e funcionou" 13:19)
- **FA-9 (aguardando_teste_humano, v0.0.88):** sync automático com o Google após confirmação do registro — botão manual vira retry
- **FA-10 (aguardando_teste_humano, v0.0.89-91):** agenda como CALENDÁRIO mensal (grade com quadrados, etiquetas por estado, detalhe do dia) — substitui a lista por dia da FA-8

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07) — REVERTIDA PARCIALMENTE pela FA-7 (12:36): o mapa volta ALÉM do semáforo.
- FA-7: regras do mapa = as aprovadas na FA-1 (nenhuma regra nova); registro do mês = fato OCORRIDO (data_fato <= hoje) — fato futuro é programada; painel de cobertura continua com a visão mensal detalhada.
- LT-2-T01: registro NUNCA depende do Google; falha → nao_sincronizada + erro + retry por botão; duração fixa 1h; quem sincroniza = criador + gestor/admin.
- LT-2-T02: login NUNCA depende do Google; disparo fire-and-forget e silencioso no primeiro carregamento da sessão (Layout); janela -7d/+14d inalterada.
- FA-8: tela de importação SAIU (a importação é automática no login — LT-2-T02); a tela Agenda mostra reuniões agendadas (futuras) e feitas (passadas) do dia, com quem registrou; consultora vê só a sua empresa (RLS); reuniões canceladas nunca são excluídas (RN-1-08).
- FA-9: sync automático SÓ em ocorrência CONFIRMADA (exceção aguardando aprovação NÃO sincroniza); botão manual permanece como retry/fallback; registro NUNCA depende do Google (garantia LT-2-T01 preservada).
- FA-10: a grade do calendário lê a intranet em tempo real — mostra as reuniões que JÁ estão sincronizadas com o Google (importação automática no login + sync na confirmação); consultora vê só a sua empresa (RLS); admin as duas.

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
