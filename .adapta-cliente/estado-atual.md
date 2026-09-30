# Estado atual — Adapta Cliente

- task_id: F1-T03
- champion: Luis Carlos - CTO (exerce também o papel de Responsável técnico do cliente)
- spec: 04_fase-atual/specs/spec-f1-001-fluxo-direto-de-registro.md §BLOQUEIO-F1-001-C
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Análise profunda da task F1-T03 concluída (selecionada por ser a mais simples das 3 restantes)
- proxima_acao: Aguardar autorização do champion para registrar a autorização da superfície técnica
- atualizado_em: 2026-09-30T13:25:00-03:00

## Histórico de tasks concluídas (referência)

- F1-T01 (2026-09-30): contrato de leitura da API do Portal validado → BLOQUEIO-F1-001-A resolvido
- F1-T02 (2026-08-26): chave oficial, elegibilidade, multiunidade, cancelamento, remarcação → SPEC-1-001
- F1-T05 (2026-08-28): política de exceção de data → SPEC-1-002
- F1-T06 (2026-09-30): consulta de recuperação provada na intranet → BLOQUEIO-F1-002-C resolvido
- F1-T07 (2026-09-22): semântica da cobertura operacional → SPEC-1-003

## Comparativo das 3 tasks restantes (escolha do champion)

| Task | Esforço | Por quê |
|---|---|---|
| **F1-T03** (escolhida) | **Baixo** — só um documento de autorização; a intranet já existe e está provada (F1-T06) | É registrar por escrito o que já é fato |
| F1-T04 | Médio — matriz de perfis/grupos + contas de teste + prova negativa | Depende de política interna de acesso |
| F1-T08 | Médio — mapa de fontes/campos/latência + autorização de destino/RLS | Depende de definir latência e RLS do painel |

## Análise F1-T03 — autorizar a superfície técnica da integração

**Critério binário:** "Repositório, ambiente, responsável por deploy e mecanismo de segredos são identificados e autorizados por escrito."

**Evidência esperada:** Registro de autorização com URL/caminho do repositório, ambiente e referência ao gerenciador de segredos, sem valores sensíveis.

**Com a emenda de arquitetura, os elementos já existem — falta formalizá-los:**

| Item do critério | Valor real (a confirmar pelo champion) |
|---|---|
| Repositório | `https://github.com/fnavaar/acuidar-adapta-cliente` (operacional) + projeto Skip 51740 (aplicação) |
| Ambiente | Preview: `adapta-cliente-c2bc2--preview.goskip.app` · Produção: `adapta-cliente-c2bc2.goskip.app` (não publicada) |
| Responsável por deploy | Luis Carlos - CTO (via Builder do Skip / MCP) |
| Mecanismo de segredos | Secrets do Skip (projeto 51740) — já com `ACUIDAR_PORTAL_TOKEN` e `ACUIDAR_PORTAL_API_KEY` |

**Ponto de parada:** se o destino não tiver dono, se não existir ambiente de teste ou se o mecanismo de segredos não for aprovado.