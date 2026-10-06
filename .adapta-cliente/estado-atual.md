# Estado atual — Adapta Cliente

- task_id: LT-1-T09 (leva técnica — refresh token automático do Google Agenda: renovação on-demand no 401)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: LT-1-T07/LT-1-T08 (mesma base) + documentação OAuth2 do Google (POST oauth2.googleapis.com/token, grant_type=refresh_token) + pendência registrada no fechamento da LT-1-T08
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-10-06T09:12-03:00 — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: aprovado — 2026-10-06T10:37-03:00 — champion: "tudo certo" (prova da renovação automática nas duas empresas pela UI)
- verificacao_automatica: passou — Skip v0.0.50 QA ✓ (debug temporário removido); prova REAL da renovação: acuidar com access token expirado → ok (renovou sozinho); donahelp SEM access token (só refresh) → ok (renovou direto); idempotência 0 criadas; RLS 403; sem auth 401; banco 22 (12 reais + 10 conferencia do teste humano)
- aprendizado: capturado — AP-2026-10-06-1045 (ver 06_notas/aprendizado-continuo/)
- ultima_acao: LT-1-T09 concluída — renovação automática provada nas duas empresas (v0.0.44→50); 2 problemas de credenciais diagnosticados e corrigidos pelo champion (refresh truncado 72 chars; refresh com client errado)
- proxima_acao: Nenhuma task ativa — próximo trabalho exige novo pedido do champion (candidatos: rotação de credenciais, validação do consultor da fase 1, publicação em produção)
- atualizado_em: 2026-10-06T10:45:00-03:00

## O que foi implementado na LT-1-T09 (v0.0.44 → v0.0.50)

1. **Hook `POST /backend/v1/agenda/importar`** — renovação on-demand: se o Google responde 401 (access token expirado) OU o access token está ausente, o hook chama `POST https://oauth2.googleapis.com/token` (grant_type=refresh_token) com os secrets `GOOGLE_OAUTH_CLIENT_ID` + `GOOGLE_OAUTH_CLIENT_SECRET` + o refresh token da empresa (`GOOGLE_CALENDAR_REFRESH_TOKEN` acuidar / `GOOGLE_CALENDAR_REFRESH_TOKEN_DONAH` donahelp) e usa o access token novo NA MESMA requisição (sem gravar de volta no secret — o Skip não permite escrever secrets em runtime; a renovação é por importação, custo de 1 POST extra).
2. **Caminhos de erro explícitos:** sem refresh token/client OAuth gravado → `credencial_ausente` com o nome do secret que falta; renovação rejeitada pelo Google → `credencial_expirada` com `erro_google` (unauthorized_client = client errado; invalid_grant = refresh inválido/truncado) e orientação de regenerar com o MESMO client.
3. **Tela `/agenda`** — novo alerta para `credencial_expirada` (além do `credencial_ausente` já existente).
4. **Debug temporário removido (v0.0.50)** — o diagnóstico de renovação permanece disponível na resposta da API (campo `erro_google`), sem console.log.

## Provas executadas (fechamento, 2026-10-06)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | acuidar com access token EXPIRADO | hook renovou sozinho (refresh token corrigido) → resultado ok | ✓ |
| 2 | donahelp SEM access token (só refresh) | hook renovou direto → resultado ok | ✓ |
| 3 | Idempotência | reimportar as duas → 0 criadas | ✓ |
| 4 | RLS: consultora-donahelp pede acuidar | 403 | ✓ |
| 5 | Sem auth | 401 | ✓ |
| 6 | Tela /agenda | 200 no preview | ✓ |
| 7 | Banco | 22 registros (12 reais + 10 conferencia do teste humano) | ✓ |
| 8 | Teste humano pela UI | champion: "tudo certo" | ✓ |

## Diagnóstico das credenciais (durante o teste humano)

- **1ª tentativa (unauthorized_client):** o refresh token foi gerado com um client OAuth diferente do gravado — corrigido quando o champion regravou o client ID/secret corretos.
- **2ª tentativa (invalid_grant na acuidar):** o refresh token estava TRUNCADO (72 chars; o Google emite ~103 começando com `1//`) — corrigido quando o champion recopiou completo.
- **Dona Help (unauthorized_client):** o refresh da automação foi gerado com o client do Playground, não o próprio — corrigido regenerando com as credenciais próprias na engrenagem.
- **Melhoria derivada (v0.0.48):** access token ausente + refresh presente → renova direto (o access token é efêmero; o refresh é a credencial real).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Validação do consultor do fechamento da fase 1 — gate humano do método
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)