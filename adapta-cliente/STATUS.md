# STATUS — Projeto Acuidar Franquias

> **Atualizado em:** 2026-09-30 · **Por:** Ethos
> O painel do projeto: fase atual, progresso e o que precisa de atenção.

## Onde estamos

- **Fase atual:** 1 — Registro mínimo confiável e cobertura operacional · aberta em 2026-08-21 · fechamento a definir com a consultoria.
- **Objetivo desta fase:** provar o fluxo de uma reunião revisada até uma ocorrência única confirmada, com recuperação segura e visão de cobertura sem Health Score.
- **No prazo?** em risco controlado — dependências de RLS e superfície autorizada ainda exigem evidência; contrato da API de leitura foi validado.
- **Canal do projeto:** `https://github.com/fnavaar/acuidar-adapta-cliente`.

## Progresso da fase

- **Tasks:** 4/8 (50%)
- **Próxima task do champion:** nenhuma na leva 1 — restam F1-T03 (superfície técnica), F1-T04 (matriz RLS), F1-T06 (Leva 2, agora desbloqueada pela F1-T01) e F1-T08 (fontes do painel).

## Travas ativas

| Trava | Desde | Quem resolve | Ação em curso |
|---|---|---|---|
| Matriz RLS | 2026-08-21 | Administrador do Portal | F1-T04 |
| Superfície técnica da integração | 2026-08-21 | Responsável técnico | F1-T03 |
| Fontes/latência/destino do painel | 2026-08-21 | Responsável técnico | F1-T08 |

## Entregas concluídas

| Fase | O que foi entregue | Fechada em |
|---|---|---|
| F1 | F1-T02 — Chave oficial da unidade e regra de elegibilidade registradas pelo Champion | 2026-08-26 |
| F1 | F1-T05 — Política de exceção de data aprovada pelo Champion (bloqueio >1 dia, exceção com justificativa, prazo 24h/48h, aprovador substituto) | 2026-08-28 |
| F1 | F1-T07 — Semântica da cobertura operacional aprovada (janela mensal por unidade, elegibilidade total, 6 estados definidos) | 2026-09-22 |
| F1 | F1-T01 — Contrato de leitura da API do Portal validado (GET /api/dados/unidades, Authorization: token, 172 unidades, códigos únicos) | 2026-09-30 |

## Próxima reunião

Data a combinar — demonstrar evidências das tasks de desbloqueio e confirmar a abertura da leva técnica.