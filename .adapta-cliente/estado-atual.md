# Estado atual — Adapta Cliente

- task_id: nenhuma (LT-1-T04 concluída — leva técnica 4/4)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-003 + mapa de fontes F1-T08 + semântica de estados F1-T07 + matriz F1-T04
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-10-02T15:28Z — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: aprovado — 2026-10-02T15:33Z — champion confirmou o teste do painel ("ok")
- verificacao_automatica: passou — revalidação do zero na v0.0.22 (9 provas RV): painel acuidar (174 unidades, 6 estados corretos), donahelp (55), RLS 3 perfis 200 / sem auth 401, empresa inválida, mês sem dados (elegibilidade total), CA-1-13 (incompleta nunca confirmada), CA-1-15 (nenhum termo de score/peso/ranking), CA-1-14 (POST negado 404), navegador (tabela 174 linhas, zero score na tela)
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-10-02-1530-res-body-bytes.md
- ultima_acao: LT-1-T04 concluída — leva técnica da fase 1 COMPLETA (4/4)
- proxima_acao: Aguardar pedido do champion (próximo marco: validação do consultor do fechamento da fase 1 e/ou novas tasks da leva técnica)
- atualizado_em: 2026-10-02T15:45:00-03:00

## Histórico da LT-1-T04 (concluída)

**Implementação (v0.0.21–v0.0.22):** hook `GET /backend/v1/painel/cobertura` (agregação unidade × mês, 6 estados da F1-T07, parser por empresa, dados_indisponiveis com fonte+timestamp, somente leitura) + tela `/painel` (filtros empresa/mês, cartões de totais, tabela com badges, link "ver na fila", banner de indisponibilidade) + navegação. Rótulos "Cobertura operacional"/"Qualidade do registro" — zero Health Score (CA-1-15 provado por varredura).

**Bug corrigido:** v0.0.21 — `JSON.parse(res.body)` falhou (res.body é bytes — AP-1715); o erro apareceu como `dados_indisponiveis` (comportamento seguro correto) e a prova P1 pegou → parser com res.json + fallback TextDecoder (v0.0.22). Aprendizado AP-2026-10-02-1530.

**Fixtures criadas:** 3l8ansm0w8zby0a, RV04 incompleta (restauradas após as provas).

## Leva técnica — status final (2026-10-02)

| Task | Escopo | Status |
|---|---|---|
| LT-1-T01 | Tela de login da intranet | ✅ concluída (2026-10-02) — v0.0.8 |
| LT-1-T02 | Registro de reunião → ocorrência | ✅ concluída (2026-10-02) — v0.0.12 |
| LT-1-T03 | Fila de revisão e aprovação de exceções | ✅ concluída (2026-10-02) — v0.0.20 |
| LT-1-T04 | Painel de cobertura operacional | ✅ concluída (2026-10-02) — v0.0.22 |

**A intranet Adapta Cliente tem agora o fluxo completo da fase 1:** login → registro de reunião → fila de revisão/aprovação → painel de cobertura. Produção ainda não publicada (deploy = Luis Carlos via Builder/MCP, quando aprovado).

## Pendências de limpeza (registradas)

- Fixtures de teste acumuladas (~22 registros) — migration de limpeza futura (exclusão exige superuser).
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task multiempresa.
- Rotação recomendada das credenciais que passaram pelo chat (chave Google, token Acuidar).
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).
- Task multiempresa Dona Help — emenda formal nas SPECs pendente (contrato já validado).