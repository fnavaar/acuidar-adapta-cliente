# Estado atual — Adapta Cliente

- task_id: CARGA-2026 (aguardando_teste_humano) + LT-2-T01 (análise entregue — aguardando_autorizacao)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 10:37 — "Registro de reunião criar evento no Google Calendar: Quero isso — analise como próxima task"; contexto: integração atual é SÓ leitura (Google→intranet, calendar.readonly, LT-1-T07/08/09); SPECs da fase 1 não cobrem escrita — task nova da leva 2 (LT-2)
- etapa: aguardando_teste_humano (carga 2026 gravada e provada — farol acendeu)
- autorizacao_implementacao: confirmada — 2026-10-07T10:55-03:00 — champion: "primeiro aprovar os 7 vínculos e rodar a carga 2026 (farol acende de verdade)" — resolve o gate da tabela de conferência da FA-3
- teste_humano: CARGA-2026 PENDENTE (champion confere o farol acendendo no preview); FA-5 e FA-6 APROVADOS ("tudo ok" 10:51)
- verificacao_automatica: passou — Skip v0.0.70 QA ✓ (carga + fix toLocaleString ramo PEDHE); revalidações anteriores v0.0.68/69 (RLS 403 consultora donahelp→acuidar; 401 sem auth; farol sem mapa; painel cobertura intacto)
- aprendizado: capturado — AP-2026-10-06-1258 (FA-1); AP-2026-10-06-1310 (FA-2); AP-2026-10-06-1710 (FA-3/4); FA-5 sem sinal (controle 09:50); FA-6 sem sinal (controle 10:54)
- ultima_acao: CARGA 2026 RODADA — 61 avaliações gravadas via hook avaliacoes/salvar (48 PECAF + 13 PEDHE; idempotência ✓; fórmula 0 erros; 41 ranqueadas); farol acendeu: acuidar verde 32/vermelho 140; donahelp verde 9/vermelho 45; fix JSVM toLocaleString no ramo PEDHE (Skip v0.0.70, QA ✓)
- proxima_acao: (1) sincronizar GitHub concluída (remoto 8630678 + alinhamento); (2) champion testar a carga no farol; (3) aguardar "pode implementar" da LT-2-T01
- atualizado_em: 2026-10-07T11:47:00-03:00

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
