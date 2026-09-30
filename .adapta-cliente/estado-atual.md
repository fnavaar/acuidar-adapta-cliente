# Estado atual — Adapta Cliente

- task_id: F1-T04
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal)
- spec: 04_fase-atual/specs/spec-f1-002-estados-excecoes-e-idempotencia.md §BLOQUEIO-F1-002-A
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-09-30T16:31:00-03:00 — champion aceitou a sugestão de matriz ("Aceitar a sugestão")
- teste_humano: pendente
- verificacao_automatica: passou — 7 provas executadas no ambiente de teste do Skip (v0.0.5, QA ✓); detalhes abaixo
- aprendizado: pendente
- ultima_acao: Matriz de perfis implementada (migrations 0002/0003) e provas executadas
- proxima_acao: Aguardar teste humano do champion
- atualizado_em: 2026-09-30T16:35:00-03:00

## O que foi implementado (F1-T04)

### 1. Documento da matriz: `06_notas/matriz-perfis-rls.md`
| Permissão | Consultor | Gestor/Coordenador | Administrador |
|---|---|---|---|
| `consultar` | ✓ | ✓ | ✓ |
| `editar rascunho` | ✓ | ✓ | ✓ |
| `solicitar exceção` | ✓ | ✓ | ✓ |
| `aprovar exceção` | ✗ | ✓ | ✓ |
| `confirmar criação` | ✓ | ✓ | ✓ |

### 2. Migration 0002 — campo `role` em `users` (consultor | gestor | administrador)

### 3. Migration 0003 — RLS da collection `ocorrencias` conforme a matriz
- list/view/create: `@request.auth.id != ''` (todos autenticados)
- update: negado ao consultor quando `estado = aguardando_aprovacao_de_excecao` (só gestor/admin)
- delete: `null` (superuser only — ninguém exclui ocorrência)

### 4. Contas de teste criadas
- `consultor-teste@acuidarbr.com.br` (role consultor)
- `gestor-teste@acuidarbr.com.br` (role gestor)
- `admin-teste@acuidarbr.com.br` (role administrador)

## Provas executadas (Skip 51740, versão 0.0.5)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Consultor cria ocorrência (editar rascunho) | HTTP 200 | ✓ |
| 2 | **Consultor tenta editar registro em `aguardando_aprovacao_de_excecao`** | **HTTP 404 (negação por RLS — registro invisível ao consultor)** | ✓ |
| 3 | Gestor edita o mesmo registro (aprovar exceção) | HTTP 200 | ✓ |
| 4 | Consultor consulta (list) | HTTP 200 | ✓ |
| 5 | Consultor tenta EXCLUIR ocorrência | **HTTP 403 "Only superusers"** | ✓ |
| 6 | Administrador edita registro em aguardando aprovação | HTTP 200 | ✓ |
| 7 | Consultor edita registro em estado normal | HTTP 200 | ✓ |

**Nota sobre a prova 2:** o PocketBase responde 404 (e não 403) quando a updateRule nega — o registro fica invisível ao perfil sem permissão. É negação efetiva (o consultor não consegue aprovar a própria exceção), conforme CA-1-10.

**Pendência de limpeza:** fixture de teste `18l0hqe6k413h4v` (estado em_revisao) permanece na base — exclusão exige superuser. Remover via painel admin do PocketBase ou na próxima migration de limpeza.