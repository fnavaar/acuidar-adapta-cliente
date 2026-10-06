# AP-2026-10-06-1112 — Marcação de critérios de aceite com integridade provada

**Task:** LT-1-T10 (fechamento formal da fase 1 — documentação)
**Data:** 2026-10-06T11:12:00-03:00

## Sinal reutilizável

Marcar critérios de aceite (`[ ]` → `[x]`) em SPECs é edição de baixo risco **se** a integridade
for provada por diff: contar as linhas removidas que **não** são checkboxes pendentes — se o
total for 0, nenhuma regra de negócio foi alterada (a edição foi estritamente append-only).

## Como aplicar

```bash
git diff <commit_anterior>..HEAD -- <specs>/ | grep "^-" | grep -v "^---" | grep -cv "\[ \]"
# saída 0 = nenhuma regra alterada; saída >0 = inspecionar linha a linha antes de concluir
```

## Por que evita erro

A conferência automática pega o erro clássico de "melhorar o texto da regra ao marcar o CA" —
que reescreve a história da SPEC (violando o princípio append-only D19) e pode desalinear a
regra documentada da regra implementada e aprovada. Na LT-1-T10, 26 linhas removidas = 26
adicionadas com 0 regras alteradas, provado antes do teste humano.