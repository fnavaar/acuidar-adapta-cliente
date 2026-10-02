# Estado atual — Adapta Cliente

- task_id: nenhuma (LT-1-T03 concluída — leva técnica 3/4)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-002 (estados, exceções auditáveis e idempotência) + política F1-T05 (RN-1-11 a RN-1-14) + matriz F1-T04
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-10-02T11:48:00-03:00 — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: aprovado — 2026-10-02T15:21Z — champion testou no preview ("agora funcionou"): aprovação de exceção pela UI com trilha gravada (t7nfareuzlwkwr7 → confirmado, aprovador pf83y2t784z0x4v, papel gestor)
- verificacao_automatica: passou — revalidação do zero na v0.0.20 (14 provas RV): consultor bloqueado na aprovação (404), motivo obrigatório, transições inválidas negadas, DELETE 403, sem auth 401, autoaprovação CA-1-07 bloqueada, gestor aprova/rejeita com trilha, consultor corrige rascunho, RLS de PATCH direto (404), idempotência CA-1-08 (reenvio não duplica), linha vermelha RN-1-05A (script bloqueado → aguardando_correcao), multiempresa Dona Help ok, build/QA v0.0.20 sem erros
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-10-02-1150-jsvm-auth-role.md
- ultima_acao: LT-1-T03 concluída após 2 rodadas de debug (roteamento preview→backend; trilha de fixture poluída foi limpa)
- proxima_acao: Aguardar pedido do champion para selecionar a próxima task (LT-1-T04 — painel de cobertura, SPEC-1-003, é a seguinte na fila)
- atualizado_em: 2026-10-02T15:30:00-03:00

## Histórico da LT-1-T03 (concluída)

**Implementação (v0.0.14–v0.0.20):** migration 0004 (criado_por/aprovador_por/empresa), hook de transição com máquina de estados + perfis + aprovador≠solicitante + trilha auditável, tela /fila com filtros por estado e empresa, navegação, gravação de criado_por/empresa na criação.

**Bugs corrigidos durante a task (todos pegos por prova antes do ar):**
1. v0.0.13→14: sintaxe + JSVM não acessa constantes top-level → lógica inline.
2. v0.0.15→16: findRecordById aplica regras no JSVM e falha (404) para aguardando_aprovacao → findRecordsByFilter.
3. v0.0.17→18: auth.role é undefined no JSVM → auth.getString('role') (AP-2026-10-02-1150).
4. v0.0.18→20 (falha do 1º teste humano): pb.send prefixa /api (404) e nginx do preview não repassa POST /backend/* (405) → fetch com URL absoluta (pb.baseUrl). Debug: 06_notas/debug/debug-2026-10-02-lt1t03-botao-aprovar.md
5. 2ª falha relatada: cache do navegador do champion (bundle antigo) + trilha poluída pelos meus testes automatizados em fixtures (limpada; registros limpos recriados). Debug: 06_notas/debug/debug-2026-10-02-lt1t03-rodada2.md

**Nota PocketBase (AP-1725):** listagem de aguardando_aprovacao aparece para o consultor (listRule é filtro), mas ele não consegue agir (hook 404; PATCH RLS 404). Botões de decisão só para gestor/admin.

**Fixtures de teste acumuladas (limpeza futura, exige superuser):** ~20 registros de teste (títulos "Teste*", "RV*", "CA-1-07*") + hook temporário validar-contrato-donahelp (v0.0.7) + rotação de credenciais que passaram pelo chat (chave Google, token Acuidar).

## Roteiro de teste humano

Concluído — champion aprovou em 2026-10-02 ("agora funcionou").

## Pendências de limpeza (registradas)

- Fixtures de teste acumuladas (~20 registros) — migration de limpeza na leva (exclusão exige superuser).
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task multiempresa.
- Arquivo `.skip.config.json` com mudança pendente no working tree (não alterado por nós) — inspecionar antes do próximo apply_changes.
- Rotação recomendada das credenciais que passaram pelo chat (chave Google, token Acuidar).
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).

## Leva técnica — progresso

| Task | Escopo | Status |
|---|---|---|
| LT-1-T01 | Tela de login da intranet | ✅ concluída (2026-10-02) |
| LT-1-T02 | Registro de reunião → ocorrência | ✅ concluída (2026-10-02) |
| LT-1-T03 | Fila de revisão e aprovação de exceções | ✅ concluída (2026-10-02) |
| LT-1-T04 | Painel de cobertura (SPEC-1-003) | na fila — próxima |