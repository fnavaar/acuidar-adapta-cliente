# Estado atual — Adapta Cliente

- task_id: nenhuma (LT-1-T05 concluída)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: emendas append-only nas SPECs 1-001, 1-002 e 1-003 (multiempresa) + `06_notas/sinal-multiempresa-dona-help.md`
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-10-02T15:42Z — champion autorizou o plano ("sim", incluindo a limpeza das fixtures)
- teste_humano: aprovado — 2026-10-02T15:47Z — champion confirmou o teste ("sim")
- verificacao_automatica: passou — revalidação do zero (v0.0.24): banco com ZERO fixtures (3 registros reais), hook temporário 404, emenda "LT-1-T05 — Formalização multiempresa" presente nas 3 SPECs (verificadas via GitHub raw), regressão pós-0006 (fila 200, painel ok 174 unidades)
- aprendizado: sem sinal reutilizável — a única lição (fixtures de teste criadas por automação devem ser limpas na mesma task que as criou) já está coberta pelas lições de teste humano registradas em 2026-10-02
- ultima_acao: LT-1-T05 concluída (migration 0006 complementar excluiu a última fixture da regressão)
- proxima_acao: Aguardar pedido do champion (LT-1-T06 — RLS por empresa — já sinalizada como próxima)
- atualizado_em: 2026-10-02T15:55:00-03:00

## Histórico da LT-1-T05 (concluída)

**Implementação (v0.0.23–v0.0.24):**
1. Migration 0005 — 26 fixtures de teste excluídas (IDs conferidos 1 a 1; banco 27 → 1).
2. Hook temporário `validar_contrato_donahelp.js` removido — rota 404 provada.
3. Emendas append-only multiempresa nas 3 SPECs (commits f73d467, 1c6fc79, 7a4ad43).
4. Regressão completa pós-limpeza: login 3 perfis, idempotência, transição com trilha, fila com RLS, painel multiempresa (Acuidar 174 / Dona Help 55).
5. Migration 0006 (v0.0.24) — última fixture da regressão ("R6 transicao") excluída; banco final: 3 registros REAIS (1 da LT-1-T03 + 2 criados pelo champion durante os testes).

**Critérios:** CA-A ✓ (emendas nas 3 SPECs) · CA-B ✓ (hook 404) · CA-C ✓ (zero fixtures) · CA-D ✓ (regressão completa).

## Próxima task sinalizada (não iniciada)

**LT-1-T06 — RLS por empresa** (decisão do champion, 2026-10-02T15:43Z): consultoras Dona Help veem só unidades Dona Help; consultoras Acuidar só Acuidar; gestores/admins veem ambas. Exige campo `empresas_autorizadas` no usuário + filtro server-side nos hooks e na lista de unidades + contas de teste para provar o isolamento.

## Pendências restantes

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.
- Conector Google Agenda — construção nova, exige decisão de escopo do champion.