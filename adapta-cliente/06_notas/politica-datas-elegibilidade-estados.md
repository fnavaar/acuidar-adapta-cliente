# Política de Datas, Elegibilidade e Estados das Reuniões

**Aprovada por:** Champion (Luis Carlos - CTO) · **Data:** 2026-09-22
**Task:** F1-T07 · **SPEC:** SPEC-1-003 §BLOQUEIO-F1-003-A

## 1. Janela de análise

As reuniões são analisadas **por mês e separadamente para cada unidade**.

Cada unidade possui sua própria apuração mensal, sem misturar reuniões de unidades diferentes.

## 2. O que é uma reunião elegível?

Para fins de cobertura, são **elegíveis todas as reuniões registradas no sistema**, independentemente do estado atual: cadastradas, em andamento, concluídas, canceladas, remarcadas e excluídas.

**Nenhuma reunião elegível deve ser perdida ou descartada da análise em razão do seu estado.**

O registro permanece rastreável mesmo quando houver alteração de status, cancelamento, remarcação ou exclusão.

## 3. Estados possíveis da reunião

Cada reunião é classificada em **um dos 6 estados**, conforme as informações disponíveis no sistema.

| Estado | Quando acontece | Exemplo |
|---|---|---|
| **reuniao_pendente** | A reunião existe no Google Calendar, é elegível, mas ainda não foi concluída. | Reunião marcada para amanhã ou em andamento. |
| **relato_pendente** | A reunião foi concluída normalmente, mas o resumo/relato ainda não foi cadastrado no sistema. | A reunião terminou às 15h, mas o consultor ainda não registrou o relato. |
| **ocorrencia_pendente** | A reunião foi cancelada ou reagendada e, por isso, exige uma ocorrência, mas ela ainda não foi cadastrada. | A reunião foi reagendada, mas o responsável ainda não registrou a ocorrência explicando a alteração. |
| **ocorrencia_incompleta** | A reunião foi cancelada ou reagendada e possui ocorrência cadastrada, porém faltam informações obrigatórias. | Foi registrada uma ocorrência de cancelamento, mas o motivo não foi informado. |
| **ocorrencia_confirmada** | A reunião foi cancelada ou reagendada e possui ocorrência cadastrada corretamente, com todas as informações obrigatórias preenchidas. | A reunião foi reagendada e a ocorrência contém o motivo, a nova data e os demais campos obrigatórios. |
| **dados_indisponiveis** | Não foi possível obter informações suficientes para identificar corretamente a situação da reunião. | A API não retornou os dados necessários para determinar se a reunião foi concluída, cancelada ou reagendada. |

## 4. Regra para reuniões canceladas e remarcadas

Reuniões **canceladas ou remarcadas continuam sendo reuniões elegíveis** e permanecem na análise de cobertura. O cancelamento ou a remarcação **não elimina a reunião da base nem da apuração**.

Nesses casos, a reunião é direcionada para o fluxo de ocorrência:

- Sem ocorrência cadastrada → `ocorrencia_pendente`;
- Com ocorrência, mas com informações obrigatórias faltantes → `ocorrencia_incompleta`;
- Com ocorrência completa → `ocorrencia_confirmada`.

A reunião original permanece rastreável, mesmo quando existir uma nova data decorrente de uma remarcação.

## 5. Regra para `dados_indisponiveis`

Quando o sistema ou a API não fornecer informações suficientes para determinar a situação da reunião, ela recebe o estado **`dados_indisponiveis`**.

Esse estado **não significa que a reunião deixou de existir ou que deve ser descartada**. A reunião permanece registrada e é contabilizada como **reunião elegível**, porém identificada separadamente no resultado da análise, pois não foi possível determinar sua situação.

O sistema **não deve fazer inferências ou preencher informações ausentes por suposição**.

## 6. O que não entra na contagem?

### 6.1. Ausência de data e horário do registro

Se a reunião não possuir **data e horário válidos do registro (timestamp)**:

> **A reunião não entra na contagem da análise mensal.**

Motivo: não é possível determinar corretamente a qual período mensal a reunião pertence.

### 6.2. Informações obrigatórias ausentes

Se a reunião possui data e horário válidos, mas a ocorrência está com informações obrigatórias faltantes:

> **A reunião continua sendo contabilizada como elegível**, porém sua ocorrência permanece como `ocorrencia_incompleta` e não pode ser considerada confirmada.

Informação faltante **não exclui a reunião da análise**.

## 7. Campos obrigatórios da ocorrência

Uma ocorrência somente é classificada como **`ocorrencia_confirmada`** quando todos os campos obrigatórios definidos pelo sistema estiverem preenchidos e válidos.

Caso qualquer informação obrigatória esteja ausente, inválida ou incompleta: **`ocorrencia_incompleta`**.

A lista de campos obrigatórios é regra fixa do sistema, aplicada de forma padronizada para todas as unidades.

## 8. Regra geral de não perda de reuniões

> **Toda reunião que possua data e horário válidos permanece registrada e rastreável na análise, independentemente de ter sido cadastrada, estar em andamento, concluída, cancelada, remarcada ou excluída posteriormente.**

O sistema não exclui uma reunião da análise simplesmente porque seu status foi alterado.

Quando não houver informações suficientes para determinar sua situação, usa-se **`dados_indisponiveis`** — nunca uma classificação baseada em suposição.

## 9. Resumo da lógica

**Reunião possui timestamp válido?**

- **Não** → não entra na contagem mensal.
- **Sim** → é elegível.

Depois:

- Ainda não concluída → `reuniao_pendente`
- Concluída sem relato → `relato_pendente`
- Cancelada/remarcada sem ocorrência → `ocorrencia_pendente`
- Cancelada/remarcada com ocorrência incompleta → `ocorrencia_incompleta`
- Cancelada/remarcada com ocorrência completa → `ocorrencia_confirmada`
- Informações insuficientes para determinar a situação → `dados_indisponiveis`

**Nenhuma reunião elegível deve ser descartada por seu status.**
