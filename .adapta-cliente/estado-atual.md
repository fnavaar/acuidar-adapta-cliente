# Estado atual — Adapta Cliente

- task_id: F1-T08
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: 04_fase-atual/specs/spec-f1-003-painel de cobertura operacional.md §BLOQUEIO-F1-003-B/C
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Análise profunda da F1-T08 concluída (última task da fase 1); contrato da API da Dona Help validado e registrado em 06_notas/sinal-multiempresa-dona-help.md
- proxima_acao: Aguardar autorização do champion para implementar o plano da F1-T08
- atualizado_em: 2026-09-30T14:05:00-03:00

## Análise F1-T08 — Documentar fontes, latência, destino e RLS do painel

**Critério binário:** "Fontes, campos, latência, destino do painel, dono do deploy e RLS de leitura documentados e autorizados."

**O que o champion precisa decidir (mapa de fontes e latência):**

| Fonte | Dados que fornece | Latência aceitável |
|---|---|---|
| Google Agenda | reuniões elegíveis (ID, unidade, data/hora, status) | [VALIDAR] |
| Ocorrências (intranet) | ocorrências + estados da SPEC-1-002 | tempo real (banco local) |
| Unidades (Portal Acuidar) | cadastro de 172 unidades | [VALIDAR] |
| Unidades (API Dona Help) | cadastro de 45 unidades | [VALIDAR] |

**Além do mapa, precisa:**
- **Destino do painel:** [VALIDAR] — com a emenda de arquitetura, a intranet é o destino natural; confirmar.
- **RLS de leitura do painel:** [VALIDAR] — quem vê o painel? (sugestão: matriz F1-T04 — todos autenticados consultam; painel não escreve)
- **Dono do deploy do painel:** [VALIDAR] — Luis Carlos via Builder/MCP (mesmo da F1-T03)?

**Pontos de parada:**
- Uma fonte sem dono/timestamp → parar
- Painel puder escrever na origem → parar
- RLS não testável → parar

## Sinal multiempresa — Dona Help (2026-09-30)

Decisões aprovadas pelo Champion (detalhes em `06_notas/sinal-multiempresa-dona-help.md`):

| Decisão | Escolha |
|---|---|
| Momento | Agora, na fase 1 |
| Contrato da API | Formato diferente da Acuidar — URL do endpoint a fornecer |
| Modelo | Mesmo sistema, separação por campo empresa |
| Governança | Mesmo champion para as duas empresas |

**Contrato VALIDADO (2026-09-30, padrão F1-T01):** `GET https://app.donahelpbr.com.br/api/dados/unidades` — auth `Authorization: <token>` puro (sem Bearer); sucesso = HTTP 200 com **array direto** (sem wrapper `{status,message,dados}` da Acuidar); **45 unidades**; códigos únicos 45/45 no campo `codigo`; mesmos 15 campos da Acuidar. Token gravado pelo Champion nos Secrets do Skip (`DONAHELP_PORTAL_TOKEN`) — nunca passou pelo chat. Validação via hook temporário `validar-contrato-donahelp` (Skip v0.0.7, lê `$secrets.get` server-side) — hook a remover após formalização. Parser futuro: detectar wrapper (Acuidar) vs array direto (Dona Help).

**Pendente:** emenda append-only nas SPECs 1-001/1-003 (multiempresa + contrato Dona Help) e formalização da task de integração — após aprovação do Champion.

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
| 6 | Administrador edita registro em aguardando aprovação | HTTP C 200 | ✓ |
| 7 | Consultor edita registro em estado normal | HTTP 200 | ✓ |

**Revalidação do zero (fechamento, RV-1 a RV-9):** reautenticação das 3 contas + repetição de todas as provas + regressão do endpoint de recuperação (resultado `inconclusivo` correto para registro não confirmado) + conferência de que nenhum artefato entregue contém segredo. Todos os critérios PASSOU.

**Nota sobre a prova 2:** o PocketBase responde 404 (e não 403) quando a updateRule nega — o registro fica invisível ao perfil sem permissão. É negação efetiva (o consultor não consegue aprovar a própria exceção), conforme CA-1-10. Documentado no aprendizado AP-2026-1006-1645-pocketbase-rls-404.md.

**Pendência de limpeza:** fixtures de teste `18l0hqe6k413h4v` e `wt02yp2kpzxm7ba` (ambos em_revisao) permanecem na base — exclusão exige superuser (prova de que a regra funciona). Remover via painel admin do PocketBase ou migration de limpeza na leva técnica.