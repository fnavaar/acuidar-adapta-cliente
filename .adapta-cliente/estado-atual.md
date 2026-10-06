# Estado atual — Adapta Cliente

- task_id: LT-1-T09 (leva técnica — refresh token automático do Google Agenda: renovação on-demand no 401)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: LT-1-T07/LT-1-T08 (mesma base) + documentação OAuth2 do Google (POST oauth2.googleapis.com/token, grant_type=refresh_token) + pendência registrada no fechamento da LT-1-T08
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: LT-1-T09 selecionada e analisada (relatório de análise entregue ao champion em 2026-10-06)
- proxima_acao: aguardar autorização para implementar
- atualizado_em: 2026-10-06T09:15:00-03:00

## O que foi implementado na LT-1-T08 (v0.0.39 → v0.0.43) — CONCLUÍDA

1. **Hook `POST /backend/v1/agenda/importar`** — credencial do Google escolhida POR EMPRESA: `acuidar` → `GOOGLE_CALENDAR_TOKEN`; `donahelp` → `GOOGLE_CALENDAR_TOKEN_DONAH` (decisão do champion de 2026-10-06: cada empresa tem agenda Google própria, emails diferentes). Sem credencial da empresa → resposta explícita `credencial_ausente` com campo `secret` (nome exato do secret a gravar) e a empresa na mensagem.
2. **Decisão (B) do champion (v0.0.42):** evento CANCELADO/EXCLUÍDO sem unidade identificável no título → ocorrência de cancelamento com `portal_unit_id='conferencia'` (marcador, não é código oficial), estado `pendente`, motivo automático e relato explicando a conferência pendente. RN-1-08 (cancelada nunca fica sem registro) vence o conflito com RN-1-19 (sem inferência de unidade — preservado: o marcador não aponta para nenhuma unidade). Evento CONFIRMADO sem unidade continua indo para a lista de conferência humana (sem ocorrência).
3. **Tela `/agenda`** — alerta de credencial ausente exibe o nome do secret que falta (ex.: "Credencial ausente (GOOGLE_CALENDAR_TOKEN_DONAH)").
4. **Limpeza (v0.0.43):** as 8 ocorrências `conferencia` da prova (agenda de teste da automação) excluídas via migration 0013 — banco final 12 registros reais.

## Fluxo de conferência aprovado pelo champion (2026-10-06)

- Reunião excluída/cancelada **com** unidade no título → ocorrência de cancelamento já vinculada, estado confirmado — sem conferência
- Reunião excluída/cancelada **sem** unidade identificável → ocorrência pendente com unidade `conferencia` — alguém da fila (gestor/admin) define a unidade ou confirma irrelevância; é edição de registro, não aprovação de exceção
- Padronizar títulos com código da unidade na agenda reduz a conferência a quase zero

## Provas executadas (fechamento LT-1-T08, 2026-10-06)

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

- **LT-1-T09 (task ativa, aguardando autorização):** refresh token automático do Google Agenda — renovação on-demand no 401
- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Validação do consultor do fechamento da fase 1 — gate humano do método
- Publicação em produção — decisão do champion via Builder/MCP