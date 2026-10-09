# Estado atual — Adapta Cliente

- task_id: FA-17 (ata de Google Doc — anexo do evento "Criar ata da reunião" OU link manual; contas de teste sem plano Gemini)
- KILL SWITCH DE E-MAILS ATIVO (segurança 10:27, reafirmado 11:04 e 11:23): NENHUM e-mail sai. Ferramentas FA-13/14/15 registradas como DESEJADAS e PRONTAS; envio só com EMAIL_ENVIOS_HABILITADO=true (secret ausente; só o champion cria)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: champion escolheu "opção a" (12:13) e mostrou o caso REAL (12:30): recurso nativo "Criar ata da reunião" do Calendar anexa Google Doc NATIVO ao evento (gratuito — não é a IA paga do Meet). Análise em `06_notas/analise-fa17-ata-doc-manual.md`; sinal em `06_notas/sinal-fa14-fonte-alternativa-ata.md`
- etapa: aguardando_teste_humano (FA-17 implementada v0.0.113-115, QA ✓)
- autorizacao_implementacao: confirmada — 2026-10-09T12:13-03:00 — "opção a" (FA-17)
- teste_humano: pendente (FA-17 — aguardando champion; a prova real exige os gates abaixo)
- verificacao_automatica: passou — v0.0.113-115 QA ✓. Hook puxar-doc: 401 sem auth; 404 inexistente; RLS hook (não-criador 403); link inválido → erro de formato; evento inexistente na agenda da credencial → falha controlada; Doc real do champion não compartilhado → falha 403 controlada com mensagem do que fazer. Fixture limpa (0042 → 404). FA-14 intacta (401). UI provada no navegador (botão "📎 Puxar ata" + campo opcional de link + "Puxar do link")
- aprendizado: capturado — AP-2026-10-09-0940 + AP-2026-10-09-1200
- ultima_acao: FA-17 IMPLEMENTADA (v0.0.113-115): hook POST /backend/v1/atas/puxar-doc com 2 modos — AUTOMÁTICO (evento da ocorrência → attachments[] com attachments=true → exporta o Doc nativo anexado, fonte='doc_anexo') e MANUAL (link colado → extrai ID /document/d/ID, fonte='doc_manual'); migration 0041 (ata_ia_fonte: meet_ia|doc_anexo|doc_manual); atas_puxar (IA) seta meet_ia; botão único "📎 Puxar ata" + campo opcional de link na Fila (v0.0.114). Re-puxar SUBSTITUI rascunho + texto-fonte. NUNCA inventa — falhas controladas com mensagem orientando o gate faltante
- proxima_acao: aguardar teste humano do champion. GATES da prova real: (1) regravar os 2 refresh tokens com escopos drive.readonly + meet.readonly (mesmo processo da LT-1-T09 — um regrave destrava FA-17 e FA-14); (2) o evento/Doc precisa estar visível pela CONTA DA CREDENCIAL da empresa (acuidar = auxiliar1.ti.acuidar@gmail.com; donahelp = acuidar.automacao@gmail.com) — convidar a conta para a reunião OU compartilhar o Doc com ela
- atualizado_em: 2026-10-09T12:40:00-03:00

## FA-16 — CONCLUÍDA (2026-10-09, "tudo ok" 11:59)

- Dashboard da unidade /unidade/:empresa/:codigo (v0.0.111-112): 100% frontend agregando hooks existentes; clique no nome no Farol/Painel; AP-2026-10-09-1200 (linha navegável + prova por URL direta).

## FA-13/14/15 (pausadas — ferramentas desejadas, não concluídas)

- FA-13 (e-mails, v0.0.102-104), FA-14 (ata IA do Meet, v0.0.105-108) e FA-15 (notificação consultora, v0.0.109-110): implementadas e provadas, PAUSADAS no teste humano por decisão do champion. Ativação de envios = champion cria EMAIL_ENVIOS_HABILITADO=true.

## Recorte FAROL-1 + LT-2 + FA-8..12 (concluídos)

- **FA-1..FA-12 ✅:** farol PECAF/PEDHE + pele NEXUS + carga 2026 + gráficos + agenda (calendário, sync, conjunta) + filtro no painel
- **LT-2-T01/T02 ✅:** escrita intranet→Google Calendar + sincronização ao logar

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta)