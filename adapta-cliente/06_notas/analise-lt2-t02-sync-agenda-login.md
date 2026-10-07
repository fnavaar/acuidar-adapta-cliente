# LT-2-T02 — Sincronização das agendas ao logar (análise 2026-10-07 12:55)

**Pedido do champion (12:53):** "quero que a sincronização das agendas aconteça toda vez que a
pessoa logar no sistema"

## Objetivo e resultado observável

Ao logar com sucesso, o sistema importa automaticamente as agendas Google das empresas que o
usuário pode ver (RLS) — sem botão, sem depender de o usuário lembrar de importar. O botão manual
da tela /agenda CONTINUA (para reimportar sob demanda).

## Estado atual verificado

- `Login.tsx`: após `authWithPassword` ok → redirect imediato para `/`. Ponto de disparo único.
- Hook `POST /backend/v1/agenda/importar`: RLS por empresa (403 se não autorizada), idempotente
  (ja_existentes), renovação on-demand do access token (LT-1-T09), caminhos explícitos
  credencial_ausente / credencial_expirada. Janela -7d/+14d.
- Empresas do usuário: gestor/admin → ambas; consultor → `empresas_autorizadas` (frontend já
  filtra assim no Farol/NovaReuniao).
- FA-7 segue aguardando teste humano — esta task entra depois da conclusão dela (uma task por vez).

## Plano (3 passos) — EXECUTADO (v0.0.81-83)

1. **Disparo:** no primeiro carregamento da sessão (Layout), fire-and-forget (sem await, sem
   bloquear o login) para cada empresa autorizada: `fetch(pb.baseUrl + '/backend/v1/agenda/importar',
   { method: 'POST', body: { empresa } })`. Falha → silenciosa (o sistema nunca depende do Google —
   mesmo princípio da LT-2-T01); NENHUM alerta bloqueante. (v1 no Login.tsx foi cancelada pelo
   redirect — AP-2026-10-07-1320; movida para o Layout.)
2. **Hook importar:** inalterado (idempotência já protege os dados; RLS já protege o escopo).
3. **Provas:** login de consultora donahelp → importação dispara só para donahelp (acuidar 403
   esperado e silencioso); login de admin → as duas; idempotência (2º login → ja_existentes, 0
   criadas); credencial expirada → login continua funcionando (falha silenciosa); sem auth → 401.

## Decisões embutidas (champion pode vetar)

- Fire-and-forget: o login NUNCA espera a importação (o Google pode estar lento/indisponível —
  o login é o caminho crítico).
- Falha silenciosa: sem alerta no login (a tela /agenda continua mostrando o estado da credencial
  quando o usuário entra nela).
- Janela inalterada (-7d/+14d) — cada login mantém a janela atualizada.
- Botão manual da /agenda CONTINUA.

## Riscos

- Quota do Google com logins repetidos: idempotência protege os DADOS (ja_existentes), não a
  quota; logins frequentes disparam chamadas Google a cada vez. Custo por login: 1-2 chamadas
  (list events por empresa) — baixo para o volume do projeto.
- Consultor sem credencial da empresa: resposta credencial_ausente silenciosa — sem impacto.

## Teste humano previsto

Logar → entrar na tela /agenda → conferir que a importação aconteceu (resumo atualizado) sem
ter clicado em nada; logar com a conta da outra empresa → agenda dela importada.
