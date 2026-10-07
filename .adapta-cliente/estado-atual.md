# Estado atual — Adapta Cliente

- task_id: LT-2-T02 (sincronização das agendas ao logar)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 12:53 ("sincronização das agendas toda vez que a pessoa logar"); sinal em `06_notas/sinal-lt2-t02-sync-agenda-login.md` + análise em `06_notas/analise-lt2-t02-sync-agenda-login.md`
- etapa: aguardando_teste_humano (LT-2-T02 implementada e provada — Skip v0.0.81-83 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T12:55-03:00 — "sim" (LT-2-T02, após o relatório)
- teste_humano: FA-7 APROVADO ("a FA-7 funcionou" 12:55); LT-2-T01 aprovado 12:30; CARGA-2026 aprovada 11:58; FA-5/FA-6 aprovados 10:51
- verificacao_automatica: passou — Skip v0.0.81-83 QA ✓. Provas: DISPARO provado por evidência de rede no navegador (performance.getEntriesByType: admin → 2 chamadas agenda/importar — acuidar+donahelp; consultora donahelp → 1 chamada — RLS no disparo ✓); idempotência (2 logins seguidos → contagem google_calendar inalterada 8 donahelp / 13 total); RLS do hook (consultora → acuidar 403); sem auth 401; login NUNCA bloqueado (consultora chegou na página inicial); registro pela UI intacto (fixture de revalidação criada e limpa — migration 0031; banco final 26 reais). Bug pego pela prova: o fetch fire-and-forget no Login era CANCELADO pelo redirect window.location.href (navegação mata requests pendentes) — movido para o Layout (página já carregada), v0.0.82
- aprendizado: capturado — AP-2026-10-07-1300 (FA-7: fato futuro é programada); anteriores: AP-2026-10-07-1215 + AP-2026-10-07-1240 (LT-2-T01); AP-2026-10-06-1258/1310/1710; FA-5/FA-6 sem sinal
- ultima_acao: LT-2-T02 implementada — Layout dispara a importação das agendas das empresas autorizadas no primeiro carregamento da sessão (fire-and-forget, silencioso); botão manual da /agenda continua
- proxima_acao: teste humano do champion (logar → entrar na tela Agenda → conferir que a importação aconteceu sem clicar em nada)
- atualizado_em: 2026-10-07T13:20:00-03:00

## Recorte FAROL-1 (concluído) + LT-2

- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70) + gráficos donut e mapa de acompanhamento de volta (v0.0.77-80, aprovado 12:55)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado ("Testei e funcionou" 12:30)
- **LT-2-T02 (aguardando_teste_humano, v0.0.81-83):** sincronização das agendas ao logar — Layout dispara a importação por empresa autorizada (fire-and-forget, silencioso)

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
