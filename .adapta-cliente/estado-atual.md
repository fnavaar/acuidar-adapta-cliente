# Estado atual — Adapta Cliente

- task_id: FA-15 (notificação à consultora: ler resumo → confirmar → e-mail da unidade PRONTO sem enviar)
- KILL SWITCH DE E-MAILS ATIVO (segurança 10:27, reafirmado 11:04): nenhum e-mail é enviado até EMAIL_ENVIOS_HABILITADO === 'true' no Secrets (ausente hoje) — provado em unidade real 105 (campos vazios/pronto_para_envio; log de e-mails sem novos envios)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-09 11:04 ("notificação pra consultora ler o resumo e confirmar e enviar o email da unidade... mas não quero que nenhum email seja enviado"; só implementação); análise em `06_notas/analise-fa15-notificacao-consultora.md`
- etapa: aguardando_teste_humano (FA-15 implementada v0.0.109-110, QA ✓; provas automáticas passaram)
- autorizacao_implementacao: confirmada — 2026-10-09T11:07-03:00 — "Pode implementar FA-15"
- teste_humano: pendente (FA-15 implementada — aguardando teste do champion)
- verificacao_automatica: passou — v0.0.109-110 QA ✓. Provas: pendências 0 sem ata (hook); salvar relato → email_relato_estado + email_agenda_estado = 'pronto_para_envio' com motivo (kill switch); log Skip SEM novos e-mails (2 antigos apenas); 401 sem auth; fixtures limpas (0040); UI carregada sem badge com 0 pendências (correto)
- aprendizado: capturado — AP-2026-10-09-0940 + AP-2026-10-09-1018
- ultima_acao: FA-15 IMPLEMENTADA e provada (v0.0.109-110): hook GET /backend/v1/notificacoes/pendencias (RLS por empresa; conta ata disponível aguardando leitura), badge no item Fila do Layout (recarrega por navegação), marca '📝 ata pronta' no item pendente, salvar relato → ata vira 'aproveitada' (fecha pendência) + email_* = 'pronto_para_envio' (NUNCA envia — kill switch)
- proxima_acao: aguardar teste humano do champion (ata real disponível → badge na Fila → ler → salvar relato → badge some + 'pronto para envio'; NENHUM e-mail sai). Ativação de envios = champion cria EMAIL_ENVIOS_HABILITADO=true
- atualizado_em: 2026-10-09T11:30:00-03:00

## FA-14 (pausada — não concluída)

- FA-14 implementada e provada (v0.0.105-108, QA ✓; hook atas/puxar + bloco '📝 Ata da reunião (IA)' na Fila). **Pausada no teste humano por decisão do champion (10:17: 'não quero testar agora').** Gate da prova real: refresh tokens com escopos meet.readonly + drive.readonly (regravar nos mesmos secrets; passo a passo disponível).

## FA-13 (pausada — não concluída)

- FA-13 implementada e provada (v0.0.102-104, QA ✓; 2 e-mails reais enviados ANTES da instrução do champion — log Skip). **Pausada no teste humano por decisão do champion (10:17).** Com kill switch ativo: 0 envios; quando o champion criar EMAIL_ENVIOS_HABILITADO=true, o fluxo envia normalmente.

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