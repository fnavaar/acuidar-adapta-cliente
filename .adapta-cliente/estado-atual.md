# Estado atual — Adapta Cliente

- task_id: FA-12 (agenda conjunta Café com Franqueados/Day Fusion — registro sem unidade + presença por unidade)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 14:15 ("assim que for marcado café com franqueados ou day fusion, pode marcar como se fosse agenda conjunta de todos os franqueados"); análise em `06_notas/analise-fa12-agenda-conjunta.md`
- etapa: concluida (FA-12 implementada v0.0.97-101 e aprovada pelo champion "ok tudo certo" 2026-10-09 09:36)
- autorizacao_implementacao: confirmada — 2026-10-09T08:59-03:00 — "sim" (FA-12; FA-11 autorizada 2026-10-07T13:58 "Pode implementar")
- teste_humano: aprovado — 2026-10-09T09:36-03:00 — "ok tudo certo" (após correção do alcance por tipo na v0.0.101: Day Fusion = 2 empresas; Café com Franqueados = 1)
- verificacao_automatica: passou — Skip v0.0.97-101 QA ✓ (lint + build + teste + integrações de hook). Provas de código: NovaReuniao.tsx com `duasEmpresas = tipo === 'Day Fusion'`; hook ocorrencias_criar com idempotência incluindo empresa; Fila carrega unidades por `oc.empresa`; aviso da tela correto por tipo
- aprendizado: capturado — AP-2026-10-09-0940-escopo-por-tipo-reuniao-conjunta (alcance de agenda conjunta varia por tipo: Day Fusion = 2 empresas, Café = 1; validar alcance por tipo ANTES de implementar; idempotência de N registros por empresa inclui a empresa na chave). Anteriores: AP-2026-10-07-1418; AP-2026-10-07-1400; AP-2026-10-07-1355; AP-2026-10-07-1350; AP-2026-10-07-1325; AP-2026-10-07-1320; AP-2026-10-07-1300; AP-2026-10-07-1215 + AP-2026-10-07-1240; AP-2026-10-06-1258/1310/1710
- ultima_acao: FA-12 CONCLUÍDA — implementada (v0.0.97-101, QA ✓) e aprovada pelo champion ("ok tudo certo" 09:36). Registro de Café com Franqueados/Day Fusion vira agenda conjunta sem unidade; Day Fusion cria 1 ocorrência em CADA empresa autorizada; presença marcada na fila (só presentes contam na cobertura); sync Google com attendees; idempotência por empresa
- proxima_acao: sem task ativa — aguardar novo pedido do champion (pendências: validação do consultor, rotação de credenciais, produção)
- atualizado_em: 2026-10-09T09:42:00-03:00

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