# FA-8 — Tela "Agenda" (reuniões agendadas + feitas) (análise 2026-10-07 13:10)

**Pedido do champion (13:07):** "quero um topico no menu que se chamará agenda e ele vai mostrar
uma agenda já com as reuniões agendadas e também feitas, dando para a consultora um panorama das
reuniões daquele dia, e essa agenda tbm vai aparecer pra administração ver tudo o que as
consultoras estão fazendo. o topico de importar da agenda pode deixar de existir sem problemas
também"

## Objetivo e resultado observável

Nova tela **Agenda** (substitui "Importar da Agenda"): calendário/lista das reuniões AGENDADAS
(futuras) e FEITAS (passadas), por data. Consultora vê o panorama do dia da SUA empresa;
gestor/admin veem as duas empresas e quem registrou cada reunião (o que as consultoras estão
fazendo). A tela de importação deixa de existir — a importação continua AUTOMÁTICA no login
(LT-2-T02) e o hook permanece.

## Estado atual verificado

- `Agenda.tsx` hoje = tela de importação (botão + resumo) — será reescrita como agenda.
- `Layout.tsx`: nav "📅 Importar da Agenda" → renomear para "📅 Agenda".
- Dados: collection `ocorrencias` tem TUDO (google_calendar + entrada_assistida): data_fato,
  horario, titulo, occurrence_type, estado, empresa, portal_unit_id, criado_por,
  google_sync_estado. Agendadas = data_fato >= hoje; feitas = data_fato < hoje.
- RLS da collection: consultor vê só as empresas autorizadas; gestor/admin ambas (já aprovado
  na LT-1-T06) — a administração já vê o que as consultoras registram (criado_por).
- Importação automática no login (LT-2-T02) mantém as agendas atualizadas — remover o botão
  manual não deixa a agenda desatualizada.

## Plano (4 passos) — EXECUTADO (v0.0.84-86)

1. **Hook `GET /backend/v1/agenda/dia?empresa=&data=YYYY-MM-DD`:** retorna as reuniões do dia
   (todas as ocorrências com data_fato = data), separadas em AGENDADAS (data >= hoje) e FEITAS
   (data < hoje), com estado, tipo, unidade, horário, criado_por (nome do usuário), sync.
   RLS por empresa (padrão LT-1-T06).
2. **Tela `Agenda.tsx` reescrita:** seletor de data (padrão: hoje) + seletor de empresa (se
   autorizadas > 1) + duas seções: "Agendadas" e "Feitas" com cards por reunião (horário,
   título, unidade, tipo, estado, quem registrou). Panorama do dia: contagens no topo.
3. **Layout:** nav "📅 Importar da Agenda" → "📅 Agenda"; rota /agenda mantém (mesma URL).
4. **Provas:** RLS 403/401; consultora vê só a sua empresa; admin vê as duas; contagens do dia
   corretas (fixtures dedicadas, limpas depois); agendadas vs feitas separadas corretamente;
   tela provada no navegador (screenshot).

## Decisões embutidas (champion pode vetar)

- "Panorama das reuniões daquele dia" = seletor de data com HOJE como padrão (consultora pode
  navegar para outros dias).
- Consultora vê a agenda da SUA empresa; gestor/admin veem as duas (RLS existente).
- "Ver tudo o que as consultoras estão fazendo" = campo quem registrou (criado_por) visível na
  card da reunião.
- A tela de IMPORTAÇÃO sai; o hook de importação permanece (login dispara). Sem botão manual.
- Reuniões canceladas aparecem na agenda (estado cancelado — RN-1-08: nunca exclui).

## Riscos

- Remover a tela de importação: mitigado pela importação automática no login (LT-2-T02) —
  se o champion quiser um botão de emergência, pode voltar como ação discreta na nova tela.
- JSVM: lógica inline no callback (AP-1215); nome do criado_por via join na collection users.

## Teste humano previsto

Consultora loga → aba Agenda → panorama do dia (agendadas + feitas da sua empresa);
admin loga → as duas empresas e quem registrou cada reunião; menu mostra "Agenda".
