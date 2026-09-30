# AP-2026-09-30-1620 — Idempotência e recuperação pós-timeout na intranet

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: F1-T06; SPEC-1-002 §BLOQUEIO-F1-002-C
- Sinal: a consulta de recuperação após timeout deve retornar SEMPRE um dos 3 resultados explícitos (confirmada / ausente / inconclusivo) e nunca inferir. Chave incompleta e falha de consulta são casos de `inconclusivo`, não de "ausente" — tratar ausência de parâmetro como ausência de registro geraria retry cego e duplicidade.
- Evidência: 6 cenários exercitados no ambiente de teste do Skip (projeto 51740, versão 0.0.4): chave incompleta → inconclusivo com lista `faltando`; sem registro → ausente; registro confirmado → confirmada com comprovante; reenvio da mesma chave → HTTP 400 validation_not_unique; registro não confirmado → inconclusivo com estado_atual; sem auth → 401.
- Regra reutilizável: modelar recuperação pós-timeout com índice UNIQUE na chave de idempotência (o banco rejeita duplicidade na origem, independentemente do endpoint) + endpoint de consulta que distingue "não existe" de "não sei"; qualquer dúvida → inconclusivo + decisão humana.
- Quando aplicar: qualquer criação remota idempotente (ocorrências, pagamentos, registros externos) onde timeout é ambíguo.
- Quando não aplicar: operações puramente locais sem efeito externo, onde retry é seguro.
- Confiança: alta — comprovada por respostas HTTP reais em ambiente de teste.
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto; fixtures removidas após as provas.