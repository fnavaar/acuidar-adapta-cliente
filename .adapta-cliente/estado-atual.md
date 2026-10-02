# Estado atual — Adapta Cliente

- task_id: LT-1-T03 (leva técnica — fila de revisão e aprovação de exceções)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-002 (estados, exceções auditáveis e idempotência) + política F1-T05 (RN-1-11 a RN-1-14) + matriz F1-T04
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Análise profunda da LT-1-T03 concluída (terceira task da leva técnica)
- proxima_acao: Aguardar autorização do champion para implementar
- atualizado_em: 2026-10-02T11:48:00-03:00

## Task LT-1-T03 — Fila de revisão e aprovação de exceções (análise)

**Objetivo:** tela onde o consultor/gestor vê as ocorrências por estado, revisa, corrige e aprova/rejeita exceções de data — aplicando a máquina de estados da SPEC-1-002 e a matriz de perfis da F1-T04 (consultor NÃO aprova exceção; gestor/admin aprovam; ninguém exclui).

**Critério binário proposto:** "A fila exibe as ocorrências por estado com detalhes e histórico; o consultor consegue mover rascunhos entre estados de correção mas recebe negação ao tentar aprovar exceção (CA-1-07/CA-1-10); o gestor/administrador aprova ou rejeita a exceção com ator, instante e motivo registrados; nenhuma transição inválida é aceita."

**Estado real (baseline inspecionado):** 16 ocorrências na base — 10 confirmado, 4 aguardando_aprovacao_de_excecao (fila real para aprovar!), 2 em_revisao. A RLS da F1-T04 já bloqueia o consultor em aguardando_aprovacao_de_excecao (404 por invisibilidade).

**Escopo mínimo (menor recorte completo da SPEC-1-002):**
1. Página `/fila` — lista de ocorrências com filtro por estado (7 estados da SPEC-1-002) e por empresa; badge de estado; ordenação por updated desc.
2. Detalhe da ocorrência (expansão ou painel) — todos os campos + motivo/justificativa quando houver.
3. Transições permitidas por perfil (máquina de estados):
   - Consultor: pode mover `aguardando_correcao` → `em_revisao` (corrigiu) e editar rascunho em estados normais; **não vê nem move** `aguardando_aprovacao_de_excecao` (RLS — invisível).
   - Gestor/Admin: aprovar exceção (`aguardando_aprovacao_de_excecao` → `confirmado` ou → `aguardando_correcao` se rejeitada) com motivo; editar qualquer estado não confirmado.
   - Ninguém: excluir; alterar `confirmado` (só correção posterior conforme política); alterar trilha.
4. Registro auditável da decisão: ator (usuário autenticado), instante (updated), motivo — campos já existem no schema (motivo); aprovador = auth.id da transição.
5. Bloqueio de transição inválida: hook valida pares permitidos (ex.: `confirmado` não volta a `pendente`); negação explícita com mensagem.

**Fora do escopo desta task:** painel de cobertura (LT-1-T04), lembretes/escalação automáticos de 24h/48h (RN-1-13 — requer cron; leva futura), comunicação ao franqueado.

**Arquivos afetados:** `src/pages/Fila.tsx` (novo), `src/App.tsx` (rota), `src/components/Layout.tsx` (navegação), `pocketbase/hooks/ocorrencias_transicao.js` (novo — valida transição por perfil e registra motivo).

**Decisões já fechadas que a task aplica:** máquina de estados e RN-1-06 a RN-1-10 (SPEC-1-002); política de exceção de data com aprovador ≠ solicitante e hierarquia (F1-T05); matriz 3×5 com RLS implementada (F1-T04); 404 = invisibilidade do consultor (AP-1645).

**Matriz critério → prova:**
| Critério | Prova |
|---|---|
| Fila exibe por estado | GET /fila → lista com filtro funcionando |
| Consultor não aprova | consultor tenta PATCH em aguardando_aprovacao → negado (404 RLS) |
| Gestor aprova com trilha | gestor aprova exceção real da fila (há 4 pendentes) → confirmado + motivo |
| Rejeição retorna a correção | gestor rejeita → aguardando_correcao + motivo |
| Transição inválida negada | tentar confirmado → pendente via hook → negado |
| Ninguém exclui | DELETE por qualquer perfil → 403 (regressão F1-T04) |

**Riscos/caminhos de erro:** consultor tentando aprovar via API direta (bypass da UI) → RLS nega (404); gestor aprovando a própria solicitação → bloquear no hook (aprovador ≠ solicitante — CA-1-07); dupla aprovação concorrente → UNIQUE/estado no hook; sessão expirada → guard da LT-1-T01.

**Pergunta bloqueante:** nenhuma — política, matriz e estados todos aprovados em tasks anteriores.

**Teste humano esperado:** abrir a fila no preview, ver as 4 exceções pendentes; com consultor, não vê-las; com gestor, aprovar uma com motivo e ver o estado mudar com trilha.