# Controle de aprendizado — Adapta Cliente

Triagem automática (silenciosa) após task/debug/recuperação. Novas linhas no fim.

- 2026-10-06T12:58:00-03:00 · task FA-1 (migration 0016) · capturado:AP-2026-10-06-1258-migration-jsvm-parametro-app.md · migration JSVM usa o parâmetro `app` (não `$app`); limpeza de fixtures revalidada por contagem real; migration aplicada não reexecuta — nova migration para cada rodada de limpeza.
- 2026-10-06T13:10:00-03:00 · task FA-2 · capturado:AP-2026-10-06-1310-number-required-rejeita-0.md · PocketBase number required rejeita 0 — campos numéricos que aceitam 0 legítimo não podem ser required; validação fica no hook.
- 2026-10-06T17:10:00-03:00 · task FAROL-1 farol consolidado · capturado:AP-2026-10-06-1710-jsvm-armadilhas.md · JSVM: declaração antes do uso (400 opaco — ver logs do Skip); number required rejeita 0 (3ª ocorrência); toLocaleString inexistente no JSVM; diagnosticar 400 opaco por logs + API nativa.
- 2026-10-07T09:50:00-03:00 · task FA-5 (pele NEXUS) · sem sinal reutilizável · retrabalho visual copia tokens/classes do projeto de referência (48835) sem tocar hooks/regras; nenhuma armadilha nova de runtime ou build além das já registradas em AP-2026-10-06-1710.
- 2026-10-07T10:54:00-03:00 · task FA-6 (farol unicamente PECAF/PEDHE) · sem sinal reutilizável · remoção de funcionalidade (mapa mensal) sem nova armadilha de runtime/build; bug do rótulo (programa undefined no escopo da tabela) é variante do padrão JSVM/escopo já registrado em AP-2026-10-06-1710 — pego pela prova no navegador antes de chegar ao champion.
- 2026-10-07T12:15:00-03:00 · task LT-2-T01 · capturado:AP-2026-10-07-1215-qa-skip-jsvm-scoping.md · QA do Skip rejeita hook com helper top-level referenciado no callback (JSVM executa callbacks em VM separada) — toda a lógica inline no callback; stage integrations pega antes do deploy.
- 2026-10-07T12:40:00-03:00 · task LT-2-T01 (prova real) · capturado:AP-2026-10-07-1240-data-fato-timestamp-pocketbase.md · campos DATE do PocketBase via getString() devolvem timestamp completo — normalizar para YYYY-MM-DD (slice 10) + regex antes de payload externo; 400 opaco do Google diagnosticado pelo formato do dado.
