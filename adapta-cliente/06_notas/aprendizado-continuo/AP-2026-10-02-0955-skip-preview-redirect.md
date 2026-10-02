# AP-2026-10-02-0955 — Redirecionamento por window.location no Skip preview

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: LT-1-T01 (tela de login)
- Sinal: no app React do Skip (SPA com react-router), navegação programática após login/logout funciona de forma confiável com `window.location.href = '/...'` — recarrega a página e reavalia o guard de rota com o authStore atualizado. `useNavigate` do react-router também funcionaria, mas o recarregamento completo evita estado de componente obsoleto (nome/role lidos no mount).
- Evidência: provas de navegador no preview (v0.0.8): login → `/` com dados corretos; logout → `/login`; acesso a `/` sem sessão → redirecionado. Duas contas testadas (consultor, gestor).
- Regra reutilizável: em telas de auth do Skip, preferir `window.location.href` pós-login/logout para garantir estado limpo; usar `useNavigate` para navegações internas que não mudam o estado de autenticação.
- Quando aplicar: login, logout, troca de sessão expirada.
- Quando não aplicar: navegação entre telas autenticadas (fila, painel) — usar router.
- Confiança: média — funciona comprovadamente, mas a alternativa com useNavigate não foi testada lado a lado.
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.