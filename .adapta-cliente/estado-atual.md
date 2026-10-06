# Estado atual — Adapta Cliente

- task_id: LT-1-T09 (leva técnica — refresh token automático do Google Agenda: renovação on-demand no 401)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: LT-1-T07/LT-1-T08 (mesma base) + documentação OAuth2 do Google (POST oauth2.googleapis.com/token, grant_type=refresh_token) + pendência registrada no fechamento da LT-1-T08
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-06T09:12-03:00 — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: pendente (roteiro entregue em 2026-10-06)
- verificacao_automatica: passou — Skip v0.0.45 QA ✓; provas: acuidar/donahelp com token válido → comportamento idêntico (ok, idempotente), RLS 403, tela 200; caminhos de renovação (refresh_ausente/renovacao_rejeitada) implementados e serão provados no teste humano quando o access token expirar (~1h)
- aprendizado: pendente
- ultima_acao: LT-1-T09 implementada (v0.0.44/45) — renovação on-demand no 401 + tela trata credencial_expirada
- proxima_acao: aguardar teste humano do champion (gravar 4 secrets de OAuth + prova de renovação com token expirado)
- atualizado_em: 2026-10-06T09:25:00-03:00

## O que foi implementado na LT-1-T09 (v0.0.44 → v0.0.45)

1. **Hook `POST /backend/v1/agenda/importar`** — renovação on-demand: se o Google responde 401 (access token expirado), o hook chama `POST https://oauth2.googleapis.com/token` (grant_type=refresh_token) com os secrets `GOOGLE_OAUTH_CLIENT_ID` + `GOOGLE_OAUTH_CLIENT_SECRET` + o refresh token da empresa (`GOOGLE_CALENDAR_REFRESH_TOKEN` para acuidar / `GOOGLE_CALENDAR_REFRESH_TOKEN_DONAH` para donahelp) e usa o access token novo NA MESMA requisição (sem gravar de volta no secret — o Skip não permite escrever secrets em runtime; a renovação é por importação, custo de 1 POST extra).
2. **Caminhos de erro explícitos:** sem refresh token/client OAuth gravado → `credencial_ausente` com o nome do secret que falta; renovação rejeitada pelo Google (refresh revogado/expirado — app em modo Teste expira em 7 dias) → `credencial_expirada` com orientação de regenerar.
3. **Tela `/agenda`** — novo alerta para `credencial_expirada` (além do `credencial_ausente` já existente).
4. **Nada mais muda:** RLS por empresa, idempotência, lookup, decisão (B) do cancelado sem unidade — intocados.

## Provas executadas (v0.0.45)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | acuidar com token válido | ok, comportamento idêntico (9 eventos, ja_existentes=5) | ✓ |
| 2 | donahelp com token válido | ok, comportamento idêntico (19 eventos, ja_existentes=8) | ✓ |
| 3 | RLS: consultora-donahelp pede acuidar | 403 | ✓ |
| 4 | Idempotência: reimportar acuidar | 0 criadas, ja_existentes=5 | ✓ |
| 5 | Tela /agenda | 200 no preview | ✓ |
| 6 | Caminho de renovação (401 → renova → reimporta) | implementado; prova real no teste humano (aguardar token expirar ~1h) | — |
| 7 | Caminho refresh_ausente / renovacao_rejeitada | implementado; prova real no teste humano | — |

**Observação do banco (22 registros):** o champion testou pela UI entre as provas — as 8 ocorrências `conferencia` da donahelp foram recriadas pela importação dele (agenda da automação continua com os eventos excluídos) + 2 novas da acuidar ("João Gomes" excluído → decisão (B) aplicada corretamente). São registros do teste humano — preservados.

## Roteiro de teste humano (LT-1-T09)

1. **Grave os 4 secrets no Builder** (nunca pelo chat):
   - `GOOGLE_OAUTH_CLIENT_ID` — o Client ID do app "ethos" (Google Cloud Console → Credenciais)
   - `GOOGLE_OAUTH_CLIENT_SECRET` — o Client Secret do mesmo app
   - `GOOGLE_CALENDAR_REFRESH_TOKEN` — refresh token da agenda da Acuidar (OAuth Playground: marque "Force approval prompt" ou use o mesmo client próprio; o refresh token aparece no Step 2 junto com o access token)
   - `GOOGLE_CALENDAR_REFRESH_TOKEN_DONAH` — refresh token da agenda da Dona Help (mesmo processo com a conta da automação)
2. **Prova da renovação:** espere o access token atual expirar (~1h da última renovação) OU regenere um access token qualquer inválido no secret `GOOGLE_CALENDAR_TOKEN` (ex.: cole um token antigo) → Importar como **Acuidar** → esperado: hook renova sozinho e a importação conclui com `resultado: ok` (sem você regenerar nada)
3. **Prova do caminho sem refresh:** (opcional) antes de gravar os secrets, deixe o access token expirar → Importar → esperado: `credencial_ausente` com o nome do secret de refresh
4. **Como reconhecer falha:** importação com erro de credencial mesmo com os 4 secrets gravados; `credencial_expirada` com refresh token válido; qualquer duplicata ao reimportar

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Validação do consultor do fechamento da fase 1 — gate humano do método
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)