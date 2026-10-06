# AP-2026-10-06-1258 — Migration JSVM: usar o parâmetro `app` do up(), não `$app`

**Task:** FAROL-1 / FA-1 (Farol das Unidades — mapa de acompanhamento)
**Data:** 2026-10-06T12:58:00-03:00

## Sinal reutilizável

No JSVM do PocketBase, `migrate(up, down)` entrega o objeto `app` como **parâmetro** das
funções up/down. Usar `$app` dentro do up funciona em hooks (routerAdd), mas em migrations o
padrão correto é o parâmetro — e uma migration que falha silenciosamente não reexecuta
(aprendizado já registrado: limpeza exige migration NOVA).

## Como aplicar

```js
migrate(
  (app) => { app.findRecordById(...) }   // ✓ parâmetro
  // $app.findRecordById(...)            // ✗ inconsistente em migration
)
```

## Por que evita erro

Na FA-1 a migration 0015 de limpeza usou `$app` no up e não removeu nada — o QA passou
(migration "aplicada") e a fixture continuou no banco. Pego na revalidação (totalItems = 3);
corrigido na 0016 com o parâmetro `app`. Regra: em migration, SEMPRE o parâmetro; e toda
migration de limpeza é revalidada com contagem real (totalItems) antes de declarar sucesso.