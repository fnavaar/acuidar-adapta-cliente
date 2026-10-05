# Debug 2026-10-05 — LT-1-T07: prova real da credencial revelou URL errada no hook

## Contexto
Champion gravou `GOOGLE_CALENDAR_TOKEN` nos Secrets do Skip (conta de teste Google; app OAuth
"ethos" em modo Teste — a conta foi adicionada como usuário de teste após erro 403 access_denied
no OAuth Playground, resolvido no dia). Nada passou pelo chat.

## Sintoma
Prova da importação real: hook respondeu
`Google Calendar respondeu HTTP 404. Verifique a credencial (token expirado?).`
Mensagem enganosa — o token era válido.

## Diagnóstico (cadeia causal)
1. Credencial confirmada nos Secrets (skip_cloud_list_secrets, 2026-10-05T12:04Z). ✓
2. RLS provada antes do Google: consultora-donahelp pedindo `empresa=acuidar` → 403. ✓
3. Com token real, o hook recebia 404 do Google. Teste de controle fora do sistema:
   `/calendar/v3/users/me/calendarList` sem auth → **401 JSON** (auth processado e negado);
   `/calendar/v3/primary/events` sem auth → **404 HTML** (rota não existe — nem chega a validar
   credencial). Conclusão: o 404 não era de token, era de ROTA.
4. Causa raiz: o hook chamava `https://www.googleapis.com/calendar/v3/primary/events` — endpoint
   inexistente. O correto (Events: list) é
   `https://www.googleapis.com/calendar/v3/calendars/primary/events`.

## Correção
`pocketbase/hooks/google_agenda_importar.js` — URL corrigida para `/calendars/primary/events`.
Skip v0.0.31, QA ✓ (setup/static/build/test ok).

## Provas pós-fix (2026-10-05)
- Google **200** com 4 eventos reais da agenda de teste; janela -7d/+14d correta
  (2026-09-28 a 2026-10-19).
- Os eventos da agenda são da **ACUIDAR** ("Consultoria | Unidade 2",
  "Acompanhamento — Acuidar João Pessoa", "Almoço" ×2) → importação como `donahelp` classificou
  os 4 em `sem_unidade` (correto — RN-1-19, sem inferência entre empresas).
- Lookup simulado localmente com dados reais: "Unidade 2" → código 2 (João Pessoa);
  nome oficial → código 2; "Almoço" → sem unidade (conferência humana). ✓
- 2ª rodada idempotente: 0 criadas; banco limpo (5 fixtures reais da LT-1-T06 intactas;
  nenhuma ocorrência `google_calendar` criada nas provas). ✓

## Aprendizado
404 HTML do googleapis = rota errada (auth nem é processado); 401 JSON = credencial
ausente/inválida/expirada. A mensagem de erro do hook ("token expirado?") deve ser lida com essa
distinção — 404 aponta para URL, não para credencial.

## Gate atual
aguardando_teste_humano — champion testa a importação como **ACUIDAR** pela UI (conta
gestor/admin): preview → Ctrl+Shift+R → Agenda → Importar. Esperado: 2 importadas
(Unidade 2 e João Pessoa → unidade 2), "Almoço" na lista de conferência.
Access token de conta de teste expira ~1h — se o teste for muito depois, regenerar o token
(ou evoluir o hook para refresh token na formalização).
