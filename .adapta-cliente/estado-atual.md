# Estado atual — Adapta Cliente

- task_id: LT-1-T03 (leva técnica — fila de revisão e aprovação de exceções)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-002 (estados, exceções auditáveis e idempotência) + política F1-T05 (RN-1-11 a RN-1-14) + matriz F1-T04
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-02T11:48:00-03:00 — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: pendente (1ª tentativa falhou — botão Aprovar não funcionava; corrigido em v0.0.20, ver 06_notas/debug/debug-2026-10-02-lt1t03-botao-aprovar.md)
- verificacao_automatica: passou — build/QA v0.0.20 sem erros; 11 provas de API (P1–P11) da implementação + regressão pós-fix (consultor 404, motivo obrigatório, DELETE 403) + prova de navegador pós-fix (gestor aprova pela UI → confirmado com trilha, diálogo fecha, sem erro)
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-10-02-1150-jsvm-auth-role.md
- ultima_acao: DEBUG — causa raiz dupla (pb.send prefixa /api + nginx do preview não repassa POST /backend/*) corrigida com fetch de URL absoluta (pb.baseUrl); v0.0.20
- proxima_acao: Aguardar novo teste humano do champion no preview
- atualizado_em: 2026-10-02T15:15:00-03:00

## O que foi implementado (LT-1-T03 — Skip v0.0.20, QA ✓)

1. **`pocketbase/migrations/0004_ocorrencias_campos_fluxo.js`** — campos `criado_por` (solicitante), `aprovador_por` (quem decidiu a exceção) e `empresa` (multiempresa) na collection `ocorrencias`.
2. **`pocketbase/hooks/ocorrencias_transicao.js` (novo)** — transição de estado com: máquina de estados da SPEC-1-002, consultor bloqueado em aguardando_aprovacao (404 defesa + RLS), aprovador ≠ solicitante (CA-1-07), motivo obrigatório nas decisões, trilha auditável no campo motivo ([instante] id (papel): texto), 401 sem auth.
3. **`src/pages/Fila.tsx` (novo)** — fila de ocorrências: filtro por estado (7 estados) e por empresa (Acuidar/Dona Help), badge de estado, detalhe em diálogo com trilha de decisão, botões por perfil (gestor/admin: aprovar/rejeitar com motivo; consultor: marcar correção concluída). Chamada da transição via `fetch` com URL absoluta do backend (ver debug).
4. **`src/App.tsx`** — rota protegida `/fila`; **`src/components/Layout.tsx`** — link "Fila de ocorrências" na navegação.
5. **`pocketbase/hooks/ocorrencias_criar.js`** — agora grava `criado_por` e `empresa` na criação (CA-1-07 passa a valer para registros novos).

## Bugs encontrados e corrigidos durante a task

1. v0.0.13 — sintaxe (patch quebrou linha) + JSVM não acessa constantes top-level → lógica inline (v0.0.14).
2. v0.0.15/16 — `findRecordById` no JSVM aplica regras da collection e falha (404) para registros em aguardando_aprovacao mesmo para gestor → `findRecordsByFilter` (via do hook F1-T06).
3. v0.0.17 — **causa raiz 1:** `auth.role` (propriedade) retorna `undefined` no JSVM — todo perfil era tratado como consultor → fix `auth.getString('role')` (v0.0.18). AP-2026-10-02-1150.
4. v0.0.18/19 — **causa raiz 2 (falha do teste humano):** `pb.send` prefixa `/api` (rota custom 404) e o nginx do preview não repassa POST `/backend/*` (405) → fix `fetch(pb.baseUrl + '/backend/v1/...')` (v0.0.20). Debug documentado.

**Nota (comportamento PocketBase, AP-1725):** a listagem em aguardando_aprovacao aparece para o consultor (listRule é filtro de estado, não nega), mas ele NÃO consegue agir (hook nega 404; PATCH direto negado pela RLS). A UI orienta e os botões de decisão só aparecem para gestor/admin.

**Fixtures de teste criadas nesta task (limpeza futura):** sc9v14w5z4w7xft, hpsbcienr2sz8io (+13 anteriores).

## Roteiro de teste humano (LT-1-T03 — refazer após o fix v0.0.20)

1. Abra o preview → login como **gestor-teste** → menu **Fila de ocorrências**.
2. Filtre estado = "Aguardando aprovação de exceção" — deve listar as pendentes (ex.: "RV divergencia", "Registro pendente teste").
3. Abra uma delas → escreva o motivo → **Aprovar exceção** → estado vira `confirmado` com trilha e o diálogo fecha.
4. Abra outra → **Rejeitar** com motivo → estado vira `aguardando_correcao`.
5. Saia e entre como **consultor-teste** → as exceções aparecem na lista, mas SEM botões de decisão; tentar agir deve ser negado.
6. **Como reconhecer falha:** consultor consegue aprovar; aprovação sem motivo passa; estado confirmado volta a pendente; erro "Falha de comunicação" ao aprovar.

## Pendências de limpeza (registradas)

- Fixtures de teste acumuladas (15 registros) — migration de limpeza na leva (exclusão exige superuser).
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task multiempresa.
- Arquivo `.skip.config.json` com mudança pendente no working tree (não alterado por nós) — inspecionar antes do próximo apply_changes.
- Rotação recomendada das credenciais que passaram pelo chat (chave Google, token Acuidar).
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).