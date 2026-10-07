# STATUS — Projeto Acuidar Franquias

> **Atualizado em:** 2026-10-07 · **Por:** Ethos
> O painel do projeto: fase atual, progresso e o que precisa de atenção.

## Onde estamos

- **Fase atual:** 1 — Registro mínimo confiável e cobertura operacional · aberta em 2026-08-21 · fechamento a definir com a consultoria.
- **Objetivo desta fase:** provar o fluxo de uma reunião revisada até uma ocorrência única confirmada, com recuperação segura e visão de cobertura sem Health Score.
- **No prazo?** ✅ **fase 1 construída ponta a ponta** — 8 tasks de desbloqueio (100%) + leva técnica 9/9 concluída (LT-1-T01..T09); fluxo completo na intranet: login → registro de reunião → fila/aprovação → painel de cobertura → importação do Google Agenda (multiempresa, RLS por perfil e empresa, idempotência, renovação automática de credenciais).
- **Canal do projeto:** `https://github.com/fnavaar/acuidar-adapta-cliente`.

## Progresso da fase

- **Tasks de desbloqueio:** 8/8 (100%) — F1-T01..T08
- **Leva técnica 1:** 9/9 (100%) — LT-1-T01 login (v0.0.8) · LT-1-T02 registro (v0.0.12) · LT-1-T03 fila (v0.0.20) · LT-1-T04 painel (v0.0.22) · LT-1-T05 multiempresa formalizada (v0.0.24) · LT-1-T06 RLS por empresa (v0.0.28) · LT-1-T07 conector Google Agenda (v0.0.37) · LT-1-T08 credencial por empresa (v0.0.43) · LT-1-T09 refresh token automático (v0.0.50) · LT-1-T10 fechamento formal (2026-10-06)
- **Leva técnica 2 + FAROL-1 + FA-8:** LT-2-T01 escrita intranet→Google Calendar (v0.0.76) · LT-2-T02 sync das agendas ao logar (v0.0.83) · FAROL-1 FA-1..FA-7 (farol PECAF/PEDHE + pele NEXUS + carga 2026 + gráficos/mapa, v0.0.80) · FA-8 tela Agenda (v0.0.86) — todas concluídas e aprovadas pelo champion
- **Critérios de aceite:** 18/18 marcados com evidência (LT-1-T10, 2026-10-06) — SPEC-1-001 8/8, SPEC-1-002 5/5, SPEC-1-003 5/5
- **Próximo passo:** validação do consultor do fechamento da fase 1 (gate humano — a documentação está pronta para a revisão)

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
| LT-1 | LT-1-T01..T04 — Fluxo completo da intranet: login → registro de reunião → fila de revisão/aprovação de exceções → painel de cobertura (Skip v0.0.8→v0.0.22) | 2026-10-02 |
| LT-1 | LT-1-T05/T06 — Multiempresa formalizada nas 3 SPECs + RLS por empresa (consultora vê só a sua; gestores/admins ambas) (Skip v0.0.24→v0.0.28) | 2026-10-02 |
| LT-1 | LT-1-T07..T09 — Conector Google Agenda multiempresa: credencial por empresa, decisão (B) para cancelados sem unidade, refresh token automático (Skip v0.0.30→v0.0.50) | 2026-10-06 |
| LT-1 | LT-1-T10 — Fechamento formal da fase 1: 18 critérios de aceite marcados com evidência nas 3 SPECs; STATUS/fase atualizados | 2026-10-06 |
| LT-2 | LT-2-T01 — Escrita intranet→Google Calendar: registro de reunião cria evento na agenda da empresa (hook sincronizar, marker anti-duplicidade, retry por botão) | 2026-10-07 |
| LT-2 | LT-2-T02 — Sincronização das agendas ao logar: Layout dispara a importação por empresa autorizada (fire-and-forget, silencioso) | 2026-10-07 |
| FAROL-1 | FA-1..FA-7 — Farol das Unidades: mapa de acompanhamento + semáforo PECAF/PEDHE por empresa + pele NEXUS + carga 2026 (61 avaliações) + gráficos donut e mapa mensal (Skip v0.0.51→v0.0.80) | 2026-10-07 |
| FA-8 | Tela Agenda — reuniões agendadas + feitas do dia (hook agenda/dia + tela reescrita + menu renomeado; importação automática no login permanece) | 2026-10-07 |

## Multiempresa — Dona Help (2026-09-30 → concluída na leva técnica)

- Decisões aprovadas: integração na fase 1; mesmo sistema com campo `empresa`; mesmo champion.
- Contrato da API Dona Help validado (45 unidades, array direto, mesmos 15 campos) — `06_notas/sinal-multiempresa-dona-help.md`.
- Formalizada na LT-1-T05 (emendas append-only nas 3 SPECs, migration de limpeza, regressão completa) e operacional na LT-1-T06/T08/T09 (RLS por empresa, credencial Google própria, refresh token próprio).

## Pendências restantes (fora de task)

1. **Validação do consultor do fechamento da fase 1** — gate humano; documentação pronta (18/18 CAs com evidência).
2. **Rotação das credenciais que passaram pelo chat** (chave Google, token Acuidar) — ação do champion nos Secrets do Skip.
3. **Publicação em produção** — decisão do champion via Builder/MCP; resolve também a expiração de 7 dias do refresh token em modo Teste.

## Próxima reunião

Data a combinar — validação do consultor do fechamento da fase 1 com as evidências das 3 SPECs.
