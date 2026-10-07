# Estado atual — Adapta Cliente

- task_id: LT-2-T01 (registro de reunião criar evento no Google Calendar — escrita intranet→Google)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: pedido do champion 2026-10-07 10:37 — "Registro de reunião criar evento no Google Calendar: Quero isso — analise como próxima task"; contexto: integração atual é SÓ leitura (Google→intranet, calendar.readonly, LT-1-T07/08/09); SPECs da fase 1 não cobrem escrita — task nova da leva 2 (LT-2)
- etapa: aguardando_autorizacao (LT-2-T01 — análise profunda entregue; aguardar autorização para implementar)
- autorizacao_implementacao: não confirmada — aguardando "pode implementar" do champion em mensagem posterior ao relatório de análise
- teste_humano: pendente (após implementação); FA-5 e FA-6 APROVADOS ("tudo ok" 10:51)
- verificacao_automatica: pendente (baseline: QA v0.0.69 passou — nenhuma alteração de produto nesta etapa de análise)
- aprendizado: capturado — AP-2026-10-06-1258 (FA-1); AP-2026-10-06-1310 (FA-2); AP-2026-10-06-1710 (FA-3/4); FA-5 sem sinal (controle 09:50); FA-6 sem sinal (controle 10:54)
- ultima_acao: LT-2-T01 analisada — achados: hook criar gera ocorrência CONFIRMADA imediatamente (entrada assistida); agenda de destino por credencial (GOOGLE_CALENDAR_REFRESH_TOKEN/_DONAH); importação lê a MESMA agenda → risco de duplicidade intranet↔Google; refresh tokens atuais são calendar.readonly (escopo de leitura)
- proxima_acao: aguardar autorização para implementar LT-2-T01
- atualizado_em: 2026-10-07T11:02:00-03:00

## Recorte FAROL-1 (concluído)

- **FA-1 ✅ CONCLUÍDA (v0.0.53):** tela /farol + mapa de acompanhamento (cadência mensal) + status de atividade provisório + gráfico
- **FA-2 ✅ CONCLUÍDA (v0.0.59):** collection avaliacoes + formulário PECAF/PEDHE com cálculo automático (fórmula confirmada: soma 20 perguntas + pontuação faturamento + pontuação contratos; PEDHE 18 perguntas, contratos = 0)
- **FA-3+FA-4 ✅ CONCLUÍDAS (v0.0.60-66):** semáforo + tela única por unidade + cliente oculto no formulário
- **FA-5 ✅ CONCLUÍDA (v0.0.67):** pele NEXUS na intranet toda — sidebar escura, PageHeader, cards clicáveis, login escuro
- **FA-6 ✅ CONCLUÍDA (v0.0.68-69):** farol UNICAMENTE PECAF/PEDHE por empresa — mapa mensal de reuniões removido do farol (hook+tela); visão mensal segue no painel de cobertura (SPEC-1-003)

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Semáforo verde/amarelo/vermelho = FA-3 (depende da avaliação PECAF/PEDHE da FA-2).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).
- Carga 2026 GATED na conferência da tabela de vínculos pelo champion (06_notas/tabela-conferencia-carga-2026.md).
- FA-6: farol unicamente PECAF/PEDHE por empresa; reuniões não influenciam mais o farol (decisão 10:33-10:37 de 2026-10-07).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)
- Carga 2026 do farol — gated na conferência dos 7 vínculos especiais nome↔código pelo champion
- LT-2-T01 gate humano: champion regenera os 2 refresh tokens com escopo calendar.events (mesmo processo da LT-1-T09)
