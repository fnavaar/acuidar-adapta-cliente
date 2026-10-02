# AP-2026-10-02-1530 — PocketBase JSVM: `res.body` é bytes; `res.json` já vem parseado

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: LT-1-T04 (hook do painel de cobertura)
- Sinal: em hooks JSVM do PocketBase, `$http.send()` retorna `res.body` como bytes — `JSON.parse(res.body)` falha silenciosamente (ou lança, dependendo do contexto), mesmo quando a resposta HTTP é 200 válida. O campo `res.json` já traz o objeto parseado (quando o content-type é JSON). Padrão seguro: usar `res.json` se for objeto; fallback `new TextDecoder().decode(res.body)` + `JSON.parse`.
- Evidência: v0.0.21 — hook do painel retornou `dados_indisponiveis` com a fonte real respondendo 200 (o catch engoliu o erro de parse); v0.0.22 (fix) — mesmo endpoint retornou `ok` com 174 unidades. O caminho de erro RN-1-14 funcionou como projetado (nenhuma contagem enganosa), o que mascarou a causa até a prova P1 comparar com o proxy da LT-1-T02 (que já usava res.json).
- Regra reutilizável: em hooks JSVM, SEMPRE parsear resposta HTTP com `res.json` primeiro; `res.body` é bytes — nunca passar direto a `JSON.parse`. Sintoma típico: "fonte indisponível" com a fonte na verdade saudável.
- Quando aplicar: todo hook que chama `$http.send` e consome JSON.
- Quando não aplicar: respostas não-JSON (texto/binário) — aí `res.body` + TextDecoder é o caminho.
- Confiança: alta — comportamento observado e corrigido com prova antes/depois (v0.0.21 vs v0.0.22); consistente com AP-2026-09-30-1715 (mesma causa, registrado na F1-T08).
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.
