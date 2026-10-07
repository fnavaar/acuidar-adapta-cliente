# Estado atual — Adapta Cliente

- task_id: FA-11 (painel de cobertura — filtro de busca por unidade, cidade e código)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 13:57 ("no painel de cobertura quero um filtro para encontrar melhor a unidade... um filtro com unidade, cidade"); análise em `06_notas/analise-fa11-filtro-painel.md`
- etapa: implementando (FA-11 — autorizado pelo champion "Pode implementar" 13:58)
- autorizacao_implementacao: confirmada — 2026-10-07T13:58-03:00 — "Pode implementar"
- teste_humano: pendente (FA-11 — buscar unidade por código, cidade e nome no painel de cobertura)
- verificacao_automatica: pendente (FA-11 — provas planejadas: busca por código/cidade/nome, case/acento insensível, contador de resultados, estado vazio, regressão do painel, tela no navegador)
- aprendizado: pendente (FA-11); anteriores capturados: AP-2026-10-07-1355; AP-2026-10-07-1350; AP-2026-10-07-1325; AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-11 implementando — campo de busca no Painel.tsx (codigo+nome+cidade, case/acento insensível, contador, estado vazio)
- proxima_acao: implementar FA-11 (campo de busca no Painel.tsx) e parar no teste humano
- atualizado_em: 2026-10-07T13:58:00-03:00

## Recorte FAROL-1 (concluído) + LT-2 + FA-8/9/10

- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70) + gráficos donut e mapa de acompanhamento de volta (v0.0.77-80, aprovado 12:55)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado ("Testei e funcionou" 12:30)
- **LT-2-T02 ✅ CONCLUÍDA (v0.0.81-83):** sincronização das agendas ao logar — Layout dispara a importação por empresa autorizada (fire-and-forget, silencioso); disparo provado por rede; teste humano aprovado ("Testei e funcionou" 13:03)
- **FA-8 ✅ CONCLUÍDA (v0.0.84-86):** tela Agenda (reuniões agendadas + feitas do dia) substitui "Importar da Agenda" — importação automática no login permanece; aprovada pelo champion ("Testei e funcionou" 13:19)
- **FA-9 ✅ CONCLUÍDA (v0.0.88):** sync automático com o Google após confirmação do registro — botão manual vira retry; aprovada pelo champion ("Testei e funcionou" 13:47)
- **FA-10 ✅ CONCLUÍDA (v0.0.89-91):** agenda como CALENDÁRIO mensal (grade com quadrados, etiquetas por estado, detalhe do dia) — substitui a lista por dia da FA-8; aprovada pelo champion ("testei e funcionou" 13:50)

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
