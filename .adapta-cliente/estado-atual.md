# Estado atual — Adapta Cliente

- task_id: LT-1-T06 (leva técnica — RLS por empresa: isolamento de acesso entre Acuidar e Dona Help)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: decisão do Champion de 2026-10-02T15:43Z (RLS por empresa) + matriz F1-T04 (`06_notas/matriz-perfis-rls.md`) + emendas multiempresa nas SPECs 1-001/1-002/1-003 (LT-1-T05)
- etapa: aguardando_autorizacao
- autorizacao_implementacao: pendente — análise apresentada ao champion em 2026-10-02T16:05Z; aguardando "sim" em mensagem posterior
- teste_humano: pendente (após implementação)
- verificacao_automatica: pendente (após implementação)
- aprendizado: pendente
- ultima_acao: análise profunda da LT-1-T06 concluída (baseline: campo role existe (0002); RLS atual de ocorrencias não filtra por empresa (0003); 3 contas de teste; listagem de users limitada pela listRule — normal)
- proxima_acao: aguardar autorização para implementar
- atualizado_em: 2026-10-02T16:05:00-03:00

## Regra aprovada pelo Champion (2026-10-02T15:43Z)

- Consultora **Dona Help** → vê/registra SOMENTE unidades e ocorrências Dona Help
- Consultora **Acuidar** → vê/registra SOMENTE unidades e ocorrências Acuidar
- **Gestores/Administradores** → veem ambas as empresas

## Plano acordado na análise (resumo para o implementador)

1. **Migration 0007 — campo `empresas_autorizadas`** na collection `users` (select múltiplo: acuidar/donahelp). Preenchimento: gestor-teste e admin-teste = ambas; consultor-teste = acuidar. Nova conta de teste `consultora-donahelp-teste` (role consultor, empresas_autorizadas = donahelp).
2. **Migration 0008 — RLS por empresa na collection `ocorrencias`**: listRule/viewRule/updateRule com filtro por `empresas_autorizadas` do auth (gestor/admin = ambas). Consultor de uma empresa NÃO vê ocorrências da outra (404 por invisibilidade — padrão PocketBase, já documentado em AP-1645).
3. **Hooks server-side (defesa em profundidade)**: `ocorrencias_criar` e `painel_cobertura` e `unidades_proxy` passam a validar a empresa contra `empresas_autorizadas` do auth (consultora Dona Help pedindo unidade Acuidar → negado explicitamente).
4. **Frontend**: `/reunioes/nova` e `/painel` mostram SOMENTE as empresas autorizadas do usuário (seletivo oculto quando há 1; filtro limitado); `/fila` lista apenas ocorrências das empresas autorizadas (a RLS já filtra; UI ajusta os filtros).
5. **Provas**: consultora-donahelp não vê unidades/ocorrências Acuidar (nem via hook, nem via API direta); consultor-acuidar não vê Dona Help; gestor vê ambas; autoaprovação e CA-1-07 continuam valendo; regressão completa do fluxo.

## Critérios de aceite (propostos, binários)

- CA-A: consultora Dona Help não lista unidades Acuidar (proxy nega) e não vê ocorrências Acuidar (RLS 404 + hook nega)
- CA-B: consultora Acuidar não vê nada de Dona Help (mesmas provas, invertido)
- CA-C: gestor/admin veem ambas as empresas (fluxo completo nas duas)
- CA-D: tentativa de burlar via API direta (PATCH/POST com empresa estrangeira) é negada server-side
- CA-E: regressão completa do fluxo (login, registro, fila, painel) passa para os 3 perfis originais

## Pendências fora do escopo

- Rotação das credenciais que passaram pelo chat — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.
- Conector Google Agenda — exige decisão de escopo do champion.

## Fontes

- Decisão do champion registrada no changelog/estado (2026-10-02T15:43Z)
- `06_notas/matriz-perfis-rls.md` (matriz de perfis vigente)
- AP-2026-09-30-1645 (RLS do PocketBase nega por invisibilidade — 404)