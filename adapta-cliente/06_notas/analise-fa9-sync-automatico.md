# FA-9 — Sincronização AUTOMÁTICA com o Google Calendar após registrar reunião (análise 2026-10-07 13:32)

**Pedido do champion (13:30):** "quero que assim que a reunião for agendada ele já sincronize com
o google agenda automaticamente"

## Objetivo e resultado observável

Ao confirmar o registro de reunião pela UI, o evento é criado no Google Calendar da empresa
AUTOMATICAMENTE (sem clicar em "Sincronizar"). O registro NUNCA depende do Google: a ocorrência
já está confirmada na intranet antes da chamada (garantia LT-2-T01 preservada). Falha de sync →
comprovante mostra aviso + botão retry (ocorrência intacta). Botão manual permanece como
retry/fallback (idempotência garante zero duplicatas).

## Estado atual verificado

- `NovaReuniao.tsx`: após `resultado === 'confirmado'` → `setSyncEstado('pendente')` + botão
  MANUAL "📅 Sincronizar com Google Calendar" que chama `sincronizarGoogle(criacao.id)`.
- Hook `POST /backend/v1/agenda/sincronizar` (LT-2-T01): RLS por empresa, permissão
  criador/gestor-admin, idempotência por google_event_id, renovação on-demand (LT-1-T09),
  duração fixa 1h, marker anti-duplicidade na importação.
- Exceção (aguardando_aprovacao_de_excecao): hoje não sincroniza (correto — a ocorrência ainda
  não está confirmada).

## Plano (3 passos) — EXECUTADO (v0.0.88)

1. **NovaReuniao.tsx:** após confirmação, disparar `sincronizarGoogle(id)` AUTOMATICAMENTE
   (uma vez por confirmação — useRef para não duplicar em re-render). O botão manual vira
   "tentar novamente" quando o sync falhar (estado nao_sincronizada) e continua disponível
   como retry.
2. **Comprovante:** mostra "Sincronizando com o Google Calendar…" durante a chamada e o
   resultado (sincronizada / nao_sincronizada + motivo + retry). Nenhuma mudança no hook.
3. **Provas:** sync automático provado por evidência de rede no navegador (registro → chamada
   agenda/sincronizar sem clique); prova real com evento criado na agenda de teste;
   idempotência (2ª chamada → ja_sincronizada, sem 2º evento); falha → ocorrência intacta +
   retry; RLS 403; exceção NÃO sincroniza; fixtures limpas.

## Decisões embutidas (champion pode vetar)

- Sync automático SÓ em ocorrência confirmada (exceção aguardando aprovação não sincroniza —
  a reunião ainda não é fato).
- Botão manual permanece como retry/fallback (não sai).
- Registro NUNCA depende do Google: se o sync falhar, a ocorrência continua confirmada e o
  champion pode tentar de novo depois (mesma garantia da LT-2-T01).

## Riscos

- Duplicidade: fechada pela idempotência do hook (google_event_id) + marker na importação.
- JSVM/roteamento: nenhuma mudança no backend — só o frontend passa a chamar sozinho.

## Teste humano previsto

Registrar uma reunião pela UI → conferir o evento na agenda Google da empresa SEM clicar em
sincronizar; comprovante mostra "sincronizada".
