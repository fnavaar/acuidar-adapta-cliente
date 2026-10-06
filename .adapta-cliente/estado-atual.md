# Estado atual — Adapta Cliente

- task_id: FAROL-1 (evolução da fase 1 — Farol das Unidades PECAF/PEDHE; FA-1 em teste)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: sinal completo em `06_notas/sinal-farol-unidades-pecaf-pedhe.md` (decisões fechadas 2026-10-06)
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-06T12:26-03:00 — champion autorizou o plano FA-1 ("sim") após o relatório de análise
- teste_humano: pendente
- verificacao_automatica: passou — Skip v0.0.53 QA ✓ (19 provas: farol acuidar 174 unidades (em_dia 2, em_atraso 172), donahelp 55 (em_dia 2, em_atraso 53); RLS consultora donahelp → 403 em acuidar; sem auth 401; empresa inválida → erro explícito; unidades_info: gestor cria ✓, consultor cria/edita NEGADO (400/404), consultor lê própria empresa ✓, lê acuidar 404, DELETE 403 ninguém exclui; classificação provada em_dia (unidade 2: 6 registros no mês + programada 2026-10-06 + ativa), nao_retorna (unidade 3: 3 tentativas vence em_dia, status suspensa); fixtures da prova limpas (migration 0016 — 0015 não rodou por $app em vez de app; banco final 0 em unidades_info); tela /farol provada no navegador (contagens, gráfico de status, tabela ordenada por prioridade)
- aprendizado: pendente
- ultima_acao: FA-1 implementada e provada (v0.0.51-53) — tela /farol + hook /backend/v1/farol + migration 0014 (unidades_info) + limpeza 0016
- proxima_acao: aguardar teste humano do champion (tela /farol no preview)
- atualizado_em: 2026-10-06T12:55:00-03:00

## Contexto do FAROL-1 (sinal completo)

- **Decisões fechadas pelo champion:** status de atividade via API (campo será criado, não agora); formulário PECAF/PEDHE na intranet com cálculo automático; cálculo padrão pela lógica dos PDFs; PECAF anual; mapa de acompanhamento por reuniões + semáforo geral pelo PECAF/PEDHE; evolução imediata da fase 1.
- **Parâmetros:** PECAF (Acuidar): mínimos contratos+faturamento por tempo (3m→5a); 50 unidades; 20 perguntas 0/1/2; resultado 43-102; 32 ranqueadas 2026 (ref. jun/jul). PEDHE (Dona Help): mínimos só faturamento (3m→3a); 13 unidades; 20 perguntas adaptadas Web Help; resultado 41-48; 9 ranqueadas 2026.
- **Semáforo proposto (a aprovar):** verde = RANQUEADA SIM; amarelo = não ranqueada mas atinge mínimos do regulamento; vermelho = abaixo dos mínimos ou sem dados.
- **Dependência futura:** campo de status de atividade na API do Portal (o farol nasce sem ele).

## Recorte FAROL-1 (4 tasks, uma por vez)

- **FA-1 (implementada, aguardando teste humano):** tela /farol + mapa de acompanhamento (cadência mensal) + status de atividade provisório + gráfico
- **FA-2:** collection avaliacoes + formulário PECAF/PEDHE com cálculo automático
- **FA-3:** semáforo (regras aprovadas) + carga inicial 2026 (32 PECAF + 9 PEDHE)
- **FA-4:** detalhe da unidade (reuniões + relatos + ocorrências + avaliação + status)

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)