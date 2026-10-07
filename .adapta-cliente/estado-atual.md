# Estado atual — Adapta Cliente

- task_id: nenhuma (FAROL-1 concluído — FA-1..FA-6; próxima: LT-2-T01 a analisar)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: decisão do champion 2026-10-07 10:33-10:37 — "a questão do farol das unidades deve ser agora unicamente sobre o pedhe e pecaf de acordo com a empresa"; referência visual em `06_notas/referencia-visual-nexus.md`
- etapa: concluida (FA-5 + FA-6 — "aprovo" + "tudo ok" do champion 10:51)
- autorizacao_implementacao: confirmada — 2026-10-07T10:37-03:00 — formulário: "Farol unicamente PECAF/PEDHE — Pode implementar"; FA-5 (pele NEXUS) autorizada 09:30 ("Pode implementar", escopo intranet toda)
- teste_humano: FA-5 (visual NEXUS) e FA-6 (farol unicamente PECAF/PEDHE) APROVADOS — "aprovo" + "tudo ok" do champion 10:51 de 2026-10-07
- verificacao_automatica: passou — Skip v0.0.68/69 QA ✓ + revalidação de fechamento (RLS 403 consultora donahelp→acuidar; 401 sem auth; farol donahelp 54 unidades PEDHE; farol acuidar 172 sem contagens/mes_referencia — mapa removido; painel /backend/v1/painel/cobertura intacto com visão mensal: 170 sem registro no mês)
- aprendizado: capturado — AP-2026-10-06-1258 (FA-1); AP-2026-10-06-1310 (FA-2); AP-2026-10-06-1710 (FA-3/4); FA-5 sem sinal (controle 09:50); FA-6 sem sinal (controle 10:54)
- ultima_acao: FA-5+FA-6 concluídas e revalidadas do zero (RLS 403/401, farol sem mapa, painel intacto)
- proxima_acao: sem task ativa — próxima: análise da LT-2-T01 (registro de reunião criar evento no Google — pedido do champion 10:37); carga 2026 segue gated nos 7 vínculos
- atualizado_em: 2026-10-07T10:54:00-03:00

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
