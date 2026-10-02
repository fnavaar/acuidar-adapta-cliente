# Debug 2026-10-02 — LT-1-T03: botão "Aprovar exceção" não funcionava na UI

## Sintoma relatado
Champion testou a fila no preview (v0.0.18): ao abrir uma ocorrência em
`aguardando_aprovacao_de_excecao` como gestor e clicar em **Aprovar exceção**, nada acontecia
(sem mudança de estado, sem mensagem). As provas de API da implementação tinham passado — a falha
era específica da chamada feita pelo navegador.

## Reprodução
Preview → login gestor-teste → Fila → abrir "RV divergencia" → motivo → Aprovar exceção.
Reproduzido em navegador automatizado: clique não mudava o estado e nenhum erro aparecia na tela.

## Diagnóstico (cadeia causal demonstrada)
1. O hook backend responde corretamente via curl direto no backend (200 com trilha) — descartado.
2. `pb.send('/backend/v1/ocorrencias/transicao')` prefixa **/api** → POST ia para
   `/api/backend/v1/...`, que não existe → **404 silencioso** (prova: curl na variante com /api = 404;
   sem /api = 200).
3. Corrigido para `fetch('/backend/v1/...')` relativo — mas o **nginx do preview não repassa
   POST /backend/*** (retorna **405** do próprio nginx; GET passa). Prova: fetch no browser para
   `/backend/v1/...` = 405; para a URL absoluta do backend = 200.
4. Correção final (v0.0.20): `fetch(pb.baseUrl + '/backend/v1/ocorrencias/transicao')` — URL
   absoluta do backend, token do authStore. Prova no navegador: clique em Aprovar → diálogo fecha,
   item vira `confirmado`, sem erro na tela.

## Causa raiz
Duas camadas de roteamento entre o SPA do preview e o backend: (1) o SDK PocketBase prefixa `/api`
em chamadas `pb.send`, e (2) o nginx do preview só repassa métodos GET/HEAD de rotas fora do padrão
`/api/*`. Rotas custom em `/backend/v1/*` só são alcançáveis do navegador via URL absoluta do
backend (`pb.baseUrl`).

## Correção
`src/pages/Fila.tsx` — substituição de `pb.send` por `fetch` com URL absoluta
(`pb.baseUrl + '/backend/v1/ocorrencias/transicao'`), token do `pb.authStore`, tratamento de erro
não-OK com mensagem na tela. Skip v0.0.20 (QA ✓).

## Verificação automática (pós-fix, v0.0.20)
- Navegador: gestor aprova "RV divergencia" pela UI → `confirmado` com trilha, diálogo fecha, sem erro. ✓
- Regressão API: consultor bloqueado (404) ✓ · gestor sem motivo negado ✓ · DELETE 403 ✓ ·
  máquina de estados intacta ✓
- Registro wt02 recolocado em `aguardando_aprovacao_de_excecao` para o teste humano do champion.

## Gate atual
aguardando_teste_humano (v0.0.20) — champion refaz o teste do botão Aprovar/Rejeitar no preview.