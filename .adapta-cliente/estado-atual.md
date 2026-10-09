# Estado atual — Adapta Cliente

- task_id: FA-16 (dashboard da unidade: clicar no nome → página com indicadores de monitoramento)
- KILL SWITCH DE E-MAILS ATIVO (segurança 10:27, reafirmado 11:04 e 11:23): NENHUM e-mail sai — champion reafirmou 'não envie nenhum email, quero somente que você fique sabendo que quero essa ferramenta'. Ferramentas FA-13/14/15 registradas como DESEJADAS e PRONTAS; envio só com EMAIL_ENVIOS_HABILITADO=true (secret ausente; só o champion cria)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-09 11:25 ("clicar no nome da unidade vai para uma página de dashboard da unidade com indicadores pra o sistema monitorar"); análise em `06_notas/analise-fa16-dashboard-unidade.md`
- etapa: concluida (FA-16 aprovada pelo champion "tudo ok" 2026-10-09 11:59)
- autorizacao_implementacao: confirmada — 2026-10-09T11:29-03:00 — "sim" (FA-16)
- teste_humano: aprovado — 2026-10-09T11:59-03:00 — "tudo ok" (FA-16)
- verificacao_automatica: passou — v0.0.111-112 QA ✓. Dashboard /unidade/donahelp/101 provado no navegador com dados reais da unidade 101 (semáforo verde ranqueada 44pts, situação do mês em dia 2 registros, avaliação jun-jul com faturamento/contratos, ocorrência real listada, botão abrir fila). Links no nome no Farol e no Painel + linha do Painel navegável (build ok). Navegação por clique sintético NÃO confirmada no ambiente (sidebar navega; links da tabela não responderam ao clique sintético — provável artefato da automação; teste humano confirma). RLS herdada dos hooks. Sem fórmula nova
- aprendizado: capturado — AP-2026-10-09-0940 + AP-2026-10-09-1200 (clique sintético em link de tabela → linha navegável + prova por URL direta)
- ultima_acao: FA-16 CONCLUÍDA (revalidação pós-aprovação: hooks farol/painel/ocorrências intactos). FA-16 IMPLEMENTADA (v0.0.111-112): página DashboardUnidade.tsx (4 cards indicadores + avaliação + timeline de ocorrências + link fila), rota /unidade/:empresa/:codigo, clique no nome no Farol (Link com stopPropagation — linha segue abrindo dialog) e no Painel (Link no nome + linha navegável com navigate; 'ver na fila' com stopPropagation). 100% frontend, hooks existentes, sem fórmula nova
- proxima_acao: sem task ativa — próximo trabalho exige novo pedido do champion (regra: uma task por vez)
- atualizado_em: 2026-10-09T12:00:00-03:00

## FA-13/14/15 (pausadas — ferramentas desejadas, não concluídas)

- FA-13 (e-mails ao franqueado, v0.0.102-104) e FA-14 (ata IA do Meet, v0.0.105-108) e FA-15 (notificação consultora + pronto_para_envio, v0.0.109-110): implementadas e provadas, PAUSADAS no teste humano por decisão do champion. Ativação de envios = champion cria EMAIL_ENVIOS_HABILITADO=true. Gate da prova real da FA-14: refresh tokens com escopos meet.readonly + drive.readonly.

## Recorte FAROL-1 (concluído) + LT-2 + FA-8..12

- **FA-1..FA-12 ✅ CONCLUÍDAS:** farol PECAF/PEDHE por empresa + pele NEXUS + carga 2026 + gráficos + agenda (calendário, sync automático, agenda conjunta) + filtro no painel
- **LT-2-T01/T02 ✅:** escrita intranet→Google Calendar com marker anti-duplicidade + sincronização das agendas ao logar; aprovadas

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)