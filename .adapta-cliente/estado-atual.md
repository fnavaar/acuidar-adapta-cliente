# Estado atual — Adapta Cliente

- task_id: CARGA-2026 (aprovação dos 7 vínculos + carga das 61 avaliações 2026) — CONCLUÍDA, aguardando teste humano
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: 06_notas/analise-fa3-semaforo-carga.md (análise FA-3) + tabela-conferencia-carga-2026.md; gate da tabela RESOLVIDO pelo champion 10:55 ("primeiro aprovar os 7 vínculos e rodar a carga 2026")
- etapa: aguardando_teste_humano (carga rodada e provada — farol acendeu)
- autorizacao_implementacao: confirmada — 2026-10-07T10:55-03:00 — champion: "primeiro aprovar os 7 vínculos e rodar a carga 2026 (farol acende de verdade)"
- teste_humano: FA-5 (visual NEXUS) e FA-6 (farol unicamente PECAF/PEDHE) APROVADOS ("aprovo" + "tudo ok" 10:51); CARGA-2026 PENDENTE (champion confere o farol com as cores reais)
- verificacao_automatica: passou — Skip v0.0.70 QA ✓ + provas: 61/61 gravadas via hook avaliacoes/salvar (48 PECAF + 13 PEDHE; contagem_qualitativa consolidada); idempotência (reenvio atualiza, não duplica — banco final 61); farol acuidar verde 32 / vermelho 140 (172); farol donahelp verde 9 / vermelho 45 (54); prova por unidade (ABC 24 verde 102; Ananindeua vermelho abaixo dos mínimos 15/R$120k; DH Aracaju vermelho abaixo do mínimo R$60k; DH BSB 153 verde); RLS 403 consultora→acuidar; consultor→403 no salvar
- aprendizado: capturado — AP-2026-10-06-1710 reafirmado (toLocaleString no ramo PEDHE do farol — 2ª ocorrência da mesma armadilha; fix v0.0.70)
- ultima_acao: carga 2026 rodada (61 avaliações) + fix toLocaleString ramo PEDHE (v0.0.70 QA ✓) — farol acendeu de verdade (32/9 verdes)
- proxima_acao: teste humano do champion — farol com as cores reais no preview (Acuidar 32 verdes; Dona Help 9 verdes); depois análise da LT-2-T01 (registro de reunião criar evento no Google — pedido do champion 10:37, análise pendente)
- atualizado_em: 2026-10-07T11:00:00-03:00

## Recorte FAROL-1 (concluído)

- **FA-1 ✅ CONCLUÍDA (v0.0.53):** tela /farol + mapa de acompanhamento (cadência mensal) + status de atividade provisório + gráfico
- **FA-2 ✅ CONCLUÍDA (v0.0.59):** collection avaliacoes + formulário PECAF/PEDHE com cálculo automático
- **FA-3+FA-4 ✅ CONCLUÍDAS (v0.0.60-66):** semáforo + tela única por unidade + cliente oculto no formulário
- **FA-5 ✅ CONCLUÍDA (v0.0.67):** pele NEXUS na intranet toda
- **FA-6 ✅ CONCLUÍDA (v0.0.68-69):** farol UNICAMENTE PECAF/PEDHE por empresa — mapa mensal sai do farol; visão mensal segue no painel de cobertura (SPEC-1-003)
- **CARGA-2026 ✅ RODADA (v0.0.70):** 61 avaliações gravadas (48 PECAF + 13 PEDHE); 7 vínculos especiais aprovados pelo champion 10:55; fórmula 0 erros; 41 ranqueadas = lista oficial; farol acendeu (32/9 verdes)

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07).
- Carga 2026: contagem_qualitativa consolidada (sem q1..q20 individuais) — decisão do champion 13:42 de 2026-10-06.

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
- LT-2-T01 (escrita intranet→Google Calendar) — análise pendente; gate humano: champion regenera os 2 refresh tokens com escopo calendar.events
