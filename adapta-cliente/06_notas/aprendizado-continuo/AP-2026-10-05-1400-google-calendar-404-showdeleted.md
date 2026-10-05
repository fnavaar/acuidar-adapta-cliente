# AP-2026-10-05-1400 — Google Calendar: rota errada dá 404 (não 401) e cancelled exige showDeleted

- Status: capturado
- Escopo: projeto do cliente
- Task/SPEC: LT-1-T07 · SPEC-1-001 (RN-1-06) + mapa F1-T08 §5
- Sinal 1: na API do Google Calendar, endpoint inexistente retorna 404 HTML sem processar autenticação; credencial ausente/inválida/expirada retorna 401 JSON. Uma mensagem de erro que sugere "token expirado" em 404 aponta para o lugar errado — 404 = rota, 401 = credencial.
- Sinal 2: events.list omite eventos com status=cancelled por padrão; sem showDeleted=true, reunião cancelada OU excluída na agenda simplesmente não vem na listagem — furo silencioso na regra de elegibilidade total (o resumo mostra "0 canceladas" como se fosse normal). Cancelar e excluir produzem o MESMO status=cancelled; o título do evento não muda.
- Evidência: prova de controle fora do sistema (calendarList sem auth → 401 JSON; /primary/events → 404 HTML); correção v0.0.31 (URL /calendars/primary/events) e v0.0.35 (showDeleted=true); prova real com 1 cancelada importada (occurrence_type=cancelamento, estado confirmado, motivo automático) e idempotência cruzada entre sessões (ja_existentes=3, 0 criadas). Debug completo em 06_notas/debug/debug-2026-10-05-lt1t07-url-google.md.
- Regra reutilizável: ao integrar API externa, provar a URL com controle de autenticação antes de culpar a credencial; toda listagem que alimenta "elegibilidade total" deve incluir explicitamente registros removidos/cancelados da fonte e provar o caminho do cancelamento com dado real.
- Quando aplicar: qualquer integração com API do Google (ou outra nuvem) no projeto; qualquer regra de "nenhuma reunião fica sem registro" que dependa de listagem externa.
- Quando não aplicar: endpoints internos do PocketBase (404 por RLS significa registro invisível, não rota errada — ver AP-2026-09-30-1645).
- Confiança: alta — comportamento observado e reproduzido em duas rodadas de prova com agenda real.
- Privacidade: sem segredo, token ou dado pessoal; IDs de evento citados são de fixtures de teste já limpas.
