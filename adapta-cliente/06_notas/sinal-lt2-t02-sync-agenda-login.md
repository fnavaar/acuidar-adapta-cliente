# Sinal — Sincronização das agendas ao logar (2026-10-07 12:53)

**Pedido do champion (12:53):** "quero que a sincronização das agendas aconteça toda vez que a
pessoa logar no sistema"

## Interpretação (validada no relatório de análise)

- Hoje a importação do Google Agenda é MANUAL (botão na tela /agenda, por empresa, janela
  -7d/+14d). O champion quer que ela aconteça AUTOMATICAMENTE no login de qualquer usuário.
- "as agendas" (plural) = as agendas das empresas que o usuário pode ver (RLS): consultora da
  Dona Help → agenda da Dona Help; gestor/admin → as duas.

## Pontos de decisão (resolvidos na implementação)

1. Onde disparar: no primeiro carregamento da sessão (Layout, após o redirect do login) — o
   fetch fire-and-forget no handler de login era CANCELADO pela navegação (AP-2026-10-07-1320).
2. Credencial ausente/expirada: a sincronização automática NUNCA bloqueia o login; falha →
   silenciosa (sem alerta) — a tela /agenda continua mostrando o estado da credencial.
3. Idempotência: o hook de importação já é idempotente (ja_existentes) — rodar no login é seguro.
4. RLS: importar só as empresas autorizadas do usuário (mesma regra da tela /agenda) — provado:
   consultora donahelp → 1 chamada; admin → 2.
5. Rate: cada login dispara 1-2 chamadas Google (por empresa) — custo baixo; idempotência
   protege os dados, não a quota.
6. A importação manual da tela /agenda CONTINUA (botão explícito permanece).
7. FA-7 concluída antes (aprovada 12:55) — uma task por vez respeitado.
