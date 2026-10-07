# Sinal LT-2-T01 — Registro de reunião criar evento no Google Calendar (escrita intranet→Google)

**Pedido do champion (2026-10-07 10:37):** "Registro de reunião criar evento no Google Calendar: Quero isso — analise como próxima task."

## Estado atual (inspecionado no Skip 51740)

- **Registro (intranet):** tela `NovaReuniao.tsx` → hook `POST /backend/v1/ocorrencias/criar` cria a ocorrência **CONFIRMADA imediatamente** (entrada assistida manual; RN-1-04/05; idempotência `entrada_assistida:manual-<data>-<ts>:unidade:tipo`).
- **Google (hoje):** integração **SÓ LEITURA** — hook `POST /backend/v1/agenda/importar` lê `calendars/primary/events` (showDeleted=true) e cria ocorrências (`google_calendar:eventId:unidade:tipo`). Refresh tokens atuais foram gerados com escopo **calendar.readonly** (LT-1-T07/08/09).
- **Credencial por empresa (LT-1-T08):** acuidar → GOOGLE_CALENDAR_REFRESH_TOKEN; donahelp → GOOGLE_CALENDAR_REFRESH_TOKEN_DONAH (+ client ID/secret compartilhados). Renovação on-demand (LT-1-T09).
- **RLS por empresa (LT-1-T06):** validação server-side em todos os hooks (403).

## Risco crítico: duplicidade intranet↔Google

A importação lê a MESMA agenda onde o evento seria criado. Sem marcação, a próxima importação reimporta o evento criado pela intranet → a MESMA reunião conta DOBRADO na cobertura (duplicata com chaves de idempotência diferentes: `entrada_assistida:...` vs `google_calendar:eventId:...`). Já existe hoje um risco análogo (mesma reunião por 2 caminhos) — a task deve FECHAR esse furo, não ampliar.

## Plano proposto (recorte mínimo completo)

1. **Migration 0026:** campos na `ocorrencias` — `google_event_id` (text), `google_sync_estado` (sincronizada | nao_sincronizada | nao_aplicavel), `google_sync_erro` (text).
2. **Hook novo `POST /backend/v1/agenda/sincronizar`** (body: `{ occurrence_id }`): chamado PELO FRONTEND após confirmação da ocorrência (o registro NUNCA depende do Google — SPEC-1-002 §109).
   - RLS por empresa (403); só o criador da ocorrência ou gestor/admin podem sincronizar.
   - Cria 1 evento na agenda da credencial da empresa (`calendars/primary/events`, POST) com `extendedProperties.private = { origem: 'intranet', occurrence_id: <id> }` e título `"<tipo> | <nome da unidade> (<código>)"`.
   - Sucesso → grava `google_event_id` + `google_sync_estado=sincronizada`.
   - Falha Google (401/403/rede/quota) → ocorrência permanece CONFIRMADA e intacta; `google_sync_estado=nao_sincronizada` + `google_sync_erro`; retry pelo botão (nunca retry cego automático — RN-1-09).
   - Multiunidade (RN-1-07): 1 evento único para a reunião (título lista as unidades), vinculado à ocorrência principal.
   - Sem credencial da empresa → `credencial_ausente` com o nome do secret (padrão LT-1-T08).
3. **Importação à prova de duplicidade:** hook `google_agenda_importar.js` pula eventos com `extendedProperties.private.origem === 'intranet'` (já registrados) — fecha o furo de contagem dobrada nos dois sentidos.
4. **Frontend:** `NovaReuniao.tsx` mostra o status de sincronização no comprovante (✅ sincronizada / ⚠️ não sincronizada + motivo); `Fila.tsx` exibe badge de sync + botão "Sincronizar com Google" (retry) em `nao_sincronizada`.
5. **Fora do recorte (futuro):** edição/cancelamento na intranet propagando ao Google; sincronização de remarcação.

## Gate humano (pré-requisito da prova real)

- **Escopo de escrita:** os 2 refresh tokens atuais são `calendar.readonly`. O champion regenera os 2 (acuidar + donah) no OAuth Playground com escopo `https://www.googleapis.com/auth/calendar.events` (mesmo processo da LT-1-T09: client próprio, Access type Offline, Force prompt Consent Screen) e regrava nos MESMOS secrets (`GOOGLE_CALENDAR_REFRESH_TOKEN` / `_DONAH`). Sem isso, a prova real da criação falha com 403 insufficient_permissions — o caminho de falha é provável mesmo assim.

## Matriz critério → prova

| Critério | Prova |
|---|---|
| Ocorrência confirmada independe do Google | Google falho/403 → ocorrência segue confirmada + flag nao_sincronizada |
| RLS por empresa | consultora donahelp → 403 em ocorrência acuidar; sem auth → 401 |
| Permissão de sync | não-criador consultor → 403; criador e gestor/admin → ok |
| Evento criado na agenda certa | evento aparece na agenda da credencial da empresa (prova real do champion) |
| Sem duplicidade na importação | importar após criar → evento com marker é pulado; 2ª rodada idempotente |
| Idempotência do sync | sincronizar 2× a mesma ocorrência → 1 evento só (google_event_id já gravado → pula) |
| Multiunidade | 1 reunião N unidades → 1 evento, título com todas as unidades |

## Decisões embutidas (o champion pode vetar na autorização)

- Duração do evento: **1 hora** a partir de data_fato + horario (campo de duração fica para depois).
- Editar/cancelar na intranet → Google: **fora do primeiro recorte**.
- Quem sincroniza: **criador da ocorrência + gestor/admin**.
