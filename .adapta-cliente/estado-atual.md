# Estado atual — Adapta Cliente

- task_id: FAROL-1 (evolução da fase 1 — Farol das Unidades PECAF/PEDHE; FA-1 concluída, FA-2 iniciada)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: sinal completo em `06_notas/sinal-farol-unidades-pecaf-pedhe.md` (decisões fechadas 2026-10-06)
- etapa: concluida (FA-1) — FA-2 em análise/implementação
- autorizacao_implementacao: confirmada — 2026-10-06T12:26-03:00 — champion autorizou o plano FA-1 ("sim") após o relatório de análise; FA-2 autorizada em 2026-10-06T12:52-03:00 ("ok pode prosseguir")
- teste_humano: aprovado — 2026-10-06T12:52-03:00 — champion: "ok pode prosseguir" (após ver a tela /farol e perguntar sobre o semáforo)
- verificacao_automatica: passou — Skip v0.0.53 QA ✓ (19 provas na implementação + 4 na revalidação do fechamento: farol acuidar 174 unidades, donahelp 55; RLS 403/401; unidades_info gestor cria ✓ consultor negado 400/404; DELETE 403; classificação em_dia/nao_retorna provadas; fixtures limpas — migration 0016, banco 0; tela provada no navegador)
- aprendizado: capturado — AP-2026-10-06-1258 (ver 06_notas/aprendizado-continuo/)
- ultima_acao: FA-1 concluída (v0.0.53) — mapa de acompanhamento + status provisório aprovados; revalidação do fechamento: 4/4 provas PASSOU
- proxima_acao: FA-2 (formulário PECAF/PEDHE com cálculo automático) — champion autorizou prosseguir ("ok pode prosseguir" 12:52); implementação iniciada
- atualizado_em: 2026-10-06T13:00:00-03:00

## Recorte FAROL-1 (4 tasks, uma por vez)

- **FA-1 ✅ CONCLUÍDA (v0.0.53):** tela /farol + mapa de acompanhamento (cadência mensal) + status de atividade provisório + gráfico
- **FA-2 (em andamento):** collection avaliacoes + formulário PECAF/PEDHE com cálculo automático
- **FA-3:** semáforo (verde = ranqueada; amarelo = atinge mínimos; vermelho = abaixo/sem dados) + carga inicial 2026 (32 PECAF + 9 PEDHE)
- **FA-4:** detalhe da unidade (reuniões + relatos + ocorrências + avaliação + status)

## Decisões registradas durante a FA-1

- Registros de teste MANTIDOS como exemplo (outubro sem dados reais — champion substituirá e limpará depois).
- Semáforo verde/amarelo/vermelho = FA-3 (depende da avaliação PECAF/PEDHE da FA-2).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)