# Matriz de perfis e RLS do fluxo

**Aprovada por:** Champion (Luis Carlos - CTO) — exerce também o papel de Administrador do Portal
**Data:** 2026-09-30
**Task:** F1-T04 · **SPEC:** SPEC-1-002 §BLOQUEIO-F1-002-B/A

## 1. Matriz de permissões (aprovada)

| Permissão | Consultor | Gestor/Coordenador | Administrador |
|---|---|---|---|
| `consultar` | ✓ | ✓ | ✓ |
| `editar rascunho` | ✓ | ✓ | ✓ |
| `solicitar exceção` | ✓ | ✓ | ✓ |
| `aprovar exceção` | ✗ | ✓ | ✓ |
| `confirmar criação` | ✓ | ✓ | ✓ |

**Regras estruturais (da política F1-T05):**
- O solicitante **nunca** aprova a própria exceção (aprovador ≠ solicitante).
- `aprovar exceção` exige perfil hierarquicamente superior (Gestor/Coordenador ou Administrador).
- Nenhum perfil altera logs de auditoria.
- Nenhum perfil exclui ocorrência (cancelamento/remarcação mantêm registro — F1-T02).

## 2. Contas de teste

| Conta | Perfil | Uso na prova |
|---|---|---|
| `consultor-teste@acuidarbr.com.br` | consultor | prova negativa de `aprovar exceção` |
| `gestor-teste@acuidarbr.com.br` | gestor | prova positiva de `aprovar exceção` |
| `admin-teste@acuidarbr.com.br` | administrador | prova de acesso pleno |

Senhas das contas de teste não são registradas neste documento (regra de segredos); residem com o Champion.

## 3. Implementação no banco (intranet — Skip 51740)

- **Campo `role`** na collection `users` (select: `consultor`, `gestor`, `administrador`) — migration 0002.
- **RLS da collection `ocorrencias`** — migration 0003:

| Regra | Valor | Efeito |
|---|---|---|
| `listRule` / `viewRule` | `@request.auth.id != ''` | `consultar`: todos autenticados |
| `createRule` | `@request.auth.id != ''` | `editar rascunho`/criar: todos autenticados |
| `updateRule` | `@request.auth.id != '' && (estado != 'aguardando_aprovacao_de_excecao' \|\| @request.auth.role != 'consultor')` | consultor não toca registro aguardando aprovação (só gestor/admin aprovam); solicitante move o registro PARA o estado, nunca edita dentro dele |
| `deleteRule` | `null` (superuser only) | ninguém exclui ocorrência |

## 4. Prova negativa (critério binário)

Consultor autenticado tentando atualizar um registro em `aguardando_aprovacao_de_excecao` recebe **HTTP 403** — captura sanitizada registrada no estado da task.