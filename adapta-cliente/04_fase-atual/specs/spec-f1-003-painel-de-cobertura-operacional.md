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

## Decisões do Champion aprovadas (F1-T07, 2026-09-22)

> Política de datas, elegibilidade e estados aprovada pelo Champion (Luis Carlos - CTO) e gravada em `06_notas/politica-datas-elegibilidade-estados.md`. Resolve o BLOQUEIO-F1-003-A.

### 1. Janela de análise
- Análise **por mês e separadamente para cada unidade**; apuração mensal por unidade, sem misturar unidades.

### 2. Reunião elegível
- São elegíveis **todas as reuniões registradas no sistema**, independentemente do estado: cadastradas, em andamento, concluídas, canceladas, remarcadas e excluídas.
- Nenhuma reunião elegível é perdida ou descartada da análise por seu estado; o registro permanece rastreável.

### 3. Definição operacional dos 6 estados
| Estado | Condição aprovada |
|---|---|
| `reuniao_pendente` | Reunião existe no Google Calendar, é elegível, mas ainda não foi concluída. |
| `relato_pendente` | Reunião concluída normalmente, mas o resumo/relato ainda não foi cadastrado. |
| `ocorrencia_pendente` | Reunião cancelada ou reagendada exige ocorrência e ela ainda não foi cadastrada. |
| `ocorrencia_incompleta` | Reunião cancelada/reagendada com ocorrência cadastrada, porém faltam informações obrigatórias. |
| `ocorrencia_confirmada` | Reunião cancelada/reagendada com ocorrência completa, com todos os campos obrigatórios preenchidos e válidos. |
| `dados_indisponiveis` | Não foi possível obter informações suficientes para identificar a situação da reunião (ex.: API não retornou dados). |

### 4. Regra para canceladas e remarcadas
- Canceladas/remarcadas **continuam elegíveis** e permanecem na análise de cobertura.
- Direcionadas ao fluxo de ocorrência: sem ocorrência → `ocorrencia_pendente`; com ocorrência incompleta → `ocorrencia_incompleta`; com ocorrência completa → `ocorrencia_confirmada`.
- A reunião original permanece rastreável mesmo com nova data de remarcação.

### 5. Regra para `dados_indisponiveis`
- Não significa que a reunião deixou de existir ou deve ser descartada; permanece elegível e é identificada separadamente.
- O sistema **não infere nem preenche informações ausentes por suposição**.

### 6. Exclusão da contagem
- **Sem timestamp válido (data/hora do registro):** a reunião **não entra na contagem mensal** — não é possível determinar o período.
- **Com timestamp válido e ocorrência incompleta:** a reunião **continua na contagem** como elegível; a ocorrência permanece `ocorrencia_incompleta` (nunca confirmada).

### 7. Campos obrigatórios da ocorrência
- `ocorrencia_confirmada` exige todos os campos obrigatórios preenchidos e válidos; qualquer campo ausente, inválido ou incompleto → `ocorrencia_incompleta`.
- A lista de campos obrigatórios é regra fixa do sistema, aplicada de forma padronizada a todas as unidades.

### 8. Regra geral de não perda
- Toda reunião com data e horário válidos permanece registrada e rastreável, independentemente do status; sem informação suficiente → `dados_indisponiveis`, nunca classificação por suposição.

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
| RN-1-16 | janela de análise | apuração por mês e por unidade, separadamente | nenhuma mistura de unidades | Champion F1-T07 |
| RN-1-17 | reunião sem timestamp válido | fora da contagem mensal | nenhuma | Champion F1-T07 |
| RN-1-18 | reunião cancelada/remarcada | permanece elegível; segue fluxo de ocorrência (pendente → incompleta → confirmada) | nunca eliminada da base | Champion F1-T07 |
| RN-1-19 | informação insuficiente | `dados_indisponiveis`, sem inferência ou suposição | nenhuma | Champion F1-T07 |

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

- [x] Estados, janela e regra de reunião elegível foram aprovados pelo Champion (F1-T07, 2026-09-22).
- [x] Fontes, campos, latência e acessos de leitura foram documentados pelo administrador (F1-T08, 2026-09-30 — `06_notas/mapa-fontes-painel.md`).
- [x] Destino do painel e RLS foram autorizados (F1-T08, 2026-09-30 — intranet Skip 51740; leitura para todos autenticados; deploy Luis Carlos via Builder/MCP).
- [x] Dados completos, incompletos e indisponíveis foram demonstrados (LT-1-T04 2026-10-02 — 12 provas na implementação + 9 na revalidação do zero: mês sem dados mantém elegibilidade, incompleta nunca confirmada, dados_indisponiveis com fonte+timestamp).
- [x] A visão não escreve nas fontes e não exibe Health Score (LT-1-T04 — POST negado 404; zero termos de score na resposta e na tela, provado por varredura; F1-T08 — RLS somente leitura autorizada).

## Critérios de aceite

- [x] **CA-1-11:** o Champion localiza uma unidade de teste em cada estado aprovado e abre sua evidência de origem. *Evidência: LT-1-T04 (2026-10-02) — tabela com badges por estado e link "ver na fila" como evidência de origem; prova de navegador aprovada pelo champion.*
- [x] **CA-1-12:** fonte ausente, atrasada ou sem timestamp aparece como `dados_indisponiveis`, sem ser contabilizada como cobertura ou pendência. *Evidência: LT-1-T04 (2026-10-02) — dados_indisponiveis com fonte+timestamp (RN-1-14); o caminho de erro funcionou como projetado na prova comparativa com o proxy da LT-1-T02.*
- [x] **CA-1-13:** uma ocorrência sem obrigatórios é exibida como incompleta e não como confirmada. *Evidência: LT-1-T04 (2026-10-02) — confirmada exige todos os obrigatórios (RN-1-12/13); prova "incompleta nunca confirmada" na revalidação do zero.*
- [x] **CA-1-14:** a visão é somente leitura e respeita a RLS aprovada. *Evidência: LT-1-T04 (2026-10-02) — POST negado 404, RLS 3 perfis 200 / sem auth 401; LT-1-T06 — RLS por empresa estendida ao painel (consultora vê só a sua empresa).*
- [x] **CA-1-15:** não há cálculo, rótulo, ranking, peso ou faixa de Health Score no painel da fase 1. *Evidência: LT-1-T04 (2026-10-02) — varredura provou zero termos de score na resposta do hook e na tela; rótulos aprovados "Cobertura operacional"/"Qualidade do registro".*

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
- **Pendência conhecida:** nenhuma — bloqueios A, B e C resolvidos (F1-T07, F1-T08).

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F1-T07 | Aprovar semântica da cobertura operacional | Champion | §Contexto — BLOQUEIO-F1-003-A | janela, elegibilidade e seis estados definidos | §Dados; CA-1-11 a CA-1-13 | decisão com exemplos | amostra mínima | ✅ concluída (2026-09-22) |
| F1-T08 | Documentar fontes, latência, destino e RLS do painel | Responsável técnico do cliente | §Contexto — BLOQUEIO-F1-003-B/C | fonte, campo, latência, destino e RLS autorizados | §Dados; CA-1-12, CA-1-14 | mapa e autorização sem segredo | responsável autorizado | ✅ concluída (2026-09-30) — mapa em `06_notas/mapa-fontes-painel.md` |

## Emendas

<!-- Append-only (D19): mudanças aprovadas depois da geração. A história não é reescrita. -->

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
| 2026-09-22 | Champion (Luis Carlos) | F1-T07 | Aprovação da janela de análise (mensal por unidade), elegibilidade (todas as reuniões) e definição dos 6 estados; resolve BLOQUEIO-F1-003-A |
| 2026-09-30 | Champion (Luis Carlos) | Emenda de arquitetura | O painel é construído na intranet (projeto Skip "Adapta Cliente"); as ocorrências residem no banco da própria intranet; o Portal Acuidar permanece como fonte somente leitura de franquias/dados cadastrados. Ver `06_notas/emenda-arquitetura-intranet.md`. |
| 2026-09-30 | Champion (Luis Carlos) | F1-T08 | Mapa de fontes, latência, destino e RLS aprovado em `06_notas/mapa-fontes-painel.md`: ocorrências (tempo real, banco local), unidades Acuidar (diária, 172), unidades Dona Help (diária, 45, array direto), reuniões (entrada assistida manual na fase 1; conector Google Agenda fica para a leva técnica); destino = intranet Skip 51740; RLS = todos autenticados, somente leitura; deploy = Luis Carlos via Builder/MCP; multiempresa por campo `empresa`. Resolve BLOQUEIO-F1-003-B e BLOQUEIO-F1-003-C. |
| 2026-10-02 | Champion (Luis Carlos) | LT-1-T05 — Formalização multiempresa | Multiempresa Dona Help formalizada no painel: filtro por empresa na tela `/painel` (Acuidar 174 unidades, Dona Help 55 — dados vivos de 2026-10-02); agregação por unidade × mês separada por empresa (RN-1-16); parser por formato de API (wrapper Acuidar / array direto Dona Help); `dados_indisponiveis` por empresa com fonte+timestamp (RN-1-14). Implementado na LT-1-T04 (Skip v0.0.22). |