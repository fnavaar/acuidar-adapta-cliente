# Estado atual — Adapta Cliente

- task_id: FA-12 (agenda conjunta Café com Franqueados/Day Fusion — registro sem unidade + presença por unidade)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 14:15 ("assim que for marcado café com franqueados ou day fusion, pode marcar como se fosse agenda conjunta de todos os franqueados"); análise em `06_notas/analise-fa12-agenda-conjunta.md`
- etapa: aguardando_autorizacao (FA-12 analisada — análise formal entregue; FA-11 concluída 14:18)
- autorizacao_implementacao: ausente (pendente para FA-12 — FA-11 teve "Pode implementar" 13:58 e foi concluída)
- teste_humano: aprovado (FA-11 — 2026-10-07T14:18 "FA-11 esta ok"); pendente para FA-12 (ainda não implementada)
- verificacao_automatica: pendente (FA-12 — 8 provas planejadas na análise; nenhuma executada ainda)
- aprendizado: capturado — AP-2026-10-07-1418-filtro-no-card (pedido de ajuste visual chega como screenshot — prova de UI compara posição no layout, não só presença). Anteriores: AP-2026-10-07-1400; AP-2026-10-07-1355; AP-2026-10-07-1350; AP-2026-10-07-1325; AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-12 analisada (código real lido: NovaReuniao, ocorrencias_criar, agenda_sincronizar, google_agenda_importar, painel_cobertura) — desenho em 7 pontos + 4 decisões embutidas + migration 0034 + 8 provas planejadas; relatório entregue ao champion
- proxima_acao: aguardar autorização do champion para implementar FA-12 ("Pode implementar")
- atualizado_em: 2026-10-09T08:55:00-03:00

## Recorte FAROL-1 (concluído) + LT-2 + FA-8..11

- **FA-11 ✅ CONCLUÍDA (v0.0.92-95, "FA-11 esta ok" 14:18):** filtro de busca por unidade, cidade e código no painel de cobertura — busca DENTRO do quadro de unidades (ajuste 14:08), case/acento insensível, contador "X de Y", estado vazio com Limpar busca; 100% frontend, backend intacto
- **FA-1..FA-7 ✅ CONCLUÍDAS (FAROL-1 completo):** farol PECAF/PEDHE por empresa + pele NEXUS + carga 2026 (61 avaliações) + gráficos donut e mapa de acompanhamento
- **LT-2-T01 ✅ (v0.0.71-76):** escrita intranet→Google Calendar com marker anti-duplicidade; aprovada
- **LT-2-T02 ✅ (v0.0.81-83):** sincronização das agendas ao logar; aprovada
- **FA-8/9/10 ✅:** tela Agenda + sync automático na confirmação + calendário mensal; aprovadas

## FA-12 — análise (aguardando_autorizacao)

- **Pedido (14:15):** Café com Franqueados / Day Fusion → agenda conjunta de todos os franqueados; consultora NÃO marca unidade.
- **Decisões do champion:** "todas" = unidades ATIVAS no momento; cobertura só se a unidade COMPARECEU; sem recorrência — título padrão definido pela consultora; cancelamento exige motivo.
- **Presença (14:18):** consultora convida os e-mails das unidades no evento do Google; intranet lê o RSVP como sugestão pré-marcada; quem define quem compareceu é a consultora (lista com busca — reusa o filtro da FA-11); só presentes contam na cobertura.
- **Desenho (7 pontos):** 1) formulário sem unidade para os 2 tipos + título pré-preenchido editável; 2) 1 ocorrência única com marcador `agenda_conjunta` (não 172) + campos unidades_presentes/rsvp_sugestoes; 3) evento Google com attendees = e-mails das unidades ativas (Portal tem email por unidade); 4) hook RSVP (sugestão, nunca presença — Google não tem presença real); 5) tela de presença com busca; 6) cobertura: presentes → confirmada, ausentes → reuniao_pendente, sem presença → nada conta (RN-1-19); 7) cancelamento único + motivo obrigatório (RN-1-08).
- **Decisões embutidas (champion pode vetar):** D1 presença EDITA a ocorrência (botão na fila); D2 RSVP sob demanda (botão); D3 presente conta como confirmada; D4 checkbox multiunidade atual permanece para os demais tipos.
- **Migration 0034:** campos agenda_conjunta (bool), unidades_presentes (json), rsvp_sugestoes (json) — sem dados existentes afetados.
- **8 provas planejadas:** registro único + idempotência; sync com attendees + marker; importação pula marker; RSVP mapeia email→código; presença com RLS/permissão; cobertura por presença; regressão geral.

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano
- Senha do admin-teste mudou (TesteA!2026x → 400) — regravar ou confirmar se foi intencional
