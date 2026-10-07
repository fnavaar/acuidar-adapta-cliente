# Estado atual — Adapta Cliente

- task_id: FA-8 (tela Agenda — reuniões agendadas + feitas; substitui "Importar da Agenda")
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 13:07 (tópico Agenda com reuniões agendadas e feitas; consultora vê o dia; administração vê tudo; "Importar da Agenda" sai); análise em `06_notas/analise-fa8-tela-agenda.md`
- etapa: aguardando_teste_humano (FA-8 implementada e provada — Skip v0.0.84-86 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T13:08-03:00 — "Pode implementar"
- teste_humano: pendente (FA-8 — consultora vê o dia da sua empresa; admin vê as duas e quem registrou; menu "Agenda")
- verificacao_automatica: passou — Skip v0.0.84-86 QA ✓. Provas: hook agenda/dia (acuidar hoje: 1 agendada 15:00 confirmada "acuidar abc acompanhamento" — Admin Teste; 2026-10-06: 1 feita pendente do Google); donahelp hoje: 3 agendadas (conferência humana); RLS 403 (consultora donahelp→acuidar); 401 sem auth; data inválida rejeitada; consultora vê a agenda da SUA empresa (donahelp 2026-10-07: 3 agendadas; fixtures feita/agendada provadas nas 2 seções com nome da unidade e quem registrou); tela provada no navegador (artifacts/fa8-agenda-admin.png — menu "Agenda", contagens do dia, seções Agendadas/Feitas, "Registrada por"); fixtures limpas (migration 0032, banco final 26 reais); regressão: farol, painel e importação ok
- aprendizado: pendente (FA-8); anteriores capturados: AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-8 implementada — hook GET /backend/v1/agenda/dia (reuniões do dia separadas em AGENDADAS (futuras) e FEITAS (passadas), RLS por empresa, nome da unidade + quem registrou) + tela Agenda reescrita (seletor de data com hoje como padrão, seletor de empresa, contagens do dia, seções Agendadas/Feitas) + menu "📅 Importar da Agenda" → "📅 Agenda"
- proxima_acao: teste humano do champion (aba Agenda: panorama do dia, navegar entre datas, consultora vê só a sua empresa, admin vê as duas e quem registrou)
- atualizado_em: 2026-10-07T13:20:00-03:00

## Recorte FAROL-1 (concluído) + LT-2

- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70) + gráficos donut e mapa de acompanhamento de volta (v0.0.77-80, aprovado 12:55)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado ("Testei e funcionou" 12:30)
- **LT-2-T02 ✅ CONCLUÍDA (v0.0.81-83):** sincronização das agendas ao logar — Layout dispara a importação por empresa autorizada (fire-and-forget, silencioso); disparo provado por rede; teste humano aprovado ("Testei e funcionou" 13:03)
- **FA-8 (aguardando_teste_humano, v0.0.84-86):** tela Agenda (reuniões agendadas + feitas do dia) substitui "Importar da Agenda" — importação automática no login permanece

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07) — REVERTIDA PARCIALMENTE pela FA-7 (12:36): o mapa volta ALÉM do semáforo.
- FA-7: regras do mapa = as aprovadas na FA-1 (nenhuma regra nova); registro do mês = fato OCORRIDO (data_fato <= hoje) — fato futuro é programada; painel de cobertura continua com a visão mensal detalhada.
- LT-2-T01: registro NUNCA depende do Google; falha → nao_sincronizada + erro + retry por botão; duração fixa 1h; quem sincroniza = criador + gestor/admin.
- LT-2-T02: login NUNCA depende do Google; disparo fire-and-forget e silencioso no primeiro carregamento da sessão (Layout); janela -7d/+14d inalterada.
- FA-8: tela de importação SAIU (a importação é automática no login — LT-2-T02); a tela Agenda mostra reuniões agendadas (futuras) e feitas (passadas) do dia, com quem registrou; consultora vê só a sua empresa (RLS); reuniões canceladas nunca são excluídas (RN-1-08).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
