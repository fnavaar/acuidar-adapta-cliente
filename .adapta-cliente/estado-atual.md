# Estado atual — Adapta Cliente

- task_id: FAROL-1 / FA-5 (retrabalho visual da intranet no estilo NEXUS CONSULTORIA)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: referência visual em `06_notas/referencia-visual-nexus.md` (NEXUS = Skip project 48835); sinal do farol em `06_notas/sinal-farol-unidades-pecaf-pedhe.md`
- etapa: aguardando_teste_humano (FA-5 — pele NEXUS implementada e provada)
- autorizacao_implementacao: confirmada — 2026-10-07T09:30-03:00 — formulário FA-5: escopo "Intranet toda (todas as abas)" + "Pode implementar"; farol consolidado FA-3+FA-4 implementado ontem (v0.0.60-66) mas teste humano SUSPENSO pelo champion ("eu ainda não gostei da parte do frontend")
- teste_humano: FA-1 aprovado ("ok pode prosseguir" 12:52); FA-2 aprovado ("ok" 13:07); farol consolidado PENDENTE — suspenso até a pele NEXUS (FA-5); FA-5 PENDENTE (champion confere o visual)
- verificacao_automatica: passou — Skip v0.0.67 QA ✓ (setup/static/build/test sem erro); 5 telas provadas no navegador (login, home, farol, fila, registrar reunião) com screenshots em artifacts/fa5-*.png
- aprendizado: capturado — AP-2026-10-06-1258 (FA-1); AP-2026-10-06-1310 (FA-2); AP-2026-10-06-1710 (FA-3/4)
- ultima_acao: FA-5 implementada e provada (pele NEXUS na intranet toda) — QA Skip v0.0.67 passou; screenshots anexados
- proxima_acao: teste humano do champion — conferir as telas no preview e dizer se o visual ficou como o NEXUS; depois retoma o teste do farol consolidado (FA-3+FA-4)
- atualizado_em: 2026-10-07T09:50:00-03:00

## Recorte FAROL-1 (4 tasks, uma por vez)

- **FA-1 ✅ CONCLUÍDA (v0.0.53):** tela /farol + mapa de acompanhamento (cadência mensal) + status de atividade provisório + gráfico
- **FA-2 ✅ CONCLUÍDA (v0.0.59):** collection avaliacoes + formulário PECAF/PEDHE com cálculo automático (fórmula confirmada: soma 20 perguntas + pontuação faturamento + pontuação contratos; PEDHE 18 perguntas, contratos = 0)
- **FA-3+FA-4 ✅ IMPLEMENTADAS (v0.0.60-66, aguardando_teste_humano SUSPENSO):** semáforo + tela única por unidade + cliente oculto no formulário
- **FA-5 (aguardando_teste_humano):** pele NEXUS na intranet toda — sidebar escura, PageHeader, cards clicáveis, login escuro

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Semáforo verde/amarelo/vermelho = FA-3 (depende da avaliação PECAF/PEDHE da FA-2).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- Carga 2026 GATED na conferência da tabela de vínculos pelo champion (06_notas/tabela-conferencia-carga-2026.md).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
