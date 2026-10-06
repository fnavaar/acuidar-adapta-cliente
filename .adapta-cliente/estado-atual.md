# Estado atual — Adapta Cliente

- task_id: LT-1-T08 (leva técnica — conector do Google Agenda para a Dona Help: credencial por empresa)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: LT-1-T07 (mesma base) + mapa de fontes F1-T08 §5 + decisões multiempresa de 2026-09-30 (mesmo sistema, campo empresa, mesmo champion) + RLS por empresa (LT-1-T06)
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-10-06T08:34-03:00 — champion autorizou o plano ("sim") após o relatório de análise; confirmação adicional: cada empresa tem agenda Google própria (emails diferentes); decisão (B) aprovada em 2026-10-06T08:56-03:00 — cancelado/excluído sem unidade identificável vira ocorrência de cancelamento com unidade pendente de conferência
- teste_humano: aprovado — 2026-10-06T09:01-03:00 — champion: "tudo ok"; decisão do fluxo de conferência confirmada: excluída COM unidade no título → cancelamento confirmado automático; SEM unidade → ocorrência pendente com unidade conferencia até resolução humana na fila (RN-1-08 vence RN-1-19, sem inferência)
- verificacao_automatica: passou — Skip v0.0.43 QA ✓ (limpeza 0013); revalidação de fechamento: banco 12 registros reais, sem auth 401, RLS 403, empresa inválida erro explícito; provas da implementação em v0.0.39–v0.0.42 (credencial por empresa, decisão (B), idempotência, prova de navegador)
- aprendizado: capturado — AP-2026-10-06-0905 (ver 06_notas/aprendizado-continuo/)
- ultima_acao: LT-1-T08 concluída — credencial Google por empresa + decisão (B) (cancelado sem unidade → ocorrência conferencia) + fixtures da prova limpas (migration 0013, v0.0.43)
- proxima_acao: Nenhuma task ativa — próximo trabalho exige novo pedido do champion (candidatos: refresh token automático, rotação de credenciais, validação do consultor da fase 1)
- atualizado_em: 2026-10-06T09:05:00-03:00

## O que foi implementado na LT-1-T08 (v0.0.39 → v0.0.43)

1. **Hook `POST /backend/v1/agenda/importar`** — credencial do Google escolhida POR EMPRESA: `acuidar` → `GOOGLE_CALENDAR_TOKEN`; `donahelp` → `GOOGLE_CALENDAR_TOKEN_DONAH` (decisão do champion de 2026-10-06: cada empresa tem agenda Google própria, emails diferentes). Sem credencial da empresa → resposta explícita `credencial_ausente` com campo `secret` (nome exato do secret a gravar) e a empresa na mensagem.
2. **Decisão (B) do champion (v0.0.42):** evento CANCELADO/EXCLUÍDO sem unidade identificável no título → ocorrência de cancelamento com `portal_unit_id='conferencia'` (marcador, não é código oficial), estado `pendente`, motivo automático e relato explicando a conferência pendente. RN-1-08 (cancelada nunca fica sem registro) vence o conflito com RN-1-19 (sem inferência de unidade — preservado: o marcador não aponta para nenhuma unidade). Evento CONFIRMADO sem unidade continua indo para a lista de conferência humana (sem ocorrência).
3. **Tela `/agenda`** — alerta de credencial ausente exibe o nome do secret que falta (ex.: "Credencial ausente (GOOGLE_CALENDAR_TOKEN_DONAH)").
4. **Limpeza (v0.0.43):** as 8 ocorrências `conferencia` da prova (agenda de teste da automação) excluídas via migration 0013 — banco final 12 registros reais.

## Fluxo de conferência aprovado pelo champion (2026-10-06)

- Reunião excluída/cancelada **com** unidade no título → ocorrência de cancelamento já vinculada, estado confirmado — sem conferência
- Reunião excluída/cancelada **sem** unidade identificável → ocorrência pendente com unidade `conferencia` — alguém da fila (gestor/admin) define a unidade ou confirma irrelevância; é edição de registro, não aprovação de exceção
- Padronizar títulos com código da unidade na agenda reduz a conferência a quase zero

## Provas executadas (fechamento, 2026-10-06)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | donahelp sem credencial DONAH | `credencial_ausente` com `secret: GOOGLE_CALENDAR_TOKEN_DONAH` + mensagem com a empresa | ✓ |
| 2 | 8 canceladas/excluídas sem unidade (agenda real da automação) | 8 ocorrências de cancelamento: portal_unit_id=conferencia, estado pendente, motivo automático | ✓ |
| 3 | 2ª rodada idempotente | ja_existentes=8, 0 criadas | ✓ |
| 4 | Confirmado sem unidade | sem_unidade (lista de conferência), sem ocorrência — comportamento mantido | ✓ |
| 5 | RLS: consultora-donahelp pede acuidar | 403 | ✓ |
| 6 | Acuidar com token renovado | Google 200, ja_existentes=3 (idempotência das 3 do teste humano da LT-1-T07) | ✓ |
| 7 | Prova de navegador | alerta "Credencial ausente (GOOGLE_CALENDAR_TOKEN_DONAH)" exibido na tela | ✓ |
| 8 | Limpeza 0013 + revalidação | banco 12 reais; sem auth 401; RLS 403; empresa inválida erro explícito | ✓ |

## Pendências restantes (fora de task)

- **Refresh token automático:** access token de conta de teste expira ~1h; evoluir o hook para renovar via `GOOGLE_CALENDAR_REFRESH_TOKEN` quando o champion quiser
- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Validação do consultor do fechamento da fase 1 — gate humano do método
- Publicação em produção — decisão do champion via Builder/MCP