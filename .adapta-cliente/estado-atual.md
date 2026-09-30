# Estado atual — Adapta Cliente

- task_id: F1-T04
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal)
- spec: 04_fase-atual/specs/spec-f1-002-estados-excecoes-e-idempotencia.md §BLOQUEIO-F1-002-A
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Análise profunda da task F1-T04 concluída (selecionada como a mais vantajosa das 2 restantes)
- proxima_acao: Aguardar autorização do champion e as decisões da matriz de perfis
- atualizado_em: 2026-09-30T13:30:00-03:00

## Comparativo das 2 tasks restantes (escolha do champion)

| Task | Esforço | Por quê |
|---|---|---|
| **F1-T04** (escolhida) | Médio-baixo — 1 matriz de perfis + 3 contas de teste; a infraestrutura de RLS já existe na collection `ocorrencias` (RLS provisória aguardando esta matriz) | Desbloqueia a SPEC-1-002 inteira; é decisão de negócio com prova técnica simples |
| F1-T08 | Médio-alto — mapa de fontes/campos/latência + destino/RLS do painel | Depende de definir latência de cada fonte e RLS do painel; mais decisões abertas |

**Nota:** F1-T04 é a mais vantajosa porque fecha o último bloqueio da SPEC-1-002 (a SPEC inteira fica desbloqueada) e a infraestrutura técnica já está pronta (RLS provisória implementada na F1-T06).

## Análise F1-T04 — formalizar matriz de perfis e RLS do fluxo

**Critério binário:** "A matriz aprovada separa `consultar`, `editar rascunho`, `solicitar exceção`, `aprovar exceção` e `confirmar criação`, com perfis/grupos e contas de teste."

**Evidência esperada:** Matriz datada e resultado de teste negativo para perfil sem acesso.

**O que o champion precisa decidir (matriz nominal):**

| Permissão | Quem tem (perfil/grupo) |
|---|---|
| `consultar` | ? |
| `editar rascunho` | ? |
| `solicitar exceção` | ? |
| `aprovar exceção` | ? (deve ser perfil superior, ≠ solicitante) |
| `confirmar criação` | ? |

**Além da matriz, precisa:**
- Contas de teste para cada perfil (3 contas: ex. consultor, gestor, administrador)
- Prova negativa: perfil sem permissão recebe acesso negado

**Pontos de parada:**
- Um perfil puder autoaprovar → parar
- Privilégio sem dono → parar
- Sem conta de teste → parar