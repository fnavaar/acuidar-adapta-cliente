# Estado atual — Adapta Cliente

- task_id: LT-1-T02 (leva técnica — registro de reunião → ocorrência)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-001 (fluxo direto de registro) + SPEC-1-002 (estados/idempotência) + RN-1-06 a RN-1-09
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Análise profunda da LT-1-T02 concluída (segunda task da leva técnica)
- proxima_acao: Aguardar autorização do champion para implementar
- atualizado_em: 2026-10-02T10:00:00-03:00

## Task LT-1-T02 — Registro de reunião → ocorrência (análise)

**Objetivo:** tela onde o consultor registra uma reunião (entrada assistida manual — decisão F1-T08), seleciona a unidade pelo código oficial, revisa os campos e confirma a criação da ocorrência na intranet, com idempotência e estados da SPEC-1-002.

**Critério binário proposto:** "Uma reunião com dados completos e unidade única cria exatamente uma ocorrência com estado inicial correto e comprovante com ID; reenvio da mesma origem não cria segunda ocorrência; campo ausente ou unidade ambígua mantém o item em aguardando_correcao."

**Escopo mínimo (menor recorte completo da SPEC-1-001):**
1. Página `/reunioes/nova` — formulário: empresa (Acuidar/Dona Help), unidade (select carregado da API da empresa), data do fato (padrão = hoje), horário, tipo, título, relato.
2. Validação de campos obrigatórios (RN-1-03): falta qualquer um → não cria, mostra pendência.
3. Unidade única obrigatória (RN-1-01): select impede ambiguidade; sem unidade → bloqueia.
4. Divergência de data (RN-1-02): data do fato ≠ data da reunião → fluxo de exceção da SPEC-1-002 (estado aguardando_aprovacao_de_excecao com justificativa).
5. Criação com idempotência (CA-1-05): `idempotency_key = SHA-256(source_system:source_meeting_id:portal_unit_id:occurrence_type)` calculada e persistida antes; reenvio com a mesma chave → rejeitado pelo índice UNIQUE (comprovado na F1-T06).
6. Comprovante: estado `confirmado` só com ID do registro criado (RN-1-04); falha → estado explícito, nunca sucesso falso (RN-1-05).
7. Multiunidade (RN-1-07): checkbox "evento multiunidade" habilita múltiplas unidades (Café com Franqueados, Day Fusion).

**Fora do escopo desta task:** fila de revisão/aprovação (LT-1-T03), painel (LT-1-T04), conector Google Agenda (leva futura), comunicação ao franqueado.

**Arquivos afetados:** `src/pages/NovaReuniao.tsx` (novo), `src/App.tsx` (rota protegida), `src/lib/unidades.ts` (novo — busca unidades das 2 empresas com parser wrapper/array), `src/components/Layout.tsx` (navegação).

**Decisões de negócio já fechadas que a task aplica:** chave = código da unidade (F1-T02); elegibilidade total (F1-T02); cancelamento/remarcação mantêm registro (F1-T02); estados da SPEC-1-002; idempotência SHA-256 (F1-T06).

**Matriz critério → prova:**
| Critério | Prova |
|---|---|
| Criação única com ID | reunião válida → 1 registro, estado confirmado, comprovante com ID |
| Reenvio não duplica | mesma origem 2× → segunda rejeitada (UNIQUE) |
| Campo ausente | sem título → não cria, mostra pendência |
| Unidade única | select sempre 1 unidade (ou multiunidade explícita) |
| Divergência de data | data diferente → estado aguardando_aprovacao_de_excecao + justificativa |
| Estados corretos | falha → falha_de_gravacao; nunca sucesso falso |

**Riscos/caminhos de erro:** API de unidades indisponível → estado dados_indisponiveis no select (RN-1-14); timeout na criação → possivel_duplicidade (nunca retry cego — RN-1-09); relato com HTML/execução → bloqueado (RN-1-05A, linha vermelha de segurança); sessão expirada → redireciona ao login (guard já pronto da LT-1-T01).

**Pergunta bloqueante:** nenhuma — todas as decisões de negócio já foram aprovadas em tasks anteriores.

**Teste humano esperado:** registrar uma reunião de teste no preview, ver o comprovante com ID, tentar reenviar a mesma reunião e ver a rejeição de duplicidade.