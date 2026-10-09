# Estado atual — Adapta Cliente

- task_id: FA-14 (ata inteligente do Google Meet → rascunho validado pela consultora antes de virar relato)
- KILL SWITCH DE E-MAILS ATIVO (segurança 10:27): nenhum e-mail é enviado até EMAIL_ENVIOS_HABILITADO === 'true' no Secrets (ausente hoje) — provado em unidade real 105 (campos vazios; log de e-mails sem novos envios)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-09 09:52/10:20 ("o sistema deve puxar a ata que a IA do Google Meet gera e a consultora valida antes de salvar"; fonte = Ata inteligente/resumo IA/Gemini); análise em `06_notas/analise-fa14-ata-ia-relato.md`
- etapa: aguardando_teste_humano (FA-14 implementada v0.0.105-108, QA ✓; provas automáticas passaram)
- autorizacao_implementacao: confirmada — 2026-10-09T10:27-03:00 — "sim" (FA-14; com kill switch de e-mails)
- teste_humano: pendente (FA-14 implementada — aguardando teste do champion)
- verificacao_automatica: passou — v0.0.105-108 QA ✓. Kill switch e-mails PROVADO (Acompanhamento unidade real 105 pós-switch → email_* vazio; log Skip sem novos envios; relato idem). FA-14 provas: atas_puxar sem vínculo → falha controlada (não inventa); 401 sem auth; 404 inexistente; RLS 403 acuidar; não-elegível (conferencia) → erro de escopo; migration 0038 aplicada (campos ata_ia_*). Fixtures limpas (0039)
- aprendizado: capturado — AP-2026-10-09-0940 (FA-12: alcance por tipo; idempotência inclui empresa) + AP-2026-10-09-1018 (FA-13 pausada pelo champion no teste humano — não concluir por inferência)
- ultima_acao: FA-14 IMPLEMENTADA e provada (v0.0.105-108): kill switch de e-mails aplicado (EMAIL_ENVIOS_HABILITADO ausente = 0 envios; provado em unidade real 105) + hook POST /backend/v1/atas/puxar (Meet API smartNotes → doc da ata → rascunho validável; nunca inventa; RLS/permissão/idempotência) + migration 0038 (ata_ia_status/erro/doc_id/origem/relato_rascunho_ia) + bloco '📝 Ata da reunião (IA)' na Fila (Puxar → rascunho editável → validar/salvar)
- proxima_acao: aguardar teste humano do champion (regravar refresh tokens com escopos meet.readonly+drive.readonly → registrar reunião real com Meet IA → 'Puxar ata' → validar/salvar). E-mail continua DESLIGADO até autorização explícita
- atualizado_em: 2026-10-09T14:10:00-03:00

## FA-13 (pausada — não concluída)

- FA-13 implementada e provada (v0.0.102-104, QA ✓; 2 e-mails reais enviados: "Reunião agendada — unidade 105" e "Relato da reunião — unidade 105", status sent no log do Skip). **Pausada no teste humano por decisão do champion (10:17: "não quero testar agora").** Não concluída; retomável a qualquer momento.

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