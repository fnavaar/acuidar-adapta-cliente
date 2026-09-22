# Estado atual — Adapta Cliente

- task_id: F1-T07
- champion: Luis Carlos - CTO
- spec: 04_fase-atual/specs/spec-f1-003-painel-de-cobertura-operacional.md §BLOQUEIO-F1-003-A
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-09-22T12:46:00-03:00 — Champion forneceu a política completa de datas, elegibilidade e estados
- teste_humano: aprovado — 2026-09-22T12:52:00-03:00 — "sim" do champion confirmou a política documentada
- verificacao_automatica: passou — política documentada em estado-atual.md, 06_notas/politica-datas-elegibilidade-estados.md e SPEC-1-003; task marcada em fase.md; STATUS atualizado (3/8)
- aprendizado: sem_sinal:política de semântica é decisão de negócio incorporada à SPEC-1-003; sem padrão técnico reutilizável
- ultima_acao: Task F1-T07 concluída formalmente
- proxima_acao: Aguardar nova solicitação do champion
- atualizado_em: 2026-09-22T12:53:00-03:00

## Decisões documentadas — F1-T07 (semântica da cobertura operacional)

### 1. Janela de análise
- Análise **por mês e separadamente para cada unidade**; apuração mensal por unidade, sem misturar unidades.

### 2. Reunião elegível
- Elegíveis: **todas as reuniões registradas no sistema**, independentemente do estado (cadastradas, em andamento, concluídas, canceladas, remarcadas, excluídas).
- Nenhuma reunião elegível é perdida ou descartada da análise por seu estado; registro permanece rastreável.

### 3. Definição dos 6 estados
| Estado | Condição |
|---|---|
| `reuniao_pendente` | Existe no Google Calendar, é elegível, mas ainda não foi concluída |
| `relato_pendente` | Concluída normalmente, mas o resumo/relato ainda não foi cadastrado |
| `ocorrencia_pendente` | Cancelada/reagendada exige ocorrência e ela ainda não foi cadastrada |
| `ocorrencia_incompleta` | Cancelada/reagendada com ocorrência cadastrada, porém faltam informações obrigatórias |
| `ocorrencia_confirmada` | Cancelada/reagendada com ocorrência completa (todos os obrigatórios preenchidos e válidos) |
| `dados_indisponiveis` | Informações insuficientes para identificar a situação (ex.: API não retornou dados) |

### 4. Canceladas e remarcadas
- Continuam elegíveis e permanecem na análise de cobertura.
- Fluxo: sem ocorrência → `ocorrencia_pendente`; incompleta → `ocorrencia_incompleta`; completa → `ocorrencia_confirmada`.
- Reunião original permanece rastreável mesmo com nova data de remarcação.

### 5. `dados_indisponiveis`
- Não significa que a reunião deixou de existir ou deve ser descartada; permanece elegível e identificada separadamente.
- O sistema **não infere nem preenche por suposição**.

### 6. Exclusão da contagem
- **Sem timestamp válido:** não entra na contagem mensal (impossível determinar o período).
- **Com timestamp + ocorrência incompleta:** continua contabilizada como elegível; ocorrência permanece `ocorrencia_incompleta` (nunca confirmada).

### 7. Campos obrigatórios da ocorrência
- `ocorrencia_confirmada` exige todos os obrigatórios preenchidos e válidos; qualquer ausente/inválido/incompleto → `ocorrencia_incompleta`.
- Lista de obrigatórios é regra fixa do sistema, padronizada para todas as unidades.

### 8. Regra geral de não perda
- Toda reunião com data e horário válidos permanece registrada e rastreável, independentemente do status.
- Sem informação suficiente → `dados_indisponiveis`; nunca classificação por suposição.

### 9. Resumo da lógica
- Sem timestamp válido → fora da contagem mensal.
- Com timestamp → elegível, então: não concluída → `reuniao_pendente`; concluída sem relato → `relato_pendente`; cancelada/remarcada sem ocorrência → `ocorrencia_pendente`; com ocorrência incompleta → `ocorrencia_incompleta`; com ocorrência completa → `ocorrencia_confirmada`; informação insuficiente → `dados_indisponiveis`.