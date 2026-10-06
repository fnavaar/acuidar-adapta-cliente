# Estado atual — Adapta Cliente

- task_id: FAROL-1 (evolução da fase 1 — Farol das Unidades PECAF/PEDHE; análise em andamento)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: sinal completo em `06_notas/sinal-farol-unidades-pecaf-pedhe.md` (decisões fechadas 2026-10-06); SPEC nova/emenda a propor na análise
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente
- teste_humano: pendente
- verificacao_automatica: pendente
- aprendizado: pendente
- ultima_acao: análise profunda do farol concluída — relatório entregue ao champion (2026-10-06)
- proxima_acao: aguardar autorização para implementar
- atualizado_em: 2026-10-06T12:45:00-03:00

## Contexto do FAROL-1 (sinal completo)

- **Decisões fechadas pelo champion:** status de atividade via API (campo será criado, não agora); formulário PECAF/PEDHE na intranet com cálculo automático; cálculo padrão pela lógica dos PDFs; PECAF anual; mapa de acompanhamento por reuniões + semáforo geral pelo PECAF/PEDHE; evolução imediata da fase 1.
- **Parâmetros:** PECAF (Acuidar): mínimos contratos+faturamento por tempo (3m→5a); 50 unidades; 20 perguntas 0/1/2; resultado 43-102; 32 ranqueadas 2026 (ref. jun/jul). PEDHE (Dona Help): mínimos só faturamento (3m→3a); 13 unidades; 20 perguntas adaptadas Web Help; resultado 41-48; 9 ranqueadas 2026.
- **Semáforo proposto (a aprovar):** verde = RANQUEADA SIM; amarelo = não ranqueada mas atinge mínimos do regulamento; vermelho = abaixo dos mínimos ou sem dados.
- **Dependência futura:** campo de status de atividade na API do Portal (o farol nasce sem ele).

## Pendências restantes (fora de task)

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets
- Publicação em produção — decisão do champion via Builder/MCP (resolve também a expiração de 7 dias do refresh token em modo Teste)
- Validação do consultor do fechamento formal da fase 1 — gate humano (documentação pronta; o farol é evolução, não depende dele)