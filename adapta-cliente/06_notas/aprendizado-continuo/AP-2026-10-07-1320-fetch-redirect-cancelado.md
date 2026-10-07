# AP-2026-10-07-1320 — Fetch fire-and-forget antes de redirect é cancelado pela navegação

**Origem:** LT-2-T02 (prova do disparo, v0.0.82) · **Data:** 2026-10-07

- **Sinal:** disparo da sincronização das agendas colocado no Login.tsx (fetch fire-and-forget
  seguido de `window.location.href = '/'`) NÃO chegava ao backend — a navegação cancela requests
  pendentes. Nenhuma chamada `agenda/importar` aparecia na rede.
- **Diagnóstico:** `performance.getEntriesByType('resource')` no navegador — 0 entradas para
  `agenda/importar` após o login. O fetch morre com o unload da página.
- **Correção (v0.0.82):** mover o disparo para o Layout (componente da página de DESTINO, já
  carregada após o redirect) com `useRef` para rodar uma vez por sessão. Evidência de rede após a
  correção: admin → 2 chamadas; consultora donahelp → 1 chamada (RLS no disparo).
- **Regra reutilizável:** trabalho assíncrono pós-login deve ser disparado no componente da
  página de destino, NUNCA no handler de login antes do redirect; `sendBeacon` não serve quando
  o endpoint exige header Authorization custom.
- **Confiança:** alta — 0 chamadas antes, 2/1 chamadas depois, provado por evidência de rede.
