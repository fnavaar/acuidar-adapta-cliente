# AP-2026-10-02-1620 — RLS por empresa exige validação em TODAS as camadas de escrita

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: LT-1-T06 (RLS por empresa)
- Sinal: ao implementar isolamento por empresa (empresas_autorizadas no usuário), a RLS da collection (list/view/update) NÃO cobre a criação via rota custom — o hook `ocorrencias_criar` gravava com a empresa pedida no body, sem validar contra as autorizadas do auth. A prova de burla (consultora donahelp criando ocorrência acuidar) passou na primeira execução. Correção: validação explícita da empresa em TODOS os hooks que escrevem ou leem por empresa (criar, painel, unidades_proxy), além da RLS.
- Evidência: v0.0.25 — prova P4 (burla) retornou 200/confirmado; v0.0.26 (fix) — mesma chamada retorna 403. Prova refeita na revalidação: 403 ✓.
- Regra reutilizável: regra de isolamento (por empresa, unidade, papel) precisa de prova de BURLA em cada ponto de entrada — RLS da collection cobre a API nativa, mas rotas custom (hooks) validam por conta própria. Sempre escrever a prova de burla ANTES de considerar a task pronta.
- Quando aplicar: qualquer task que adicione uma regra de acesso/visibilidade.
- Quando não aplicar: regras puramente de exibição (UI) sem consequência de dados.
- Confiança: alta — comportamento observado e corrigido com prova antes/depois (v0.0.25 vs v0.0.26).
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.
