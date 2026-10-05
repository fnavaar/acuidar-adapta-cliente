# Controle de aprendizado contínuo

> Registro interno automático da triagem de aprendizado do projeto. Não é apresentado ao
> cliente no fluxo normal; mantido para auditoria do consultor.
> Formato: `- <ISO-8601> · task <ID|nenhuma> · sem sinal reutilizável · <motivo concreto>`
> Quando há sinal: `- <ISO-8601> · task <ID> · capturado:<arquivo> · <resumo>`

## Registro

- 2026-10-05T14:00:00-03:00 · task LT-1-T07 · capturado:AP-2026-10-05-1400-google-calendar-404-showdeleted.md · API do Google Calendar: 404 HTML = rota errada (auth nem processado) vs 401 JSON = credencial; events.list omite cancelled sem showDeleted=true — furo silencioso na elegibilidade total (cancelar e excluir produzem o mesmo status; título não muda). Dois bugs pegos por prova real (v0.0.31, v0.0.35).
- 2026-10-02T09:55:00-03:00 · task LT-1-T01 · capturado:AP-2026-10-02-0955-skip-preview-redirect.md · Redirecionamento pós-login/logout via window.location.href garante estado limpo do guard de rota no SPA do Skip; useNavigate reservado para navegação interna sem mudança de autenticação.
- 2026-09-30T17:15:00-03:00 · task F1-T08 · capturado:AP-2026-09-30-1715-http-send-body-bytes.md · `$http.send` no PocketBase v0.36 retorna `res.body` como bytes — `JSON.parse` direto falha silenciosamente; usar `res.json` ou `TextDecoder`. Prova antes/depois (v0.0.6 erro silencioso → v0.0.7 ok).
- 2026-09-30T16:45:00-03:00 · task F1-T04 · capturado:AP-2026-09-30-1645-pocketbase-rls-404.md · Negação por updateRule no PocketBase retorna 404 (registro invisível ao perfil), não 403; deleteRule null = só superuser; fixtures de teste exigem migration de limpeza em contexto superuser.
- 2026-09-30T16:28:00-03:00 · task F1-T03 · sem sinal reutilizável · task de autorização documental: os elementos (repositório, ambiente, deploy, segredos) já estavam em operação; o registro formal não revela causa, padrão ou orientação técnica reutilizável.
- 2026-09-30T16:20:00-03:00 · task F1-T06 · capturado:AP-2026-09-30-1620-idempotencia-recuperacao.md · Recuperação pós-timeout: índice UNIQUE na chave de idempotência + consulta que distingue "não existe" de "não sei"; dúvida → inconclusivo, nunca retry cego.
- 2026-09-30T15:35:00-03:00 · task F1-T01 · capturado:AP-2026-09-30-1535-contrato-api-acuidar.md · API do Portal autentica com `Authorization: <token>` puro (sem Bearer); erro "Erro no Token" não diferencia formato errado de token inválido.
- 2026-09-22T12:53:00-03:00 · task F1-T07 · sem sinal reutilizável · política de semântica da cobertura é decisão de negócio já incorporada integralmente à SPEC-1-003 (RN-1-16 a RN-1-19) e ao documento `06_notas/politica-datas-elegibilidade-estados.md`; não há padrão técnico reutilizável que evite erro em outra task.
- 2026-08-28T11:56:00-03:00 · task F1-T05 · sem sinal reutilizável · política de exceção de data é decisão de negócio já incorporada integralmente à SPEC-1-002 (RN-1-11 a RN-1-14) e ao documento `06_notas/politica-excecao-de-data.md`; não há padrão técnico reutilizável que evite erro em outra task.
- 2026-08-26T15:23:00-03:00 · task F1-T02 · sem sinal reutilizável · task de decisão de negócio (chave, elegibilidade, multiunidade, cancelamento, remarcação) já incorporada integralmente à SPEC-1-001 como regra de negócio; não há padrão técnico reutilizável que evite erro em outra task.
