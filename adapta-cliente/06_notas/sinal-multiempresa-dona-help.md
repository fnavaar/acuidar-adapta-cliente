# Sinal — Multiempresa: Dona Help Franquias

**Registrado em:** 2026-09-30
**Origem:** Champion (Luis Carlos - CTO), mensagem direta
**Status:** contrato da API validado — pronto para emenda nas SPECs e formalização da task

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

## Contrato da API da Dona Help — VALIDADO (2026-09-30)

**Endpoint:** `https://app.donahelpbr.com.br/api/dados/unidades`
**Credencial:** `DONAHELP_PORTAL_TOKEN` nos Secrets do Skip (gravado pelo Champion via Builder; nunca passou pelo chat)
**Validação:** hook temporário server-side (`validar-contrato-donahelp`, Skip v0.0.7) lendo `$secrets.get` — resposta sanitizada, token nunca exposto.

| Item | Acuidar (F1-T01) | Dona Help (validado) |
|---|---|---|
| Método | GET | GET |
| Auth | `Authorization: <token>` puro, sem Bearer | `Authorization: <token>` puro (mesmo padrão — Bearer rejeitado na sondagem) |
| Sucesso | HTTP 200, JSON `{status, message, dados:[...]}` com wrapper | **HTTP 200, JSON array direto `[...]` — SEM wrapper `{status,message,dados}`** |
| Total de unidades | 172 | **45** |
| Códigos únicos | 172/172 | **45/45 (`codigos_unicos: true`)** |
| Campos por unidade | codigo, subdominio, nome, razao_social, cnpj, email, endereco, numero, complemento, bairro, cep, cidade, estado, celular, telefone | **mesmos 15 campos** (nome, cnpj, email, complemento, cidade, subdominio, endereco, numero, bairro, estado, codigo, razao_social, cep, celular, telefone) |
| Campo chave | `codigo` (string numérica) | `codigo` (string numérica) — unicidade provada |
| Erro de auth | HTTP 400 `{"status":"error","message":"Erro no Token"}` | HTTP 400 idem (sondagem sem credencial) |

**Conclusão do contrato:** a diferença real é a **estrutura de sucesso** — a Acuidar embrulha a lista em `{status, message, dados}` e a Dona Help retorna o **array direto**. Os campos das unidades são os mesmos 15, e o campo chave `codigo` é único (45/45). Para o sistema: o parser precisa detectar wrapper (Acuidar) vs array direto (Dona Help).

## Implicações registradas (para emenda nas SPECs — nada implementado ainda)

- Campo `empresa` nas entidades da intranet (usuários, ocorrências, unidades) — define a emenda técnica.
- Matriz de perfis (F1-T04) vale para as duas empresas sob a mesma governança; visibilidade de dados por empresa é decisão da leva técnica.
- Painel (F1-T08) precisará de filtro/visão por empresa.
- Parser de unidades com detecção de wrapper por empresa (Acuidar = `{status,message,dados}`; Dona Help = array direto).
- Credencial por empresa nos Secrets do Skip: `ACUIDAR_PORTAL_TOKEN` (existente) e `DONAHELP_PORTAL_TOKEN` (gravado).

## Pendências

- Emenda append-only nas SPECs 1-001 e 1-003 (multiempresa + contrato Dona Help) — após aprovação do Champion nos detalhes.
- Formalização da task de integração multiempresa na fase 1 (padrão F1-T01).
- Remoção do hook temporário `validar-contrato-donahelp` após a formalização (não fica em produção).