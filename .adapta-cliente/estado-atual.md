# Estado atual — Adapta Cliente

- task_id: F1-T04
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal)
- spec: 04_fase-atual/specs/spec-f1-002-estados-excecoes-e-idempotencia.md §BLOQUEIO-F1-002-A
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-09-30T16:31:00-03:00 — champion aceitou a sugestão de matriz ("Aceitar a sugestão")
- teste_humano: aprovado — 2026-09-30T16:43:00-03:00 — champion escolheu aprovar pela rota de evidência ("Aprovar pela evidência") após receber matriz + 7 provas registradas
- verificacao_automatica: passou — revalidação do zero (RV-1 a RV-9): 9 provas independentes reproduziram os resultados originais, incluindo prova negativa (consultor bloqueado em aguardando_aprovacao_de_excecao, 404 por invisibilidade RLS), exclusão negada a não-superuser (403) e regressão da consulta de recuperação da F1-T06 (endpoint segue íntegro); build/QA v0.0.5 sem erros
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-09-30-1645-pocketbase-rls-404.md
- ultima_acao: F1-T04 concluída — fase.md, STATUS.md (7/8, 87,5%), changelog.md e controle de aprendizado atualizados
- proxima_acao: Única task restante da fase 1 é F1-T08 (fontes, latência, destino e RLS do painel) — aguarda pedido do champion para nova análise (proxima-task)
- atualizado_em: 2026-09-30T16:48:00-03:00

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

**Revalidação do zero (fechamento, RV-1 a RV-9):** reautenticação das 3 contas + repetição de todas as provas + regressão do endpoint de recuperação (resultado `inconclusivo` correto para registro não confirmado) + conferência de que nenhum artefato entregue contém segredo. Todos os critérios PASSOU.

**Nota sobre a prova 2:** o PocketBase responde 404 (e não 403) quando a updateRule nega — o registro fica invisível ao perfil sem permissão. É negação efetiva (o consultor não consegue aprovar a própria exceção), conforme CA-1-10. Documentado no aprendizado AP-2026-09-30-1645.

**Pendência de limpeza:** fixtures de teste `18l0hqe6k413h4v` e `wt02yp2kpzxm7ba` (ambos em_revisao) permanecem na base — exclusão exige superuser (prova de que a regra funciona). Remover via painel admin do PocketBase ou migration de limpeza na leva técnica.