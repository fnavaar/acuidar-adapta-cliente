# Mapa de fontes, latência, destino e RLS do painel de cobertura

**Aprovado por:** Champion (Luis Carlos - CTO) — exerce os papéis de Administrador do Portal e Responsável técnico
**Data:** 2026-09-30
**Task:** F1-T08 · **SPEC:** SPEC-1-003 §BLOQUEIO-F1-003-B e BLOQUEIO-F1-003-C

## 1. Destino do painel (BLOQUEIO-F1-003-C)

- **Destino autorizado:** a **intranet Adapta Cliente** (projeto Skip 51740) — emenda de arquitetura de 2026-09-30 (`06_notas/emenda-arquitetura-intranet.md`).
- **Natureza:** visão **somente leitura** construída sobre o banco local de ocorrências + fontes de leitura externas. O painel **não escreve em nenhuma origem** (sem createRule/updateRule/deleteRule na visão; sem escrita nas APIs dos Portais).
- **Dono do deploy:** **Luis Carlos - CTO via Builder/MCP** — mesmo padrão autorizado na F1-T03 (`06_notas/autorizacao-superficie-tecnica.md`).

## 2. Mapa de fontes (BLOQUEIO-F1-003-B)

| Fonte | Dados que fornece | Campos-chave | Latência aprovada | Dono | Credencial | Timestamp |
|---|---|---|---|---|---|---|
| **Ocorrências (intranet)** | ocorrências + estados da SPEC-1-002 (banco local PocketBase) | id, portal_unit_id, empresa, estado, data_fato, timestamps | **tempo real** (banco local) | — (interna) | — | `created`/`updated` nativos |
| **Unidades Acuidar** | cadastro de franquias Acuidar | codigo (chave oficial), nome, razao_social, cnpj, cidade, estado | **diária** | Luis Carlos - CTO | `ACUIDAR_PORTAL_TOKEN` (Secrets do Skip) | registrada a cada busca diária |
| **Unidades Dona Help** | cadastro de franquias Dona Help | codigo (chave oficial), mesmos 15 campos da Acuidar | **diária** | Luis Carlos - CTO | `DONAHELP_PORTAL_TOKEN` (Secrets do Skip) | registrada a cada busca diária |
| **Reuniões** | reuniões elegíveis por unidade | source_system, source_meeting_id, unidade, data/hora, status | **no ato do registro** (entrada assistida manual) | consultores designados | — | data/hora do registro obrigatória (RN-1-17) |

**Notas do contrato das fontes de unidades:**
- Acuidar: `GET https://app.acuidarbr.com.br/api/dados/unidades` — HTTP 200 com wrapper `{status, message, dados:[...]}`; 172 unidades; códigos únicos (F1-T01).
- Dona Help: `GET https://app.donahelpbr.com.br/api/dados/unidades` — HTTP 200 com **array direto** (sem wrapper); 45 unidades; códigos únicos; mesmos 15 campos (validado 2026-09-30 — `06_notas/sinal-multiempresa-dona-help.md`).
- **Parser por empresa:** detectar wrapper (Acuidar) vs array direto (Dona Help). Credenciais somente via Secrets do Skip, nunca em arquivo ou log.
- **Multiempresa:** separação por campo `empresa` (decisão do Champion, 2026-09-30); o painel terá filtro/visão por empresa.

## 3. RLS de leitura do painel (BLOQUEIO-F1-003-C)

- **Quem consulta:** **todos os perfis autenticados** (consultor, gestor, administrador) — consistente com a permissão `consultar` da matriz F1-T04 (`06_notas/matriz-perfis-rls.md`).
- **O painel não escreve:** nenhum perfil cria, edita ou exclui dados através do painel; correções seguem o fluxo de registro da SPEC-1-001/1-002.
- **Testabilidade:** prova com as 3 contas de teste da F1-T04 (leitura positiva para os 3 perfis; negação de escrita; negação de acesso não autenticado).
- **Multiempresa na RLS:** visibilidade de dados por empresa segue a mesma governança (mesmo champion); filtro por empresa na UI, sem restrição de perfil entre as empresas nesta fase.

## 4. Latência e indisponibilidade (RN-1-14)

- Fonte fora da latência aprovada ou sem timestamp → o item aparece como `dados_indisponiveis` no painel, com fonte e timestamp da última busca bem-sucedida — nunca contabilizado como cobertura ou pendência.
- A busca diária de unidades registra data/hora da última consulta por empresa; falha não sobrescreve o último dado válido, apenas marca a indisponibilidade.

## 5. O que fica para a leva técnica (fora desta task)

- Conector automático do Google Agenda (decisão do Champion: entrada assistida manual na fase 1; conector é construção nova da leva técnica).
- Implementação da UI do painel (a leva técnica constrói a visão conforme este mapa e as regras da SPEC-1-003).
- Remoção do hook temporário `validar-contrato-donahelp` (Skip) após formalização — pendência de limpeza registrada.