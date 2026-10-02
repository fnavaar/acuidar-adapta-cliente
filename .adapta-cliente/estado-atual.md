# Estado atual — Adapta Cliente

- task_id: LT-1-T02 (leva técnica — registro de reunião → ocorrência)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-001 (fluxo direto de registro) + SPEC-1-002 (estados/idempotência) + RN-1-06 a RN-1-09
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada — 2026-10-02T09:58:00-03:00 — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: pendente
- verificacao_automatica: passou — build/QA v0.0.12 sem erros; 11 provas de API (proxy de unidades Acuidar 174 + Dona Help 45; criação confirmada com ID; reenvio rejeitado como duplicata; campo ausente → aguardando_correcao; divergência → aguardando_aprovacao_de_excecao; HTML bloqueado; multiunidade 2 unidades → 2 ocorrências; sem auth 401; Dona Help confirmada) + prova de navegador no preview (formulário carrega 174 unidades, validação de pendências na UI, criação confirmada com comprovante ID vawpiamoyef54q2)
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-10-02-1010-acuidar-wrapper-array.md
- ultima_acao: LT-1-T02 implementada (NovaReuniao.tsx, unidades.ts, hook unidades_proxy.js, hook ocorrencias_criar.js, Layout com navegação) e provas executadas
- proxima_acao: Aguardar teste humano do champion no preview
- atualizado_em: 2026-10-02T10:15:00-03:00

## O que foi implementado (LT-1-T02 — Skip v0.0.12, QA ✓)

1. **`src/pages/NovaReuniao.tsx` (novo)** — formulário de registro: empresa (Acuidar/Dona Help), unidade por código oficial (select carregado da API), data do fato (padrão = hoje), horário, tipo, título, relato; checkbox de multiunidade (RN-1-07); checkbox de divergência de data com justificativa (RN-1-02); validação de obrigatórios na UI (RN-1-03); bloqueio de HTML/script no relato (RN-1-05A); comprovante com ID após confirmação.
2. **`src/lib/unidades.ts` (novo)** — cliente do proxy de unidades com tratamento de `dados_indisponiveis` (RN-1-14).
3. **`pocketbase/hooks/unidades_proxy.js` (novo)** — proxy server-side que lê os tokens dos Secrets (frontend nunca vê credencial), chama a API da empresa escolhida e sanitiza (apenas codigo/nome/razao_social — minimização LGPD).
4. **`pocketbase/hooks/ocorrencias_criar.js` (novo)** — criação com idempotência SHA-256 persistida antes (CA-1-05), consulta de duplicata antes de criar (F1-T06), estados da SPEC-1-002 (confirmado / aguardando_correcao / aguardando_aprovacao_de_excecao / possivel_duplicidade / falha_de_gravacao), multiunidade = 1 reunião → N ocorrências (RN-1-07), bloqueio de HTML (RN-1-05A), 401 sem auth.
5. **`src/App.tsx`** — rota protegida `/reunioes/nova`; **`src/components/Layout.tsx`** — header com navegação (Início / Registrar reunião) e usuário/role.

## Provas executadas (v0.0.12)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Proxy Acuidar | ok, 174 unidades (código+nome+razão social) | ✓ |
| 2 | Proxy Dona Help | ok, 45 unidades | ✓ |
| 3 | Proxy sem auth | 401 | ✓ |
| 4 | Criação válida | confirmado, ID znyparwfzzv1sbq | ✓ |
| 5 | Reenvio mesma origem | duplicata detectada, MESMO ID retornado, nenhuma segunda ocorrência | ✓ |
| 6 | Campo ausente (sem título) | aguardando_correcao com lista de pendências | ✓ |
| 7 | Divergência de data + justificativa | aguardando_aprovacao_de_excecao, ID b60k2nkubfyg8ha | ✓ |
| 8 | Relato com `<script>` | bloqueado (aguardando_correcao) — RN-1-05A | ✓ |
| 9 | Multiunidade (2 unidades) | 2 ocorrências confirmadas, mesma reunião | ✓ |
| 10 | Criação sem auth | 401 | ✓ |
| 11 | Unidade Dona Help (código 101) | confirmado, ID gjpixwleunh03m7 | ✓ |
| 12 | Navegador no preview | form carrega 174 unidades; submit sem título/relato → "Campos pendentes: Título, Relato"; preenchido → comprovante ID vawpiamoyef54q2 | ✓ |

**Nota técnica:** a API da Acuidar retorna o array DIRETO via `res.json` no PocketBase (o wrapper `{status,message,dados}` visto na F1-T01 não aparece no parse do hook) — o proxy aceita ambos os formatos. Registrado no aprendizado AP-2026-10-02-1010.

**Fixtures de teste criados nesta task (limpeza futura):** znyparwfzzv1sbq, b60k2nkubfyg8ha, znw1umdd77mvf42, njim8llgjdcc863, gjpixwleunh03m7, vawpiamoyef54q2 — exclusão exige superuser.

## Roteiro de teste humano (LT-1-T02)

1. Abra o preview → login → menu **Registrar reunião**.
2. Confira o select de unidades (deve listar as Acuidar com código + nome).
3. Preencha data/horário/tipo/título/relato e clique **Registrar ocorrência**.
4. **Esperado:** alerta verde "✅ Ocorrência confirmada — comprovante ID: …".
5. Marque **divergência de data** com justificativa e registre outra → **esperado:** alerta âmbar "aguardando_aprovacao_de_excecao".
6. Troque a empresa para **Dona Help** → o select deve recarregar com as 45 unidades.
7. **Como reconhecer falha:** criação sem comprovante; reenvio criando segunda ocorrência; select vazio sem aviso de indisponibilidade.

## Pendências de limpeza (registradas)

- Fixtures de teste acumuladas (F1-T04: 2; LT-1-T02: 6) — exclusão exige superuser; migration de limpeza na leva.
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task multiempresa.
- Arquivo `.skip.config.json` com mudança pendente no working tree (não alterado por nós) — inspecionar antes do próximo apply_changes.
- Rotação recomendada das credenciais que passaram pelo chat (chave Google, token Acuidar).
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).