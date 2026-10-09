# Passo a passo — Refresh tokens com escopos drive.readonly + meet.readonly (FA-17 + FA-14)

**Pedido do champion (2026-10-09 12:39):** passo a passo para destravar a prova real da ata.
**Mesmo processo da LT-2-T01/LT-1-T09** — muda só a lista de escopos. Um regrave destrava:
- **FA-17** — puxar a ata do Doc anexado ao evento ("Criar ata da reunião") ou por link (precisa `drive.readonly`)
- **FA-14** — puxar a ata IA do Meet/smartNotes (precisa `meet.readonly` + `drive.readonly`)

## Por quê

Os refresh tokens atuais têm escopo `calendar.events` (eventos) mas NÃO têm acesso ao Drive nem ao Meet. A exportação do Doc (Drive API) e a leitura da ata IA (Meet API) falham com `403 insufficient authentication scopes` — caminho já provado na FA-17 (Doc real do champion → falha controlada com essa mensagem).

## Passos (para CADA empresa — Acuidar e Dona Help)

1. **Abrir o OAuth Playground:** https://developers.google.com/oauthplayground
2. **Engrenagem** (canto superior direito) → marcar **"Use your own OAuth credentials"** → colar o **Client ID** e o **Client Secret** (os MESMOS já gravados nos Secrets do Skip: `GOOGLE_OAUTH_CLIENT_ID` / `GOOGLE_OAUTH_CLIENT_SECRET`).
3. Ainda na engrenagem, conferir: **Access type: Offline** e **Force prompt: Consent Screen** marcados (sem isso o refresh token não aparece no Step 2).
4. **Scope** (campo à esquerda, Step 1) — colar os TRÊS escopos, um por linha (ou separados por espaço):
   ```
   https://www.googleapis.com/auth/calendar.events
   https://www.googleapis.com/auth/drive.readonly
   https://www.googleapis.com/auth/meet.readonly
   ```
   (o `calendar.events` mantém a escrita de eventos que a LT-2-T01 já usa — sem ele, a sincronização de eventos quebra.)
5. **Authorize APIs** → entrar com a conta da empresa:
   - Acuidar: `auxiliar1.ti.acuidar@gmail.com`
   - Dona Help: `acuidar.automacao@gmail.com`
   - Se aparecer "app não verificado": Avançado → Continuar (a conta é usuário de teste do app "ethos" em modo Teste).
6. **Step 2** (Exchange authorization code for tokens): o **refresh token** aparece no campo — copiar COMPLETO (~103 caracteres; refresh truncado dá `invalid_grant` — já ocorreu na LT-1-T09). NÃO recarregar a página (o Playground faz o exchange automático ao voltar do redirect).
7. **Gravar nos Secrets do Skip (Builder)** — NUNCA pelo chat:
   - Acuidar → `GOOGLE_CALENDAR_REFRESH_TOKEN` (mesmo nome — substitui)
   - Dona Help → `GOOGLE_CALENDAR_REFRESH_TOKEN_DONAH` (nome PRÓPRIO — não sobrescrever o da Acuidar)
8. **Avisar no chat** — eu rodo a prova real da FA-17 (e da FA-14 se houver ata IA disponível).

## Preparar a matéria-prima da prova (FA-17)

Para o sistema enxergar o evento + Doc anexado, a CONTA DA CREDENCIAL precisa de acesso:
- **Opção recomendada:** na reunião de teste, **convidar** a conta da credencial (Acuidar: `auxiliar1.ti.acuidar@gmail.com`; Dona Help: `acuidar.automacao@gmail.com`) — o evento aparece na agenda dela com o anexo.
- **Alternativa:** compartilhar o Doc diretamente com a conta da credencial e usar o modo MANUAL (colar o link do Doc na Fila).
- O Doc do exemplo (Unidade Baixa Mogiana - MMirim/Mogi Guaçu, criado por liliankmcoradini@gmail.com) precisa de UMA dessas duas coisas.

## Roteiro da prova real (depois dos gates)

1. Registrar a reunião na intranet (entrada assistida) OU usar ocorrência já vinculada ao evento.
2. Na Fila, abrir a ocorrência → bloco "📝 Ata da reunião" → **📎 Puxar ata** (automático) ou colar o link + **Puxar do link** (manual).
3. Esperado: rascunho com o texto do Doc + status `disponivel` + fonte (`doc_anexo`/`doc_manual`).
4. Validar/editar o rascunho → salvar como relato → **nenhum e-mail sai** (kill switch EMAIL_ENVIOS_HABILITADO ausente).
5. Champion declara o teste → FA-17 concluída.

## Observações

- O access token do secret (`GOOGLE_CALENDAR_TOKEN`/`_DONAH`) não precisa ser regenerado — o hook renova on-demand com o refresh token novo (LT-1-T09).
- Refresh token em modo Teste expira em 7 dias — publicar o app "ethos" em produção resolve (pendência já registrada).
- Se o Step 2 der `invalid_grant`: refazer o Step 1 (código já usado/expirado). Se der `unauthorized_client`: o client ID/secret colados na engrenagem não são os mesmos dos Secrets.
- A conta da credencial precisa aceitar o convite da reunião para o evento aparecer na agenda `primary` dela.