# Estado atual — Adapta Cliente

- task_id: F1-T03
- champion: Luis Carlos - CTO (exerce também o papel de Responsável técnico do cliente)
- spec: 04_fase-atual/specs/spec-f1-001-fluxo-direto-de-registro.md §BLOQUEIO-F1-001-C
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-09-30T16:22:00-03:00 — "sim" do champion autorizou implementar o plano da F1-T03
- teste_humano: pendente
- verificacao_automatica: passou — documento de autorização criado com os 4 itens do critério, sem valores sensíveis; emenda registrada na SPEC-1-001
- aprendizado: pendente
- ultima_acao: Autorização da superfície técnica documentada (F1-T03)
- proxima_acao: Aguardar teste humano do champion
- atualizado_em: 2026-09-30T16:24:00-03:00

## O que foi implementado (F1-T03)

### Documento de autorização: `06_notas/autorizacao-superficie-tecnica.md`

| Item do critério | Valor registrado |
|---|---|
| Repositório | `github.com/fnavaar/acuidar-adapta-cliente` (operacional) + projeto Skip 51740 (aplicação) |
| Ambiente | Preview `adapta-cliente-c2bc2--preview.goskip.app` (teste) · Produção `adapta-cliente-c2bc2.goskip.app` (não publicada) · Backend `adapta-cliente-c2bc2.shrd00.internal.goskip.dev` |
| Responsável por deploy | Luis Carlos - CTO (Builder do Skip / MCP, sob autorização do Champion) |
| Mecanismo de segredos | Secrets do Skip (projeto 51740) — referências `ACUIDAR_PORTAL_TOKEN` e `ACUIDAR_PORTAL_API_KEY`, sem valores |

### Escopo da autorização
- Inclui: collections, hooks e páginas da intranet no Skip 51740; commits no repositório operacional; provas no preview.
- Não inclui: Portal em produção (só leitura), cadastro mestre, credenciais fora do Secrets, comunicação a franqueados, publicação em produção sem decisão do Champion.
- Vigência: fase 1; reavaliada na abertura da fase 2.

### Emenda registrada
- SPEC-1-001 §Emendas: F1-T03 resolve BLOQUEIO-F1-001-C (commit `716a4a6`).

## Histórico de tasks concluídas (referência)

- F1-T01 (2026-09-30): contrato de leitura da API do Portal validado → BLOQUEIO-F1-001-A resolvido
- F1-T02 (2026-08-26): chave oficial, elegibilidade, multiunidade, cancelamento, remarcação → SPEC-1-001
- F1-T05 (2026-08-28): política de exceção de data → SPEC-1-002
- F1-T06 (2026-09-30): consulta de recuperação provada na intranet → BLOQUEIO-F1-002-C resolvido
- F1-T07 (2026-09-22): semântica da cobertura operacional → SPEC-1-003