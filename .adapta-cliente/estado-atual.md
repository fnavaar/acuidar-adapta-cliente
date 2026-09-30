# Estado atual — Adapta Cliente

- task_id: F1-T01
- champion: Luis Carlos - CTO (com acesso ao Administrador do Portal)
- spec: 04_fase-atual/specs/spec-f1-001-fluxo-direto-de-registro.md §BLOQUEIO-F1-001-A
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-09-30T14:59:00-03:00 — champion forneceu Base URL e token via Secrets do Skip
- teste_humano: aprovado — 2026-09-30T12:32:00-03:00 — "sim" do champion confirmou a leitura validada (exemplo 1547 ilustrativo; código real de João Pessoa = 2)
- verificacao_automatica: passou — GET /api/dados/unidades com Authorization: <token> retornou HTTP 200, 172 unidades, 172 códigos únicos; token gravado em ACUIDAR_PORTAL_TOKEN
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-09-30-1535-contrato-api-acuidar.md
- ultima_acao: Task F1-T01 concluída formalmente
- proxima_acao: Aguardar nova solicitação do champion
- atualizado_em: 2026-09-30T15:35:00-03:00

## Histórico de tasks concluídas (referência)

- F1-T01 (2026-09-30): contrato de leitura da API do Portal validado → BLOQUEIO-F1-001-A resolvido
- F1-T02 (2026-08-26): chave oficial, elegibilidade, multiunidade, cancelamento, remarcação → SPEC-1-001
- F1-T05 (2026-08-28): política de exceção de data → SPEC-1-002 e `06_notas/politica-excecao-de-data.md`
- F1-T07 (2026-09-22): semântica da cobertura operacional → SPEC-1-003 e `06_notas/politica-datas-elegibilidade-estados.md`

## Contrato validado da API do Portal (F1-T01)

| Item | Valor |
|---|---|
| Base URL | `https://app.acuidarbr.com.br/api/dados` |
| Endpoint de leitura | `GET /unidades` |
| Autenticação | `Authorization: <token>` — token puro do Portal, **sem** prefixo Bearer |
| Resposta de sucesso | HTTP 200, JSON array de unidades |
| Resposta de erro de auth | HTTP 400 `{"status":"error","message":"Erro no Token"}` |
| Total de unidades | 172 |
| Unicidade da chave | 172 códigos únicos, sem duplicados |
| Campos | `codigo`, `subdominio`, `nome`, `razao_social`, `cnpj`, `email`, `endereco`, `numero`, `complemento`, `bairro`, `cep`, `cidade`, `estado`, `celular`, `telefone` |
| Segredo | `ACUIDAR_PORTAL_TOKEN` (Secrets do Skip, projeto 51740) |
| Observação | Exemplo 1547 da F1-T02 era ilustrativo; código real de João Pessoa = 2 |

## Emenda de arquitetura (2026-09-30)

- Intranet (Skip 51740) = superfície de todo o fluxo; ocorrência criada e armazenada na intranet (ID próprio); Portal Acuidar = somente leitura de franquias.
- Ver `06_notas/emenda-arquitetura-intranet.md`.