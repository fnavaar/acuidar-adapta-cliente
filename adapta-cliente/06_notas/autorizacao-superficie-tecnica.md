# Autorização da superfície técnica da integração

**Autorizada por:** Champion (Luis Carlos - CTO) — exerce também o papel de Responsável técnico do cliente
**Data:** 2026-09-30
**Task:** F1-T03 · **SPEC:** SPEC-1-001 §BLOQUEIO-F1-001-C

## Autorização

O Champion, na qualidade de Responsável técnico do cliente, autoriza por escrito a superfície técnica abaixo como **destino único e oficial** da integração do projeto Adapta Cliente (fase 1).

## 1. Repositório

| Papel | Local |
|---|---|
| Repositório operacional (handoff do cliente) | `https://github.com/fnavaar/acuidar-adapta-cliente` |
| Aplicação (intranet) | Projeto Skip "Adapta Cliente", ID 51740, org `luiscarlos-9b8dd` |
| Fonte do plugin | `https://github.com/drkgod/Plugin-Cliente---Adapta` (referência; não é destino de código do cliente) |

## 2. Ambiente

| Ambiente | URL | Uso |
|---|---|---|
| **Preview (teste)** | `https://adapta-cliente-c2bc2--preview.goskip.app` | Todas as provas e testes da fase 1 |
| **Produção** | `https://adapta-cliente-c2bc2.goskip.app` | Somente após `skip_project_publish`; não publicada até a fase 1 fechar |
| Backend (PocketBase) | `https://adapta-cliente-c2bc2.shrd00.internal.goskip.dev` | API da intranet (collections + hooks) |

## 3. Responsável por deploy

- **Luis Carlos - CTO** — deploys executados via Builder do Skip (`https://goskip.dev/luiscarlos-9b8dd/builder/6e8a021b-bfb9-4e9e-80b2-3804ca0f3776`) e MCP Skip pelo assistente ETHOS, sob autorização do Champion.

## 4. Mecanismo de segredos

- **Secrets do Skip** (projeto 51740) — gerenciador aprovado.
- Chaves em uso (apenas referência, sem valores): `ACUIDAR_PORTAL_TOKEN`, `ACUIDAR_PORTAL_API_KEY`.
- Regras: nenhum segredo em arquivo, código, task, changelog ou log; valores só via `$secrets.get(...)` em hooks/migrations.

## 5. Escopo da autorização

- **Inclui:** criação e edição de collections, hooks e páginas da intranet no projeto Skip 51740; commits no repositório operacional; provas no ambiente de preview.
- **Não inclui:** Portal Acuidar em produção (somente leitura da API de dados); cadastro mestre de unidades; credenciais fora do Secrets do Skip; comunicação a franqueados; publicação em produção sem decisão explícita do Champion.

## 6. Vigência

- Autorização válida para a fase 1; reavaliada na abertura da fase 2.