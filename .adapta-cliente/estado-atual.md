# Estado atual — Adapta Cliente

- task_id: LT-1-T05 (leva técnica — formalização multiempresa Dona Help + limpeza técnica)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: emendas append-only nas SPECs 1-001, 1-002 e 1-003 (multiempresa já decidida pelo Champion em 2026-09-30 e implementada de fato na leva técnica; falta a formalização documental) + `06_notas/sinal-multiempresa-dona-help.md` + contrato validado (padrão F1-T01)
- etapa: aguardando_autorizacao
- autorizacao_implementacao: pendente — análise apresentada ao champion em 2026-10-02T15:42Z; aguardando "sim" em mensagem posterior
- teste_humano: pendente (após implementação — verificação documental + regressão)
- verificacao_automatica: pendente (após implementação)
- aprendizado: pendente
- ultima_acao: análise profunda da LT-1-T05 concluída (inventário: multiempresa implementada de fato no fluxo inteiro; emenda formal ausente nas 3 SPECs; hook temporário validar_contrato_donahelp.js no projeto; 26 fixtures de teste no banco)
- proxima_acao: aguardar autorização para implementar
- atualizado_em: 2026-10-02T15:45:00-03:00

## Plano acordado na análise (resumo para o implementador)

1. **Emenda formal multiempresa nas 3 SPECs** (append-only): SPEC-1-001 (registro — campo empresa no formulário e na ocorrência), SPEC-1-002 (estados — campo empresa na collection e nos filtros da fila), SPEC-1-003 (painel — filtro por empresa, parser por formato de API). Base: decisões do Champion de 2026-09-30 + contrato validado.
2. **Remoção do hook temporário** `pocketbase/hooks/validar_contrato_donahelp.js` (Skip v0.0.7) — sua função (validação de contrato) está cumprida e documentada.
3. **Migration de limpeza 0005** — exclusão das 26 fixtures de teste (superuser context na migration), deixando o banco limpo para uso real.
4. **Regressão completa** — as provas padrão (login, registro, fila, painel, multiempresa) reexecutadas após a limpeza.

## Critérios de aceite (propostos, binários)

- CA-A: as 3 SPECs contêm a emenda multiempresa formal (append-only, datada, sem reescrever história)
- CA-B: hook temporário removido do projeto (arquivo + versão Skip nova sem ele)
- CA-C: banco sem fixtures de teste (contagem = 0 nos padrões de título de teste)
- CA-D: regressão completa do fluxo passa após a limpeza (login, registro com idempotência, fila com RLS, painel com 6 estados)

## Pendências fora do escopo desta task

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.
- Conector Google Agenda — construção nova, exige decisão de escopo do champion (fora desta task).

## Fontes

- `06_notas/sinal-multiempresa-dona-help.md` (decisões + contrato validado)
- `06_notas/mapa-fontes-painel.md` (parser por empresa aprovado)
- Changelog 2026-09-30 (decisões multiempresa do Champion)