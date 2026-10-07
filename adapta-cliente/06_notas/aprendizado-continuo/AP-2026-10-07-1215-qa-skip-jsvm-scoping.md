# AP-2026-10-07-1215 — QA do Skip valida scoping do JSVM antes do deploy

**Origem:** LT-2-T01 (v0.0.71) · **Data:** 2026-10-07

- **Sinal:** `skip_project_apply_changes` rejeitou o hook `agenda_sincronizar.js` com erro explícito: "top-level declaration 'marcarErro' is referenced inside a callback — PocketBase's JSVM executes callbacks in a separate VM pool, so top-level functions and variables are not accessible at runtime and will cause ReferenceError".
- **Padrão confirmado (2ª ocorrência):** AP-2026-10-06-1710 registrou "ReferenceError cannot access before initialization" por ordem de declaração; agora o QA do Skip formalizou a regra — **funções/constantes top-level NÃO são acessíveis dentro do callback do routerAdd**.
- **Correção:** toda a lógica inline no callback (`marcarErro` definida dentro dele, fechando sobre `oc`).
- **Regra reutilizável:** antes de gravar um hook novo no Skip, mover qualquer helper top-level para dentro do callback; o QA do Skip (stage integrations) pega isso antes do deploy — nunca ignorar o erro do stage integrations.
- **Confiança:** alta — erro explícito do QA + correção provada (v0.0.72 QA ✓).
