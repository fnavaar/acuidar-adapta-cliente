# Análise FA-12 — Agenda conjunta (Café com Franqueados / Day Fusion)

**Data:** 2026-10-07 (análise 2026-10-09) · **Base:** código real do Skip 51740 (v0.0.95) + decisões do champion 14:15/14:18

## Achados do código atual (verificados, não presumidos)

| Peça | Como funciona hoje | Impacto da FA-12 |
|---|---|---|
| `src/pages/NovaReuniao.tsx` | Checkbox "Evento multiunidade (Café com Franqueados, Day Fusion…)" abre lista de checkboxes de unidades (1 unidade principal + extras). Unidade é OBRIGATÓRIA (validação). | Os 2 tipos viram agenda conjunta: campo de unidade sai; título padrão editável; sem seleção |
| `pocketbase/hooks/ocorrencias_criar.js` | RN-1-07: multiunidade cria **1 ocorrência POR unidade** (loop), mesma source_meeting_id; "Unidade" é obrigatório (RN-1-03) | Novo ramo: `agenda_conjunta=true` → **1 ocorrência ÚNICA** com marcador (não 172) |
| `pocketbase/hooks/agenda_sincronizar.js` | 1 evento único no Google; título lista unidades; marker `extendedProperties.private.origem='intranet'`; SEM attendees | Evento da agenda conjunta: título sem unidade + **attendees = e-mails das unidades ativas** (Portal tem `email` por unidade — 15 campos, sinal multiempresa) |
| `pocketbase/hooks/google_agenda_importar.js` | Pula eventos com marker intranet (anti-duplicidade); identifica unidade por código/nome oficial; sem unidade → conferência humana | Agenda conjunta da intranet JÁ é pulada (marker). Evento manual do Google sem unidade → conferência (RN-1-19: nunca adivinha) — correto |
| `pocketbase/hooks/painel_cobertura.js` | Conta por `portal_unit_id`; unidade sem registro no mês = reuniao_pendente | Agenda conjunta: unidades **presentes** (`unidades_presentes`) recebem cobertura; ausentes continuam reuniao_pendente |

## Desenho proposto (com as decisões do champion)

1. **Formulário (NovaReuniao.tsx):** tipo Café com Franqueados / Day Fusion → seção "Agenda conjunta": campo de unidade OCULTO, aviso "Vale para todas as unidades ATIVAS da empresa", **título padrão pré-preenchido** (ex.: "Café com Franqueados — {dia} de {mês}") editável pela consultora. Checkbox multiunidade fica para os demais tipos.
2. **Ocorrência (ocorrencias_criar.js):** `agenda_conjunta=true` → **1 ocorrência única**, `portal_unit_id='agenda_conjunta'` (marcador, como 'conferencia' — não é unidade real), campos novos `unidades_presentes` (JSON) e `rsvp_sugestoes` (JSON). Idempotência: chave inclui marcador (sem unidade).
3. **Sync Google (agenda_sincronizar.js):** evento único com o título da consultora + **attendees = e-mails das unidades ativas** (busca no Portal por empresa), marker `agenda_conjunta=true`. Convite dispara o RSVP no Google.
4. **RSVP (hook novo `agenda_rsvp.js`):** botão "Carregar RSVP do Google" na tela de presença → lê `attendees[]` do evento (email → código da unidade pelo Portal) → devolve sugestões (accepted → pré-marcado). **RSVP é sugestão, nunca presença** (Google não tem presença real).
5. **Presença (tela + hook `ocorrencias_presenca.js`):** consultora marca quem compareceu — lista das unidades ATIVAS com **busca (reusa o filtro da FA-11)**, pré-marcada pelo RSVP; salva `unidades_presentes`. Permissão: criador + gestor/admin (padrão LT-2-T01); RLS por empresa (AP-1620: prova de burla).
6. **Cobertura (painel_cobertura.js):** ocorrência agenda_conjunta CONFIRMADA com presença salva → cada unidade em `unidades_presentes` recebe `ocorrencia_confirmada` (conta na cobertura); unidades ausentes continuam `reuniao_pendente`. Sem presença salva → nada conta (não infere — RN-1-19).
7. **Cancelamento:** cancelamento da ocorrência única + motivo obrigatório (RN-1-08 já existe na máquina de estados — sem mudança).

## Decisões embutidas (champion pode vetar)

- **D1:** presença salva EDITA a ocorrência (não é aprovação de exceção) — na fila de ocorrências, botão "Marcar presença" para agenda_conjunta.
- **D2:** RSVP carregado sob demanda (botão), não automático — evita latência/quota sem necessidade.
- **D3:** unidade presente conta como `ocorrencia_confirmada` (o relato é único da agenda conjunta).
- **D4:** o checkbox multiunidade atual permanece para os OUTROS tipos (ex.: "Outro" com unidades específicas) — nada muda nele.

## Migração

- **0034:** campos `agenda_conjunta` (bool, default false), `unidades_presentes` (json, opcional), `rsvp_sugestoes` (json, opcional) na collection `ocorrencias`. Sem dados existentes afetados (nenhuma ocorrência real é agenda conjunta hoje).

## Provas planejadas (antes do teste humano)

1. Registro agenda conjunta → 1 ocorrência única com marcador (não 172) + estado confirmado.
2. Idempotência: reenvio → duplicata rejeitada (mesma chave).
3. Sync: evento criado com attendees das unidades ativas + marker; falha do Google → ocorrência intacta + retry.
4. Importação: evento com marker pulado (sem duplicação).
5. RSVP: attendees lidos, e-mail→código mapeado, sem credencial → resposta explícita.
6. Presença: salvar unidades_presentes (criador ok; outra consultora 403; RLS 403 empresa cruzada).
7. Cobertura: unidades presentes → confirmada; ausentes → reuniao_pendente; sem presença → nada conta.
8. Regressão: registro normal 1→1 unidade, multiunidade antiga, farol, painel, importação normal.