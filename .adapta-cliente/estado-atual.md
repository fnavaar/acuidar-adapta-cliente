# Estado atual — Adapta Cliente

- task_id: LT-1-T02 (leva técnica — registro de reunião → ocorrência)
- champion: Luis Carlos - CTO (exerce também o papel de Administrador do Portal e Responsável técnico; mesmo champion para Acuidar e Dona Help)
- spec: SPEC-1-001 (fluxo direto de registro) + SPEC-1-002 (estados/idempotência) + RN-1-06 a RN-1-09
- etapa: concluida
- autorizacao_implementacao: confirmada — 2026-10-02T09:58:00-03:00 — champion autorizou o plano ("sim") após o relatório de análise
- teste_humano: aprovado — 2026-10-02T11:42:00-03:00 — champion testou no preview e confirmou ("funcionou")
- verificacao_automatica: passou — revalidação do zero (RV-1 a RV-13): proxy Acuidar 174 / Dona Help 55 unidades (crescimento normal do cadastro), criação confirmada com ID, reenvio → duplicata com mesmo ID, campo ausente → aguardando_correcao, HTML → bloqueado (linha vermelha), divergência → aguardando_aprovacao_de_excecao, multiunidade → 2 ocorrências, sem auth → 401, regressão do login da LT-1-T01 ok, nenhum segredo no código
- aprendizado: capturado:06_notas/aprendizado-continuo/AP-2026-10-02-1010-acuidar-wrapper-array.md
- ultima_acao: LT-1-T02 concluída — segunda task da leva técnica fechada com teste humano aprovado
- proxima_acao: Aguardar pedido do champion para a próxima task da leva técnica (fila de revisão/aprovação — SPEC-1-002)
- atualizado_em: 2026-10-02T11:50:00-03:00

## O que foi implementado (LT-1-T02 — Skip v0.0.12, QA ✓)

1. **`src/pages/NovaReuniao.tsx` (novo)** — formulário de registro: empresa (Acuidar/Dona Help), unidade por código oficial (select carregado da API), data do fato (padrão = hoje), horário, tipo, título, relato; checkbox de multiunidade (RN-1-07); checkbox de divergência de data com justificativa (RN-1-02); validação de obrigatórios na UI (RN-1-03); bloqueio de HTML/script no relato (RN-1-05A); comprovante com ID após confirmação.
2. **`src/lib/unidades.ts` (novo)** — cliente do proxy de unidades com tratamento de `dados_indisponiveis` (RN-1-14).
3. **`pocketbase/hooks/unidades_proxy.js` (novo)** — proxy server-side que lê os tokens dos Secrets (frontend nunca vê credencial), chama a API da empresa escolhida e sanitiza (apenas codigo/nome/razao_social — minimização LGPD).
4. **`pocketbase/hooks/ocorrencias_criar.js` (novo)** — criação com idempotência SHA-256 persistida antes (CA-1-05), consulta de duplicata antes de criar (F1-T06), estados da SPEC-1-002, multiunidade = 1 reunião → N ocorrências (RN-1-07), bloqueio de HTML (RN-1-05A), 401 sem auth.
5. **`src/App.tsx`** — rota protegida `/reunioes/nova`; **`src/components/Layout.tsx`** — header com navegação e usuário/role.

## Provas da implementação (v0.0.12) + revalidação do zero (RV-1 a RV-13)

| # | Prova | Resultado | ✓ |
|---|---|---|---|
| 1 | Proxy Acuidar | ok, 174 unidades | ✓ |
| 2 | Proxy Dona Help | ok (45 na implementação; 55 na revalidação — cadastro cresceu) | ✓ |
| 3 | Proxy sem auth | 401 | ✓ |
| 4 | Criação válida | confirmado com ID | ✓ |
| 5 | Reenvio mesma origem | duplicata detectada, MESMO ID, nenhuma segunda ocorrência | ✓ |
| 6 | Campo ausente | aguardando_correcao com pendências | ✓ |
| 7 | Divergência de data | aguardando_aprovacao_de_excecao | ✓ |
| 8 | Relato com HTML/script | bloqueado (RN-1-05A — linha vermelha) | ✓ |
| 9 | Multiunidade (2 unidades) | 2 ocorrências confirmadas, mesma reunião | ✓ |
| 10 | Criação sem auth | 401 | ✓ |
| 11 | Unidade Dona Help | confirmado | ✓ |
| 12 | Navegador no preview | unidades carregadas, validação na UI, comprovante exibido | ✓ |
| 13 | Regressão login (LT-1-T01) | gestor-teste autentica com role correto | ✓ |

**Fixtures de teste acumuladas (limpeza futura — exclusão exige superuser):** F1-T04 (2) + LT-1-T02 (6: znyparwfzzv1sbq, b60k2nkubfyg8ha, znw1umdd77mvf42, njim8llgjdcc863, gjpixwleunh03m7, vawpiamoyef54q2) + revalidação (5: bzuq0l0e47ldr4x, ddfbi2z928szif8, cw2dnnimvb9lqwb, curtk1y74pshddx, 2fkg4usxi9rbxc3).

## Pendências de limpeza (registradas)

- Fixtures de teste acumuladas (13 registros) — migration de limpeza na leva (exclusão exige superuser).
- Hook temporário `validar-contrato-donahelp` (Skip v0.0.7) — remover após formalização da task multiempresa.
- Arquivo `.skip.config.json` com mudança pendente no working tree (não alterado por nós) — inspecionar antes do próximo apply_changes.
- Rotação recomendada das credenciais que passaram pelo chat (chave Google, token Acuidar).
- Validação do consultor do fechamento da fase 1 — pendência registrada no changelog (2026-10-02).