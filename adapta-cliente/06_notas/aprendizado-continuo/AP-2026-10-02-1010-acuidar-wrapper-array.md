# AP-2026-10-02-1010 — Wrapper da Acuidar não aparece via `res.json` no PocketBase

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: LT-1-T02 (proxy de unidades)
- Sinal: na F1-T01 (curl direto), a API da Acuidar retornou wrapper `{status, message, dados:[...]}`; via `$http.send` + `res.json` no PocketBase v0.36, o parse chega como ARRAY DIRETO (chaves numéricas no diagnóstico sanitizado). A Dona Help já era array direto. Causa provável: camada do gateway do Skip ou do próprio `$http.send` normaliza o corpo; o wrapper só é visível na leitura crua via curl externo.
- Evidência: diagnóstico sanitizado do hook (v0.0.11): `tipo_raiz: object, chaves_raiz: ["0","1",...], tipo_dados: undefined` — ou seja, o objeto é o array de unidades. Fix v0.0.12: parser aceita array direto OU wrapper com `dados`/`data`/`unidades` para as duas empresas.
- Regra reutilizável: em hooks PocketBase que consomem API externa, não confiar no formato visto via curl — sempre diagnosticar com chaves/tipos sanitizados e escrever parser tolerante (array direto ou wrapper). O diagnóstico sanitizado (só chaves/tipos, nunca valores) é o padrão para depurar estrutura sem expor dados.
- Quando aplicar: qualquer integração de fonte externa na intranet (unidades, Google Agenda futuro).
- Quando não aplicar: chamadas entre hooks/endpoints internos, onde o formato é controlado por nós.
- Confiança: alta — observado com diagnóstico antes/depois (v0.0.11 falha → v0.0.12 ok, 174 unidades).
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.