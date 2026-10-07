# Passo a passo — Refresh tokens com escopo calendar.events (LT-2-T01)

**Pedido do champion (2026-10-07 11:58):** "Preciso do passo a passo" para regenerar os 2 refresh tokens com escopo de ESCRITA no Google Calendar. Mesmo processo da LT-1-T09, mudando só o escopo.

## Por quê

Os refresh tokens atuais foram gerados com escopo `calendar.readonly` (só leitura). A prova real da escrita (criar evento) falha com `403 insufficient authentication scopes` — caminho já provado na implementação (v0.0.72). O escopo novo `calendar.events` cobre leitura E escrita de eventos — a importação continua funcionando com o mesmo token.

## Passos (para CADA empresa — Acuidar e Dona Help)

1. **Abrir o OAuth Playground:** https://developers.google.com/oauthplayground
2. **Engrenagem** (canto superior direito) → marcar **"Use your own OAuth credentials"** → colar o **Client ID** e o **Client Secret** (os MESMOS já gravados nos Secrets do Skip: `GOOGLE_OAUTH_CLIENT_ID` / `GOOGLE_OAUTH_CLIENT_SECRET`).
3. Ainda na engrenagem, conferir: **Access type: Offline** e **Force prompt: Consent Screen** marcados (sem isso o refresh token não aparece no Step 2).
4. **Scope** (campo à esquerda, Step 1): colar `https://www.googleapis.com/auth/calendar.events` (substitui o readonly).
5. **Authorize APIs** → entrar com a conta da empresa:
   - Acuidar: `auxiliar1.ti.acuidar@gmail.com`
   - Dona Help: `acuidar.automacao@gmail.com`
   - Se aparecer "app não verificado": Avançado → Continuar (a conta é usuário de teste do app "ethos" em modo Teste).
6. **Step 2** (Exchange authorization code for tokens): o **refresh token** aparece no campo — copiar COMPLETO (~103 caracteres; refresh truncado dá `invalid_grant` — já ocorreu na LT-1-T09). NÃO recarregar a página (o Playground faz o exchange automático ao voltar do redirect).
7. **Gravar nos Secrets do Skip (Builder)** — NUNCA pelo chat:
   - Acuidar → `GOOGLE_CALENDAR_REFRESH_TOKEN` (mesmo nome — substitui o readonly)
   - Dona Help → `GOOGLE_CALENDAR_REFRESH_TOKEN_DONAH` (nome PRÓPRIO — não sobrescrever o da Acuidar)
8. **Avisar no chat** — eu rodo a prova real: sincronizar uma ocorrência → evento criado na agenda da empresa → importação pula o evento (marker `origem=intranet`) → teste humano do champion.

## Observações

- O access token do secret (`GOOGLE_CALENDAR_TOKEN`/`_DONAH`) não precisa ser regenerado — o hook renova on-demand com o refresh token novo (LT-1-T09).
- Refresh token em modo Teste expira em 7 dias — publicar o app "ethos" em produção resolve (pendência já registrada).
- Se o Step 2 der `invalid_grant`: refazer o Step 1 (código já usado/expirado). Se der `unauthorized_client`: o client ID/secret colados na engrenagem não são os mesmos dos Secrets.
