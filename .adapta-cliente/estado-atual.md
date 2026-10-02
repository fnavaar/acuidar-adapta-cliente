# Estado atual — Adapta Cliente

- task_id: nenhuma (LT-1-T06 concluída — leva técnica 6/6)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: decisão do Champion de 2026-10-02T15:43Z (RLS por empresa) + matriz F1-T04 + emendas multiempresa (LT-1-T05)
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-10-02T16:10Z — champion autorizou o plano ("pode") após o relatório de análise
- teste_humano: aprovado — 2026-10-02T16:24Z — champion confirmou o teste ("tudo ok")
- verificacao_automatica: passou — revalidação do zero na v0.0.28 (8 provas RV): isolamento na fila (consultora-donahelp 4 registros todos donahelp; consultor-acuidar 3 todos acuidar; gestor 7 ambas), burlas server-side negadas 403 (criar/unidades/painel), gestor vê ambas (174+55), consultora usa o que é dela (painel donahelp ok), CA-1-07 cruza empresas (ela aprova a própria → 404; gestor → 200), PATCH direto em acuidar → 404, sem auth → 401
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-10-02-1620-validacao-empresa-hooks.md
- ultima_acao: LT-1-T06 concluída
- proxima_acao: Aguardar pedido do champion (pendências: rotação de credenciais, validação do consultor, conector Google Agenda)
- atualizado_em: 2026-10-02T16:30:00-03:00

## Histórico da LT-1-T06 (concluída)

**Implementação (v0.0.25–v0.0.28):**
1. Migration 0007 — campo `empresas_autorizadas` nos usuários + contas de teste preenchidas + nova conta `consultora-donahelp-teste@donahelpbr.com.br` (role consultor, só donahelp).
2. Migration 0008 — RLS por empresa em `ocorrencias` (list/view/update filtram por empresas_autorizadas; gestor/admin passam sempre; empresa vazia = acuidar).
3. Hooks server-side (v0.0.26) — `ocorrencias_criar`, `painel_cobertura` e `unidades_proxy` negam (403) empresa não autorizada.
4. Frontend (v0.0.28) — formulário e painel mostram só as empresas autorizadas do usuário.
5. Migration 0009 (v0.0.27) — fixture "Burla RLS" (prova P4 pre-fix) excluída.

**Bug pego pela prova:** a tentativa de burla (criar ocorrência Acuidar como consultora Dona Help) passou na primeira execução — o hook criar não validava empresa. Corrigido com validação server-side nos 3 hooks; prova refeita: 403 ✓.

**Critérios:** CA-A ✓ (consultora donahelp isolada) · CA-B ✓ (consultor acuidar isolado) · CA-C ✓ (gestor/admin ambas) · CA-D ✓ (burla via API negada server-side) · CA-E ✓ (regressão completa dos 3 perfis originais).

## Leva técnica — status final (2026-10-02)

| Task | Escopo | Status |
|---|---|---|
| LT-1-T01 | Tela de login da intranet | ✅ v0.0.8 |
| LT-1-T02 | Registro de reunião → ocorrência | ✅ v0.0.12 |
| LT-1-T03 | Fila de revisão e aprovação de exceções | ✅ v0.0.20 |
| LT-1-T04 | Painel de cobertura operacional | ✅ v0.0.22 |
| LT-1-T05 | Formalização multiempresa + limpeza | ✅ v0.0.24 |
| LT-1-T06 | RLS por empresa | ✅ v0.0.28 |

**Fluxo completo da fase 1 na intranet, com isolamento por empresa:** login → registro (só na(s) sua(s) empresa(s)) → fila (só a sua empresa) → painel (só a sua empresa). Gestores/admins com visão consolidada das duas.

## Pendências restantes

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.
- Conector Google Agenda — exige decisão de escopo do champion.