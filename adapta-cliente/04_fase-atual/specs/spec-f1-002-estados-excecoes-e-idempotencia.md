# SPEC-1-002 — Estados, exceções auditáveis e idempotência

**Fase:** 1  
**Status:** bloqueada  
**Dono:** Champion (regra e veredito); usuário de maior patente (aprovação); administrador do Portal (matriz de perfis); executor Ethos (após desbloqueio)  
**Origem no escopo:** Fase 1; DC-004 e DC-005; RQ-002, RQ-004, RQ-005 e RQ-008  
**Degrau da solução:** construção mínima — o fluxo requer persistência de estados e trilha de auditoria não demonstradas como recurso nativo aprovado do Portal.

## Contexto e decisões fechadas

- **Estado atual:** o fluxo manual observado não prova tratamento de duplicidade, falha ou exceção; a sessão pode expirar durante o registro.
- **Estado desejado:** cada item tem estado explícito, uma chave idempotente estável, motivo auditável e retomada segura; somente perfil superior aprova uma exceção.
- **Decisões já fechadas:** exceção manual é permitida com justificativa e aprovação de usuário de maior patente; Champion consulta e edita dentro da matriz aprovada; duplicidade e falha não podem produzir sucesso falso.
- **Bloqueios:** **BLOQUEIO-F1-002-A** — Champion/cliente deve registrar a matriz nominal de perfis ou grupos e as permissões `consultar`, `editar rascunho`, `solicitar exceção`, `aprovar exceção` e `confirmar criação`. **BLOQUEIO-F1-002-B** — deve definir se uma divergência de data bloqueia sempre ou pode receber exceção, o prazo de decisão e o destino de uma solicitação sem aprovador disponível. **BLOQUEIO-F1-002-C** — o contrato do Portal deve confirmar se há consulta por chave de origem ou como localizar ocorrência após timeout; sem essa prova, nenhuma política de retry é segura.

## Decisões do Champion aprovadas (F1-T05, 2026-08-28)

> Política de exceção de data aprovada pelo Champion (Luis Carlos - CTO) e gravada em `06_notas/politica-excecao-de-data.md`. Resolve o BLOQUEIO-F1-002-B.

### 1. Regra de aprovação (CA-1-07)
- Solicitação **não pode ser aprovada pelo próprio solicitante**.
- Aprovação por: usuário com perfil **hierarquicamente superior**, OU **gestor/administrador autorizado** previamente definido.
- O aprovador deve ser **sempre pessoa distinta** do solicitante. Ex.: Solicitante = Consultor; Aprovador = Gestor, Coordenador ou Administrador.

### 2. Quando a divergência de data bloqueia
| Diferença | Comportamento |
|---|---|
| Mesma data | Aprovado normalmente |
| Até 1 dia | Solicita justificativa e envia para análise de exceção |
| Superior a 1 dia | Bloqueia o processo automaticamente |

O sistema **não encerra o caso automaticamente**; permanece em **"Pendente de regularização"**.

### 3. Quando admite exceção
- Exceção permitida com **justificativa comprovável**.
- Aceitáveis: erro de preenchimento/registro; remarcação não atualizada corretamente; falha técnica na plataforma/integração; problema de conexão/indisponibilidade comprovada; outro motivo excepcional aprovado pelo responsável.
- **Não aceito:** justificativa genérica ("esqueci de atualizar", "foi um engano") sem explicação clara.

### 4. Prazo de decisão da exceção
- Responsável responde em **até 24 horas úteis**.
- Durante o prazo: status **"Pendente de aprovação"**; processo bloqueado para conclusão definitiva.
- Após o prazo: **lembrete automático** ao aprovador.
- Sem resposta após **48 horas úteis**: **escalada automática** a responsável superior ou administrador.

### 5. Destino da solicitação sem aprovador disponível
- Aprovador principal indisponível → **aprovador substituto** predefinido.
- Hierarquia: **Aprovador principal → Aprovador substituto → Administrador/Gestor**.
- Nenhum disponível → permanece registrada, status **"Aguardando definição de responsável"**; sistema notifica o administrador.
- **Nenhuma alteração definitiva é aprovada automaticamente pela ausência de um aprovador.**

### 6. Registro obrigatório da divergência
- Data oficial; data informada; quantidade de dias de divergência; justificativa; solicitante; aprovador responsável; data/hora da decisão; resultado da solicitação.

### 7. Exemplos (critério de aceite)
- **Exemplo 1 — Aprovado:** remarcação com falha técnica comprovada, divergência 1 dia, aprovado pelo gestor → exceção registrada, processo segue.
- **Exemplo 2 — Rejeitado:** divergência 3 dias, justificativa "foi apenas um engano" sem evidência → rejeitado, caso bloqueado/pendente de correção.
- **Exemplo 3 — Sem aprovador:** aprovador principal ausente → substituto → gestor/administrador; sem responsável, status "AGUARDANDO DEFINIÇÃO DE APROVADOR"; sistema notifica; nenhuma aprovação automática.

## Resultado observável

O Champion consegue identificar o estado de um registro; solicita uma exceção de data com motivo; somente o aprovador autorizado decide; um timeout gera `possivel_duplicidade`; e a mesma reunião não é criada novamente até existir conferência documentada do destino.

## Limites e dependências

- **Inclui:** máquina de estados, chave idempotente, validação de transição, solicitação/aprovação de exceção, trilha de auditoria e recuperação controlada.
- **Fora de escopo:** conceder permissões no Portal, aprovar exceções automaticamente, editar ou excluir ocorrência confirmada, redefinir RLS global ou comunicar franqueados.
- **Entradas e pré-condições:** matriz de `BLOQUEIO-F1-002-A`, política de `BLOQUEIO-F1-002-B`, mecanismo de consulta de `BLOQUEIO-F1-002-C` e item originado pela SPEC-1-001.
- **Saídas/artefatos:** estado atual, chave idempotente, histórico de transições, solicitação de exceção e decisão com ator/instante/motivo.
- **Dependências e responsáveis:** Champion fecha regras; administrador fornece perfil e consulta de destino; Ethos codifica somente a matriz aprovada.
- **Atores e permissões mínimas:** consultor designado cria/edita rascunho e solicita exceção; Champion consulta/edita conforme matriz; perfil superior aprova/rejeita; nenhum papel altera logs de auditoria.
- **Superfícies/arquivos/configurações afetadas:** a superfície de execução ainda depende de `BLOQUEIO-F1-001-C`; configuração de autorização deve residir somente no mecanismo aprovado pelo cliente.
- **Risco e plano B:** indisponibilidade do aprovador ou do Portal. Plano B: manter `aguardando_aprovacao_de_excecao` ou `possivel_duplicidade`; o Champion resolve manualmente e anexa o ID do Portal sem nova criação automática.
- **Rollback ou reversão:** transição de rascunho pode voltar a `aguardando_correcao`; exceção aprovada e registro confirmado não são apagados, apenas recebem correção posterior conforme Portal e auditoria.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Registro de fluxo | superfície autorizada | `source_system`, `source_meeting_id`, `portal_unit_id`, `occurrence_type`, estado, timestamps | RLS da matriz aprovada | `idempotency_key = SHA-256(source_system + ':' + source_meeting_id + ':' + portal_unit_id + ':' + occurrence_type)`; sem um componente, bloquear criação | item permanece rastreável com motivo |
| Solicitação de exceção | mesma superfície | ID do item, campo divergente, valor origem, valor proposto, motivo, solicitante, aprovador, decisão e instante | solicitar ≠ aprovar; aprovador não é o solicitante | nenhuma criação é tentada enquanto pendente | ausência de aprovador mantém fila explícita |
| Consulta de recuperação | Portal Acuidar | chave de origem ou método documentado pelo Portal para localizar a ocorrência | leitura mínima aprovada | após timeout, consultar antes de permitir ação humana; nunca repetir automaticamente | se consulta inconclusiva, manter `possivel_duplicidade` |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-1-06 | todos os dados validados | `em_revisao` → `confirmado` somente após ID do Portal | nenhuma | Fase 1 |
| RN-1-07 | dado ausente ou unidade ambígua | mover a `aguardando_correcao` | nenhuma | RQ-002, RQ-005 |
| RN-1-08 | data diverge e motivo é submetido | mover a `aguardando_aprovacao_de_excecao` | não permite autoaprovação | DC-004 |
| RN-1-09 | timeout/resposta inconclusiva | mover a `possivel_duplicidade` e consultar destino | nenhuma nova escrita | RQ-005, RQ-008 |
| RN-1-10 | falha comprovada sem criação | mover a `falha_de_gravacao` e permitir retomada humana | reutilizar a mesma chave após confirmação de ausência | Fase 1 |
| RN-1-11 | divergência de data de até 1 dia | solicitar justificativa e enviar para análise de exceção | nenhuma | Champion F1-T05 |
| RN-1-12 | divergência de data superior a 1 dia | bloquear o processo; permanece "Pendente de regularização" | exceção apenas com justificativa comprovável e aprovação | Champion F1-T05 |
| RN-1-13 | exceção sem resposta em 24h úteis | enviar lembrete automático ao aprovador | escalar após 48h úteis | Champion F1-T05 |
| RN-1-14 | aprovador principal indisponível | encaminhar ao substituto predefinido; se nenhum, notificar administrador | nenhuma aprovação automática pela ausência | Champion F1-T05 |

## Fluxo e regras

1. Um item novo nasce em `pendente`; ao ser aberto, entra em `em_revisao`.
2. Campo ausente, unidade ambígua ou informação conflitante leva a `aguardando_correcao`.
3. Divergência de data com pedido válido leva a `aguardando_aprovacao_de_excecao`; rejeição retorna a `aguardando_correcao`.
4. Na confirmação, a chave idempotente é calculada e persistida antes da chamada ao Portal.
5. Resposta com ID gera `confirmado`; falha comprovada gera `falha_de_gravacao`; timeout ou resposta inconclusiva gera `possivel_duplicidade`.
6. Só a consulta de recuperação documentada pelo Portal libera decisão humana de retomar; nenhuma transição reinvoca automaticamente a criação.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | dados completos e retorno com ID | `confirmado` com trilha | exibir comprovante |
| Limite | data divergente e aprovador distinto | decisão auditável antes da criação | sem aprovação, manter pendente |
| Falha | timeout depois da solicitação | `possivel_duplicidade` | consultar Portal; escalar ao Champion se inconclusivo |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** SPEC-1-001; `02-Escopo-Definitivo.md` seções 3, 5 (Fase 1) e 6; `requisitos.md` RQ-002, RQ-004, RQ-005 e RQ-008; matriz de perfis e contrato entregues.
2. **Alterar somente:** modelos, regras e configuração autorizados no repositório/superfície liberada.
3. **Não alterar:** RLS do Portal, contas, credenciais, ocorrências confirmadas ou permissões sem matriz assinada.
4. **Executar nesta ordem:** validar matriz; implementar estados e transições; implementar chave; implementar auditoria; implementar consulta de recuperação; executar TDD; solicitar teste humano.
5. **Parar e pedir validação quando:** perfil ou política não estiver documentado, solicitante e aprovador coincidirem, a consulta pós-timeout for inexistente, ou a chave não puder ser formada.
6. **Estado válido ao parar:** cada item possui estado e histórico; nenhuma exceção autoaprovada; nenhuma repetição automática após timeout.

## Checklist de execução

- [x] Política de exceção de data foi aprovada pelo Champion (F1-T05, 2026-08-28).
- [ ] Matriz de perfis foi aprovada pelo Champion/cliente (BLOQUEIO-F1-002-A).
- [ ] Todas as transições inválidas são recusadas e registradas.
- [ ] A chave idempotente é persistida antes de criar a ocorrência.
- [ ] Timeout resulta em conferência, não em retry cego.
- [ ] Evidência demonstra separação entre solicitante e aprovador.

## Critérios de aceite

- [ ] **CA-1-06:** cada item apresenta exatamente um dos estados aprovados e um histórico imutável de transições.
- [ ] **CA-1-07:** quem solicita uma exceção não consegue aprová-la; decisão registra ator, instante e motivo.
- [ ] **CA-1-08:** duas confirmações do mesmo identificador de origem reutilizam a mesma chave e não criam duas ocorrências.
- [ ] **CA-1-09:** resposta de criação inconclusiva bloqueia nova escrita até consulta de recuperação ou decisão humana documentada.
- [ ] **CA-1-10:** nenhum perfil fora da matriz lê ou altera item, exceção ou trilha de auditoria.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | solicitar aprovação pelo mesmo usuário e transitar de `pendente` a `confirmado` sem ID | executar testes de autorização/transição na superfície liberada | ambas as ações falham | resultado dos testes e log sanitizado |
| GREEN | exceção aprovada por perfil distinto e criação com chave única | executar fixture de reunião válida | transições corretas e uma única criação | trilha de auditoria e ID mascarado |
| REFACTOR/REGRESSÃO | timeout, reenvio e consulta inconclusiva | executar fixture de falha duas vezes | mantém `possivel_duplicidade`, sem segunda escrita | estados, logs sanitizados e decisão humana |

**Dados/fixtures:** três contas de teste com perfis distintos, reunião com ID estável, unidade de teste, divergência de data e respostas simuladas do Portal.  
**Caminhos de erro obrigatórios:** matriz ausente, autoaprovação, transição inválida, chave incompleta, 401/403, timeout e consulta inconclusiva.  
**Evidência exigida:** matriz aprovada, relatório de transições, testes de autorização e captura da consulta de recuperação.

## Handoff e operação

- **Como demonstrar:** solicitar exceção, aprová-la com perfil distinto, confirmar uma ocorrência e simular timeout/reenvio.
- **Como operar depois:** Champion revisa filas e exceções; perfil superior decide exceções; administrador revisa acesso.
- **Como monitorar:** contagem de `aguardando_aprovacao_de_excecao`, `falha_de_gravacao` e `possivel_duplicidade`, além de tentativas de transição negadas.
- **Pendência conhecida:** bloqueios de matriz (A) e consulta de recuperação (C).

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F1-T04 | Formalizar matriz de perfis e RLS do fluxo | Administrador do Portal | §Contexto — BLOQUEIO-F1-002-A | matriz e conta negativa de teste completas | §Checklist; CA-1-10; TDD RED/REGRESSÃO | matriz datada e teste de acesso negado | política de acesso | ☐ aberta |
| F1-T05 | Aprovar política de exceção de data | Champion | §Contexto — BLOQUEIO-F1-002-B | bloqueio, exceção, prazo e ausência de aprovador definidos | §Fluxo 3–6; CA-1-07 | política com três exemplos | Champion designado | ✅ concluída (2026-08-28) |
| F1-T06 | Provar consulta de recuperação após timeout | Administrador do Portal | §Contexto — BLOQUEIO-F1-002-C | consulta pós-timeout comprovada em teste | §Dados; CA-1-09; TDD REGRESSÃO | consulta sanitizada | F1-T01 concluída: contrato e ambiente de teste | ☐ aberta — Leva 2/F1-T01 |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 2026-08-28 | Champion (Luis Carlos) | F1-T05 | Aprovação da política de exceção de data (bloqueio >1 dia, exceção com justificativa comprovável, prazo 24h/48h, aprovador substituto); resolve BLOQUEIO-F1-002-B |