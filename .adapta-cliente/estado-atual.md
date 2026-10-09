# Estado atual — Adapta Cliente

- task_id: FA-13 (e-mail automático ao franqueado: agendamento + relato)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-09 09:52 ("enviar email assim que registrar/agendar a reunião para o email de franquia da unidade e também quando o relato for cadastrado"); análise em `06_notas/analise-fa13-email-franqueado.md`
- etapa: aguardando_autorizacao (FA-13 analisada — análise formal entregue; FA-12 concluída 09:36)
- autorizacao_implementacao: ausente (pendente para FA-13)
- teste_humano: pendente (FA-13 ainda não implementada)
- verificacao_automatica: pendente (FA-13 — provas planejadas na análise; nenhuma executada)
- aprendizado: capturado — AP-2026-10-09-0940 (FA-12: alcance por tipo; idempotência inclui empresa). Anteriores: AP-2026-10-07-1418; AP-2026-10-07-1400; AP-2026-10-07-1355; AP-2026-10-07-1350; AP-2026-10-07-1325; AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-13 analisada (decisões do champion 09:52 + código real lido: unidades_proxy, ocorrencias_criar, agenda_sincronizar, Fila) — desenho em 5 pontos + 5 decisões embutidas + migration 0036; relatório entregue ao champion
- proxima_acao: aguardar autorização do champion para implementar FA-13 ("Pode implementar")
- atualizado_em: 2026-10-09T09:58:00-03:00

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