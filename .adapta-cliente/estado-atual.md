# Estado atual — Adapta Cliente

- task_id: LT-1-T08 (leva técnica — conector do Google Agenda para a Dona Help: credencial por empresa)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: LT-1-T07 (mesma base) + mapa de fontes F1-T08 §5 + decisões multiempresa de 2026-09-30 (mesmo sistema, campo empresa, mesmo champion) + RLS por empresa (LT-1-T06)
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-06T08:34-03:00 — champion autorizou o plano ("sim") após o relatório de análise; confirmação adicional: cada empresa tem agenda Google própria (emails diferentes); decisão (B) aprovada em 2026-10-06T08:56-03:00 — cancelado/excluído sem unidade identificável vira ocorrência de cancelamento com unidade pendente de conferência
- teste_humano: pendente (2ª rodada — correção da decisão (B) entregue em 2026-10-06T09:00-03:00)
- verificacao_automatica: passou — Skip v0.0.42 QA ✓; provas da correção: 8 canceladas/excluídas sem unidade → ocorrências de cancelamento com portal_unit_id=conferencia, estado pendente, motivo automático; 2ª rodada idempotente (ja_existentes=8, 0 criadas); RLS 403; acuidar idempotente (ja_existentes=3); banco 20 registros (12 reais + 8 fixtures da prova a limpar após teste humano)
- aprendizado: pendente
- ultima_acao: correção da decisão (B) implementada (v0.0.42) — cancelado/excluído sem unidade identificável vira ocorrência de cancelamento com unidade conferencia (RN-1-08 vence; sem inferência RN-1-19)
- proxima_acao: aguardar teste humano do champion (2ª rodada: canceladas aparecem na fila com unidade conferencia)
- atualizado_em: 2026-10-06T09:00:00-03:00

## O que foi implementado na LT-1-T08 (v0.0.39 → v0.0.42)

1. **Hook `POST /backend/v1/agenda/importar`** — credencial do Google escolhida POR EMPRESA: `acuidar` → `GOOGLE_CALENDAR_TOKEN`; `donahelp` → `GOOGLE_CALENDAR_TOKEN_DONAH` (decisão do champion de 2026-10-06: cada empresa tem agenda Google própria, emails diferentes). Sem credencial da empresa → resposta explícita `credencial_ausente` com campo `secret` (nome exato do secret a gravar) e a empresa na mensagem.
2. **Decisão (B) do champion (v0.0.42):** evento CANCELADO/EXCLUÍDO sem unidade identificável no título → ocorrência de cancelamento com `portal_unit_id='conferencia'` (marcador, não é código oficial), estado `pendente`, motivo automático e relato explicando a conferência pendente. RN-1-08 (cancelada nunca fica sem registro) vence o conflito com RN-1-19 (sem inferência de unidade — preservado: o marcador não aponta para nenhuma unidade). Evento CONFIRMADO sem unidade continua indo para a lista de conferência humana (sem ocorrência).
3. **Tela `/agenda`** — alerta de credencial ausente exibe o nome do secret que falta (ex.: "Credencial ausente (GOOGLE_CALENDAR_TOKEN_DONAH)").

## Provas executadas (v0.0.42)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | donahelp sem credencial DONAH | `credencial_ausente` com `secret: GOOGLE_CALENDAR_TOKEN_DONAH` + mensagem com a empresa | ✓ |
| 2 | 8 canceladas/excluídas sem unidade (agenda real da automação) | 8 ocorrências de cancelamento criadas: portal_unit_id=conferencia, estado pendente, motivo automático | ✓ |
| 3 | 2ª rodada idempotente | ja_existentes=8, 0 criadas | ✓ |
| 4 | Confirmado sem unidade | sem_unidade (lista de conferência), sem ocorrência — comportamento mantido | ✓ |
| 5 | RLS: consultora-donahelp pede acuidar | 403 | ✓ |
| 6 | Acuidar com token renovado | Google 200, ja_existentes=3 (idempotência das 3 do teste humano de ontem) | ✓ |
| 7 | Banco | 20 registros (12 reais + 8 fixtures da prova a limpar após teste humano) | ✓ |

**Diagnóstico do teste humano 1ª rodada (registrado como DÚVIDA no changelog, commit 4d242e8):** a credencial DONAH é da conta `acuidar.automacao@gmail.com` (agenda própria da automação, confirmado pelo champion) — a agenda continha eventos de teste com títulos Acuidar, por isso tudo caía em sem_unidade. showDeleted=true provado funcionando (8 cancelled devolvidos pelo Google). O problema do champion ("não está pegando as reuniões que foram excluídas") era o caminho cancelado-sem-unidade → corrigido pela decisão (B).

## Roteiro de teste humano (2ª rodada — LT-1-T08)

1. Preview → **Ctrl+Shift+R** → login admin → **Importar da Agenda** → **Dona Help** → Importar
2. Esperado: resumo com **canceladas = 8** (as excluídas da agenda da automação) e sem_unidade para os confirmados sem unidade
3. **Fila de ocorrências** → filtrar Dona Help → as 8 canceladas aparecem com **unidade `conferencia`**, estado pendente, motivo "Reunião cancelada na agenda… unidade pendente de conferência"
4. Reimportar → **0 criadas** (idempotência)
5. **Acuidar** → Importar → Google 200, resumo normal (token renovado pelo champion)
6. **Como reconhecer falha:** canceladas não aparecem na fila; duplicatas ao reimportar; unidade conferencia entrando na cobertura do painel (não deve)

## Pendências restantes (fora de task)

- **Limpeza:** as 8 ocorrências `conferencia` da prova são fixtures da agenda de teste — excluir via migration após o teste humano (padrão 0010/0011)
- **Refresh token automático:** access token de conta de teste expira ~1h; evoluir o hook para renovar via `GOOGLE_CALENDAR_REFRESH_TOKEN` quando o champion quiser
- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Validação do consultor do fechamento da fase 1 — gate humano do método