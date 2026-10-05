# Estado atual — Adapta Cliente

- task_id: LT-1-T07 (leva técnica — conector do Google Agenda: importação de reuniões elegíveis)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: mapa de fontes F1-T08 §5 + regras F1-T02 + SPEC-1-001 (RN-1-06 a RN-1-09) + SPEC-1-003 + RLS por empresa (LT-1-T06)
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-10-02T16:30Z — champion autorizou o plano ("pode") após o relatório de análise
- teste_humano: aprovado — 2026-10-05 — champion declarou "testei e aprovou"; registros confirmam importação pela UI às 12:45Z (3 ocorrências criadas pela conta dele: 2 pendentes + 1 cancelamento confirmado, preservadas como histórico real)
- verificacao_automatica: passou — build/QA v0.0.37 sem erros; provas da implementação (v0.0.30) + revalidação independente de fechamento (2026-10-05): sem auth 401, empresa inválida erro explícito, RLS consultora-donahelp 403 em acuidar, idempotência cruzada entre sessões (ja_existentes=3, 0 criadas), banco final 9 reais + 3 do teste humano
- aprendizado: capturado — AP-2026-10-05 (ver 06_notas/aprendizado-continuo/) + debug doc 06_notas/debug/debug-2026-10-05-lt1t07-url-google.md
- ultima_acao: LT-1-T07 concluída — credencial provada (Google 200), 2 bugs corrigidos (URL /calendars/primary/events v0.0.31; showDeleted=true v0.0.35), prova real completa (2 pendentes + 1 cancelamento), fixtures das provas limpas (migrations 0010/0011)
- proxima_acao: Nenhuma task ativa — leva técnica 7/7 completa. Próximo trabalho exige novo pedido do champion (candidatos registrados: agenda Dona Help com credencial própria, refresh token automático, validação do consultor do fechamento da fase 1)
- atualizado_em: 2026-10-05T14:00:00-03:00

## O que foi entregue na LT-1-T07 (v0.0.30 → v0.0.37)

1. **Hook `POST /backend/v1/agenda/importar`** — consulta o Google Calendar (janela 7 dias atrás a 14 à frente, configurável, showDeleted=true para elegibilidade total), identifica a unidade pelo título (código explícito ou nome oficial exato — nunca adivinha, RN-1-19), aplica as regras F1-T02 (elegibilidade total; cancelada/excluída → ocorrência tipo cancelamento, estado confirmado, motivo automático; remarcação só com identificação confiável), cria a ocorrência com idempotência `google_calendar:eventId:unidade:tipo` (CA-1-05/1-08). Evento sem unidade identificável → lista de conferência humana. Credencial `GOOGLE_CALENDAR_TOKEN` lida dos Secrets do Skip; sem credencial → resposta explícita `credencial_ausente`. RLS por empresa (403).
2. **Tela `/agenda`** — seletivo de empresa (limitado às autorizadas do usuário), botão "Importar da Agenda", resumo em cartões + lista das reuniões sem unidade para conferência.
3. **Navegação** — link "Importar da Agenda" no menu.

## Bugs pegos pelas provas reais (2026-10-05)

1. **URL do Google errada (v0.0.31):** hook chamava `/calendar/v3/primary/events` (inexistente, 404 HTML — nem processa auth); correto é `/calendar/v3/calendars/primary/events`. Sintoma enganoso: parecia "token expirado".
2. **Furo na RN-1-06 (v0.0.35):** sem `showDeleted=true`, a API do Google omite eventos cancelled — cancelado/excluído na agenda não era importado, silenciosamente. Corrigido e provado (1 cancelada importada com motivo automático).

## Provas executadas (fechamento, 2026-10-05)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Importação sem credencial | resposta explícita `credencial_ausente` | ✓ |
| 2 | RLS: consultora-donahelp pede acuidar | 403 | ✓ |
| 3 | RLS: consultora-donahelp importa donahelp | 200, 4 eventos → sem_unidade (correto, agenda não é dela) | ✓ |
| 4 | Google 200 com agenda real | 4–6 eventos lidos, janela -7d/+14d correta | ✓ |
| 5 | Lookup de unidade | código no título ("Unidade 2" → 2) e nome oficial ("Acuidar João Pessoa" → 2); "Almoço" e "João Gomes" → sem_unidade (RN-1-19) | ✓ |
| 6 | Cancelamento importado | occurrence_type=cancelamento, estado confirmado, motivo automático | ✓ |
| 7 | Idempotência (mesma sessão e cruzada) | reimportar → 0 criadas, ja_existentes corretos | ✓ |
| 8 | Navegador: tela /agenda + importação pela UI | champion importou às 12:45Z (3 ocorrências) | ✓ |
| 9 | Limpeza de fixtures | migrations 0010/0011 — banco final 9 reais + 3 do teste humano | ✓ |

## Pendências restantes (fora de task)

- **Agenda Dona Help:** hook atual lê a agenda da credencial única (Acuidar). Importar a agenda da Dona Help exige credencial própria (ex.: `GOOGLE_CALENDAR_TOKEN_DONAH`) + adaptação do hook — evolução futura a pedido do champion.
- **Refresh token automático:** access token de conta de teste expira ~1h; evoluir o hook para renovar via `GOOGLE_CALENDAR_REFRESH_TOKEN` quando o champion quiser.
- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.
