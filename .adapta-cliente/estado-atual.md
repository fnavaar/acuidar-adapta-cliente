# Estado atual — Adapta Cliente

- task_id: LT-1-T05 (leva técnica — formalização multiempresa Dona Help + limpeza técnica)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: emendas append-only nas SPECs 1-001, 1-002 e 1-003 (multiempresa) + `06_notas/sinal-multiempresa-dona-help.md` + contrato validado (padrão F1-T01)
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-02T15:41Z — champion autorizou o plano ("sim", incluindo a limpeza das fixtures)
- teste_humano: pendente
- verificacao_automatica: passou — build/QA v0.0.23 (migration 0005 executada: 26 fixtures excluídas; hook temporário removido: rota 404); emendas multiempresa gravadas nas 3 SPECs (commits f73d467, 1c6fc79, 7a4ad43); regressão completa pós-limpeza: 3 perfis autenticam (200), registro com idempotência (reenvio não duplica), fila com RLS (consultor 404 / gestor 200), painel multiempresa (acuidar 174, donahelp 55, 6 estados corretos)
- aprendizado: pendente
- ultima_acao: LT-1-T05 implementada (emendas nas 3 SPECs + remoção do hook temporário + migration 0005 de limpeza + regressão completa)
- proxima_acao: Aguardar teste humano do champion (verificação documental + tela limpa)
- atualizado_em: 2026-10-02T15:50:00-03:00

## O que foi implementado (LT-1-T05)

1. **Emenda multiempresa nas 3 SPECs** (append-only, datada de 2026-10-02): SPEC-1-001 (campo empresa no registro, parser por formato, credenciais nos Secrets), SPEC-1-002 (campo empresa na collection, fila filtra por empresa, CA-1-07 cruza empresas), SPEC-1-003 (painel com filtro por empresa, agregação separada, dados_indisponiveis por empresa).
2. **Remoção do hook temporário** `pocketbase/hooks/validar_contrato_donahelp.js` (Skip v0.0.23) — função cumprida na validação do contrato (2026-09-30); rota confirmada inexistente (404).
3. **Migration 0005 — limpeza de fixtures:** 26 registros de teste excluídos (IDs fixos conferidos um a um antes da execução); 1 registro real preservado (t7nfareuzlwkwr7 — aprovado pelo champion na LT-1-T03). Banco final: 3 registros (o preservado + 2 da regressão).
4. **Regressão completa pós-limpeza (todas PASSOU):** autenticação 3 perfis; registro com idempotência (reenvio → mesma ocorrência, sem duplicata); fila com RLS (consultor bloqueado 404, gestor aprova 200); painel multiempresa (acuidar 174 unidades com 6 estados corretos, donahelp 55).

## Critérios de aceite — status

- CA-A (emendas nas 3 SPECs): ✓ — commits f73d467, 1c6fc79, 7a4ad43 (append-only, sem reescrever história)
- CA-B (hook temporário removido): ✓ — arquivo fora do projeto, rota 404, v0.0.23
- CA-C (banco sem fixtures): ✓ — 26 excluídas, contagem de padrões de teste = 0
- CA-D (regressão completa): ✓ — 4 provas RG executadas, todas passaram

## Roteiro de teste humano (LT-1-T05)

1. Abra o repositório (`fnavaar/acuidar-adapta-cliente`) → verifique as 3 SPECs em `04_fase-atual/specs/` — cada uma tem a emenda "LT-1-T05 — Formalização multiempresa" no fim da tabela de Emendas, sem alterar o que já estava lá.
2. Abra o preview → **Painel de cobertura** → confira que só existem registros reais (nada de "Teste*", "RV*", "CA-1*").
3. **Fila de ocorrências** → os antigos registros de teste sumiram; só o histórico real aparece.
4. **Como reconhecer falha:** fixture de teste ainda visível na fila/painel; emenda faltando em alguma SPEC; hook temporário ainda respondendo.

## Pendências restantes (fora desta task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.
- **RLS por empresa (nova decisão do champion, 2026-10-02T15:43Z):** consultoras Dona Help veem só unidades Dona Help; consultoras Acuidar só Acuidar; gestores/admins veem ambas — exige campo `empresas_autorizadas` no usuário + filtro nas telas/hooks; será a próxima task (LT-1-T06) após a conclusão desta.