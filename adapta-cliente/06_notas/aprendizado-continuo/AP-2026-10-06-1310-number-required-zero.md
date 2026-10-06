# AP-2026-10-06-1310 — PocketBase: campo number `required` rejeita 0

**Task:** FAROL-1 / FA-2 (formulário PECAF/PEDHE com cálculo automático)
**Data:** 2026-10-06T13:10:00-03:00

## Sinal reutilizável

No PocketBase, campo `number` com `required: true` rejeita o valor **0** ("Cannot be blank") —
0 é tratado como vazio. Quando 0 é um valor de negócio válido (ex.: pontuação zero, q19/q20 do
PEDHE que não existem), o campo NÃO pode ser required; a validação de obrigatoriedade fica no
hook/código da aplicação.

## Como aplicar

```js
// Migration: campo number que aceita 0
{ name: 'pontuacao_contratos', type: 'number', required: false, min: 0, max: 20 }
// Hook: valida presença explicitamente
if (isNaN(v) || v < 0 || v > 20) → erro explícito
```

## Por que evita erro

Na FA-2, q19/q20 e pontuacao_contratos gravavam 0 e a collection rejeitava ("Falha ao gravar"
sem detalhe no hook). O erro real só apareceu gravando pela API nativa (400 com o campo
específico). Regra: diagnosticar gravação falha pela API nativa (mostra o campo exato), e
nunca usar required em number onde 0 é válido.