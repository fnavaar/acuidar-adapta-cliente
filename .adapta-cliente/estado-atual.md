# Estado atual — Adapta Cliente

- task_id: F1-T06
- champion: Luis Carlos - CTO (com acesso ao Administrador do Portal)
- spec: 04_fase-atual/specs/spec-f1-002-estados-excecoes-e-idempotencia.md §BLOQUEIO-F1-002-C
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Análise profunda da task F1-T06 concluída
- proxima_acao: Aguardar autorização do champion para provar a consulta de recuperação
- atualizado_em: 2026-09-30T13:15:00-03:00

## Histórico de tasks concluídas (referência)

- F1-T01 (2026-09-30): contrato de leitura da API do Portal validado → BLOQUEIO-F1-001-A resolvido
- F1-T02 (2026-08-26): chave oficial, elegibilidade, multiunidade, cancelamento, remarcação → SPEC-1-001
- F1-T05 (2026-08-28): política de exceção de data → SPEC-1-002 e `06_notas/politica-excecao-de-data.md`
- F1-T07 (2026-09-22): semântica da cobertura operacional → SPEC-1-003 e `06_notas/politica-datas-elegibilidade-estados.md`

## Análise F1-T06 — consulta de recuperação após timeout

**Contexto da emenda de arquitetura (2026-09-30):** a ocorrência é criada e armazenada na intranet (banco próprio). A consulta de recuperação é, portanto, LOCAL — o bloqueio original (contrato de consulta do Portal) deixa de se aplicar.

### O que a task exige (critério binário)

> "Há consulta documentada por chave de origem ou procedimento equivalente que localiza uma ocorrência após timeout, comprovada em ambiente de teste."

### O que precisa ser provado

1. **Consulta documentada** — como localizar uma ocorrência pelo `source_system + source_meeting_id` (+ unidade, + tipo)
2. **Comprovada em ambiente de teste** — a consulta executada e diferenciando os 3 resultados:
   - Criação **confirmada** (ocorrência existe, com ID)
   - **Ausência** de criação (nada gravado — pode criar com segurança)
   - Resultado **inconclusivo** (não sabe — manter `possivel_duplicidade`, nunca repetir)

### Plano de prova (na intranet)

- Banco da intranet: ocorrência tem `idempotency_key = SHA-256(source_system + ':' + source_meeting_id + ':' + portal_unit_id + ':' + occurrence_type)`
- Consulta: `SELECT` pela chave de idempotência — determinística e indexada
- Os 3 cenários exercidos com fixtures no ambiente de teste (preview do Skip)
- Evidência: captura sanitizada de cada cenário (ID mascarado)

### Pontos de parada

- A consulta não diferenciar confirmada/ausente/inconclusivo → parar
- A prova exigir escrita no Portal → parar (fora do escopo da emenda)