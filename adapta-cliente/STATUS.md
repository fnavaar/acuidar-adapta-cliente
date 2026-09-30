# STATUS — Projeto Acuidar Franquias

> **Atualizado em:** 2026-09-30 · **Por:** Ethos
> O painel do projeto: fase atual, progresso e o que precisa de atenção.

## Onde estamos

- **Fase atual:** 1 — Registro mínimo confiável e cobertura operacional · aberta em 2026-08-21 · fechamento a definir com a consultoria.
- **Objetivo desta fase:** provar o fluxo de uma reunião revisada até uma ocorrência única confirmada, com recuperação segura e visão de cobertura sem Health Score.
- **No prazo?** ✅ **todas as 8 tasks de desbloqueio concluídas** — as 3 SPECs da fase 1 estão integralmente desbloqueadas; próximo passo é a geração da leva técnica (após validação do consultor).
- **Canal do projeto:** `https://github.com/fnavaar/acuidar-adapta-cliente`.

## Progresso da fase

- **Tasks:** 8/8 (100%)
- **Próximo passo:** validação do consultor do fechamento da fase 1 → geração da leva técnica (`gerar-tasks`) para as SPECs 1-001, 1-002 e 1-003.

## Travas ativas

Nenhuma — todas as travas de desbloqueio da fase 1 foram resolvidas.

| Trava | Desde | Quem resolve | Ação em curso |
|---|---|---|---|
| ~~Matriz RLS~~ | 2026-08-21 | Administrador do Portal | ✅ resolvida (F1-T04, 2026-09-30) |
| ~~Fontes/latência/destino do painel~~ | 2026-08-21 | Responsável técnico | ✅ resolvida (F1-T08, 2026-09-30) |

## Entregas concluídas

| Fase | O que foi entregue | Fechada em |
|---|---|---|
| F1 | F1-T02 — Chave oficial da unidade e regra de elegibilidade registradas pelo Champion | 2026-08-26 |
| F1 | F1-T05 — Política de exceção de data aprovada pelo Champion (bloqueio >1 dia, exceção com justificativa, prazo 24h/48h, aprovador substituto) | 2026-08-28 |
| F1 | F1-T07 — Semântica da cobertura operacional aprovada (janela mensal por unidade, elegibilidade total, 6 estados definidos) | 2026-09-22 |
| F1 | F1-T01 — Contrato de leitura da API do Portal validado (GET /api/dados/unidades, Authorization: token, 172 unidades, códigos únicos) | 2026-09-30 |
| F1 | F1-T06 — Consulta de recuperação pós-timeout provada na intranet (confirmada/ausente/inconclusivo + idempotência UNIQUE) | 2026-09-30 |
| F1 | F1-T03 — Superfície técnica autorizada por escrito (repositório, ambiente, deploy, segredos) | 2026-09-30 |
| F1 | F1-T04 — Matriz de perfis e RLS aprovada e implementada (3 perfis × 5 permissões, campo role, RLS de ocorrências, 3 contas de teste, prova negativa de autoaprovação) | 2026-09-30 |
| F1 | F1-T08 — Mapa de fontes, latência, destino e RLS do painel aprovado (intranet como destino; ocorrências tempo real; unidades Acuidar 172 + Dona Help 45 diárias; reuniões entrada manual; RLS todos autenticados; deploy Luis Carlos) | 2026-09-30 |

## Multiempresa — Dona Help (2026-09-30)

- Decisões aprovadas: integração na fase 1; mesmo sistema com campo `empresa`; mesmo champion.
- Contrato da API Dona Help validado (45 unidades, array direto, mesmos 15 campos) — `06_notas/sinal-multiempresa-dona-help.md`.
- Emendas nas SPECs 1-002 (F1-T04) e 1-003 (F1-T08) já refletem a multiempresa; emenda na SPEC-1-001 pendente de formalização na leva técnica.

## Próxima reunião

Data a combinar — demonstrar evidências das tasks de desbloqueio e confirmar a abertura da leva técnica.