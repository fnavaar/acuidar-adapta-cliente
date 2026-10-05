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

## Prova REAL completa (2026-10-05, admin-teste)
- Importação `empresa=acuidar` → **2 importadas**: "Consultoria | Unidade 2" e
  "Acompanhamento — Acuidar João Pessoa" → **unidade 2 (João Pessoa)**, estado `pendente`,
  empresa acuidar, origem `google_calendar`.
- 2 eventos "Almoço" → `sem_unidade` (conferência humana — RN-1-19). ✓
- 2ª rodada: `ja_existentes=2`, 0 criadas — **idempotência provada com dados reais**. ✓
- Janela -7d/+14d correta (2026-09-28 a 2026-10-19). ✓
- RLS: consultora-donahelp → 403 em acuidar; donahelp → 4 sem unidade (eventos não são dela). ✓
- **Limpeza:** as 2 ocorrências da prova eram fixtures da agenda de teste — excluídas via
  migration 0010 (mesmo critério 0005/0006/0009; Skip v0.0.33, QA ✓). Banco final: 9 registros
  reais, zero `google_calendar`.

## Aprendizado
- 404 HTML do googleapis = rota errada (auth nem é processado); 401 JSON = credencial
  ausente/inválida/expirada. A mensagem de erro do hook ("token expirado?") deve ser lida com essa
  distinção — 404 aponta para URL, não para credencial.
- Migration já aplicada não reexecuta: limpeza de fixture pós-prova exige migration NOVA com IDs
  fixos (não editar migration anterior — 0009 foi restaurada ao original).

## Gate atual
aguardando_teste_humano — champion testa a importação como **ACUIDAR** pela UI (conta
gestor/admin): preview → Ctrl+Shift+R → Agenda → Importar. Esperado: 2 importadas
(Unidade 2 e João Pessoa → unidade 2), "Almoço" na lista de conferência; reimportar → 0 novas.
Access token de conta de teste expira ~1h — se o teste for muito depois, regenerar o token
(ou evoluir o hook para refresh token na formalização).
