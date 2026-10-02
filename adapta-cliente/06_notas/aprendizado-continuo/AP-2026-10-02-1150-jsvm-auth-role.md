# AP-2026-10-02-1150 — JSVM do PocketBase: `auth.role` é `undefined`; usar `auth.getString('role')`

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: LT-1-T03 (hook de transição)
- Sinal: no JSVM do PocketBase v0.36, o record de autenticação (`e.auth`) não expõe campos como propriedade — `auth.role` retorna `undefined`, e `String(undefined || 'consultor')` fez todo perfil ser tratado como consultor (gestor/admin bloqueados com 404 na aprovação de exceção). O acesso correto é `auth.getString('role')`. Mesmo padrão vale para qualquer campo do record em hooks.
- Evidência: v0.0.17 — prova de navegador mostrou `papel: "consultor"` para o admin; v0.0.18 (fix) — gestor aprovou com `papel: "gestor"` e admin com `papel: "administrador"`.
- Regra reutilizável: em hooks JSVM, SEMPRE ler campos de record via `getString('campo')`/`getBool`/`getFloat` — nunca por propriedade direta. Sintoma típico: `undefined` silencioso virando default errado.
- Quando aplicar: todo hook que lê campos de `e.auth` ou de registros (`rec.campo` → `rec.getString('campo')`).
- Quando não aplicar: `e.auth.id` (campo de sistema exposto como propriedade) e `e.json`/`e.requestInfo()` (métodos, não campos).
- Confiança: alta — comportamento observado e corrigido com prova antes/depois (v0.0.17 vs v0.0.18).
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.