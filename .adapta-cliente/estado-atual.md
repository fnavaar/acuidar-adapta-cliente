# Estado atual — Adapta Cliente

- task_id: F1-T06
- champion: Luis Carlos - CTO (com acesso ao Administrador do Portal)
- spec: 04_fase-atual/specs/spec-f1-002-estados-excecoes-e-idempotencia.md §BLOQUEIO-F1-002-C
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-09-30T16:08:00-03:00 — "sim" do champion autorizou implementar o plano da F1-T06
- teste_humano: aprovado — 2026-09-30T16:18:00-03:00 — "ok" do champion após roteiro de teste (collection + índice único + endpoint)
- verificacao_automatica: passou — 6 cenários exercitados no ambiente de teste do Skip; build/QA OK (versão 0.0.4)
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-09-30-1620-idempotencia-recuperacao.md
- ultima_acao: Task F1-T06 concluída formalmente
- proxima_acao: Aguardar nova solicitação do champion
- atualizado_em: 2026-09-30T16:20:00-03:00

## O que foi implementado (F1-T06)

### 1. Collection `ocorrencias` (migration 0001)
- Banco da intranet (Skip 51740) — emenda de arquitetura: ocorrência vive na intranet.
- Chave de idempotência: `idempotency_key = SHA-256(source_system:source_meeting_id:portal_unit_id:occurrence_type)`, **UNIQUE index** — reenvio da mesma chave é rejeitado.
- Estados: pendente, em_revisao, aguardando_correcao, aguardando_aprovacao_de_excecao, possivel_duplicidade, falha_de_gravacao, confirmado.
- RLS provisória: somente autenticados (matriz nominal da F1-T04 substituirá).

### 2. Endpoint de recuperação (hook)
- `GET /backend/v1/ocorrencias/recuperar?source_system=&source_meeting_id=&portal_unit_id=&occurrence_type=`
- Diferencia os 3 resultados exigidos: **confirmada** / **ausente** / **inconclusivo**.
- Chave incompleta → `inconclusivo` com lista do que falta (nunca adivinha).
- Registro existente não confirmado → `inconclusivo` com estado atual (mantém possivel_duplicidade até decisão humana).
- Falha da consulta → `inconclusivo` com errorId (nunca "confirmado" por suposição).
- Autenticação obrigatória (401 sem token).

## Provas executadas (ambiente de teste do Skip, versão 0.0.4)

| # | Cenário | Resultado | ✓ |
|---|---|---|---|
| 1 | Chave incompleta (faltam 2 componentes) | `inconclusivo` + lista `faltando` | ✓ |
| 2 | Chave completa, sem ocorrência | `ausente` + idempotency_key (retomada segura) | ✓ |
| 3 | Ocorrência confirmada com a mesma chave | `confirmada` + comprovante (ID, estado, data, título) | ✓ |
| 4 | Reenvio da mesma chave (CA-1-05/08) | HTTP 400 `validation_not_unique` — sem duplicidade | ✓ |
| 5 | Registro existente NÃO confirmado | `inconclusivo` + estado_atual `falha_de_gravacao` | ✓ |
| 6 | Consulta sem autenticação | HTTP 401 — acesso negado explícito | ✓ |

Fixtures de teste foram removidas após as provas (base limpa: 0 registros).

## Evidência

- Logs do backend (Skip): 14 requisições registradas, incluindo os 6 cenários acima.
- QA pipeline: setup ✓, static ✓, build ✓, integrations ✓, test ✓ — versão 0.0.4.