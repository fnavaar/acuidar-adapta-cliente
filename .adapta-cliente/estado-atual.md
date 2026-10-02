# Estado atual — Adapta Cliente

- task_id: LT-1-T06 (leva técnica — RLS por empresa: isolamento de acesso entre Acuidar e Dona Help)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: decisão do Champion de 2026-10-02T15:43Z + matriz F1-T04 + emendas multiempresa (LT-1-T05)
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-02T16:10Z — champion autorizou o plano ("pode") após o relatório de análise
- teste_humano: pendente
- verificacao_automatica: passou — build/QA v0.0.28 sem erros; 12 provas de API + prova de navegador (resumo abaixo)
- aprendizado: pendente
- ultima_acao: LT-1-T06 implementada (migrations 0007/0008, validação server-side nos 3 hooks, frontend limitado, conta consultora-donahelp-teste)
- proxima_acao: Aguardar teste humano do champion no preview
- atualizado_em: 2026-10-02T16:20:00-03:00

## O que foi implementado (LT-1-T06 — Skip v0.0.28, QA ✓)

1. **Migration 0007** — campo `empresas_autorizadas` (select múltiplo: acuidar/donahelp) na collection `users`; contas de teste preenchidas (consultor=acuidar; gestor/admin=ambas); nova conta `consultora-donahelp-teste@donahelpbr.com.br` (role consultor, só donahelp, senha TesteD!2026x).
2. **Migration 0008** — RLS por empresa na collection `ocorrencias`: listRule/viewRule/updateRule filtram por `empresas_autorizadas` do auth (gestor/admin passam sempre; empresa vazia conta como acuidar). Consultora de uma empresa NÃO vê ocorrências da outra (404 por invisibilidade).
3. **Hooks server-side (defesa em profundidade)** — `ocorrencias_criar` (403 se empresa não autorizada), `painel_cobertura` (403), `unidades_proxy` (403): tentativa de burlar via API direta é negada no servidor.
4. **Frontend** — `/reunioes/nova` e `/painel` mostram SOMENTE as empresas autorizadas do usuário (seletivo oculto quando há 1).

## Provas executadas (v0.0.28)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Conta consultora-donahelp autentica | ok — role consultor, empresas=[donahelp] | ✓ |
| 2 | Consultora-donahelp vê só ocorrências donahelp | 2 donahelp; 0 acuidar (RLS) | ✓ |
| 3 | Consultor-acuidar vê só acuidar | 3 acuidar; 0 donahelp | ✓ |
| 4 | Gestor vê todas | 5 (acuidar + donahelp) | ✓ |
| 5 | Consultora-donahelp tenta criar ACUIDAR via hook | 403 — negado server-side | ✓ |
| 6 | Consultor-acuidar tenta unidades DONAHELP | 403 | ✓ |
| 7 | Consultora-donahelp tenta painel ACUIDAR | 403 | ✓ |
| 8 | Consultora-donahelp usa o que é dela | unidades donahelp 55 ok; painel donahelp ok | ✓ |
| 9 | Gestor continua vendo ambas | acuidar 174 + donahelp 55 | ✓ |
| 10 | CA-1-07 cruza empresas | consultora-donahelp cria exceção → gestor aprova (ok); ela mesma aprovar → 404 | ✓ |
| 11 | PATCH direto da consultora-donahelp em ocorrência acuidar | 404 (RLS) | ✓ |
| 12 | Navegador: consultora-donahelp no formulário | só "Dona Help Franquias" (sem Acuidar); painel sem unidades Acuidar | ✓ |

**Bug pego pela prova durante a task:** a prova P4 (burla) passou na primeira execução — o hook `criar` não validava a empresa. Corrigido na v0.0.26 (validação server-side nos 3 hooks); a fixture da burla foi excluída (migration 0009, v0.0.27). Prova refeita: 403 ✓.

**Fixtures criadas nesta task (registros reais de teste, preservados):** 5 registros donahelp + 1 exceção (todos confirmados) — dão vida ao painel/fila da Dona Help.

## Roteiro de teste humano (LT-1-T06)

1. **Recarregue com Ctrl+Shift+R.**
2. Login como **consultora-donahelp-teste@donahelpbr.com.br / TesteD!2026x**.
3. **Registrar reunião** → o seletivo de empresa só mostra "Dona Help Franquias" (sem Acuidar) e as unidades são todas Dona Help.
4. **Painel de cobertura** → só Dona Help (55 unidades); nenhuma unidade Acuidar na tabela.
5. **Fila** → só ocorrências Dona Help.
6. Saia e entre como **gestor-teste** → o seletivo mostra as DUAS empresas; painel e fila mostram ambas.
7. **Como reconhecer falha:** consultora-donahelp vendo qualquer unidade/ocorrência Acuidar; erro 403 ao usar a própria empresa; gestor sem acesso a alguma empresa.

## Pendências restantes (fora desta task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.
- Conector Google Agenda — exige decisão de escopo do champion.