# STATUS — Projeto Acuidar Franquias

> **Atualizado em:** 2026-09-30 · **Por:** Ethos
> O painel do projeto: fase atual, progresso e o que precisa de atenção.

## Onde estamos

- **Fase atual:** 1 — Registro mínimo confiável e cobertura operacional · aberta em 2026-08-21 · fechamento a definir com a consultoria.
- **Objetivo desta fase:** provar o fluxo de uma reunião revisada até uma ocorrência única confirmada, com recuperação segura e visão de cobertura sem Health Score.
- **No prazo?** em risco controlado — resta apenas F1-T08 (fontes do painel); todas as 7 demais tasks de desbloqueio concluídas, SPEC-1-002 100% desbloqueada.
- **Canal do projeto:** `https://github.com/fnavaar/acuidar-adapta-cliente`.

## Progresso da fase

- **Tasks:** 7/8 (87,5%)
- **Próxima task do champion:** F1-T08 (fontes, latência, destino e RLS do painel) — champion exerce o papel de responsável técnico.

## Travas ativas

| Trava | Desde | Quem resolve | Ação em curso |
|---|---|---|---|
| Fontes/latência/destino do painel | 2026-08-21 | Responsável técnico | F1-T08 |

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

## Próxima reunião

Data a combinar — demonstrar evidências das tasks de desbloqueio e confirmar a abertura da leva técnica.