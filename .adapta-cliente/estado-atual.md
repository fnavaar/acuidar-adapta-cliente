# Estado atual — Adapta Cliente

- task_id: FAROL-1 (evolução da fase 1 — Farol das Unidades PECAF/PEDHE; FA-2 concluída, FA-3 selecionada)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: sinal completo em `06_notas/sinal-farol-unidades-pecaf-pedhe.md` (decisões fechadas 2026-10-06)
- etapa: concluida (FA-2) — FA-3 selecionada, aguardando autorização
- autorizacao_implementacao: confirmada — 2026-10-06T12:26-03:00 — FA-1 ("sim"); FA-2 autorizada em 2026-10-06T12:52-03:00 ("ok pode prosseguir") + fórmula confirmada 12:58 ("sim": resultado = soma 20 perguntas + pontuação faturamento + pontuação contratos)
- teste_humano: FA-1 aprovado ("ok pode prosseguir" 12:52); FA-2 aprovado ("ok" 13:07)
- verificacao_automatica: passou — Skip v0.0.59 QA ✓ (12 provas FA-2 na implementação + 5 na revalidação do fechamento: PECAF 40+20+20=80 ✓; idempotência ✓; PEDHE 18+10+0=28 ✓; RLS 403 ✓; papel 403 ✓; 401 ✓; listar ✓; fixtures limpas — 0019/0020, banco 0; tela provada no navegador)
- aprendizado: capturado — AP-2026-10-06-1258 (FA-1); AP-2026-10-06-1310 (FA-2: PocketBase number required rejeita 0)
- ultima_acao: FA-2 concluída (v0.0.59) — revalidação 4/4 PASSOU; fixtures limpas (0020, banco 0)
- proxima_acao: FA-3 selecionada (semáforo + carga inicial 2026) — análise entregue; aguardar autorização
- atualizado_em: 2026-10-06T13:12:00-03:00

## Recorte FAROL-1 (4 tasks, uma por vez)

- **FA-1 ✅ CONCLUÍDA (v0.0.53):** tela /farol + mapa de acompanhamento (cadência mensal) + status de atividade provisório + gráfico
- **FA-2 ✅ CONCLUÍDA (v0.0.59):** collection avaliacoes + formulário PECAF/PEDHE com cálculo automático (fórmula confirmada: soma 20 perguntas + pontuação faturamento + pontuação contratos; PEDHE 18 perguntas, contratos = 0)
- **FA-3 (selecionada):** semáforo (verde = ranqueada; amarelo = atinge mínimos; vermelho = abaixo/sem dados) + carga inicial 2026 (32 PECAF + 9 PEDHE)
- **FA-4:** detalhe da unidade (reuniões + relatos + ocorrências + avaliação + status)

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Semáforo verde/amarelo/vermelho = FA-3 (depende da avaliação PECAF/PEDHE da FA-2).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)