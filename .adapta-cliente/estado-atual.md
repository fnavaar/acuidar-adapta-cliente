# Estado atual — Adapta Cliente

- task_id: nenhuma (FA-11 CONCLUÍDA 2026-10-07 14:18 — "FA-11 esta ok" do champion)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 13:57 ("no painel de cobertura quero um filtro para encontrar melhor a unidade... um filtro com unidade, cidade") + ajuste 14:08 ("coloca o filtro dentro do quadro de unidades"); análise em `06_notas/analise-fa11-filtro-painel.md`
- etapa: concluida (FA-11 — filtro de busca no painel de cobertura)
- autorizacao_implementacao: confirmada — 2026-10-07T13:58-03:00 — "Pode implementar"
- teste_humano: aprovado — 2026-10-07T14:18-03:00 — "FA-11 esta ok"
- verificacao_automatica: passou — Skip v0.0.95 QA ✓. Provas na tela (navegador): campo "Buscar unidade, cidade ou código…" DENTRO do quadro de unidades (cabeçalho do card, à direita do título/resumo — pedido do champion 14:08); busca "joao pessoa" (sem acento) → só a unidade 2 Acuidar João Pessoa; busca "2" → todas as unidades com 2 no código; backend INTACTO (hook painel/cobertura responde com 172 unidades; RLS 403/401 provados na FA-8); contagens/totais do mês NÃO mudam (a busca filtra só a lista). Revalidação do fechamento: bundle do preview v0.0.95 contém o filtro (prova por curl), análise documentada, RLS/hook intactos
- aprendizado: capturado (AP-2026-10-07-1418-filtro-no-card.md); anteriores capturados: AP-2026-10-07-1400; AP-2026-10-07-1355; AP-2026-10-07-1350; AP-2026-10-07-1325; AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-11 CONCLUÍDA — revalidação do zero (bundle v0.0.95 contém o filtro; análise documentada; RLS/hook intactos), fase/STATUS/changelog/controle atualizados
- proxima_acao: FA-12 (agenda conjunta Café com Franqueados/Day Fusion) — análise formal pendente do pedido do champion; sinal em 06_notas/sinal-fa12-agenda-conjunta.md; decisão pendente: fonte da presença (a) manual na intranet ou (b) RSVP Google como sugestão pré-marcada
- atualizado_em: 2026-10-07T14:18:00-03:00

## Recorte FAROL-1 (concluído) + LT-2 + FA-8/9/10/11

- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol unicamente PECAF/PEDHE por empresa (v0.0.68-69) + pele NEXUS (v0.0.67) + carga 2026 (61 avaliações, farol acendeu — verde 32 acuidar / 9 donahelp, v0.0.70) + gráficos donut e mapa de acompanhamento de volta (v0.0.77-80, aprovado 12:55)
- **LT-2-T01 ✅ CONCLUÍDA (v0.0.71-76):** escrita intranet→Google Calendar — hook sincronizar + migration 0026 + filtro anti-duplicidade na importação + status/botão nas telas + prova real nas 2 empresas + teste humano aprovado ("Testei e funcionou" 12:30)
- **LT-2-T02 ✅ CONCLUÍDA (v0.0.81-83):** sincronização das agendas ao logar — Layout dispara a importação por empresa autorizada (fire-and-forget, silencioso); disparo provado por rede; teste humano aprovado ("Testei e funcionou" 13:03)
- **FA-8 ✅ CONCLUÍDA (v0.0.84-86):** tela Agenda (reuniões agendadas + feitas do dia) substitui "Importar da Agenda" — importação automática no login permanece; aprovada pelo champion ("Testei e funcionou" 13:19)
- **FA-9 ✅ CONCLUÍDA (v0.0.88):** sync automático com o Google após confirmação do registro — botão manual vira retry; aprovada pelo champion ("Testei e funcionou" 13:47)
- **FA-10 ✅ CONCLUÍDA (v0.0.89-91):** agenda como CALENDÁRIO mensal (grade com quadrados, etiquetas por estado, detalhe do dia) — substitui a lista por dia da FA-8; aprovada pelo champion ("testei e funcionou" 13:50)
- **FA-11 ✅ CONCLUÍDA (v0.0.92-95):** filtro de busca por unidade, cidade e código no painel de cobertura — busca DENTRO do quadro de unidades (ajuste 14:08), case/acento insensível, contador "X de Y", estado vazio com Limpar busca; 100% frontend, backend intacto; aprovada pelo champion ("FA-11 esta ok" 14:18)

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
- FA-11: busca ÚNICA casa unidade + cidade + código (decisão embutida aprovada por silêncio do champion); a cidade JÁ existia na coluna Unidade; contagens/totais do mês NÃO mudam (a busca filtra só a lista); busca fica DENTRO do quadro de unidades (pedido 14:08).

## FA-12 — agenda conjunta (sinal registrado, análise pendente)

- **Pedido (14:15):** Café com Franqueados / Day Fusion → registro como agenda conjunta de todos os franqueados; consultora NÃO marca unidade específica.
- **Decisões do champion (14:15):** "todas" = unidades ATIVAS da empresa no momento do registro; cobertura só se a unidade COMPARECEU ("se a unidade compareceu a reunião, então pode contar sim"); sem recorrência — título padrão organizado definido pela consultora; cancelamento exige cadastro do motivo.
- **Presença (decisão 14:18):** consultora convida os e-mails das unidades no evento do Google (cadastro do Portal tem e-mail por unidade); a intranet lê o RSVP como sugestão pré-marcada; quem define quem compareceu é a consultora, marcando a presença na intranet (lista com busca — reusa o filtro da FA-11); só as marcadas como presentes contam na cobertura.
- **Análise técnica (sinal):** Google Calendar NÃO tem presença real — só RSVP de convite (aceitou ≠ compareceu); desenho proposto: 1 ocorrência única com marcador agenda_conjunta (não 172 registros); evento único no Google com extendedProperties para a importação não duplicar; cancelamento = cancela o registro único + motivo obrigatório (RN-1-08).
- **Estado:** sinal em 06_notas/sinal-fa12-agenda-conjunta.md (commit 7b938ee); análise formal + implementação após fechamento da FA-11 (uma task por vez).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
- Senha do admin-teste mudou (TesteA!2026x → 400) — regravar ou confirmar se foi intencional
