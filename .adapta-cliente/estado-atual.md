# Estado atual — Adapta Cliente

- task_id: LT-1-T07 (leva técnica — conector do Google Agenda: importação de reuniões elegíveis)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: mapa de fontes F1-T08 (`06_notas/mapa-fontes-painel.md` §5 — "Conector automático do Google Agenda... conector é construção nova da leva técnica") + regras F1-T02 (elegibilidade, chave oficial, multiunidade, cancelamento/remarcação) + SPEC-1-001 (RN-1-06 a RN-1-09) + SPEC-1-003 (reuniao_pendente/relato_pendente)
- etapa: aguardando_autorizacao
- autorizacao_implementacao: pendente — análise apresentada ao champion em 2026-10-02T16:35Z; aguardando "sim" em mensagem posterior
- teste_humano: pendente (após implementação)
- verificacao_automatica: pendente (após implementação)
- aprendizado: pendente
- ultima_acao: análise profunda da LT-1-T07 concluída (baseline: entrada assistida manual funciona; conector exige credencial Google que NÃO pode passar pelo chat — precedentes: chave Google exposta em 2026-09-30, token Acuidar idem; credencial vai para os Secrets do Skip)
- proxima_acao: aguardar autorização para implementar
- atualizado_em: 2026-10-02T16:35:00-03:00

## Decisões do Champion já aprovadas que amarram esta task

- F1-T08 (mapa de fontes): reuniões = entrada assistida manual NA FASE 1; conector Google Agenda fica para a leva técnica (esta task).
- F1-T02: elegibilidade total (agendadas, remarcadas, canceladas, concluídas — nenhuma excluída); chave oficial = código da unidade (lookup por nome na agenda → API); multiunidade (Café com Franqueados, Day Fusion); cancelamento mantém registro + motivo; remarcação = original mantém status + novo registro vinculado (só com identificação confiável — nunca por semelhança).
- RN-1-17: reunião sem timestamp válido fica fora da contagem mensal.
- RN-1-19: sem inferência por suposição — unidade não identificada não é adivinhada.

## Plano acordado na análise (resumo para o implementador)

1. **Credencial Google via Secrets do Skip** (nunca pelo chat): champion grava `GOOGLE_CALENDAR_TOKEN` (ou credencial de service account) nos Secrets do Skip 51740 — mesmo padrão de ACUIDAR_PORTAL_TOKEN/DONAHELP_PORTAL_TOKEN. A task fica BLOQUEADA até o champion gravar a credencial (bloqueio explícito, dono: champion).
2. **Hook `google_agenda_importar.js`** (`POST /backend/v1/agenda/importar`): lê a credencial dos Secrets, consulta a agenda (janela de datas configurável), para cada evento: extrai unidade pelo título (lookup na API da empresa — código oficial), aplica regras F1-T02 (elegibilidade, cancelada/remarcada), e cria ocorrência via a MESMA lógica do hook criar (idempotência por source_system=google_calendar + source_meeting_id=eventId — CA-1-05/1-08 já garantem não-duplicação). Evento sem unidade identificável → fila de conferência humana (RN-1-19 — nunca adivinhar).
3. **Frontend — botão "Importar da Agenda"** na home (gestor/admin): dispara a importação e mostra o resumo (importadas, já existentes, sem unidade identificável, canceladas/remarcadas).
4. **RLS por empresa (LT-1-T06)**: a importação respeita empresas_autorizadas do usuário que dispara (consultora só importa a agenda da sua empresa; gestor/admin ambas).
5. **Provas**: importação idempotente (reimportar não duplica), evento cancelado → ocorrência com estado correto, evento sem unidade → fila de conferência, burla de empresa negada, regressão do fluxo manual intacta.

## Critérios de aceite (propostos, binários)

- CA-A: credencial somente nos Secrets do Skip (nada no código/chat/logs)
- CA-B: importação cria ocorrências com idempotência (reimportar → zero duplicatas)
- CA-C: evento cancelado/remarcado gera ocorrência com motivo (regras F1-T02)
- CA-D: evento sem unidade identificável vai para conferência humana, sem inferência (RN-1-19)
- CA-E: RLS por empresa respeitada na importação (burla → 403)
- CA-F: regressão do fluxo manual (registro assistido) intacta

## Bloqueio conhecido (dono: champion)

**BLOQUEIO-LT07-A:** credencial do Google Agenda precisa ser gravada pelo champion nos Secrets do Skip (`GOOGLE_CALENDAR_TOKEN` ou service account JSON). A implementação pode começar sem ela (código + provas com credencial simulada nos testes), mas a prova final com a agenda real exige a credencial. Alternativa se preferir: implementar primeiro e você grava a credencial quando o sistema estiver pronto para consumi-la.

## Pendências fora do escopo

- Rotação das credenciais que passaram pelo chat (chave Google, token Acuidar) — ação do champion nos Secrets.
- Validação do consultor do fechamento da fase 1 — gate humano do método.