# Estado atual — Adapta Cliente

- task_id: FA-10 (agenda em CALENDÁRIO mensal com quadrados — substitui a lista por dia) — CONCLUÍDA
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 13:20, reafirmado 13:42 ("agenda em formato de calendário real com os dias certinhos — quadrados, não lista; consultora e admin"); análise em `06_notas/analise-fa10-calendario.md`
- etapa: concluida (FA-10 — teste humano aprovado pelo champion "testei e funcionou" 13:50; revalidação 7/7 PASSOU — Skip v0.0.89-91 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T13:43-03:00 — "Pode implementar"
- teste_humano: FA-10 APROVADO ("testei e funcionou" 13:50)
- verificacao_automatica: passou — revalidação do fechamento 7/7 PASSOU (Skip v0.0.89-91 QA ✓): hook agenda/mes com dados reais (acuidar: 11 reuniões em 6 dias; donahelp: 16 reuniões); RLS 403/401; mês inválido rejeitado; consultora vê a SUA empresa; fixtures limpas (FA-10 fixture: 0; banco final 27 reais); regressão: agenda/dia, farol, painel e importação ok; tela reprovada no navegador (grade com quadrados, etiquetas coloridas, contagens, navegação)
- aprendizado: capturado — AP-2026-10-07-1355 (visão mensal = hook que devolve o mês agrupado por dia; a grade é só apresentação — dados de ocorrências já bastam); anteriores capturados: AP-2026-10-07-1350; AP-2026-10-07-1325; AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-10 CONCLUÍDA — revalidação 7/7 PASSOU (hook com dados reais das 2 empresas, RLS 403/401, fixtures limpas, regressão agenda/dia+farol+painel+importação, tela reprovada no navegador); fase/STATUS/changelog/estado atualizados — hook GET /backend/v1/agenda/mes (reuniões do mês agrupadas por dia, RLS por empresa) + tela Agenda reescrita como CALENDÁRIO mensal (grade 7 colunas domingo-sábado, etiquetas coloridas por estado: confirmado verde/pendente âmbar/cancelado vermelho, hoje destacado, navegação ← → e botão Hoje, clique no dia abre o detalhe com Agendadas/Feitas e quem registrou)
- proxima_acao: sem task ativa — próxima task exige novo pedido do champion; pendências humanas: validação do consultor (fase 1), rotação de credenciais, publicação em produção
- atualizado_em: 2026-10-07T13:55:00-03:00

## Recorte FAROL-1 (concluído) + LT-2 + FA-8/9/10

- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70) + gráficos donut e mapa de acompanhamento de volta (v0.0.77-80, aprovado 12:55)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado ("Testei e funcionou" 12:30)
- **LT-2-T02 ✅ CONCLUÍDA (v0.0.81-83):** sincronização das agendas ao logar — Layout dispara a importação por empresa autorizada (fire-and-forget, silencioso); disparo provado por rede; teste humano aprovado ("Testei e funcionou" 13:03)
- **FA-8 ✅ CONCLUÍDA (v0.0.84-86):** tela Agenda (reuniões agendadas + feitas do dia) substitui "Importar da Agenda" — importação automática no login permanece; aprovada pelo champion ("Testei e funcionou" 13:19)
- **FA-9 ✅ CONCLUÍDA (v0.0.88):** sync automático com o Google após confirmação do registro — botão manual vira retry; aprovada pelo champion ("Testei e funcionou" 13:47)
- **FA-10 ✅ CONCLUÍDA (v0.0.89-91):** agenda em CALENDÁRIO mensal (grade com quadrados por dia, etiquetas por estado, navegação por mês, clique abre o detalhe do dia); aprovada pelo champion ("testei e funcionou" 13:50)

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07) — REVERTIDA PARCIALMENTE pela FA-7 (12:36): o mapa volta ALÉM do semáforo.
- FA-7: regras do mapa = as aprovadas na FA-1 (nenhuma regra nova); registro do mês = fato OCORRIDO (data_fato <= hoje) — fato futuro é programada; painel de cobertura continua com a visão mensal detalhada.
- LT-2-T01: registro NUNCA depende do Google; falha → nao_sincronizada + erro + retry por botão; duração fixa 1h; quem sincroniza = criador + gestor/admin.
- LT-2-T02: login NUNCA depende do Google; disparo fire-and-forget e silencioso no primeiro carregamento da sessão (Layout); janela -7d/+14d inalterada.
- FA-8: tela de importação SAIU (a importação é automática no login — LT-2-T02); a tela Agenda mostra reuniões agendadas (futuras) e feitas (passadas) do dia, com quem registrou; consultora vê só a sua empresa (RLS); reuniões canceladas nunca são excluídas (RN-1-08).
- FA-9: sync automático SÓ em ocorrência CONFIRMADA (exceção aguardando aprovação NÃO sincroniza); botão manual permanece como retry/fallback; registro NUNCA depende do Google (garantia LT-2-T01 preservada).
- FA-10: a grade lê a intranet em TEMPO REAL — as reuniões mostradas são as que JÁ estão sincronizadas com o Google (importação no login + sync na confirmação); nada é buscado direto do Google ao desenhar a tela.

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
