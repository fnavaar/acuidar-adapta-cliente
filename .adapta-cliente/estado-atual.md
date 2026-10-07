# Estado atual — Adapta Cliente

- task_id: LT-2-T02 (sincronização das agendas ao logar) — CONCLUÍDA
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 12:53 ("sincronização das agendas toda vez que a pessoa logar"); sinal em `06_notas/sinal-lt2-t02-sync-agenda-login.md` + análise em `06_notas/analise-lt2-t02-sync-agenda-login.md`
- etapa: concluida (LT-2-T02 — teste humano aprovado pelo champion "Testei e funcionou" 13:03; revalidação 4/4 PASSOU — Skip v0.0.81-83 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T12:55-03:00 — "sim" (LT-2-T02, após o relatório)
- teste_humano: LT-2-T02 APROVADO ("Testei e funcionou" 13:03); FA-7 aprovado 12:55; LT-2-T01 aprovado 12:30; CARGA-2026 aprovada 11:58; FA-5/FA-6 aprovados 10:51
- verificacao_automatica: passou — Skip v0.0.81-83 QA ✓. Revalidação do fechamento 4/4 PASSOU: importação ok nas 2 empresas com idempotência (importadas 0, ja_existentes 5/8); RLS 403 (consultora donahelp→acuidar); 401 sem auth; fixtures limpas (0031); farol e painel de cobertura intactos (regressão). Provas da implementação: disparo provado por evidência de rede (admin → 2 chamadas; consultora → 1 — RLS no disparo); idempotência (2 logins → contagem inalterada); login nunca bloqueado. Bug pego pela prova: fetch fire-and-forget no Login cancelado pelo redirect — movido para o Layout (AP-2026-10-07-1320)
- aprendizado: capturado — AP-2026-10-07-1320 (fetch antes de redirect é cancelado — disparar no Layout); conclusão sem sinal novo (controle.md); anteriores: AP-2026-10-07-1300 (FA-7); AP-2026-10-07-1215 + AP-2026-10-07-1240 (LT-2-T01); AP-2026-10-06-1258/1310/1710
- ultima_acao: LT-2-T02 CONCLUÍDA — revalidação 4/4 PASSOU (importação idempotente nas 2 empresas, RLS 403/401, fixtures limpas, farol/painel intactos); fase/STATUS/changelog/estado atualizados
- proxima_acao: sem task ativa — próxima task exige novo pedido do champion; pendências humanas: validação do consultor (fase 1), rotação de credenciais, publicação em produção
- atualizado_em: 2026-10-07T13:05:00-03:00

## Recorte FAROL-1 (concluído) + LT-2

- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70) + gráficos donut e mapa de acompanhamento de volta (v0.0.77-80, aprovado 12:55)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado ("Testei e funcionou" 12:30)
- **LT-2-T02 ✅ CONCLUÍDA (v0.0.81-83):** sincronização das agendas ao logar — Layout dispara a importação por empresa autorizada (fire-and-forget, silencioso); disparo provado por rede; teste humano aprovado ("Testei e funcionou" 13:03)

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07) — REVERTIDA PARCIALMENTE pela FA-7 (12:36): o mapa volta ALÉM do semáforo.
- FA-7: regras do mapa = as aprovadas na FA-1 (nenhuma regra nova); registro do mês = fato OCORRIDO (data_fato <= hoje) — fato futuro é programada; painel de cobertura continua com a visão mensal detalhada.
- LT-2-T01: registro NUNCA depende do Google; falha → nao_sincronizada + erro + retry por botão; duração fixa 1h; quem sincroniza = criador + gestor/admin.
- LT-2-T02: login NUNCA depende do Google; disparo fire-and-forget e silencioso no primeiro carregamento da sessão (Layout); botão manual da /agenda continua; janela -7d/+14d inalterada.

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
