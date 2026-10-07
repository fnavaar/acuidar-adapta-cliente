# Estado atual — Adapta Cliente

- task_id: LT-2-T01 (registro de reunião criar evento no Google Calendar — escrita intranet→Google) — CONCLUÍDA
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 10:37 + autorização 11:58 ("Pode implementar"); sinal em `06_notas/sinal-lt2-t01-escrita-google-calendar.md`
- etapa: concluida (LT-2-T01 — teste humano aprovado pelo champion "Testei e funcionou" 12:30; revalidação 12/12 PASSOU — Skip v0.0.71-76 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T11:58-03:00 — formulário: "LT-2-T01 — Pode implementar"; carga 2026 APROVADA ("Testei e está ok"); refresh tokens calendar.events REGRAVADOS pelo champion (12:22 — gate humano resolvido)
- teste_humano: LT-2-T01 APROVADO ("Testei e funcionou" 12:30 — ocorrência do champion confirmada + sincronizada na agenda acuidar); CARGA-2026 APROVADA ("Testei e está ok" 11:58); FA-5 e FA-6 APROVADOS ("tudo ok" 10:51)
- verificacao_automatica: passou — Skip v0.0.76 QA ✓. Revalidação do fechamento 12/12 PASSOU: 401 sem auth; occurrence_id obrigatório; 404 inexistente; RLS 403 (consultora donahelp→ocorrência acuidar); permissão 403 (consultor não-criador); idempotência (retry na ocorrência do champion → ja_sincronizada, sem 2º evento); anti-duplicidade (importação acuidar pulou 2 eventos com marker, donahelp pulou 1); importação das 2 empresas ok; registro pela UI intacto (fixture de revalidação criada, sincronizada — evento tgu3ant7ars1j9lmbqm8tddf84 — e limpa via migration 0029; banco final 23 reais). Prova real da escrita (v0.0.74-75): eventos criados nas agendas das 2 empresas; bug do timestamp de data_fato corrigido (AP-2026-10-07-1240)
- aprendizado: capturado — AP-2026-10-07-1215 (QA Skip valida scoping JSVM) + AP-2026-10-07-1240 (data_fato é timestamp PocketBase); conclusão sem sinal novo (controle.md); anteriores: AP-2026-10-06-1258 (FA-1); AP-2026-10-06-1310 (FA-2); AP-2026-10-06-1710 (FA-3/4); FA-5/FA-6 sem sinal
- ultima_acao: LT-2-T01 CONCLUÍDA — revalidação do fechamento 12/12 PASSOU (401, RLS 403, permissão 403, idempotência na ocorrência do champion, anti-duplicidade 2+1 markers, importação 2 empresas, registro pela UI intacto, fixture limpa 0029); fase.md/STATUS/changelog/estado atualizados
- proxima_acao: sem task ativa — próxima task exige novo pedido do champion; pendências humanas: validação do consultor (fase 1), rotação de credenciais, publicação em produção
- atualizado_em: 2026-10-07T12:55:00-03:00

## Recorte FAROL-1 (concluído) + LT-2

- **FA-1..FA-6 ✅ CONCLUÍDAS:** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07).
- LT-2-T01: registro NUNCA depende do Google; falha → nao_sincronizada + erro + retry por botão; duração fixa 1h; edição/cancelamento intranet→Google fora do primeiro recorte; quem sincroniza = criador + gestor/admin.

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
