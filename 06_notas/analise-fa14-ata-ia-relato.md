# FA-14 — Ata inteligente do Google Meet como rascunho de relato (validação da consultora)

- **Data:** 2026-10-09
- **Pedido do champion (09:52–10:17):** o sistema deve puxar a ata que a IA do Google Meet gera (a assinatura permite) e a consultora **valida antes de ficar realmente salvo** como relato. Champion confirmou no formulário (10:20): fonte = **Ata inteligente (resumo IA/Gemini)**.
- **Status:** análise formalizada — `aguardando_autorizacao`. Nada implementado.
- **Task relacionada (pausada):** FA-13 (e-mail ao franqueado) segue **pausada no teste humano** por decisão do champion ("não quero testar agora", 10:17) — **não concluída**.

## 1. Capacidade real (grounded — Google oficial)

| Recurso | Fonte Google | O que entrega |
|---|---|---|
| `conferenceRecords.smartNotes` | https://developers.google.com/workspace/meet/api/reference/rest/v2/conferenceRecords.smartNotes | Resumo gerado pela IA do Meet ("smart notes"), com `name`, `state` (`FILE_GENERATED` quando pronto), `startTime/endTime` e `docsDestination` (aponta o Google Doc da ata no Drive do organizador) |
| `conferenceRecords.transcripts` (+ entries) | https://developers.google.com/workspace/meet/api/reference/rest/v2/conferenceRecords.transcripts | Transcrição literal com falas por participante (não escolhida; fica como fallback/conferência futura) |
| "Take notes for me" (notas IA) | https://support.google.com/meet/answer/14754931 | Ata gerada **logo após a reunião** e salva no Drive do organizador (pasta Google Meet) — latência real a tratar |

- **Sim, é possível por API oficial** — não é scraping. O que recebo é o **conteúdo do doc (ata)** via `docsDestination` (leitura do Drive/Docs).
- **Entender como relato = análise assistida minha** (resumo/estrutura do texto-fonte), separada do conteúdo original — alinhado ao SOUL (separar conteúdo-fonte de análise assistida).

## 2. Fluxo proposto (menor recorte completo)

1. A reunião vira ocorrência (importação do Google ou entrada assistida) com `relato` **vazio** e `ata_ia_status = ausente | disponivel | rascunho_pronto | falha`.
2. Novo hook `POST /backend/v1/atas/puxar` recebe `{ occurrence_id }`:
   - descobre o `conferenceId` do Meet pelo evento vinculado (Calendar API, campo `conferenceData.conferenceId` do evento — o mesmo vínculo já usado na importação); fallback por janela de tempo via `conferenceRecords.list`;
   - chama meet API → `smartNotes.get`; se `FILE_GENERATED` → lê o doc da ata (Drive/Docs export);
   - grava em campos novos: `ata_ia_origem` (texto-fonte, **nunca exposto ao frontend**), `relato_rascunho_ia` (meu resumo assistido, separado) e status;
   - **nunca inventa conteúdo**: se a ata ainda não foi gerada → `ausente` + retry idempotente (botão), nunca cria relato sozinho.
3. Na **Fila**, a consultora vê o bloco "📝 Ata da reunião (IA)": o rascunho vem pré-preenchido (editável) + link/visualização do original; ela **valida/edita e salva** → só aí o `relato` oficial é gravado (e o hook da FA-13 dispara o e-mail, quando a FA-13 for concluída).
4. Se a consultora **não validar**, o relato continua vazio — **nada é salvo sozinho** (pedido explícito do champion).

## 3. Decisões embutidas (champion pode vetar)

- Gatilho: **botão "Puxar ata" na Fila + automático na confirmação** pós-reunião (janela segura) — retry manual sempre disponível.
- Mesmo recorte da FA-13: só **Acompanhamento/Consultoria** com unidade real; agenda conjunta fica fora por ora (1 ocorrência cobre N unidades — a ata vale para a reunião, mas o relato por unidade perde sentido; decidir depois).
- Ata original (texto-fonte) fica **só no servidor** (LGPD/sanitização — padrão `unidades_proxy`); frontend recebe apenas o rascunho + flag de disponível.
- Sem email da unidade e sem Meet vinculado → `falha` com motivo; nunca bloqueia o registro.

## 4. Infra necessária (gate humano — único bloqueio real)

- **Novos escopos OAuth nos refresh tokens** (mesmo processo LT-1-T09/LT-2-T01, client próprio + Access type Offline + Force prompt): adicionar `https://www.googleapis.com/auth/meet.readonly` e `https://www.googleapis.com/auth/drive.readonly` (para ler o doc da ata) aos **clientes OAuth da Acuidar e da Dona Help** e **regravar os 2 refresh tokens nos mesmos secrets**. Sem isso a prova real falha (403/escopo).
- **Nenhum secret novo** — reutiliza client_id/client_secret/refresh tokens existentes.

## 5. Arquivos provavelmente afetados (Skip 51740)

- `pocketbase/migrations/0038_ata_ia.js` — campos na ocorrências: `ata_ia_status`, `ata_ia_origem`, `ata_ia_doc_id`, `relato_rascunho_ia`.
- `pocketbase/hooks/atas_puxar.js` (novo) — Meet API + Drive/Docs + idempotência + RLS por empresa/perfil.
- `src/pages/Fila.tsx` — bloco de validação da ata (rascunho editável + botão Puxar/Retry + status).
- Reaproveita: credenciais/refresh (LT-1-T09), vínculo evento↔ocorrência (LT-1-T07), padrão de RLS e provas (LT-2-T01/FA-12/FA-13).

## 6. Matriz critério → prova

| Critério (o que precisa ser verdade) | Prova verificável |
|---|---|
| Puxa a ata oficial por API quando disponível | Prova real com agenda/Meet reais: `smartNotes.get` retorna doc → rascunho preenchido (gate humano: tokens com escopo) |
| Ata ainda não gerada → nunca inventa, fica `ausente` + retry | Fixture/estado simulado: `ausente` idempotente, sem relato |
| Relato só é gravado após validação da consultora | Antes: `relato` vazio; após salvar na Fila: `relato` = versão validada |
| RLS por empresa e permissão | 403 consultora donahelp→acuidar; 401 sem auth (padrão provas anteriores) |
| Texto-fonte nunca exposto ao frontend | Hook retorna só rascunho/status; doc original só no servidor |
| Registro nunca depende do Google | Falha/ausência → ocorrência intacta (padrão LT-2-T01) |

## 7. Riscos e mitigação

- **Latência da ata** ("logo após a reunião") → status `ausente` + retry por botão; nunca block no registro.
- **Licença/AI desabilitada no Workspace** → se `smartNotes` indisponível para a conta, o hook responde `falha` com motivo claro; vale validar na prova real.
- **LGPD**: ata é dado sensível → minimização, texto-fonte server-only, rascunho revisável antes de gravar.
- **Duplicidade**: idempotência por occurrence_id/evento (padrão já provado).

## 8. Teste humano (quando autorizado)

1. Regravar os 2 refresh tokens com escopo extra (passo a passo junto).
2. Registro/importação de uma reunião real no Meet com IA → "Puxar ata" → rascunho aparece.
3. Consultora edita/valida → salvar → conferir que o relato oficial ficou o validado (e, com FA-13, o e-mail dispara).

**Próxima ação única:** aguardar autorização do champion para implementar.
