# Estado atual — Adapta Cliente

- task_id: LT-2-T01 (registro de reunião criar evento no Google Calendar — escrita intranet→Google)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 10:37 + autorização 11:58 ("Pode implementar"); sinal em `06_notas/sinal-lt2-t01-escrita-google-calendar.md`
- etapa: aguardando_teste_humano (LT-2-T01 implementada + PROVA REAL da escrita PASSOU — Skip v0.0.71-75 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T11:58-03:00 — formulário: "LT-2-T01 — Pode implementar"; carga 2026 APROVADA ("Testei e está ok"); refresh tokens calendar.events REGRAVADOS pelo champion (12:22 — gate humano resolvido)
- teste_humano: LT-2-T01 PENDENTE (prova real da escrita JÁ PASSOU — falta o teste do champion pela UI); CARGA-2026 APROVADA ("Testei e está ok" 11:58); FA-5 e FA-6 APROVADOS ("tudo ok" 10:51)
- verificacao_automatica: passou — Skip v0.0.74/75 QA ✓. PROVA REAL da escrita PASSOU com os refresh tokens calendar.events regravados pelo champion (12:22): acuidar → evento criado (cbc2t6fic06d8ohjf4aeb1am00), donahelp → evento criado (9mlofb51c6i5krhe3qmi928bh4); importação das 2 empresas PULOU os eventos com marker (pulados_origem_intranet=1 em cada — duplicidade fechada nos 2 sentidos); idempotência ok (retry → ja_sincronizada, sem 2º evento); ocorrências confirmadas + sincronizada. Bug pego pela prova real: data_fato é timestamp PocketBase ("2026-10-07 00:00:00.000Z") — data crua gerava datetime inválido e o Google respondia 400 Bad Request; corrigido v0.0.74 (slice(0,10) + validação regex). Fixtures da prova limpas (migration 0028; banco final 22 reais). Provas anteriores (v0.0.71-73): 401, RLS 403, permissão 403, caminho de falha com escopo readonly (403 insufficient authentication scopes, ocorrência intacta), registro pela UI intacto
- aprendizado: capturado — AP-2026-10-07-1215 (QA Skip valida scoping JSVM) + AP-2026-10-07-1240 (data_fato é timestamp PocketBase); anteriores: AP-2026-10-06-1258 (FA-1); AP-2026-10-06-1310 (FA-2); AP-2026-10-06-1710 (FA-3/4); FA-5/FA-6 sem sinal
- ultima_acao: PROVA REAL da escrita passou nas 2 empresas (eventos criados na agenda + importação pulou os markers + idempotência) — hook sincronizar completo; falta teste humano do champion pela UI
- proxima_acao: teste humano do champion pela UI (registrar reunião → botão sincronizar → conferir o evento na agenda da empresa; fila mostra status/botão retry)
- atualizado_em: 2026-10-07T12:45:00-03:00

## Recorte FAROL-1 (concluído) + LT-2

- **FA-1..FA-6 ✅ CONCLUÍDAS:** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70)
- **LT-2-T01 (aguardando_teste_humano, v0.0.71-75):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + PROVA REAL da escrita passou nas 2 empresas (v0.0.74 corrigiu o bug do timestamp de data_fato)

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07).
- LT-2-T01: registro NUNCA depende do Google; falha → nao_sincronizada + erro + retry por botão; duração fixa 1h; edição/cancelamento intranet→Google fora do primeiro recorte; quem sincroniza = criador + gestor/admin.

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
