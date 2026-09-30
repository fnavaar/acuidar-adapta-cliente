# Sinal — Multiempresa: Dona Help Franquias

**Registrado em:** 2026-09-30
**Origem:** Champion (Luis Carlos - CTO), mensagem direta
**Status:** sinal registrado — detalhes pendentes de definição

## O que está confirmado

- O mesmo sistema (intranet Adapta Cliente — Skip 51740) atenderá **duas empresas**: Acuidar Franquias e Dona Help Franquias.
- A Dona Help possui **API própria** que precisa ser integrada (análogo ao contrato de leitura da Acuidar validado na F1-T01).

## O que está pendente de definição

- **Momento:** se a integração da Dona Help entra na fase 1, na leva técnica pós-fase 1 ou em fase futura.
- **Contrato:** se a API da Dona Help tem o mesmo formato da Acuidar (`GET /api/dados/unidades`, `Authorization: <token>` puro) ou contrato distinto.
- **Modelo multiempresa:** como as duas empresas convivem no sistema — campo `empresa` separando os dados no mesmo banco vs. instâncias separadas. Impacta SPECs, migrations, matriz de perfis (F1-T04) e o painel (F1-T08).
- **Credencial:** token da Dona Help deve entrar pelos **Secrets do Skip** (chave sugerida: `DONAHELP_PORTAL_TOKEN`), nunca por chat ou arquivo.
- **Governança:** champion/aprovadores do lado da Dona Help e se a matriz de perfis aprovada vale para as duas empresas.

## Regras do método que se aplicam

- Emenda append-only nas SPECs afetadas **somente após** detalhes definidos e aprovados pelo Champion.
- Nenhuma implementação ou migração de dados antes disso.
- Credenciais nunca passam pelo chat (precedentes no projeto: chave Google e token Acuidar passaram pelo chat — rotação recomendada). O endpoint/contrato sem credencial pode ser compartilhado no chat.