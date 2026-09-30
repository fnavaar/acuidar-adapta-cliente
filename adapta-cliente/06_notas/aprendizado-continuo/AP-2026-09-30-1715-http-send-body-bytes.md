# AP-2026-09-30-1715 — `$http.send` no PocketBase v0.36: `res.body` é bytes, não string

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: F1-T08 (contexto multiempresa Dona Help) · ferramenta de validação de contrato
- Sinal: em hooks PocketBase v0.36, `res.body` de `$http.send` retorna bytes; `JSON.parse(res.body)` falha silenciosamente (parse → null) mesmo com HTTP 200. Usar `res.json` (parsed) ou decodificar `res.body` com `TextDecoder` antes do `JSON.parse`. Além disso, `res.json`/`res.body` só existem quando o corpo existe — testar `res.json && typeof res.json === 'object'` antes de usar.
- Evidência: hook `validar-contrato-donahelp` — v0.0.6 retornava `erro_http` com `http_status: 200` e corpo não-parseável; v0.0.7 (fix com `res.json`/`TextDecoder`) retornou `ok` com 45 unidades. QA ✓ nas duas versões.
- Regra reutilizável: em qualquer hook que chame API externa, usar `res.json` como primeira fonte de parse e `TextDecoder` como fallback; nunca `JSON.parse(res.body)` direto.
- Quando aplicar: hooks de leitura de fontes externas (unidades Acuidar/Dona Help, Google Agenda futuro), validações de contrato e integrações da leva técnica.
- Quando não aplicar: chamadas que já recebem JSON garantido via res.json; transportes que falham antes do parse (try/catch de transporte é separado).
- Confiança: alta — comportamento observado e corrigido com prova antes/depois (v0.0.6 vs v0.0.7).
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.