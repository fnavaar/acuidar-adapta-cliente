# Estado atual — Adapta Cliente

- task_id: F1-T08
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: 04_fase-atual/specs/spec-f1-003-painel-de-cobertura-operacional.md §BLOQUEIO-F1-003-B/C
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente — decisões de negócio recebidas (formulário 2026-09-30T14:11), autorização de implementação ainda não dada
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Decisões de negócio da F1-T08 recebidas do champion (latência unidades = diária; reuniões = entrada assistida manual agora; RLS painel = todos autenticados; deploy = Luis Carlos via Builder/MCP)
- proxima_acao: Aguardar autorização do champion para implementar o plano da F1-T08
- atualizado_em: 2026-09-30T14:12:00-03:00

## Análise F1-T08 — Documentar fontes, latência, destino e RLS do painel

**Critério binário:** "Fontes, campos, latência, destino do painel, dono do deploy e RLS de leitura documentados e autorizados."

**Decisões do Champion recebidas (2026-09-30, formulário):**

| # | Decisão | Escolha |
|---|---|---|
| 1 | Latência das fontes de unidades (Acuidar + Dona Help) | **Diária** |
| 2 | Fonte de reuniões | **Entrada assistida manual agora** (conector Google Agenda na leva técnica, se aprovado) |
| 3 | RLS de leitura do painel | **Todos autenticados** (consistente com matriz F1-T04; painel não escreve) |
| 4 | Dono do deploy do painel | **Luis Carlos via Builder/MCP** (mesmo padrão da F1-T03) |

**Plano (aguardando autorização):**
1. Criar `06_notas/mapa-fontes-painel.md` — mapa fonte → dados → latência → dono → timestamp, cobrindo as duas empresas (Acuidar 172 unidades / Dona Help 45 unidades) e as 4 decisões acima.
2. Registrar destino do painel (intranet Skip 51740) e RLS de leitura (todos autenticados, somente leitura) no mesmo documento.
3. Emenda append-only na SPEC-1-003 (decisões de fontes/latência/destino/RLS + multiempresa) e correção do checklist da SPEC-1-002 (matriz F1-T04 aprovada — registro omitido no fechamento da F1-T04).
4. Marcar F1-T08 em `fase.md`, atualizar STATUS.md (8/8 — 100%), changelog.md e estado.
5. Remover o hook temporário `validar-contrato-donahelp` (Skip) após formalização — registrado como pendência de limpeza.

**Pontos de parada:** fonte sem dono/timestamp → parar; painel puder escrever na origem → parar; RLS não testável → parar.

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

**Pendência de limpeza:** fixtures de teste `18l0hqe6k413h4v` e `wt02yp2kpzxm7ba` (ambos em_revisao) permanecem na base — exclusão exige superuser (prova de que a regra funciona). Remover via painel admin do PocketBase ou migration de limpeza na leva técnica.