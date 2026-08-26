# Estado atual — Adapta Cliente

- task_id: F1-T05
- champion: Luis Carlos - CTO
- spec: 04_fase-atual/specs/spec-f1-002-estados-excecoes-e-idempotencia.md §BLOQUEIO-F1-002-B
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Análise profunda da task F1-T05 concluída
- proxima_acao: Aguardar autorização do champion para documentar política de exceção
- atualizado_em: 2026-08-26T12:30:00-03:00

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