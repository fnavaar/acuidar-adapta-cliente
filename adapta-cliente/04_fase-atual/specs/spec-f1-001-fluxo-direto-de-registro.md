# SPEC-1-001 — Fluxo direto de registro de acompanhamento

**Fase:** 1  
**Status:** bloqueada  
**Dono:** Cliente/administrador do Portal Acuidar (contrato e acesso); Champion (validação); executor Ethos (após desbloqueio)  
**Origem no escopo:** Fase 1; DC-002, DC-003, DC-004; RQ-001 a RQ-005 e RQ-008  
**Degrau da solução:** construção mínima — a rota direta foi aprovada, mas o workspace não contém recurso nativo ou dependência já instalada que implemente a interface única e o adaptador da API.

## Contexto e decisões fechadas

- **Estado atual:** o consultor abre a reunião, copia o relato e cria manualmente uma ocorrência no Portal Acuidar. O vídeo confirma os campos data, horário, tipo, título e relato, além da aba Ocorrências.
- **Estado desejado:** uma reunião elegível é aberta em uma única superfície de revisão, com unidade, dados sugeridos e relato; após confirmação humana, a ocorrência é criada no Portal e a confirmação exibe o identificador retornado pelo destino.
- **Decisões já fechadas:** integração direta é a rota preferida; a data do fato é a data da reunião; o Champion/consultor designado confirma antes da gravação; não há Health Score nesta entrega; fallback assistido não pode escrever duas vezes.
- **Bloqueios:** **BLOQUEIO-F1-001-A** — antes de qualquer código ou conexão, o administrador do Portal deve entregar em canal seguro: base URL/ambiente permitido, especificação de leitura e criação de ocorrência, método de autenticação, escopos, códigos de erro, limites, identificador retornado e conta de teste. **BLOQUEIO-F1-001-B** — Champion deve registrar a chave oficial da unidade e a regra de elegibilidade da reunião, incluindo multiunidade, cancelamento e remarcação. **BLOQUEIO-F1-001-C** — o repositório/superfície de implantação autorizada não está identificado no plano; o Ethos não pode criar uma aplicação ou publicar uma integração até receber esse destino.

## Decisões do Champion aprovadas (F1-T02, 2026-08-25)

> Decisões registradas pelo Champion (Luis Carlos - CTO) e gravadas em `.adapta-cliente/estado-atual.md` e `changelog.md`. Resolvem o BLOQUEIO-F1-001-B.

### 1. Chave oficial da unidade
- **Chave principal:** o **código da unidade** fornecido pelo banco de dados do sistema oficial (ex.: `1547`).
- **Regra:** o código identifica, relaciona e contabiliza todas as reuniões da unidade. O **nome** da unidade **não** é identificador único, pois pode sofrer variações de escrita ("Acuidar JP Centro", "João Pessoa Centro", "Unidade João Pessoa", erros de digitação).
- **Fonte:** API do sistema oficial (a fornecer pelo Champion), que contém o cadastro das unidades (código + nome oficial).
- **Exemplo:** reuniões "Consultoria | Acuidar JP Centro", "Reunião de acompanhamento — João Pessoa Centro" e "Consultoria | Unidade 1547" devem todas ser vinculadas ao código `1547` (Acuidar João Pessoa Centro).

### 2. Regra de elegibilidade
- **Regra:** **todas** as reuniões devem ser registradas, independentemente do status: agendadas, remarcadas/reagendadas, canceladas e concluídas.
- **Exceção:** nenhuma — nenhuma reunião pode ser excluída ou ignorada por ter sido cancelada ou remarcada.
- **Métricas:** todas as reuniões entram no histórico e nas métricas (ex.: total 5 → agendadas 2, remarcadas 1, canceladas 1, concluídas 1).

### 3. Tratamento de multiunidade
- **Regra padrão:** cada reunião pertence a **uma única unidade** (um vínculo reunião → unidade).
- **Exceção:** eventos **Café com Franqueados**, **Day Fusion** e demais eventos multiunidade a definir pelo Champion podem ser vinculados a **múltiplas unidades** participantes.
- **Regra:** uma única reunião/evento é vinculada e contabilizada para todas as unidades participantes, mas continua sendo **uma** reunião (não vira várias).
- **Exemplo:** "Café com Franqueados — Unidades da Paraíba" → vínculos `1547`, `1620`, `1743`.

### 4. Tratamento de cancelamento
- **Regra:** uma reunião cancelada **nunca** é excluída, apagada ou removida do histórico.
- **Status:** permanece registrada com status `Cancelada` + **motivo** do cancelamento.
- **Métrica:** continua a existir e entra na contagem de reuniões canceladas.
- **Exemplo:** reunião ID 8001 cancelada → mantém registro com status `Cancelada`, motivo "Franqueado indisponível".

### 5. Tratamento de remarcação
- **Regra:** o registro original **não** desaparece, é excluído ou substituído. Permanece no histórico com status `Remarcada` + **motivo**.
- **Novo agendamento:** a nova data/hora gera um novo registro vinculado ao original, sempre que a relação for identificada de forma **confiável**.
- **Regra de vínculo:** o sistema **não** presume remarcação por semelhança de nome, unidade ou participantes — só vincula com identificação confiável.
- **Métricas:** cada remarcação é contabilizada individualmente; a sequência é preservada.
- **Exemplo:** 9001 (Remarcada, viajando) → 9002 (Remarcada, conflito) → 9003 (Agendada/Concluída).

## Resultado observável

Em ambiente de teste autorizado, o Champion demonstra uma reunião elegível: seleciona a unidade única, revisa data, horário, tipo, título e relato, confirma a operação e recebe o identificador da ocorrência criada no Portal. Reenvio da mesma reunião não cria segunda ocorrência; falha de sessão ou do destino nunca é exibida como sucesso.

## Limites e dependências

- **Inclui:** fila ou seleção de uma reunião elegível; leitura autorizada da unidade; revisão humana; pré-preenchimento; confirmação; criação direta no Portal; comprovante do destino; preservação do rascunho em falha.
- **Fora de escopo:** geração de relatório por IA, cálculo de Health Score, comunicação ao franqueado, histórico avançado, mudança no cadastro mestre de unidades e distribuição de credenciais.
- **Entradas e pré-condições:** contrato e conta de teste de `BLOQUEIO-F1-001-A`; chave oficial e regras de `BLOQUEIO-F1-001-B`; uma reunião de teste com identificador de origem, data/hora e relato autorizado; superfície autorizada de `BLOQUEIO-F1-001-C`.
- **Saídas/artefatos:** ocorrência confirmada com ID do Portal; estado do fluxo; registro de tentativa sem segredo; roteiro de demonstração e evidência dos testes.
- **Dependências e responsáveis:** administrador do Portal entrega contrato/acesso; Champion valida unidade, tipos e resultado; Ethos constrói somente no destino autorizado.
- **Atores e permissões mínimas:** Talent Agent ou consultor designado pode revisar e solicitar criação; a conta técnica tem somente leitura de unidade e criação de ocorrência no ambiente aprovado; perfil superior aprova exceção conforme SPEC-1-002.
- **Superfícies/arquivos/configurações afetadas:** repositório e ambiente ainda não identificados — este é `BLOQUEIO-F1-001-C`; a integração só pode receber segredos por gerenciador aprovado, nunca por arquivo, tarefa ou log.
- **Risco e plano B:** contrato, sessão ou escrita do Portal podem falhar. Plano B: o mesmo rascunho revisado é entregue ao usuário em modo assistido, sem chamada de criação; após ele registrar manualmente, informa o ID do Portal para encerrar o item. Não haverá segunda tentativa automática.
- **Rollback ou reversão:** antes de confirmação do Portal, descartar somente o rascunho local mediante ação explícita; após confirmação, não excluir/alterar a ocorrência pela integração. Correção posterior segue o Portal e a política aprovada.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Reunião de origem → superfície de revisão | Google Agenda ou entrada assistida autorizada | `source_system`, `source_meeting_id`, data/hora, relato e referência da unidade; o relato é input não confiável e só pode ser transmitido na representação explicitamente permitida pelo contrato do Portal, sem HTML externo ou execução de conteúdo; sem `source_meeting_id`, o item não entra na rota direta | leitura somente com escopo aprovado | não reprocessar o mesmo `source_system + source_meeting_id`; origem indisponível mantém `pendente` | informar origem indisponível e permitir retomada, sem inventar reunião |
| Portal: unidade | Portal | **chave oficial = código da unidade** (BD), razão social e CNPJ somente para conferência; o campo exato depende do contrato aprovado | leitura de unidade no escopo mínimo | consulta com timeout e sem retry automático após erro de autorização | zero ou múltiplas unidades gera `aguardando_correcao` |
| Superfície de revisão → Portal: ocorrência | Portal | unidade (código), data do fato, horário, tipo, título, relato, `idempotency_key` e ID retornado; nomes de payload só serão codificados após contrato | criação de ocorrência no escopo mínimo | uma única chamada por chave; timeout ambíguo gera `possivel_duplicidade`, nunca retry cego | retorno sem ID, erro HTTP ou sessão expirada gera `falha_de_gravacao` |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-1-01 | unidade não é única | bloquear confirmação e abrir correção | nenhuma gravação direta | RQ-002; Fase 1 |
| RN-1-02 | data do fato difere da data da reunião | bloquear ou enviar à aprovação de exceção da SPEC-1-002 | somente aprovação auditável | RQ-004, RQ-005; DC-004 |
| RN-1-03 | falta unidade, data, horário, tipo, título ou relato | manter em `aguardando_correcao` | nenhuma | RQ-005 |
| RN-1-04 | Portal retorna ID de criação | marcar `confirmado` e mostrar comprovante | nenhuma | Fase 1 |
| RN-1-05 | resposta não confirma criação | manter estado explícito e não declarar sucesso | usar conferência por chave antes de qualquer nova ação | RQ-005, RQ-008 |
| RN-1-05A | relato contém formato não permitido pelo contrato do Portal | bloquear envio e manter o texto revisável, sem conversão silenciosa | usuário ajusta o conteúdo na representação autorizada | revisão de risco F1 |
| RN-1-06 | reunião elegível (qualquer status) | registrar a reunião; nenhuma excluída ou ignorada | nenhuma — todas as reuniões registradas | Champion F1-T02 |
| RN-1-07 | reunião multiunidade (Café com Franqueados, Day Fusion, eventos definidos) | vincular a mesma reunião a todas as unidades participantes | regra padrão = 1 reunião → 1 unidade | Champion F1-T02 |
| RN-1-08 | reunião cancelada | manter registro com status `Cancelada` + motivo; nunca excluir | nenhuma — continua nas métricas | Champion F1-T02 |
| RN-1-09 | reunião remarcada | manter original com status `Remarcada` + motivo; criar novo registro vinculado quando identificação confiável | não vincular por semelhança de nome/unidade | Champion F1-T02 |

## Fluxo e regras

1. O usuário abre um item de reunião com `source_system` e `source_meeting_id` válidos.
2. O sistema consulta a unidade pela chave oficial (código da unidade); zero ou múltiplos resultados interrompem o fluxo em `aguardando_correcao`.
3. O formulário apresenta os dados de origem como sugestões editáveis; a data inicial é a data do fato, não a data corrente.
4. O usuário revisa relato e todos os campos obrigatórios; o sistema valida presença e divergência de data.
5. O sistema valida que o relato está na representação autorizada pelo contrato do Portal; o usuário então confirma explicitamente a criação usando a chave da SPEC-1-002.
6. Somente resposta com identificador do Portal muda o item para `confirmado`; o ID integra o comprovante.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | reunião identificada, unidade única e dados completos | uma ocorrência com ID do Portal | guardar comprovante |
| Limite | data divergente com justificativa aprovada | ocorrência criada com trilha da exceção | registrar aprovador e motivo |
| Falha | sessão expirada, timeout, resposta sem ID ou relato em formato não permitido | item não é confirmado | preservar rascunho; autenticar, ajustar conteúdo ou conferir duplicidade antes de nova ação |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** este arquivo; `02-Escopo-Definitivo.md` seções 3, 5 (Fase 1) e 6; `requisitos.md` RQ-001 a RQ-005 e RQ-008; contrato técnico entregue pelo administrador.
2. **Alterar somente:** o repositório, ambiente e configuração explicitamente autorizados após `BLOQUEIO-F1-001-C`.
3. **Não alterar:** Portal em produção, cadastro mestre, políticas de credenciais, regras de Health Score, comunicação ou permissões fora do escopo concedido.
4. **Executar nesta ordem:** validar acesso de leitura; validar criação no ambiente de teste; implementar revisão; implementar confirmação e estados; provar os cenários do TDD; solicitar demonstração humana.
5. **Parar e pedir validação quando:** qualquer bloqueio permanecer, o contrato divergir da SPEC, houver unidade ambígua, relato em formato não permitido, escopo de permissão exceder o mínimo, ou uma resposta de criação não trouxer ID verificável.
6. **Estado válido ao parar:** nenhum segredo persistido; nenhum item marcado como `confirmado` sem ID do Portal; rascunhos falhos recuperáveis e sem nova escrita automática.

## Checklist de execução

- [x] Chave oficial da unidade e regras de elegibilidade foram registradas pelo Champion (F1-T02, 2026-08-25).
- [ ] Contrato, ambiente e conta de teste do Portal foram entregues e validados sem expor segredo.
- [ ] Superfície de implantação foi autorizada.
- [ ] Fluxo principal, unidade ambígua, formato de relato inválido, sessão expirada, falha de destino e reenvio foram exercitados.
- [ ] Evidências contêm IDs mascarados, logs sem segredo e roteiro de demonstração.

## Critérios de aceite

- [ ] **CA-1-01:** uma reunião de teste elegível cria exatamente uma ocorrência no Portal e mostra seu ID retornado.
- [ ] **CA-1-02:** unidade ausente ou ambígua não chama a criação no Portal.
- [ ] **CA-1-03:** data divergente não é gravada sem correção ou aprovação rastreável da SPEC-1-002.
- [ ] **CA-1-04:** timeout, sessão expirada ou resposta sem ID não produz mensagem de sucesso e preserva um caminho de retomada.
- [ ] **CA-1-05:** o reenvio do mesmo `source_system + source_meeting_id` não cria segunda ocorrência.
- [ ] **CA-1-05A:** relato fora da representação autorizada não é enviado nem convertido silenciosamente.
- [ ] **CA-1-06 (Champion):** todas as reuniões (agendadas, remarcadas, canceladas, concluídas) são registradas; cancelamento nunca remove o registro e mantém motivo.
- [ ] **CA-1-07 (Champion):** reunião multiunidade (Café com Franqueados, Day Fusion, eventos definidos) é vinculada às unidades participantes como uma única reunião.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | criação sem unidade única, relato inválido e criação sem ID retornado | executar fixtures autorizadas no ambiente de teste | todos falham sem chamada válida ou sem estado `confirmado` | log sanitizado e captura do estado |
| GREEN | reunião válida e unidade única | executar o roteiro de criação no ambiente de teste | uma chamada, um ID e estado `confirmado` | ID mascarado, registro do Portal e captura da revisão |
| REFACTOR/REGRESSÃO | reenvio, timeout e sessão expirada | repetir fixtures com a mesma chave e simular respostas | nenhuma duplicidade ou sucesso falso; rascunho recuperável | log sanitizado, estado final e roteiro assinado |

**Dados/fixtures:** uma unidade de teste, uma reunião com `source_meeting_id` estável, relato não sensível e conta de menor privilégio.  
**Caminhos de erro obrigatórios:** unidade zero/múltipla, campo obrigatório ausente, formato de relato inválido, data divergente, 401/403, timeout, 5xx e retorno sem ID.  
**Evidência exigida:** contrato de integração aprovado, captura da ocorrência no Portal, logs sanitizados por cenário e confirmação humana do Champion.

## Handoff e operação

- **Como demonstrar:** selecionar reunião de teste, revisar, confirmar, abrir a ocorrência retornada pelo ID e repetir o envio para provar idempotência.
- **Como operar depois:** Champion revisa e confirma; administrador do Portal mantém os acessos; erros entram na fila com estado explícito.
- **Como monitorar:** contagem por estado (`pendente`, `aguardando_correcao`, `falha_de_gravacao`, `possivel_duplicidade`, `confirmado`) e amostra semanal de comprovantes.
- **Pendência conhecida:** dois bloqueios pendentes (A e C); não iniciar implementação enquanto qualquer um persistir.

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 2026-08-25 | Champion (Luis Carlos) | F1-T02 | Registro da chave oficial, elegibilidade, multiunidade, cancelamento e remarcação; resolve BLOQUEIO-F1-001-B |