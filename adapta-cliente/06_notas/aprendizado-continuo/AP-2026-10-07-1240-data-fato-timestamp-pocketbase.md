# AP-2026-10-07-1240 — data_fato é timestamp PocketBase, não YYYY-MM-DD

**Origem:** LT-2-T01 (prova real da escrita, v0.0.74) · **Data:** 2026-10-07

- **Sinal:** prova real da escrita falhou com `400 Bad Request` do Google mesmo com o escopo calendar.events correto. Causa: `data_fato` na collection `ocorrencias` é campo DATE do PocketBase e `getString('data_fato')` devolve o timestamp completo `"2026-10-07 00:00:00.000Z"` — concatenar direto (`dataFato + 'T' + horario + ...`) gerava `"2026-10-07 00:00:00.000ZT16:00:00-03:00"` (datetime inválido → 400).
- **Correção:** `slice(0, 10)` + validação regex `^\d{4}-\d{2}-\d{2}$` antes de montar o payload (v0.0.74).
- **Regra reutilizável:** campos DATE do PocketBase via `getString()` devolvem timestamp completo — SEMPRE normalizar para YYYY-MM-DD (slice 10) antes de usar em payload externo; validar com regex antes de chamar a API.
- **Confiança:** alta — 400 reproduzido, correção provada (evento criado nas 2 empresas).
