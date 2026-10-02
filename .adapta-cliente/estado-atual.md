# Estado atual — Adapta Cliente

- task_id: LT-1-T04 (leva técnica — painel de cobertura operacional, SPEC-1-003)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-003 (painel de cobertura operacional) + mapa de fontes F1-T08 (`06_notas/mapa-fontes-painel.md`) + semântica de estados F1-T07 (`06_notas/politica-datas-elegibilidade-estados.md`) + matriz F1-T04
- etapa: aguardando_autorizacao
- autorizacao_implementacao: pendente — análise apresentada ao champion em 2026-10-02T15:30Z; aguardando "sim" em mensagem posterior
- teste_humano: pendente (após implementação)
- verificacao_automatica: pendente (após implementação)
- aprendizado: pendente
- ultima_acao: análise profunda da LT-1-T04 concluída (baseline medido: 25 ocorrências — 21 confirmado, 3 em_revisao, 1 aguardando_aprovacao; 8 acuidar + 1 donahelp + 16 pré-LT03 sem campo empresa; proxy de unidades ok 174+55)
- proxima_acao: aguardar autorização para implementar
- atualizado_em: 2026-10-02T15:35:00-03:00

## Plano acordado na análise (resumo para o implementador)

1. **Backend — hook `painel_cobertura.js`** (`GET /backend/v1/painel/cobertura`): agrega ocorrências por unidade × mês (janela RN-1-16), calcula os 6 estados da F1-T07 sobre ocorrências da intranet, lista unidades por empresa via proxy existente (`/backend/v1/unidades`), trata `dados_indisponiveis` (RN-1-14) com fonte+timestamp, somente leitura. Unidades sem ocorrência no mês = `reuniao_pendente` (elegibilidade total RN-1-18 — toda unidade ativa aparece).
2. **Frontend — `src/pages/Painel.tsx`** (rota `/painel`, link no Layout): filtro por empresa + mês; tabela por unidade com os 6 estados (contagem), rótulos "Cobertura operacional" / "Qualidade do registro" (RN-1-15 — NUNCA Health Score/score/ranking/peso); timestamp da última atualização; link para a fila (evidência de origem via `/fila`); RLS = todos autenticados (F1-T08), somente leitura.
3. **Migração de dados (campo empresa):** 16 ocorrências pré-LT03 têm `empresa` vazio — o painel as agrupa sob "acuidar" por padrão (todas as fixtures são da Acuidar; Dona Help nunca teve registro sem empresa). Sem escrita nos registros.
4. **Navegação:** App.tsx (rota protegida) + Layout.tsx (link "Painel de cobertura").

## Critérios de aceite da SPEC-1-003 (alvo das provas)

- CA-1-11: unidade localizável em cada estado + evidência de origem (link para a fila)
- CA-1-12: fonte indisponível → `dados_indisponiveis` com fonte+timestamp, fora da contagem
- CA-1-13: incompleta exibida como incompleta, nunca confirmada
- CA-1-14: somente leitura + RLS (3 perfis leem; escrita negada)
- CA-1-15: nenhum score/peso/faixa/ranking no painel

## Pendências de limpeza (carregadas da LT-1-T03)

- ~20 fixtures de teste em ocorrencias (exclusão exige superuser) — poluem um pouco o painel de teste; limpeza futura.
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task multiempresa.
- Rotação de credenciais que passaram pelo chat (chave Google, token Acuidar).
- Validação do consultor do fechamento da fase 1 (changelog 2026-10-02).