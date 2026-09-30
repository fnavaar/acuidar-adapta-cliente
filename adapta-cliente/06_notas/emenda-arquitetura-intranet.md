# Emenda de arquitetura — Intranet como superfície de registro

**Aprovada por:** Champion (Luis Carlos - CTO) · **Data:** 2026-09-30
**Origem:** conversa com o Champion · **Afeta:** SPEC-1-001, SPEC-1-002, SPEC-1-003

## Decisão

1. **A intranet** (projeto Skip "Adapta Cliente", ID 51740) é a superfície onde **todo o fluxo será construído**: registro de reuniões, revisão de relato, criação e armazenamento de ocorrências (banco e identificador próprios), exceções auditáveis e painel de cobertura.
2. O **Portal Acuidar** passa a ser **fonte de dados somente leitura**: fornece as franquias (código da unidade, nome oficial e dados já cadastrados) via API.
3. A **ocorrência é criada e armazenada na intranet** — não no Portal.

## Impacto nos bloqueios

| Bloqueio | Efeito da emenda |
|---|---|
| BLOQUEIO-F1-001-A (F1-T01) | Contrato exigido restringe-se à **leitura de franquias**: base URL/ambiente, endpoint de leitura, autenticação, escopos (somente leitura), erros, limites, formato dos dados e conta de teste de leitura. Não há mais criação de ocorrência no Portal. |
| BLOQUEIO-F1-001-C (F1-T03) | Destino autorizado = **a intranet**. Falta a autorização formal por escrito (repositório/ambiente/segredos). |
| BLOQUEIO-F1-002-C (F1-T06) | A consulta de recuperação pós-timeout passa a ser **local** (banco da intranet); o bloqueio de contrato de escrita do Portal deixa de se aplicar. |
| BLOQUEIO-F1-003-C (F1-T08) | Destino do painel = **a própria intranet**; RLS a definir conforme matriz aprovada. |

## O que NÃO muda

- Todas as regras de negócio aprovadas (F1-T02, F1-T05, F1-T07): chave oficial = código da unidade; elegibilidade total; multiunidade; cancelamento; remarcação; política de exceção de data; semântica dos 6 estados.
- Idempotência, máquina de estados, trilha de auditoria e separação solicitante/aprovador.
- Proibição de Health Score na fase 1.

## Registro

- Emendas registradas nas seções "Emendas" das SPECs 1-001, 1-002 e 1-003 (append-only).
- O corpo original das SPECs é preservado; esta emenda prevalece sobre ele onde houver conflito, até a próxima sincronização com o consultor.
