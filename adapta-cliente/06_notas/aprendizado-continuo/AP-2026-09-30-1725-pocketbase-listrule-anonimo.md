# AP-2026-09-30-1725 — PocketBase listRule: anônimo recebe lista vazia (200), não 401

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: F1-T08 (prova de RLS do painel) · regressão das regras da F1-T04
- Sinal: em PocketBase v0.36, `listRule` que exige autenticação (`@request.auth.id != ''`) NÃO retorna 401 para anônimo — retorna HTTP 200 com lista vazia (`totalItems: 0`), porque a regra age como filtro por registro. `viewRule` (GET individual) sim retorna 404. Dados ficam protegidos (0 itens sem auth vs N com auth), mas a API não erro.
- Evidência: regressão pós-recuperação do Skip Cloud (2026-09-30 ~14:35): list sem auth → 200 `{"items":[],"totalItems":0}`; list com auth (3 perfis) → 200 com 2 itens; GET individual sem auth → 404; DELETE por consultor → 403.
- Regra reutilizável: em testes de RLS de listagem, o resultado esperado para anônimo é 200 + lista vazia (não 401); a UI do painel deve checar estado de autenticação no cliente — usuário não logado verá painel vazio, não tela de erro. Para negação explícita de listagem, seria preciso middleware/hook próprio.
- Quando aplicar: prova de RLS de qualquer listagem (ocorrências, unidades, painel) e design da UI da leva técnica.
- Quando não aplicar: GET de registro individual (viewRule → 404) e escritas (createRule/updateRule → 404/403).
- Confiança: alta — observado diretamente na regressão com comparação auth vs anônimo.
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.