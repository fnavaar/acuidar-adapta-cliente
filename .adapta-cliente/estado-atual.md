# Estado atual — Adapta Cliente

- task_id: F1-T01
- champion: Luis Carlos - CTO (com acesso ao Administrador do Portal)
- spec: 04_fase-atual/specs/spec-f1-001-fluxo-direto-de-registro.md §BLOQUEIO-F1-001-A
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Análise profunda da task F1-T01 concluída
- proxima_acao: Aguardar autorização do champion e entrega do contrato da API pelo Administrador do Portal
- atualizado_em: 2026-09-22T13:05:00-03:00

## Histórico de tasks concluídas (referência)

- F1-T02 (2026-08-26): chave oficial, elegibilidade, multiunidade, cancelamento, remarcação → SPEC-1-001
- F1-T05 (2026-08-28): política de exceção de data → SPEC-1-002 e `06_notas/politica-excecao-de-data.md`
- F1-T07 (2026-09-22): semântica da cobertura operacional → SPEC-1-003 e `06_notas/politica-datas-elegibilidade-estados.md`

## Análise F1-T01 — checklist do contrato da API (a validar pelo Administrador do Portal)

1. Base URL / ambiente permitido (teste ≠ produção)
2. Especificação de leitura de ocorrência (endpoint, parâmetros, resposta)
3. Especificação de criação de ocorrência (endpoint, payload, resposta)
4. Método de autenticação
5. Escopos (menor privilégio: leitura de unidade + criação de ocorrência)
6. Códigos de erro (401/403/timeout/5xx/retorno sem ID)
7. Limites (rate limit, timeout)
8. Identificador retornado (formato do ID da ocorrência criada)
9. Conta de teste (menor privilégio)

## Provas exigidas no ambiente de teste

- Leitura comprovada (captura sanitizada)
- Criação controlada comprovada (ID mascarado)
- TDD RED: unidade ambígua, relato inválido e resposta sem ID falham sem estado `confirmado`
- TDD GREEN: reunião válida e unidade única → uma chamada, um ID
- CA-1-04: timeout/sessão expirada/resposta sem ID nunca exibem sucesso

## Ponto de parada

- Qualquer item do contrato faltando
- Conta excedendo menor privilégio
- Escrita tocando produção
