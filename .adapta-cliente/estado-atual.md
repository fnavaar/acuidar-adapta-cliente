# Estado atual — Adapta Cliente

- task_id: F1-T05
- champion: Luis Carlos - CTO
- spec: 04_fase-atual/specs/spec-f1-002-estados-excecoes-e-idempotencia.md §BLOQUEIO-F1-002-B
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-08-28T08:51:00-03:00 — Champion forneceu todas as decisões da política de exceção de data
- teste_humano: aprovado — 2026-08-28T08:53:00-03:00 — "sim" do champion confirmou a política documentada
- verificacao_automatica: passou — política documentada em estado-atual.md, 06_notas/politica-excecao-de-data.md e SPEC-1-002; task marcada em fase.md; STATUS atualizado (2/8)
- aprendizado: sem_sinal:task de política de negócio incorporada à SPEC-1-002; sem padrão técnico reutilizável
- ultima_acao: Task F1-T05 concluída formalmente
- proxima_acao: Aguardar nova solicitação do champion
- atualizado_em: 2026-08-28T08:55:00-03:00

## Decisões documentadas — F1-T02 (referência, concluída)

### 1. Chave oficial única
- **Campo:** Código da unidade (banco de dados do sistema oficial)
- **Exemplo:** 1547 — Acuidar João Pessoa Centro
- **Regra:** O código é a referência definitiva; o nome da unidade NÃO deve ser usado como chave (variações de escrita)
- **Fonte:** API do sistema oficial (a ser fornecida pelo champion)

### 2. Regra de elegibilidade
- **Regra:** TODAS as reuniões devem ser registradas, independente do status
- **Status incluídos:** Agendada, Remarcada, Cancelada, Concluída
- **Exceção:** Nenhuma — nenhuma reunião pode ser excluída ou ignorada
- **Métricas:** Todas as reuniões entram na contagem (total, por status)

### 3. Tratamento de multiunidade
- **Regra padrão:** Cada reunião pertence a apenas uma unidade
- **Exceções:** Café com Franqueados, Day Fusion e eventos multiunidade definidos posteriormente
- **Nestes casos:** A mesma reunião é vinculada e contabilizada para todas as unidades participantes
- **Exemplo:** "Café com Franqueados — Unidades da Paraíba" → vincula 1547, 1620 e 1743

### 4. Tratamento de cancelamento
- **Regra:** Nunca excluir, apagar ou remover a reunião do histórico
- **Status:** "Cancelada"
- **Obrigatório:** Registrar motivo do cancelamento
- **Métricas:** Reunião cancelada continua existindo e entra na contagem

### 5. Tratamento de remarcação
- **Regra:** Registro original NÃO desaparece nem é substituído
- **Status do original:** "Remarcada"
- **Obrigatório:** Registrar motivo da remarcação
- **Novo agendamento:** Gerado com vínculo ao registro original (quando identificação confiável)
- **Cadeia:** Original → 1ª remarcação → 2ª remarcação → realização final
- **Alerta:** Não considerar automaticamente como remarcação por semelhança de nome/unidade

## Decisões documentadas — F1-T05 (política de exceção de data)

### 1. Quando a divergência de data bloqueia
- **Mesma data** → aprovado normalmente
- **Diferença de até 1 dia** → solicita justificativa e envia para análise de exceção
- **Diferença superior a 1 dia** → bloqueia o processo automaticamente
- **Bloqueio:** o sistema não encerra o caso automaticamente; permanece "Pendente de regularização"

### 2. Quando admite exceção
- Exceção permitida quando houver **justificativa comprovável**
- Justificativas aceitáveis: erro de preenchimento/registro; alteração/remarcação não atualizada corretamente; falha técnica na plataforma ou integração; problemas de conexão/indisponibilidade comprovada; outro motivo excepcional aprovado pelo responsável
- **Não aceita:** justificativa genérica ("esqueci de atualizar", "foi um engano") sem explicação clara

### 3. Prazo de decisão da exceção
- Responsável analisa e responde em **até 24 horas úteis**
- Durante o prazo: status "Pendente de aprovação"; processo bloqueado para conclusão definitiva
- Após o prazo: sistema envia **lembrete automático** ao aprovador
- Sem resposta após **48 horas úteis**: escalada automática para responsável superior ou administrador

### 4. Destino da solicitação sem aprovador disponível
- Aprovador principal indisponível → encaminhada automaticamente a **aprovador substituto** previamente definido
- Hierarquia: Aprovador principal → Aprovador substituto → Administrador/Gestor do sistema
- Nenhum aprovador disponível → permanece registrada, status "Aguardando definição de responsável"; sistema notifica automaticamente o administrador
- **Nenhuma alteração definitiva é aprovada automaticamente pela ausência de um aprovador**

### 5. Regra de aprovação (CA-1-07)
- Uma solicitação **não pode ser aprovada pelo próprio solicitante**
- Aprovação por: usuário com perfil hierarquicamente superior, OU gestor/administrador autorizado previamente definido
- **O aprovador deve ser sempre pessoa distinta do solicitante**
- Exemplo: Solicitante = Consultor; Aprovador permitido = Gestor, Coordenador ou Administrador

### 6. Registro obrigatório da divergência
O sistema deve registrar:
- Data oficial
- Data informada
- Quantidade de dias de divergência
- Justificativa apresentada
- Solicitante
- Aprovador responsável
- Data e horário da decisão
- Resultado da solicitação

### 7. Exemplos (para o critério de aceite)
- **Exemplo 1 — Aprovado:** remarcação com falha técnica, divergência 1 dia, justificativa comprovada, aprovado pelo gestor → exceção registrada, processo segue
- **Exemplo 2 — Rejeitado:** data diferente, divergência 3 dias, justificativa "foi apenas um engano" sem evidência → rejeitado, caso bloqueado/pendente de correção
- **Exemplo 3 — Sem aprovador:** aprovador principal ausente → substituto → gestor/administrador; sem responsável, status "Aguardando definição de aprovador"; sistema notifica; nenhuma aprovação automática pela ausência