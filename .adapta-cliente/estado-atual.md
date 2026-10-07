# Estado atual — Adapta Cliente

- task_id: LT-2-T01 (registro de reunião criar evento no Google Calendar — escrita intranet→Google)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 10:37 + autorização 11:58 ("Pode implementar"); sinal em `06_notas/sinal-lt2-t01-escrita-google-calendar.md`
- etapa: aguardando_teste_humano (LT-2-T01 implementada e provada — Skip v0.0.71-73 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T11:58-03:00 — formulário: "LT-2-T01 — Pode implementar"; carga 2026 APROVADA ("Testei e está ok"); champion pediu passo a passo dos refresh tokens com escopo calendar.events (gate humano em andamento)
- teste_humano: LT-2-T01 PENDENTE (prova real da escrita depende dos refresh tokens com escopo calendar.events); CARGA-2026 APROVADA ("Testei e está ok" 11:58); FA-5 e FA-6 APROVADOS ("tudo ok" 10:51)
- verificacao_automatica: passou — Skip v0.0.72/73 QA ✓ (v0.0.71 pegou scoping JSVM: marcarErro top-level não acessível no callback — inline corrigido, AP-2026-10-07-1215). 15 provas: 401 sem auth; occurrence_id obrigatório; 404 inexistente; RLS 403 (consultora donahelp→ocorrência acuidar); permissão 403 (consultor não-criador); PROVA REAL de escrita: Google 403 insufficient authentication scopes (refresh tokens ainda readonly — caminho de falha provado, ocorrência INTACTA + flag nao_sincronizada + erro gravado); retry reproduz falha (nunca retry cego); importação acuidar/donahelp ok com filtro anti-duplicidade (pulados_origem_intranet); registro pela UI intacto (fixture criada e limpa — migration 0027, banco final 22 reais)
- aprendizado: capturado — AP-2026-10-07-1215 (QA Skip valida scoping JSVM); anteriores: AP-2026-10-06-1258 (FA-1); AP-2026-10-06-1310 (FA-2); AP-2026-10-06-1710 (FA-3/4); FA-5/FA-6 sem sinal
- ultima_acao: LT-2-T01 implementada — hook POST /backend/v1/agenda/sincronizar (RLS+permissão criador/gestor+idempotência+renovação on-demand+marker extendedProperties.origem=intranet+duração 1h), migration 0026 (google_event_id/google_sync_estado/google_sync_erro), importação pula eventos origem=intranet, status de sync no comprovante (NovaReuniao) e na fila (badge+botão retry)
- proxima_acao: (1) champion regenera os 2 refresh tokens com escopo calendar.events (passo a passo enviado); (2) prova real de escrita (evento criado na agenda + importação pula); (3) teste humano do champion
- atualizado_em: 2026-10-07T12:20:00-03:00

## Recorte FAROL-1 (concluído) + LT-2

- **FA-1..FA-6 ✅ CONCLUÍDAS:** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70)
- **LT-2-T01 (aguardando_teste_humano, v0.0.71-73):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07).
- LT-2-T01: registro NUNCA depende do Google; falha → nao_sincronizada + erro + retry por botão; duração fixa 1h; edição/cancelamento intranet→Google fora do primeiro recorte; quem sincroniza = criador + gestor/admin.

## Pendências restantes (fora de task)

- Refresh tokens com escopo calendar.events (Acuidar + Dona Help) — ação do champion (passo a passo enviado); desbloqueia a prova real da escrita
- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
- Sincronização GitHub dos commits da LT-2-T01 (224c8da, 1f684ed) — dono: assistente
