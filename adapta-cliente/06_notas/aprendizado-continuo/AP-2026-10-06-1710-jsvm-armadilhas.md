# AP-2026-10-06-1710 — Armadilhas do JSVM (PocketBase) no farol consolidado

**Origem:** FA-3/FA-4 (farol consolidado, v0.0.60-66) · **Data:** 2026-10-06

- **`toLocaleString` NÃO existe no JSVM:** crash silencioso no hook do farol — só apareceu quando havia avaliações gravadas (o caminho sem dados passava). Correção: motivo do semáforo com número puro, sem formatação.
- **ReferenceError "cannot access before initialization":** variável declarada APÓS o uso dentro de um patch do hook (ordem de declaração importa no JSVM).
- **Migrations aplicadas não reexecutam:** confirmado 2×. Limpeza de fixture pós-prova exige migration NOVA (0010 rodou com 2 IDs; 2ª prova criou 3 novas fixtures → 0011). Editar migration antiga não tem efeito.
- **Number `required` rejeita 0** (AP-2026-10-06-1310, FA-2): obrigatoriedade de campo numérico que aceita 0 fica no hook, não na collection.
