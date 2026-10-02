# Estado atual — Adapta Cliente

- task_id: LT-1-T04 (leva técnica — painel de cobertura operacional, SPEC-1-003)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-003 + mapa de fontes F1-T08 + semântica de estados F1-T07 + matriz F1-T04
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-02T15:28Z — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: pendente
- verificacao_automatica: passou — build/QA v0.0.22 sem erros; 12 provas de API + prova de navegador (resumo abaixo)
- aprendizado: pendente
- ultima_acao: LT-1-T04 implementada (hook painel_cobertura + tela /painel + navegação) e provas executadas
- proxima_acao: Aguardar teste humano do champion no preview
- atualizado_em: 2026-10-02T15:40:00-03:00

## O que foi implementado (LT-1-T04 — Skip v0.0.22, QA ✓)

1. **`pocketbase/hooks/painel_cobertura.js` (novo)** — `GET /backend/v1/painel/cobertura?empresa=&mes=`: agrega ocorrências por unidade × mês (RN-1-16), classifica os 6 estados da F1-T07 (confirmada exige todos os obrigatórios — RN-1-12/13; não-confirmada → ocorrência_pendente; unidade sem registro no mês → reuniao_pendente — RN-1-18), busca unidades via fonte autorizada com parser por empresa (wrapper Acuidar / array Dona Help), `dados_indisponiveis` com fonte+timestamp quando a fonte falha (RN-1-14), ocorrências com unidade fora do cadastro contadas à parte sem inferência (RN-1-19). Somente leitura.
2. **`src/pages/Painel.tsx` (novo)** — rota `/painel`: filtros empresa + mês (4 meses), cartões de totais por estado, tabela das 174/55 unidades com badges por estado, link "ver na fila" por unidade com ocorrências (CA-1-11), banner de dados indisponíveis, timestamp das fontes. Rótulos "Cobertura operacional"/"Qualidade do registro"; NENHUM score/peso/faixa/ranking (CA-1-15, provado por varredura).
3. **`src/App.tsx`** — rota protegida `/painel`; **`src/components/Layout.tsx`** — link "Painel de cobertura".

## Provas executadas (v0.0.22)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Painel acuidar 2026-10 (gestor) | ok — 174 unidades, 8 ocorrências no mês, 172 sem registro (reuniao_pendente), 2 pendentes, 6 confirmadas | ✓ |
| 2 | Painel donahelp 2026-10 | ok — 55 unidades, 1 confirmada (unidade 101) | ✓ |
| 3 | RLS: consultor acessa | 200 (todos autenticados — F1-T08) | ✓ |
| 4 | Sem auth | 401 | ✓ |
| 5 | Empresa inválida | erro explícito | ✓ |
| 6 | Mês sem dados (2026-05) | 0 ocorrências; 174 unidades em reuniao_pendente (elegibilidade total, nunca some da lista) | ✓ |
| 7 | CA-1-13: ocorrência com relato vazio → INCOMPLETA | unidade 24: incompleta=1, confirmada=0 (nunca confirmada) | ✓ |
| 8 | Mês inválido ("lixo") | cai no mês atual sem quebrar | ✓ |
| 9 | CA-1-15: varredura score/health/peso/faixa/ranking | NENHUM termo na resposta | ✓ |
| 10 | CA-1-14: POST na rota GET-only | 404 (sem escrita) | ✓ |
| 11 | Escrita via painel | não existe endpoint de escrita; RLS das ocorrências inalterada (provas LT-1-T03) | ✓ |
| 12 | Navegador: /painel carrega tabela com 174 linhas, badges e "ver na fila"; sem Health Score na tela | ✓ |

**Bug corrigido durante a implementação:** v0.0.21 — `JSON.parse(res.body)` falhou silenciosamente (res.body é bytes — AP-1715) → parser com res.json + fallback TextDecoder (v0.0.22). O caminho de erro apareceu como dados_indisponiveis (comportamento correto do RN-1-14) e a prova P1 pegou a falha.

**Fixtures criadas nesta task:** 3l8ansm0w8zby0a ("Painel incompleta teste" — relato restaurado após a prova).

## Roteiro de teste humano (LT-1-T04)

1. **Recarregue com Ctrl+Shift+R** (evitar cache do bundle antigo — lição da LT-1-T03).
2. Login (qualquer perfil — painel é para todos autenticados) → menu **Painel de cobertura**.
3. Confira: cartões de totais, tabela com 174 unidades (Acuidar) e filtro de mês.
4. Troque a empresa para **Dona Help** — 55 unidades, unidade 101 com 1 confirmada.
5. Clique em **"ver na fila"** numa unidade com ocorrências → deve abrir a fila (evidência de origem).
6. Confira que NÃO existe score, ranking, peso ou "Health Score" em lugar nenhum — apenas os rótulos "Cobertura operacional" e "Qualidade do registro".
7. **Como reconhecer falha:** tela em branco, erro de comunicação, unidade com ocorrência aparecendo como confirmada sem ter relato, ou qualquer número/score que não seja contagem de estados.

## Pendências de limpeza (registradas)

- Fixtures de teste acumuladas (~20) — migration de limpeza futura (exclusão exige superuser).
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task multiempresa.
- Rotação recomendada das credenciais que passaram pelo chat (chave Google, token Acuidar).
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).