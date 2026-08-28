# Política de Tratamento de Divergências e Exceções em Reuniões

**Aprovada por:** Champion (Luis Carlos - CTO) · **Data:** 2026-08-28
**Task:** F1-T05 · **SPEC:** SPEC-1-002 §BLOQUEIO-F1-002-B

## 1. Regra de aprovação (CA-1-07)

Uma solicitação **não pode ser aprovada pelo próprio solicitante**.

A aprovação deve ser feita obrigatoriamente por:
- Um usuário com perfil **hierarquicamente superior** ao solicitante; ou
- Um **gestor ou administrador autorizado**, previamente definido no sistema.

O aprovador deve ser **sempre uma pessoa distinta** do solicitante.

**Exemplo:** Solicitante = Consultor; Aprovador permitido = Gestor, Coordenador ou Administrador.
**Não permitido:** Solicitante → aprovar a própria solicitação.

## 2. Quando uma divergência de data bloqueia

Qualquer divergência entre a data registrada no sistema oficial e a data informada na solicitação gera validação.

| Diferença | Comportamento |
|---|---|
| **Mesma data** | Aprovado normalmente |
| **Até 1 dia** | Solicita justificativa e envia para análise de exceção |
| **Superior a 1 dia** | Bloqueia o processo automaticamente |

O sistema **não encerra o caso automaticamente**; permanece como **"Pendente de regularização"**.

## 3. Quando admite exceção

A exceção pode ser solicitada quando houver uma **justificativa comprovável** para a divergência.

**Justificativas aceitáveis:**
- Erro de preenchimento ou registro no sistema
- Alteração ou remarcação da reunião não atualizada corretamente
- Falha técnica na plataforma de reunião ou integração
- Problemas de conexão ou indisponibilidade comprovada
- Outro motivo excepcional, desde que analisado e aprovado pelo responsável

**Não aceito:** justificativa genérica ("esqueci de atualizar", "foi um engano") sem explicação clara.

## 4. Prazo de decisão da exceção

- O responsável analisa e responde em **até 24 horas úteis**.
- Durante o prazo: status **"Pendente de aprovação"**; processo bloqueado para conclusão definitiva.
- Após o prazo: o sistema pode enviar um **lembrete automático** ao aprovador.
- Sem resposta após **48 horas úteis**: escalada automática para um responsável superior ou administrador do sistema.

## 5. Destino da solicitação sem aprovador disponível

- Aprovador principal indisponível → encaminhada automaticamente a **aprovador substituto** previamente definido.
- Hierarquia: **Aprovador principal → Aprovador substituto → Administrador/Gestor do sistema**.
- Nenhum aprovador disponível → solicitação permanece registrada, status **"Aguardando definição de responsável"**; sistema notifica automaticamente o administrador.
- **Nenhuma alteração definitiva é aprovada automaticamente apenas pela ausência de um aprovador.**

## 6. Processo

Divergência → validação automática → justificativa → aprovação → prazo de 24h úteis → escalonamento após 48h úteis → substituto em caso de ausência.

## 7. Tratamento das divergências de data

| Situação | Comportamento |
|---|---|
| Sem divergência | Processo segue normalmente |
| Divergência identificada | Sistema solicita justificativa |
| Justificativa enviada | Encaminhada para aprovação |
| Aguardando decisão | Processo permanece pendente |

**O sistema deve registrar:**
- Data oficial
- Data informada
- Quantidade de dias de divergência
- Justificativa apresentada
- Solicitante
- Aprovador responsável
- Data e horário da decisão
- Resultado da solicitação

## 8. Exemplos (critério de aceite)

### Exemplo 1 — Aprovado
- **Situação:** reunião remarcada por indisponibilidade técnica, alteração não registrada corretamente.
- **Solicitante:** Consultor responsável. **Divergência:** 1 dia.
- **Justificativa:** falha técnica comprovada durante a remarcação.
- **Aprovador:** Gestor responsável (perfil superior). **Decisão:** APROVADO.
- **Resultado:** exceção registrada, divergência aceita, processo segue normalmente.

### Exemplo 2 — Rejeitado
- **Situação:** solicitante informa data diferente sem evidências ou justificativa válida.
- **Solicitante:** Consultor. **Divergência:** 3 dias.
- **Justificativa:** "Foi apenas um engano." **Aprovador:** Gestor. **Decisão:** REJEITADO.
- **Resultado:** divergência não aceita, caso permanece bloqueado/pendente de correção.

### Exemplo 3 — Sem aprovador disponível
- **Situação:** solicitação enviada, aprovador principal ausente.
- **Fluxo:** sistema identifica indisponibilidade → encaminha a substituto predefinido → se também ausente, encaminha a gestor/administrador superior.
- **Status:** "AGUARDANDO DEFINIÇÃO DE APROVADOR".
- **Resultado:** sistema notifica automaticamente os responsáveis; nenhuma solicitação aprovada automaticamente pela ausência de aprovador.
