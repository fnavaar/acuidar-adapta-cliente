# Estado atual — Adapta Cliente

- task_id: FA-9 (sincronização AUTOMÁTICA com o Google Calendar após registrar reunião)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 13:30 ("quero que assim que a reunião for agendada ele já sincronize com o google agenda automaticamente"); análise em `06_notas/analise-fa9-sync-automatico.md`
- etapa: aguardando_teste_humano (FA-9 implementada — Skip v0.0.88 QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-07T13:35-03:00 — "Pode implementar"
- teste_humano: pendente (FA-9 — registrar reunião pela UI e o evento aparecer no Google Calendar SEM clicar em sincronizar)
- verificacao_automatica: parcial — Skip v0.0.88 QA ✓ (setup/static/build/test ok). Implementação provada por código: disparo automático após confirmação (useRef 1× por confirmação), botão vira retry em nao_sincronizada, exceção não sincroniza (só ramo confirmado). Prova da UI no ambiente do assistente INCOMPLETA (senha do admin-teste mudou — TesteA!2026x falhou 400 "Failed to authenticate"; prova seguiu com a consultora donahelp mas o formulário não chegou a submeter). Falta a prova real do sync automático (registro → evento no Google sem clique) — fica para o teste humano do champion, que também é o gate. Idempotência/RLS/falha já provados na LT-2-T01 (hook inalterado).
- aprendizado: pendente (FA-9); anteriores capturados: AP-2026-10-07-1325; AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-9 implementada (v0.0.88) — NovaReuniao dispara agenda/sincronizar AUTOMATICAMENTE após confirmação (useRef 1×), comprovante mostra "Sincronizando…" → resultado, botão manual vira "Tentar sincronizar de novo" na falha; hook inalterado (idempotência/RLS/falha já provados na LT-2-T01)
- proxima_acao: teste humano do champion (registrar reunião pela UI e conferir o evento no Google Calendar SEM clicar em sincronizar; comprovante mostra "Evento criado no Google Calendar da empresa")
- atualizado_em: 2026-10-07T13:45:00-03:00

## Recorte FAROL-1 (concluído) + LT-2 + FA-8

- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70) + gráficos donut e mapa de acompanhamento de volta (v0.0.77-80, aprovado 12:55)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado ("Testei e funcionou" 12:30)
- **LT-2-T02 ✅ CONCLUÍDA (v0.0.81-83):** sincronização das agendas ao logar — Layout dispara a importação por empresa autorizada (fire-and-forget, silencioso); disparo provado por rede; teste humano aprovado ("Testei e funcionou" 13:03)
- **FA-8 ✅ CONCLUÍDA (v0.0.84-86):** tela Agenda (reuniões agendadas + feitas do dia) substitui "Importar da Agenda" — importação automática no login permanece; aprovada pelo champion ("Testei e funcionou" 13:19)
- **FA-9 (aguardando_teste_humano, v0.0.88):** sync automático com o Google após confirmação do registro — botão manual vira retry

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07) — REVERTIDA PARCIALMENTE pela FA-7 (12:36): o mapa volta ALÉM do semáforo.
- FA-7: regras do mapa = as aprovadas na FA-1 (nenhuma regra nova); registro do mês = fato OCORRIDO (data_fato <= hoje) — fato futuro é programada; painel de cobertura continua com a visão mensal detalhada.
- LT-2-T01: registro NUNCA depende do Google; falha → nao_sincronizada + erro + retry por botão; duração fixa 1h; quem sincroniza = criador + gestor/admin.
- LT-2-T02: login NUNCA depende do Google; disparo fire-and-forget e silencioso no primeiro carregamento da sessão (Layout); janela -7d/+14d inalterada.
- FA-8: tela de importação SAIU (a importação é automática no login — LT-2-T02); a tela Agenda mostra reuniões agendadas (futuras) e feitas (passadas) do dia, com quem registrou; consultora vê só a sua empresa (RLS); reuniões canceladas nunca são excluídas (RN-1-08).
- FA-9: sync automático SÓ em ocorrência CONFIRMADA (exceção aguardando aprovação NÃO sincroniza); botão manual permanece como retry/fallback; registro NUNCA depende do Google (garantia LT-2-T01 preservada).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
