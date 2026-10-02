# Estado atual — Adapta Cliente

- task_id: LT-1-T03 (leva técnica — fila de revisão e aprovação de exceções)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-002 (estados, exceções auditáveis e idempotência) + política F1-T05 (RN-1-11 a RN-1-14) + matriz F1-T04
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-02T11:48:00-03:00 — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: pendente
- verificacao_automatica: passou — build/QA v0.0.18 sem erros; 11 provas de API (P1–P11): consultor bloqueado na aprovação (404 defesa), gestor sem motivo → negado, transição inválida → negada, gestor aprova com trilha (papel gestor), gestor rejeita → aguardando_correcao, autoaprovação bloqueada (CA-1-07), DELETE → 403, consultor corrige rascunho, RLS de PATCH direto ok, migração 0004 (criado_por/aprovador_por/empresa) aplicada; prova de navegador: fila carrega, gestor aprova exceção pela UI com motivo → confirmado
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-10-02-1150-jsvm-auth-role.md
- ultima_acao: LT-1-T03 implementada (Fila.tsx, hook ocorrencias_transicao.js, migration 0004, navegação) e provas executadas
- proxima_acao: Aguardar teste humano do champion no preview
- atualizado_em: 2026-10-02T11:55:00-03:00

## O que foi implementado (LT-1-T03 — Skip v0.0.18, QA ✓)

1. **`pocketbase/migrations/0004_ocorrencias_campos_fluxo.js`** — campos `criado_por` (solicitante), `aprovador_por` (quem decidiu a exceção) e `empresa` (multiempresa) na collection `ocorrencias`.
2. **`pocketbase/hooks/ocorrencias_transicao.js` (novo)** — transição de estado com: máquina de estados da SPEC-1-002 (tabela de pares permitidos), consultor bloqueado em aguardando_aprovacao (404 defesa + RLS), aprovador ≠ solicitante (CA-1-07), motivo obrigatório nas decisões, trilha auditável no campo motivo ([instante] id (papel): texto), 401 sem auth.
3. **`src/pages/Fila.tsx` (novo)** — fila de ocorrências: filtro por estado (7 estados) e por empresa (Acuidar/Dona Help), badge de estado, detalhe em diálogo com trilha de decisão, botões por perfil (gestor/admin: aprovar/rejeitar com motivo; consultor: marcar correção concluída).
4. **`src/App.tsx`** — rota protegida `/fila`; **`src/components/Layout.tsx`** — link "Fila de ocorrências" na navegação.
5. **`pocketbase/hooks/ocorrencias_criar.js`** — agora grava `criado_por` e `empresa` na criação (CA-1-07 passa a valer para registros novos).

## Provas executadas (v0.0.18)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Fila lista 4 exceções pendentes (via gestor) | ok | ✓ |
| 2 | Consultor tenta aprovar exceção | 404 (defesa em profundidade) | ✓ |
| 3 | Gestor aprova sem motivo | negado — motivo obrigatório | ✓ |
| 4 | Transição inválida (confirmado→pendente) | negada | ✓ |
| 5 | Gestor aprova COM motivo | ok — confirmado, decidido_por, papel gestor | ✓ |
| 6 | Gestor rejeita exceção | ok — aguardando_correcao com trilha | ✓ |
| 7 | Consultor tenta aprovar a PRÓPRIA exceção | 404 (CA-1-07 + RLS) | ✓ |
| 7c | Gestor aprova exceção do consultor | ok (pessoa distinta) | ✓ |
| 8 | DELETE por gestor | 403 — ninguém exclui | ✓ |
| 9 | Consultor corrige rascunho | ok — em_revisao com trilha | ✓ |
| 10 | PATCH direto do consultor em exceção | RLS nega (404) | ✓ |
| 11 | Navegador: gestor aprova pela UI | confirmado com trilha | ✓ |

**Bugs encontrados e corrigidos durante a implementação:**
1. v0.0.13 — sintaxe (patch quebrou linha) + JSVM não acessa constantes top-level → lógica inline (v0.0.14).
2. v0.0.15/16 — `findRecordById` no JSVM aplica regras da collection e falha (404) para registros em aguardando_aprovacao mesmo para gestor → substituído por `findRecordsByFilter` (via do hook F1-T06).
3. v0.0.17 — **causa raiz real:** `auth.role` (propriedade) retorna `undefined` no JSVM — todo perfil era tratado como consultor → fix `auth.getString('role')` (v0.0.18). Capturado como AP-2026-10-02-1150.

**Nota (comportamento PocketBase, AP-1725):** a listagem em aguardando_aprovacao aparece para o consultor (listRule é filtro de estado, não nega), mas ele NÃO consegue agir (hook nega 404; PATCH direto negado pela RLS). A UI orienta: "exceções aguardando aprovação não são visíveis ao consultor" — os botões de decisão só aparecem para gestor/admin.

**Fixtures de teste criadas nesta task (limpeza futura):** sc9v14w5z4w7xft, hpsbcienr2sz8io (+13 anteriores).

## Roteiro de teste humano (LT-1-T03)

1. Abra o preview → login como **gestor-teste** → menu **Fila de ocorrências**.
2. Filtre estado = "Aguardando aprovação de exceção" — deve listar as pendentes.
3. Abra uma delas → escreva o motivo → **Aprovar exceção** → estado vira `confirmado` com trilha.
4. Abra outra → **Rejeitar** com motivo → estado vira `aguardando_correcao`.
5. Saia e entre como **consultor-teste** → as exceções aparecem na lista, mas SEM botões de decisão; tentar agir deve ser negado.
6. **Como reconhecer falha:** consultor consegue aprovar; aprovação sem motivo passa; estado confirmado volta a pendente.

## Pendências de limpeza (registradas)

- Fixtures de teste acumuladas (15 registros) — migration de limpeza na leva (exclusão exige superuser).
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task multiempresa.
- Arquivo `.skip.config.json` com mudança pendente no working tree (não alterado por nós) — inspecionar antes do próximo apply_changes.
- Rotação recomendada das credenciais que passaram pelo chat (chave Google, token Acuidar).
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).