# AP-2026-09-30-1535 — Contrato de autenticação da API do Portal Acuidar

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: F1-T01; SPEC-1-001 §BLOQUEIO-F1-001-A
- Sinal: a API de dados do Portal Acuidar autentica com o header `Authorization: <token>` — o valor do token **puro**, sem o prefixo `Bearer`. Enviar `Authorization: Bearer <token>` produz `{"status":"error","message":"Erro no Token"}` (HTTP 400), erro idêntico ao de um token inválido.
- Evidência: teste real em 2026-09-30 — `GET https://app.acuidarbr.com.br/api/dados/unidades` com `Authorization: <token>` retornou HTTP 200 com 172 unidades; com `Authorization: Bearer <token>` retornou HTTP 400 "Erro no Token".
- Regra reutilizável: ao integrar APIs PHP próprias de clientes, testar o token puro no header `Authorization` ANTES de presumir o padrão Bearer; o erro de autenticação genérico ("Erro no Token") não diferencia token ausente, formato errado ou token inválido — testar as três hipóteses com o mesmo token.
- Quando aplicar: qualquer integração futura com endpoints `app.acuidarbr.com.br/api/dados/*` e APIs PHP legadas com resposta `{status, message, dados}`.
- Quando não aplicar: APIs que documentam explicitamente OAuth2/Bearer (JWT), onde o prefixo é obrigatório.
- Confiança: alta — comprovada por resposta HTTP real com dados válidos.
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto; token reside apenas no Secrets do Skip.