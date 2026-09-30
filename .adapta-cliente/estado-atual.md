# Estado atual — Adapta Cliente

- task_id: F1-T08
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: 04_fase-atual/specs/spec-f1-003-painel-de-cobertura-operacional.md §BLOQUEIO-F1-003-B/C
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-09-30T14:11:00-03:00 — champion autorizou o plano ("sim") após as decisões de negócio do formulário
- teste_humano: aprovado — 2026-09-30T14:15:00-03:00 — champion revisou mapa, emendas e STATUS ("tudo certo")
- verificacao_automatica: passou — mapa verificado no repo (SHA 69653827, 4 fontes com dono/latência/timestamp, destino intranet, RLS, deploy); emendas append-only na SPEC-1-003 (B/C) e SPEC-1-002 (checkbox F1-T04) confirmadas por commit; fase.md 8/8; STATUS 100%; changelog; nenhum segredo em artefatos. NOTA: tentativa de regressão RLS em runtime ao vivo bloqueada por indisponibilidade do Skip Cloud (HTTP 503 em todas as chamadas, 20 tentativas, 2026-09-30 ~14:15-14:30); a testabilidade da RLS está sustentada pelas provas RV-1 a RV-9 da F1-T04 executadas hoje contra as mesmas regras (migrations 0002/0003 inalteradas de v0.0.5 a v0.0.7) — regressão recomendada quando a plataforma recuperar
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-09-30-1715-http-send-body-bytes.md
- ultima_acao: F1-T08 concluída — fase 1 de desbloqueio completa (8/8, 100%)
- proxima_acao: Fase só fecha após validação do consultor; então geração da leva técnica (gerar-tasks) para as SPECs 1-001, 1-002 e 1-003
- atualizado_em: 2026-09-30T14:32:00-03:00

## Pendências de limpeza (registradas)

- Fixtures de teste `18l0hqe6k413h4v` e `wt02yp2kpzxm7ba` na base — exclusão exige superuser; remover via painel admin ou migration de limpeza na leva técnica.
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task de integração multiempresa.
- Arquivo `.skip.config.json` aparece com mudança pendente no working tree do Skip (não alterado por nós) — inspecionar antes do próximo apply_changes.
- Indisponibilidade Skip Cloud (503) em 2026-09-30 ~14:15-14:30 — reexecutar regressão RLS (leitura 3 perfis, negação sem auth, negação de delete) quando a plataforma recuperar.
- Rotação recomendada das credenciais que passaram pelo chat antes dos Secrets (chave Google, token Acuidar) — após estabilização.

## Decisões do Champion implementadas na F1-T08 (2026-09-30)

| # | Decisão | Escolha |
|---|---|---|
| 1 | Latência das fontes de unidades (Acuidar + Dona Help) | Diária |
| 2 | Fonte de reuniões | Entrada assistida manual agora (conector Google Agenda na leva técnica) |
| 3 | RLS de leitura do painel | Todos autenticados (consistente com matriz F1-T04); painel não escreve |
| 4 | Dono do deploy do painel | Luis Carlos via Builder/MCP (mesmo padrão F1-T03) |

**Artefatos entregues:**
- `06_notas/mapa-fontes-painel.md` — mapa completo de fontes/latência/destino/RLS (commit 89b7b94)
- SPEC-1-003 emendada (commit 995c0dd) — resolve BLOQUEIO-F1-003-B e C
- SPEC-1-002 emendada (commit 062c400) — checkbox da matriz F1-T04 corrigido
- fase.md 8/8 (commit 4d2eda3), STATUS.md 100% (commit a1b81c9), changelog (commit 509f040)

## Sinal multiempresa — Dona Help (2026-09-30)

Decisões aprovadas pelo Champion (detalhes em `06_notas/sinal-multiempresa-dona-help.md`):

| Decisão | Escolha |
|---|---|
| Momento | Agora, na fase 1 |
| Contrato da API | Formato diferente da Acuidar — URL do endpoint a fornecer |
| Modelo | Mesmo sistema, separação por campo empresa |
| Governança | Mesmo champion para as duas empresas |

**Contrato VALIDADO (2026-09-30, padrão F1-T01):** `GET https://app.donahelpbr.com.br/api/dados/unidades` — auth `Authorization: <token>` puro (sem Bearer); sucesso = HTTP 200 com **array direto** (sem wrapper `{status,message,dados}` da Acuidar); **45 unidades**; códigos únicos 45/45 no campo `codigo`; mesmos 15 campos da Acuidar. Token gravado pelo Champion nos Secrets do Skip (`DONAHELP_PORTAL_TOKEN`) — nunca passou pelo chat. Validação via hook temporário `validar-contrato-donahelp` (Skip v0.0.7, lê `$secrets.get` server-side) — hook a remover após formalização. Parser futuro: detectar wrapper (Acuidar) vs array direto (Dona Help).

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