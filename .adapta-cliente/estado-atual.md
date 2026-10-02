# Estado atual — Adapta Cliente

- task_id: LT-1-T01 (leva técnica — tela de login da intranet)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: leva técnica (LT) gerada a partir das SPECs 1-001/1-002/1-003 desbloqueadas + matriz F1-T04
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-10-02T09:30:00-03:00 — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: aprovado — 2026-10-02T09:51:00-03:00 — champion testou no preview e confirmou ("tudo ok")
- verificacao_automatica: passou — revalidação do zero: build/QA v0.0.8 sem erros; provas de navegador repetidas (login consultor-teste → home com role; logout; guard de rota; erro genérico em senha errada) + prova adicional com gestor-teste (login válido → home com badge "gestor") confirmando que o role vem do registro do usuário; conferência de segredos no código entregue (nenhuma senha/token hardcoded)
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-10-02-0955-skip-preview-redirect.md
- ultima_acao: LT-1-T01 concluída — primeira task da leva técnica fechada com teste humano aprovado
- proxima_acao: Aguardar pedido do champion para a próxima task da leva técnica (fluxo de registro — SPEC-1-001)
- atualizado_em: 2026-10-02T09:56:00-03:00

## O que foi implementado (LT-1-T01 — Skip v0.0.8, QA ✓)

1. **`src/pages/Login.tsx` (novo)** — formulário e-mail + senha via `pb.collection('users').authWithPassword`; erro genérico "E-mail ou senha inválidos." para qualquer falha (não revela existência de conta); estado de carregamento no botão.
2. **`src/pages/Index.tsx` (home autenticada mínima)** — saudação com nome do usuário, badge do role (consultor/gestor/administrador) e botão Sair (`authStore.clear()`).
3. **`src/App.tsx`** — rota `/login` pública; rota `/` protegida pelo `RequireAuth` (não autenticado → redireciona a `/login`).
4. Sessão persistida pelo store do PocketBase (sobrevive a recarregar a página).

## Provas executadas (preview, v0.0.8)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Login válido (consultor-teste) | home "Bem-vindo, Consultor Teste" + badge "consultor" | ✓ |
| 2 | Logout | volta a `/login` | ✓ |
| 3 | Acesso a `/` sem sessão | redirecionado a `/login` (guard de rota) | ✓ |
| 4 | Senha errada | mensagem genérica "E-mail ou senha inválidos." | ✓ |
| 5 | Login válido (gestor-teste) — revalidação | home "Bem-vindo, Gestor Teste" + badge "gestor" | ✓ |
| 6 | Logout (gestor) — revalidação | volta a `/login` | ✓ |

**Segurança conferida:** nenhuma senha/token no código entregue; erro de login genérico (não revela existência de conta); senha nunca logada.

## Pendências de limpeza (registradas)

- Fixtures de teste `18l0hqe6k413h4v` e `wt02yp2kpzxm7ba` na base — exclusão exige superuser; remover via painel admin ou migration de limpeza.
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task de integração multiempresa.
- Arquivo `.skip.config.json` aparece com mudança pendente no working tree do Skip (não alterado por nós) — inspecionar antes do próximo apply_changes.
- Rotação recomendada das credenciais que passaram pelo chat antes dos Secrets (chave Google, token Acuidar) — após estabilização.
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).