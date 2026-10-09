# Estado atual — Adapta Cliente

- task_id: FA-17 (ata de Google Doc manual — fonte alternativa da FA-14; contas de teste sem plano Gemini)
- KILL SWITCH DE E-MAILS ATIVO (segurança 10:27, reafirmado 11:04 e 11:23): NENHUM e-mail sai. Ferramentas FA-13/14/15 registradas como DESEJADAS e PRONTAS; envio só com EMAIL_ENVIOS_HABILITADO=true (secret ausente; só o champion cria)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: champion escolheu "opção A" (12:13) — contas de teste sem plano Gemini (smartNotes indisponível); puxar Google Doc com texto da ata como rascunho. Análise em `06_notas/analise-fa17-ata-doc-manual.md`; sinal em `06_notas/sinal-fa14-fonte-alternativa-ata.md`
- etapa: aguardando_autorizacao (FA-17 analisada — nada implementado)
- autorizacao_implementacao: ausente (FA-17 — aguardando "Pode implementar")
- teste_humano: pendente (FA-17 — não implementada; FA-16 aprovada "tudo ok" 11:59)
- verificacao_automatica: pendente (FA-17 — provas planejadas na análise; nenhuma executada)
- aprendizado: capturado — AP-2026-10-09-0940 + AP-2026-10-09-1200 (FA-16)
- ultima_acao: FA-17 ANALISADA (06_notas/analise-fa17-ata-doc-manual.md): modo doc_manual — migration 0041 (ata_ia_fonte), hook /backend/v1/atas/puxar-doc (auth 401 → RLS 403 → criador/gestor-admin → elegibilidade → extrai ID do link /document/d/ID → renova token → Drive export text/plain → grava status=disponivel, fonte=doc_manual, doc_id, origem (texto-fonte server-only), relato_rascunho_ia; NUNCA inventa — link inválido/doc sem texto/sem permissão = falha controlada); atas_puxar (IA) seta ata_ia_fonte=meet_ia; botão "Puxar do Doc" no bloco de ata da Fila. Re-puxar SUBSTITUI o rascunho. GATE da prova real: refresh tokens com escopo drive.readonly (+meet.readonly destrava também a FA-14 original)
- proxima_acao: aguardar autorização do champion para implementar FA-17 ("Pode implementar")
- atualizado_em: 2026-10-09T12:15:00-03:00

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