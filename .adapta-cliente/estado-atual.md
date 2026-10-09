# Estado atual — Adapta Cliente

- task_id: FA-13 (e-mail automático ao franqueado: agendamento + relato)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-09 09:52 ("enviar email assim que registrar/agendar a reunião para o email de franquia da unidade e também quando o relato for cadastrado"); análise em `06_notas/analise-fa13-email-franqueado.md`
- etapa: aguardando_teste_humano (FA-13 implementada v0.0.102-104, QA ✓; provas automáticas passaram)
- autorizacao_implementacao: confirmada — 2026-10-09T09:58-03:00 — "sim" (FA-13)
- teste_humano: pendente (FA-13 implementada — aguardando teste do champion)
- verificacao_automatica: passou — Skip v0.0.102-104 QA ✓. Provas: 1) criação Consultoria unid. inexistente → sem_email (RN-1-19); 2) relato atualizado → sem_email; 3) PROVA REAL: e-mail "Reunião agendada — unidade 105" e "Relato da reunião — unidade 105" ENVIADOS (logs Skip: status sent, 2 emails); 4) idempotência relato (sem_mudanca, sem reenvio); 5) RN-1-05A bloqueia HTML/script; 6) 401 sem auth; 7) RLS 403 consultora donahelp→acuidar; 8) obrigatórios id/relato; 9) tipo Outro não envia; 10) agenda conjunta não envia; 11) 404 inexistente; 12) UI: botão Editar relato abre editor (donahelp, criador); fixtures limpas (0036/0037)
- aprendizado: capturado — AP-2026-10-09-0940 (FA-12: alcance por tipo; idempotência inclui empresa). Anteriores: AP-2026-10-07-1418; AP-2026-10-07-1400; AP-2026-10-07-1355; AP-2026-10-07-1350; AP-2026-10-07-1325; AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-13 IMPLEMENTADA e provada (v0.0.102-104): migration 0035 (campos email_*), hook ocorrencias_relato (edição + e-mail do relato), disparo de agendamento no criar, botão "Editar relato" na Fila; 2 e-mails REAIS enviados (agendamento + relato, unidade 105 donahelp) com logs de envio no Skip
- proxima_acao: aguardar teste humano do champion (registrar reunião na UI → conferir e-mail; editar relato → conferir e-mail)
- atualizado_em: 2026-10-09T13:15:00-03:00

## Recorte FAROL-1 (concluído) + LT-2 + FA-8..12

- **FA-11 ✅ CONCLUÍDA (v0.0.92-95, "FA-11 esta ok" 14:18):** filtro de busca por unidade, cidade e código no painel de cobertura — busca DENTRO do quadro de unidades (ajuste 14:08), case/acento insensível, contador "X de Y", estado vazio com Limpar busca; 100% frontend, backend intacto
- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol PECAF/PEDHE por empresa + pele NEXUS + carga 2026 (61 avaliações) + gráficos donut e mapa de acompanhamento
- **LT-2-T01 ✅ (v0.0.71-76):** escrita intranet→Google Calendar com marker anti-duplicidade; aprovada
- **LT-2-T02 ✅ (v0.0.81-83):** sincronização das agendas ao logar; aprovada
- **FA-8/9/10 ✅:** tela Agenda + sync automático na confirmação + calendário mensal; aprovadas
- **FA-12 ✅ CONCLUÍDA (v0.0.97-101, "ok tudo certo" 09:36):** agenda conjunta para Café com Franqueados/Day Fusion — registro sem unidade (1 ocorrência única com marcador), presença marcada pela consultora na fila (RSVP Google como sugestão), só presentes contam na cobertura, cancelamento com motivo. ALCANCE POR TIPO (decisão 09:34): Day Fusion = DUAS empresas (1 ocorrência em cada); Café com Franqueados = só a empresa selecionada. Sync Google com attendees por empresa; idempotência inclui empresa.

## FA-12 — decisões do champion

- **Pedido (14:15):** Café com Franqueados / Day Fusion → agenda conjunta de todos os franqueados; consultora NÃO marca unidade.
- **Decisões do champion:** "todas" = unidades ATIVAS no momento; cobertura só se a unidade COMPARECEU; sem recorrência — título padrão definido pela consultora; cancelamento exige motivo.
- **Presença (14:18):** consultora convida os e-mails das unidades no evento do Google; intranet lê o RSVP como sugestão pré-marcada; quem define quem compareceu é a consultora (lista com busca — reusa o filtro da FA-11); só presentes contam na cobertura.
- **Alcance por tipo (09:34, corrige 09:19):** Day Fusion = Acuidar + Dona Help (2 ocorrências + 2 eventos + presença por empresa); Café com Franqueados = só a empresa selecionada.

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)