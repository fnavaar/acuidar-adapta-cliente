# Análise FA-13 — E-mail automático ao franqueado

**Data:** 2026-10-09 · **Pedido:** champion (via chat) · **Decisões:** formulário 09:52

## Pedido

"Enviar e-mail assim que registrar/agendar a reunião para o e-mail de franquia da unidade e
também quando o relato for cadastrado" — o franqueado recebe via e-mail.

## Decisões do champion (formulário 09:52)

1. **Disparos:** ao agendar/registrar a reunião **e** quando o relato for cadastrado.
2. **Relato "cadastrado":** em **atualização posterior** (não no mesmo ato do registro).
3. **Escopo:** só **Acompanhamentos e Consultorias**.
4. **Agenda conjunta (Café/Day Fusion):** **não envia**.
5. **Infra:** **Relay padrão do Skip**.
6. **Remetente:** **Acuidar / Dona Help** conforme a empresa.

## Achados do código real (verificados, não presumidos)

| Peça | Como funciona hoje | Impacto da FA-13 |
|---|---|---|
| `unidades_proxy.js` | Proxy server-side; sanitiza **apenas** codigo/nome/razao_social (minimização LGPD) — o **e-mail da unidade NÃO vai ao frontend** | O disparo será **100% server-side**: o hook busca o `email` da unidade direto no Portal (mesmo padrão do `agenda_sincronizar.js` para attendees) e envia — sem expor dado pessoal ao navegador |
| `ocorrencias_criar.js` | Cria ocorrência (entrada assistida) com relato no mesmo ato; estado confirmado ou exceção | Ponto de disparo do e-mail de **agendamento** (só confirmado) |
| Fila (`Fila.tsx`) | Exibe o relato; **NÃO permite editar relato depois**; transição de estado via `ocorrencias/transicao` | Para o disparo de **relato em atualização posterior**, é preciso **adicionar "Editar relato" na fila** (hoje não existe edição pós-criação) |
| `google_agenda_importar.js` | Importação automática no login cria ocorrências (source google_calendar) | **Fora do disparo**: importação em massa não deve virar spam — escopo = entrada assistida manual |
| API `$app.newMailClient()` | API oficial do PocketBase (PocketBase docs "Sending emails") | Envio de e-mail customizado direto do hook; logs de envio no Skip (`_logs`, source=email) |
| SMTP atual | Relay padrão Skip: sender `noreply@mail.goskip.dev` | Remetente será o da plataforma; **nome** no e-mail = empresa (Acuidar/Dona Help). Recomendação futura: SMTP próprio para identidade de marca e entrega |

## Desenho proposto

1. **Migration 0036** na collection `ocorrencias` (rastreabilidade do envio):
   - `email_agenda_estado` ('' | enviado | falha | sem_email)
   - `email_agenda_enviado_em` (date) · `email_agenda_erro` (text)
   - `email_relato_estado` ('' | enviado | falha | sem_email)
   - `email_relato_enviado_em` (date) · `email_relato_erro` (text)
   - `email_relato_hash` (sha256 do relato já enviado — idempotência do disparo de relato)
2. **Disparo de agendamento** (em `ocorrencias_criar.js`, só quando confirmado):
   - escopo: entrada assistida + tipo Acompanhamento/Consultoria + NÃO agenda conjunta + portal_unit_id real
   - busca e-mail da unidade no Portal (server-side); sem e-mail → `sem_email` (nunca adivinha, RN-1-19)
   - `$app.newMailClient().send(new MailerMessage({ from: { address: senderAddress, name: '<Empresa>' }, to: [{address}], subject: 'Reunião agendada — <unidade>', html: ... }))`
   - grava `email_agenda_estado`/`enviado_em`/`erro`
3. **Disparo de relato** (hook novo `ocorrencias_relato.js`): `onRecordAfterUpdateSuccess` em `ocorrencias` detecta relato alterado (hash) e envia o relato por e-mail ao franqueado — idempotente (hash) e não bloqueia o save (after-success)
4. **Frontend (Fila):** botão **"Editar relato"** (criador + gestor/admin — padrão LT-2-T01) chamando rota custom `POST /backend/v1/ocorrencias/relato` com a mesma validação RN-1-05A (bloqueia HTML/script) + RLS empresa; o after-update dispara o e-mail de relato
5. **Prova técnica:** criar ocorrência teste (Consultoria, unidade com e-mail) → conferir log de envio no Skip; editar relato → novo e-mail; idempotência (mesmo relato não reenvia); RLS; limpeza de fixtures

## Decisões embutidas (champion pode vetar)

- **D1:** disparo de agendamento apenas em registro MANUAL confirmado (entrada_assistida) — importação automática do Google não envia (evita spam em massa).
- **D2:** divergência de data (aguardando_aprovacao_de_excecao) não envia e-mail até a confirmação.
- **D3:** edição de relato só por criador + gestor/admin (padrão LT-2-T01), com RN-1-05A preservada.
- **D4:** no relay padrão o endereço de envio é `noreply@mail.goskip.dev` (limitação da plataforma); o nome exibido será a empresa. SMTP próprio fica como evolução futura.
- **D5:** unidade sem e-mail cadastrado → não envia e registra `sem_email` (nunca adivinha — RN-1-19).