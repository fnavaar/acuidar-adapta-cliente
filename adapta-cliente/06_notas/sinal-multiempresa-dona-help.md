# Sinal — Multiempresa: Dona Help Franquias

**Registrado em:** 2026-09-30
**Origem:** Champion (Luis Carlos - CTO), mensagem direta
**Status:** decisões aprovadas + endpoint recebido — validação de contrato pendente do token

## O que está confirmado

- O mesmo sistema (intranet Adapta Cliente — Skip 51740) atenderá **duas empresas**: Acuidar Franquias e Dona Help Franquias.
- A Dona Help possui **API própria** que precisa ser integrada (análogo ao contrato de leitura da Acuidar validado na F1-T01).

## Decisões do Champion (2026-09-30)

| Decisão | Escolha do Champion |
|---|---|
| Momento | **Agora, na fase 1** |
| Contrato da API | **Formato diferente da Acuidar** — Champion fornecerá a URL do endpoint |
| Modelo multiempresa | **Mesmo sistema, separação por campo empresa** (uma intranet, dados marcados por empresa) |
| Governança | **Mesmo champion para as duas empresas** (Luis Carlos - CTO) |

## Endpoint recebido (2026-09-30)

- **URL:** `https://app.donahelpbr.com.br/api/dados/unidades` (mesmo caminho da Acuidar, domínio próprio)
- **Sonda sem credencial (executada pelo assistente, sem token):**
  - Sem auth → HTTP 400, JSON `{"status":"error","message":"Erro no Token"}` — igual ao padrão de erro da Acuidar.
  - Token falso no header → HTTP 400, mesma mensagem.
  - `Authorization: Bearer <token>` → HTTP 400, mesma mensagem (prefixo Bearer rejeitado, como na Acuidar — a confirmar com token real).
  - POST → HTTP 400 (endpoint responde; método esperado é GET).
- **Interpretação:** o contrato de ERRO é idêntico ao da Acuidar ("Erro no Token", HTTP 400, token puro sem Bearer). O formato dos DADOS de resposta (campos, estrutura, quantidade de unidades) só pode ser confirmado com o token real — o Champion classificou o formato como diferente da Acuidar, portanto a estrutura de sucesso deve ser validada com credencial.

## Implicações registradas (para emenda nas SPECs — nada implementado ainda)

- Campo `empresa` nas entidades da intranet (usuários, ocorrências, unidades) — define a emenda técnica.
- Matriz de perfis (F1-T04) vale para as duas empresas sob a mesma governança; visibilidade de dados por empresa é decisão da leva técnica.
- Painel (F1-T08) precisará de filtro/visão por empresa.
- Credencial da Dona Help: `DONAHELP_PORTAL_TOKEN` nos Secrets do Skip — nunca por chat ou arquivo.

## Pendências

- **Token da Dona Help** — Champion grava `DONAHELP_PORTAL_TOKEN` nos Secrets do Skip (não pelo chat). Com o token, o assistente valida o contrato completo no padrão F1-T01 (estrutura de sucesso, campos, códigos de unidade, duplicados, limites).
- Emenda append-only nas SPECs afetadas (1-001, 1-003) e formalização da task de validação — após contrato completo validado.