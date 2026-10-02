# Estado atual — Adapta Cliente

- task_id: LT-1-T01 (leva técnica — tela de login da intranet)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: leva técnica (LT) gerada a partir das SPECs 1-001/1-002/1-003 desbloqueadas + matriz F1-T04
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-02T09:30:00-03:00 — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: pendente
- verificacao_automatica: passou — build/QA v0.0.8 sem erros (setup, staticAnalysis, build, integrations, test); provas em navegador no preview: login válido (consultor-teste) → home com nome e role "consultor"; logout → volta ao login; acesso a / sem sessão → redirecionado ao login (guard de rota); senha errada → mensagem genérica "E-mail ou senha inválidos." sem revelar detalhes; screenshots em artifacts/ (lt1t01-home-consultor.png, lt1t01-erro-credencial.png)
- aprendizado: pendente
- ultima_acao: LT-1-T01 implementada (Login.tsx novo, Index.tsx home autenticada, App.tsx com guard de rota) e provas de navegador executadas no preview
- proxima_acao: Aguardar teste humano do champion no preview
- atualizado_em: 2026-10-02T09:35:00-03:00

## O que foi implementado (LT-1-T01 — Skip v0.0.8, QA ✓)

1. **`src/pages/Login.tsx` (novo)** — formulário e-mail + senha via `pb.collection('users').authWithPassword`; erro genérico "E-mail ou senha inválidos." para qualquer falha (não revela existência de conta); estado de carregamento no botão.
2. **`src/pages/Index.tsx` (home autenticada mínima)** — saudação com nome do usuário, badge do role (consultor/gestor/administrador) e botão Sair (`authStore.clear()`).
3. **`src/App.tsx`** — rota `/login` pública; rota `/` protegida pelo `RequireAuth` (não autenticado → redireciona a `/login`).
4. Sessão persistida pelo store do PocketBase (sobrevive a recarregar a página).

**Fora do escopo desta task:** fluxo de registro, ocorrências, painel, gestão de usuários, recuperação de senha (tasks seguintes da leva técnica).

## Provas de navegador executadas (preview, v0.0.8)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Login válido (consultor-teste) | redireciona a `/` — home mostra "Bem-vindo, Consultor Teste" + badge "consultor" | ✓ |
| 2 | Logout | volta a `/login` | ✓ |
| 3 | Acesso a `/` sem sessão | redirecionado a `/login` (guard de rota) | ✓ |
| 4 | Senha errada | mensagem genérica "E-mail ou senha inválidos.", permanece no login | ✓ |

## Roteiro de teste humano (LT-1-T01)

1. Abra o preview: https://adapta-cliente-c2bc2--preview.goskip.app/login
2. Logue com `consultor-teste@acuidarbr.com.br` / senha da conta de teste (com o champion).
3. **Esperado:** home com "Bem-vindo, Consultor Teste" e badge "consultor".
4. Clique em **Sair** — deve voltar ao login.
5. Tente acessar a home de novo sem logar — deve ser redirecionado ao login.
6. (Opcional) Tente logar com senha errada — deve aparecer "E-mail ou senha inválidos." sem revelar mais nada.

**Como reconhecer falha:** login válido não entra; erro genérico não aparece em senha errada; rota `/` acessível sem sessão; logout não bloqueia a home.

## Pendências de limpeza (registradas)

- Fixtures de teste `18l0hqe6k413h4v` e `wt02yp2kpzxm7ba` na base — exclusão exige superuser; remover via painel admin ou migration de limpeza.
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task de integração multiempresa.
- Arquivo `.skip.config.json` aparece com mudança pendente no working tree do Skip (não alterado por nós) — inspecionar antes do próximo apply_changes.
- Rotação recomendada das credenciais que passaram pelo chat antes dos Secrets (chave Google, token Acuidar) — após estabilização.
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).