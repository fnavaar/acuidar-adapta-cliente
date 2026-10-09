# Estado atual — Adapta Cliente

- task_id: FA-18 (fluxo completo de atas: puxar automático → aprovar → e-mail com ata)
- KILL SWITCH DE E-MAILS ATIVO (segurança 10:27, reafirmado 11:04 e 11:23): NENHUM e-mail sai. Ferramentas FA-13/14/15 registradas como DESEJADAS e PRONTAS; envio só com EMAIL_ENVIOS_HABILITADO=true (secret ausente; só o champion cria)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: visão do champion (13:56) — sistema puxa as atas DIRETO das reuniões cadastradas no Google Calendar; consultora só APROVA o conteúdo; aprovado → e-mail ao franqueado COM a ata. Análise em `06_notas/analise-fa18-fluxo-atas.md`
- etapa: aguardando_autorizacao (FA-18 analisada — nada implementado)
- autorizacao_implementacao: ausente (FA-18 — aguardando "Pode implementar")
- teste_humano: pendente (FA-18 — não implementada; FA-17 implementada e provada, prova real pendente do gate do Doc)
- verificacao_automatica: pendente (FA-18 — provas planejadas na análise; nenhuma executada)
- aprendizado: capturado — AP-2026-10-09-0940 + AP-2026-10-09-1200
- ultima_acao: FA-18 ANALISADA (06_notas/analise-fa18-fluxo-atas.md): 3 pontos — (1) puxar automático em lote (na importação das agendas + botão "Puxar todas" na Agenda), (2) botão "✅ Aprovar ata e salvar relato" na Fila (rascunho aprovado vira o relato; edição opcional antes; reaproveita hook relato), (3) e-mail do relato COM o texto da ata no corpo (anexo de arquivo só se o Mailer suportar — provado antes). Decisões embutidas D1-D5 (2 pontos de entrada; enviado não reenvia; elegibilidade idêntica). Kill switch intacto
- proxima_acao: aguardar autorização do champion para implementar FA-18 ("Pode implementar")
- atualizado_em: 2026-10-09T13:58:00-03:00

## FA-17 — implementada e provada (prova real pendente do gate do Doc)

- Hook atas/puxar-doc 2 modos (doc_anexo via attachments[] + doc_manual por link) + migration 0041 + botão na Fila (v0.0.113-118). Prova real executada: drive.readonly FUNCIONANDO; cadeia automática ok até o fim (fallback events.list). Falta só o gesto do champion: Doc anexado ao evento na agenda da credencial OU compartilhar o Doc com a credencial.

## FA-13/14/15 (pausadas — ferramentas desejadas, não concluídas)

- FA-13 (e-mails, v0.0.102-104), FA-14 (ata IA do Meet, v0.0.105-108) e FA-15 (notificação consultora, v0.0.109-110): implementadas e provadas, PAUSADAS no teste humano por decisão do champion. Ativação de envios = champion cria EMAIL_ENVIOS_HABILITADO=true.

## Recorte FAROL-1 + LT-2 + FA-8..16 (concluídos)

- **FA-1..FA-12 ✅ + FA-16 ✅:** farol PECAF/PEDHE + pele NEXUS + carga 2026 + gráficos + agenda (calendário, sync, conjunta) + filtro no painel + dashboard da unidade
- **LT-2-T01/T02 ✅:** escrita intranet→Google Calendar + sincronização ao logar

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta)