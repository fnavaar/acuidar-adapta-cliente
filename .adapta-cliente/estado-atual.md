# Estado atual — Adapta Cliente

- task_id: LT-1-T08 (leva técnica — conector do Google Agenda para a Dona Help: credencial por empresa)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: LT-1-T07 (mesma base) + mapa de fontes F1-T08 §5 + decisões multiempresa de 2026-09-30 (mesmo sistema, campo empresa, mesmo champion) + RLS por empresa (LT-1-T06)
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-06T08:34-03:00 — champion autorizou o plano ("sim") após o relatório de análise; confirmação adicional: cada empresa tem agenda Google própria (emails diferentes)
- teste_humano: pendente (roteiro entregue em 2026-10-06)
- verificacao_automatica: passou — Skip v0.0.39 QA ✓; provas: donahelp sem credencial → credencial_ausente com nome do secret (GOOGLE_CALENDAR_TOKEN_DONAH), RLS 403 nas duas direções, empresa inválida erro explícito, sem auth 401, tela /agenda 200, prova de navegador (alerta com nome do secret exibido), banco intacto (12 registros)
- aprendizado: pendente
- ultima_acao: LT-1-T08 implementada (v0.0.39) — credencial Google por empresa no hook + tela exibe o secret que falta
- proxima_acao: aguardar teste humano do champion (importação acuidar com token renovado + credencial DONAH gravada para a prova donahelp)
- atualizado_em: 2026-10-06T08:55:00-03:00

## O que foi implementado na LT-1-T08 (Skip v0.0.39, QA ✓)

1. **Hook `POST /backend/v1/agenda/importar`** — credencial do Google escolhida POR EMPRESA: `acuidar` → `GOOGLE_CALENDAR_TOKEN`; `donahelp` → `GOOGLE_CALENDAR_TOKEN_DONAH` (decisão do champion de 2026-10-06: cada empresa tem agenda Google própria, emails diferentes). Sem credencial da empresa → resposta explícita `credencial_ausente` com campo `secret` (nome exato do secret a gravar) e a empresa na mensagem. Todo o restante intocado: janela -7d/+14d com showDeleted=true, lookup de unidade (RN-1-19), regras F1-T02, idempotência `google_calendar:eventId:unidade:tipo`, RLS por empresa (403).
2. **Tela `/agenda`** — alerta de credencial ausente agora exibe o nome do secret que falta (ex.: "Credencial ausente (GOOGLE_CALENDAR_TOKEN_DONAH)").

## Provas executadas (v0.0.39)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | donahelp sem credencial DONAH | `credencial_ausente` com `secret: GOOGLE_CALENDAR_TOKEN_DONAH` + mensagem com a empresa | ✓ |
| 2 | RLS: consultora-donahelp pede acuidar | 403 | ✓ |
| 3 | RLS: consultor-acuidar pede donahelp | 403 | ✓ |
| 4 | Empresa inválida | erro explícito | ✓ |
| 5 | Sem auth | 401 | ✓ |
| 6 | Tela /agenda | 200 no preview | ✓ |
| 7 | Prova de navegador | login admin → seleciona Dona Help → Importar → alerta "Credencial ausente (GOOGLE_CALENDAR_TOKEN_DONAH)" exibido | ✓ |
| 8 | Banco intacto | 12 registros reais, nenhuma importação nas provas | ✓ |

**Limitação real registrada:** a prova P1 (importação acuidar com Google 200) e a prova donahelp com agenda real dependem de credenciais válidas — o access token da Acuidar expirou (~1h, limitação conhecida) e o secret da Dona Help ainda não foi gravado. Ambas entram no teste humano do champion.

## Roteiro de teste humano (LT-1-T08)

1. **Renove o token da Acuidar** (OAuth Playground, passos de ontem) → atualize `GOOGLE_CALENDAR_TOKEN` nos Secrets → importe como **Acuidar**: esperado Google 200 e resumo idêntico ao comportamento provado (reimportar → 0 criadas).
2. **Gere o token da agenda da Dona Help** (mesmo processo, conta Google da Dona Help) → grave `GOOGLE_CALENDAR_TOKEN_DONAH` nos Secrets → importe como **Dona Help**: esperado Google 200 com os eventos da agenda dela (ou sem_unidade para títulos sem unidade identificável — RN-1-19).
3. **Sem a credencial DONAH** (estado atual): importar como Dona Help mostra o alerta com o nome do secret — comportamento correto provado.
4. **Como reconhecer falha:** importação Acuidar com erro de credencial após renovar; alerta sem o nome do secret; duplicatas ao reimportar; consultora vendo empresa alheia.

## Pendências restantes (fora de task)

- **Refresh token automático:** access token de conta de teste expira ~1h; evoluir o hook para renovar via `GOOGLE_CALENDAR_REFRESH_TOKEN` quando o champion quiser.
- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.