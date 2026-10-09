# Sinal FA-12 — Agenda conjunta (Café com Franqueados / Day Fusion)

**Data:** 2026-10-07 14:15 · **Pedido:** champion (Luis Carlos) via chat

## Pedido original

> "no topíco de registrar reunião, assim que for marcado café com franqueados ou day fusion, pode marcar como se fosse agenda conjunta de todos os franqueados, então a consultora não precisa marcar a unidade em especifico porque seria para todas"

## Respostas do champion às 5 perguntas da análise (14:15)

1. **"Todas"** = todas as unidades **ATIVAS** da empresa no momento do registro.
2. **Cobertura:** "se a unidade compareceu a reunião, então pode contar sim" — e perguntou: *"teria como você pegar o registro direto do Google Calendar pra saber se a unidade participou?"*
3. **Recorrência:** não precisa — pode ter um **título padrão organizado definido pela consultora**.
4. **Cancelamento:** precisa **cadastrar o porquê** (motivo).

## Análise técnica — o que o Google Calendar tem (e o que NÃO tem)

- **Tem:** lista de convidados do evento (`attendees[]`) com a resposta de cada um (`responseStatus`: accepted / declined / needsAction / tentative).
- **NÃO tem:** presença real. O Google não sabe quem efetivamente compareceu — só quem respondeu ao convite.
- **Caminho possível (RSVP):** se a consultora convidar os e-mails das unidades (o cadastro do Portal tem campo `email` por unidade), o sistema lê via API quem ACEITOU o convite. Limitação: "aceitou o convite" ≠ "compareceu".
- **Caminho confiável:** consultora marca a presença na intranet (lista de unidades com busca — reusa o filtro da FA-11).

## Desenho proposto (a validar pelo champion)

1. **Formulário de registro:** tipo Café com Franqueados / Day Fusion → campo de unidade sai; título padrão editável pela consultora (ex.: "Café com Franqueados — 15/10"); sem recorrência (cada registro é um evento único).
2. **Ocorrência:** cria **1 ocorrência única** com marcador `agenda_conjunta` (escopo = unidades ativas no momento do registro) — NÃO cria 172 registros.
3. **Presença:** consultora marca as unidades que COMPARECERAM (lista com busca) → **só as presentes contam na cobertura** (resposta 2 do champion).
4. **Google Calendar:** 1 evento único; importação reconhece o marcador (extendedProperties) e não duplica; attendees opcionais (RSVP como sugestão pré-marcada, se o champion quiser na fase 2).
5. **Cancelamento:** cancela o registro único + motivo obrigatório (RN-1-08 já existente — nunca exclui).

## Decisões pendentes do champion

- **Fonte da presença:** (a) manual na intranet [recomendado — confiável]; (b) RSVP do Google como sugestão pré-marcada [híbrido]; (c) só RSVP do Google [não confiável — aceitar convite ≠ comparecer].
- **Cobertura:** confirmar que a agenda conjunta NÃO acende cobertura automática para todas — só para as marcadas como presentes.

## Estado

- Sinal registrado. **Análise formal + implementação só após fechamento da FA-11** (uma task por vez; FA-11 em aguardando_teste_humano).

## Alcance por tipo (corrigido 2026-10-09 09:34)

- **Day Fusion:** Acuidar + Dona Help (as DUAS empresas — 1 ocorrência em cada, idempotência inclui empresa).
- **Café com Franqueados:** só a empresa selecionada (ou Acuidar ou Dona Help).
- Primeira versão (09:19) tinha invertido; corrigida na v0.0.101.