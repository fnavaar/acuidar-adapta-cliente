# SPEC-1-003 — Painel de cobertura operacional

**Fase:** 1  
**Status:** bloqueada  
**Dono:** Champion (definições e aceite); administrador do Portal (fontes e acesso); executor Ethos (após desbloqueio)  
**Origem no escopo:** Fase 1; DC-001, DC-003 e DC-005; RQ-001, RQ-006, RQ-007 e RQ-009  
**Degrau da solução:** construção mínima — a visão consolidada não foi demonstrada no Portal e nenhum recurso de BI/plataforma aprovado está registrado no workspace.

## Contexto e decisões fechadas

- **Estado atual:** há histórico de ocorrências por unidade no Portal, mas não há visão consolidada demonstrada de reunião, relato e ocorrência; o vídeo evidencia apenas um caso de unidade.
- **Estado desejado:** o Champion vê uma lista por unidade e período com cobertura operacional, pendências e link ao comprovante de ocorrência, sem exibir ou inferir nota de Health Score.
- **Decisões já fechadas:** Scorecard é a métrica norte, mas pesos, faixas e fórmula de Health Score ficam fora; fase 1 entrega somente cobertura/completude; Champion consulta e edita conforme matriz aprovada.
- **Bloqueios:** **BLOQUEIO-F1-003-A** — Champion deve aprovar a janela de análise, a regra de reunião elegível e a definição operacional dos estados `reuniao_pendente`, `relato_pendente`, `ocorrencia_pendente`, `ocorrencia_incompleta` e `ocorrencia_confirmada`. **BLOQUEIO-F1-003-B** — administrador deve identificar fontes autorizadas, campos e latência de atualização para reuniões e ocorrências. **BLOQUEIO-F1-003-C** — o destino do painel e seus controles RLS ainda não foram escolhidos; o Ethos não pode publicar uma dashboard nem conceder acesso.

## Resultado observável

O Champion abre a visão autorizada para um período de teste, identifica uma unidade em cada estado definido e chega ao registro/ocorrência de origem. A tela usa os rótulos “Cobertura operacional” e “Qualidade do registro”; não contém score, peso, faixa de risco ou recomendação de Health Score.

## Limites e dependências

- **Inclui:** visão por unidade/período, estados operacionais aprovados, contagem, timestamp de atualização, filtros aprovados, link para evidência e indicação de dado indisponível.
- **Fora de escopo:** Health Score, ranking de franqueados, alertas automáticos, comunicação, plano de ação, edição de ocorrência confirmada e acesso de gestores não previstos na matriz.
- **Entradas e pré-condições:** regras de `BLOQUEIO-F1-003-A`, fontes de `BLOQUEIO-F1-003-B`, destino/RLS de `BLOQUEIO-F1-003-C` e registros originados pela SPEC-1-001.
- **Saídas/artefatos:** painel autorizado, definição de cada estado, timestamp de atualização e rota para evidência de origem.
- **Dependências e responsáveis:** Champion aprova semântica e aceite; administrador fornece leitura e RLS; Ethos implementa no destino liberado.
- **Atores e permissões mínimas:** Champion consulta a visão; demais leitores só depois de inclusão explícita na matriz aprovada; a visão não permite criação ou confirmação de ocorrência.
- **Superfícies/arquivos/configurações afetadas:** destino do painel ainda não identificado, sob `BLOQUEIO-F1-003-C`; fontes permanecem de leitura e sem credenciais em artefatos.
- **Risco e plano B:** dados atrasados ou incompletos podem ser interpretados como cobertura. Plano B: mostrar “dados indisponíveis” com fonte e timestamp, excluindo o item de qualquer contagem de cobertura.
- **Rollback ou reversão:** desativar a visão ou remover somente o acesso concedido pelo destino aprovado; nunca apagar ocorrências-fonte.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Reuniões elegíveis → painel | Agenda/fonte aprovada | ID da reunião, unidade, data/hora, status da origem e timestamp; campos finais vêm de `BLOQUEIO-F1-003-B` | leitura mínima autorizada | atualização é leitura idempotente; não escrever na origem | fonte indisponível mostra estado de indisponibilidade |
| Ocorrências → painel | Portal Acuidar | ID da ocorrência, unidade, data do fato, estado da SPEC-1-002, completude e timestamp | leitura mínima autorizada | reconciliar por IDs de origem, sem criar dado novo | resposta incompleta não gera `confirmada` |
| Painel → usuário | painel autorizado | unidade, período, estado, motivo, fonte, timestamp e link de evidência | RLS aprovada | sem ação de escrita | acesso negado é explícito e auditável |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-1-11 | reunião elegível sem ocorrência | `ocorrencia_pendente` | origem indisponível vira `dados_indisponiveis` | RQ-001, RQ-007 |
| RN-1-12 | ocorrência sem todos os obrigatórios | `ocorrencia_incompleta` | não contar como cobertura | RQ-005, RQ-007 |
| RN-1-13 | ocorrência confirmada com ID verificável | `ocorrencia_confirmada` | nenhuma | SPEC-1-001 |
| RN-1-14 | fonte sem timestamp ou fora da latência aprovada | `dados_indisponiveis` | não inferir pendência | RQ-007 |
| RN-1-15 | exibição da visão | rotular cobertura/qualidade, nunca Health Score | nenhuma | DC-001; RQ-009 |

## Fluxo e regras

1. O painel recebe somente leituras autorizadas de reuniões e ocorrências.
2. Ele aplica as definições aprovadas por `BLOQUEIO-F1-003-A` e mostra o estado por unidade/período.
3. Todo estado traz fonte e timestamp; ausência ou atraso de fonte é explicitado como indisponibilidade.
4. O Champion filtra a amostra, abre a evidência de uma unidade e confirma que a visão não muda dados-fonte.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | reunião e ocorrência confirmada no período | `ocorrencia_confirmada` com ID de origem | link para comprovante |
| Limite | ocorrência existe mas falta relato | `ocorrencia_incompleta` | abrir item para correção no fluxo de registro |
| Falha | fonte indisponível ou dado atrasado | `dados_indisponiveis`, sem contagem enganosa | exibir fonte/timestamp e acionar administrador |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; SPEC-1-001 e SPEC-1-002; `02-Escopo-Definitivo.md` Fase 1 e riscos; `requisitos.md` RQ-001, RQ-006, RQ-007 e RQ-009; definição de estados e matriz RLS aprovadas.
2. **Alterar somente:** o destino do painel e conectores de leitura autorizados.
3. **Não alterar:** fontes de reunião/Portal, fórmula de Health Score, permissões fora da matriz, comunicação ou qualquer registro operacional.
4. **Executar nesta ordem:** validar semântica dos estados; validar leituras; implementar visão sem escrita; exercitar dados completos/incompletos/indisponíveis; validar RLS; solicitar demonstração humana.
5. **Parar e pedir validação quando:** uma fonte/campo não estiver autorizado, latência não for definida, o painel receber pedido de score/ranking, ou o destino/RLS não estiver liberado.
6. **Estado válido ao parar:** nenhuma escrita nas fontes; dado indisponível é visível; nenhum rótulo de Health Score aparece.

## Checklist de execução

- [ ] Estados, janela e regra de reunião elegível foram aprovados pelo Champion.
- [ ] Fontes, campos, latência e acessos de leitura foram documentados pelo administrador.
- [ ] Destino do painel e RLS foram autorizados.
- [ ] Dados completos, incompletos e indisponíveis foram demonstrados.
- [ ] A visão não escreve nas fontes e não exibe Health Score.

## Critérios de aceite

- [ ] **CA-1-11:** o Champion localiza uma unidade de teste em cada estado aprovado e abre sua evidência de origem.
- [ ] **CA-1-12:** fonte ausente, atrasada ou sem timestamp aparece como `dados_indisponiveis`, sem ser contabilizada como cobertura ou pendência.
- [ ] **CA-1-13:** uma ocorrência sem obrigatórios é exibida como incompleta e não como confirmada.
- [ ] **CA-1-14:** a visão é somente leitura e respeita a RLS aprovada.
- [ ] **CA-1-15:** não há cálculo, rótulo, ranking, peso ou faixa de Health Score no painel da fase 1.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | fixture de fonte sem timestamp e ocorrência incompleta marcada como confirmada | executar testes de transformação/visão no destino aprovado | ambos os casos falham | resultado dos testes e captura da visão |
| GREEN | fixtures uma por estado aprovado | atualizar a visão de teste | cada unidade mostra o estado e o link corretos | captura, IDs mascarados e timestamp |
| REFACTOR/REGRESSÃO | acesso sem permissão, atraso de fonte e tentativa de rótulo Health Score | testar RLS e filtro de campos | acesso negado, indisponibilidade explícita e nenhum score | logs sanitizados e roteiro do Champion |

**Dados/fixtures:** unidades de teste para cada estado, reunião/ocorrência com IDs estáveis, fonte atrasada e perfil sem acesso.  
**Caminhos de erro obrigatórios:** leitura negada, fonte vazia, fonte sem timestamp, latência excedida, ocorrência sem obrigatório e filtro de período sem dado.  
**Evidência exigida:** definição aprovada de estados, captura de cada cenário, prova de RLS e confirmação humana do Champion.

## Handoff e operação

- **Como demonstrar:** filtrar o período de teste, abrir uma unidade por estado, mostrar fonte/timestamp e validar que não há escrita nem Health Score.
- **Como operar depois:** Champion revisa pendências e completude; administrador acompanha disponibilidade das fontes.
- **Como monitorar:** timestamp da última atualização, volume de `dados_indisponiveis`, itens incompletos e acesso negado.
- **Pendência conhecida:** definição operacional, fontes/latência e destino/RLS ainda precisam ser fornecidos.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F1-T07 | Aprovar semântica da cobertura operacional | Champion | §Contexto — BLOQUEIO-F1-003-A | janela, elegibilidade e seis estados definidos | §Dados; CA-1-11 a CA-1-13 | decisão com exemplos | amostra mínima | ☐ aberta |
| F1-T08 | Documentar fontes, latência, destino e RLS do painel | Responsável técnico do cliente | §Contexto — BLOQUEIO-F1-003-B/C | fonte, campo, latência, destino e RLS autorizados | §Dados; CA-1-12, CA-1-14 | mapa e autorização sem segredo | responsável autorizado | ☐ aberta |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
