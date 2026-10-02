# Estado atual — Adapta Cliente

- task_id: LT-1-T07 (leva técnica — conector do Google Agenda: importação de reuniões elegíveis)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: mapa de fontes F1-T08 §5 + regras F1-T02 + SPEC-1-001 (RN-1-06 a RN-1-09) + SPEC-1-003 + RLS por empresa (LT-1-T06)
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-02T16:30Z — champion autorizou o plano ("pode") após o relatório de análise
- teste_humano: pendente
- verificacao_automatica: passou — build/QA v0.0.30 sem erros; 8 provas de API + prova de navegador (resumo abaixo)
- aprendizado: pendente
- ultima_acao: LT-1-T07 implementada (hook google_agenda_importar + tela /agenda + navegação)
- proxima_acao: Aguardar teste humano do champion (a prova final com a agenda real exige a credencial — BLOQUEIO-LT07-A)
- atualizado_em: 2026-10-02T16:45:00-03:00

## O que foi implementado (LT-1-T07 — Skip v0.0.30, QA ✓)

1. **Hook `POST /backend/v1/agenda/importar`** — consulta o Google Calendar (janela 7 dias atrás a 14 à frente, configurável), para cada evento: identifica a unidade pelo título (código explícito ou nome oficial exato — nunca adivinha, RN-1-19), aplica as regras F1-T02 (elegibilidade total; cancelada → ocorrência com motivo; remarcação só com identificação confiável), cria a ocorrência com idempotência `google_calendar:eventId:unidade:tipo` (CA-1-05/1-08 — reimportar não duplica). Evento sem unidade identificável → lista de conferência humana. Credencial `GOOGLE_CALENDAR_TOKEN` lida dos Secrets do Skip (nunca no código/chat); sem credencial → resposta explícita `credencial_ausente` (BLOQUEIO-LT07-A, dono: champion). RLS por empresa (403 para empresa não autorizada).
2. **Tela `/agenda`** — seletivo de empresa (limitado às autorizadas do usuário), botão "Importar da Agenda", resumo em cartões (eventos, importadas, já existentes, canceladas, sem unidade, erros) + lista das reuniões sem unidade para conferência.
3. **Navegação** — link "Importar da Agenda" no menu.

## Provas executadas (v0.0.30)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Importação sem credencial | resposta explícita `credencial_ausente` com instrução (nada quebra) | ✓ |
| 2 | Consultora-donahelp tenta importar ACUIDAR | 403 (RLS por empresa) | ✓ |
| 3 | Consultor-acuidar tenta importar DONAHELP | 403 | ✓ |
| 4 | Empresa inválida | erro explícito | ✓ |
| 5 | Regressão: fluxo manual intacto | criar + reenvio → idempotência ok (mesma ocorrência) | ✓ |
| 6 | Regressão: fila com RLS | consultora-donahelp vê 5 registros, todos donahelp | ✓ |
| 7 | Regressão: painel | acuidar 174 unidades ok | ✓ |
| 8 | Navegador: tela /agenda carrega, botão funciona, mensagem de credencial ausente exibida | ✓ | ✓ |

**Limitação real registrada:** a prova final com a agenda REAL exige a credencial `GOOGLE_CALENDAR_TOKEN` nos Secrets do Skip (BLOQUEIO-LT07-A, dono: champion). As provas 1–8 cobrem todo o comportamento sem a credencial; ao gravá-la, o mesmo botão executa a importação real.

## Roteiro de teste humano (LT-1-T07)

1. **Recarregue com Ctrl+Shift+R.**
2. Login como **gestor-teste** → menu **Importar da Agenda**.
3. Selecione a empresa → **Importar da Agenda** → deve aparecer o alerta "Credencial ausente" com a instrução (comportamento correto até a credencial ser gravada).
4. Confira que a tela mostra o seletivo com as DUAS empresas (gestor) e que a mensagem orienta a gravar `GOOGLE_CALENDAR_TOKEN` nos Secrets do Builder — nunca pelo chat.
5. **Próximo passo (sua ação):** grave a credencial nos Secrets do Skip (Builder) → refaça a importação → o resumo deve mostrar eventos importados/já existentes/sem unidade.
6. **Como reconhecer falha:** erro genérico sem orientação; duplicatas ao reimportar; consultora vendo empresa alheia; tela quebrada.

## Pendências restantes (fora desta task)

- **BLOQUEIO-LT07-A:** credencial Google nos Secrets do Skip (dono: champion).
- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.