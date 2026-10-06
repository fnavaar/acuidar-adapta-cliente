# Estado atual — Adapta Cliente

- task_id: FAROL-1 (evolução da fase 1 — Farol das Unidades PECAF/PEDHE; FA-2 em teste)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: sinal completo em `06_notas/sinal-farol-unidades-pecaf-pedhe.md` (decisões fechadas 2026-10-06)
- etapa: aguardando_teste_humano (FA-2)
- autorizacao_implementacao: confirmada — 2026-10-06T12:26-03:00 — FA-1 ("sim"); FA-2 autorizada em 2026-10-06T12:52-03:00 ("ok pode prosseguir") + fórmula confirmada 12:58 ("sim": resultado = soma 20 perguntas + pontuação faturamento + pontuação contratos)
- teste_humano: FA-1 aprovado — 2026-10-06T12:52-03:00 — "ok pode prosseguir"; FA-2 pendente
- verificacao_automatica: passou — Skip v0.0.58 QA ✓ (12 provas FA-2: PECAF soma conhecida 40+20+20=80 ✓; idempotência reenvio atualiza ✓ PECAF e PEDHE; PEDHE 18 perguntas 18+10+0=28 ✓ (bug corrigido: q19/q20/pontuacao_contratos required rejeitavam 0 — migration 0018); campo ausente → erro explícito ✓; consultora donahelp salva PECAF → 403 ✓; consultor salva → 403 papel ✓; listar PECAF/PEDHE ✓; consultora lê própria empresa ✓ / PECAF 403 ✓; sem auth 401 ✓; programa inválido → erro explícito ✓; bug pefcab→pecaf corrigido; fixtures limpas (0019, banco 0); tela /avaliacao provada no navegador (20 perguntas PECAF, cálculo em tempo real, botão desabilitado com faltando))
- aprendizado: capturado — AP-2026-10-06-1258 (FA-1); FA-2: PocketBase number required rejeita 0 (q19/q20/pontuacao_contratos — migration 0018)
- ultima_acao: FA-2 implementada e provada (v0.0.54-58) — collection avaliacoes + hooks salvar/listar + formulário /avaliacao com cálculo automático
- proxima_acao: aguardar teste humano do champion (formulário /avaliacao no preview)
- atualizado_em: 2026-10-06T13:25:00-03:00

## Recorte FAROL-1 (4 tasks, uma por vez)

- **FA-1 ✅ CONCLUÍDA (v0.0.53):** tela /farol + mapa de acompanhamento (cadência mensal) + status de atividade provisório + gráfico
- **FA-2 (em teste, v0.0.58):** collection avaliacoes + formulário PECAF/PEDHE com cálculo automático (fórmula confirmada: soma 20 perguntas + pontuação faturamento + pontuação contratos; PEDHE 18 perguntas, contratos = 0)
- **FA-3:** semáforo (verde = ranqueada; amarelo = atinge mínimos; vermelho = abaixo/sem dados) + carga inicial 2026 (32 PECAF + 9 PEDHE)
- **FA-4:** detalhe da unidade (reuniões + relatos + ocorrências + avaliação + status)

## Decisões registradas

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Semáforo verde/amarelo/vermelho = FA-3 (depende da avaliação PECAF/PEDHE da FA-2).
- Fórmula do resultado geral confirmada pelo champion (12:58): soma das 20 perguntas + pontuação do faturamento (0-20) + pontuação dos contratos (0-20; PEDHE = 0).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)