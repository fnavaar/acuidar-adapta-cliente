# Sinal — Multiempresa: Dona Help Franquias

**Registrado em:** 2026-09-30
**Origem:** Champion (Luis Carlos - CTO), mensagem direta
**Status:** decisões aprovadas pelo Champion — aguardando URL do endpoint da API

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

**Implicações registradas (para emenda nas SPECs — nada implementado ainda):**
- Campo `empresa` nas entidades da intranet (usuários, ocorrências, unidades) — define a emenda técnica.
- Matriz de perfis (F1-T04) vale para as duas empresas sob a mesma governança; visibilidade de dados por empresa é decisão da leva técnica.
- Painel (F1-T08) precisará de filtro/visão por empresa.
- Credencial da Dona Help: `DONAHELP_PORTAL_TOKEN` nos Secrets do Skip — nunca por chat ou arquivo.

## Pendências

- **URL do endpoint da API da Dona Help** (sem token) — para validação de contrato no padrão da F1-T01.
- Token da Dona Help — Champion grava `DONAHELP_PORTAL_TOKEN` nos Secrets do Skip (não pelo chat).
- Emenda append-only nas SPECs afetadas (1-001, 1-003) e formalização da task de validação — após URL recebida e contrato conhecido.