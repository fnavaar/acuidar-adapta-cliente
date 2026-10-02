# Estado atual — Adapta Cliente

- task_id: LT-1-T01 (leva técnica — tela de login da intranet)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-001/1-002/1-003 (leva técnica gerada a partir das 3 SPECs desbloqueadas) + matriz F1-T04
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: Leva técnica aberta por decisão do champion (2026-10-02); primeira task técnica selecionada e analisada: tela de login da intranet
- proxima_acao: Aguardar autorização do champion para implementar a tela de login
- atualizado_em: 2026-10-02T09:30:00-03:00

## Nota sobre o portão de validação do consultor

O método prevê validação do consultor do fechamento da fase 1 antes da leva técnica. O champion autorizou explicitamente começar a leva técnica agora ("Começar a leva técnica agora"); a pendência de validação do consultor está registrada no changelog (2026-10-02) e permanece aberta para a próxima sincronização.

## Task LT-1-T01 — Tela de login da intranet (análise)

**Por que esta task primeiro:** todas as telas do fluxo dependem de autenticação; as 3 contas de teste da F1-T04 existem e funcionam via API, mas não há como logar pela interface. É o menor recorte que destrava a demonstração de tudo o mais.

**Critério binário proposto:** "Um usuário com conta na intranet consegue autenticar-se pela tela de login e acessar a área autenticada; credenciais inválidas recebem erro claro sem revelar detalhes; usuário não autenticado que acessa rota protegida é redirecionado ao login."

**Escopo mínimo:**
1. Página `/login` com e-mail + senha (PocketBase auth-with-password).
2. Rota protegida `/` (home autenticada mínima: saudação + role do usuário + botão sair).
3. Guard de rota: não autenticado → redireciona a `/login`.
4. Sessão persistida (PocketBase store) e logout funcional.
5. Erro de credencial inválida: mensagem genérica ("E-mail ou senha inválidos"), sem revelar se a conta existe.

**Fora do escopo desta task:** fluxo de registro de reuniões, ocorrências, painel, gestão de usuários (criação continua via admin/API), recuperação de senha (leva futura).

**Arquivos afetados:** `src/pages/Login.tsx` (novo), `src/pages/Index.tsx` (home autenticada mínima), `src/App.tsx` (rotas), `src/lib/pocketbase/client.ts` (reuso), `src/components/Layout.tsx` (header com usuário/sair).

**Matriz critério → prova:**
| Critério | Prova |
|---|---|
| Login válido autentica | login com consultor-teste → home com nome/role |
| Login inválido nega | senha errada → mensagem genérica, sem acesso |
| Guard de rota | acessar `/` sem sessão → redireciona a `/login` |
| Logout | sair → volta ao login; rota protegida bloqueada de novo |
| Sessão persiste | recarregar a página mantém a sessão |

**Riscos/caminhos de erro:** timeout do backend (mensagem de indisponibilidade), 404 de usuário (mesma mensagem de credencial inválida — não revelar existência), sessão expirada (redireciona ao login). Segurança: nada de senha em log; auto-cancellation do PB configurado.

**Teste humano esperado:** logar com `consultor-teste@acuidarbr.com.br` no preview, ver a home com o role, sair, tentar acessar sem login e ser redirecionado.

## Pendências de limpeza (registradas)

- Fixtures de teste `18l0hqe6k413h4v` e `wt02yp2kpzxm7ba` na base — exclusão exige superuser; remover via painel admin ou migration de limpeza.
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task de integração multiempresa.
- Arquivo `.skip.config.json` aparece com mudança pendente no working tree do Skip (não alterado por nós) — inspecionar antes do próximo apply_changes.
- Rotação recomendada das credenciais que passaram pelo chat antes dos Secrets (chave Google, token Acuidar) — após estabilização.
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).