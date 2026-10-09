# AP-2026-10-09-0940 — alcance por tipo de reunião conjunta (Day Fusion = 2 empresas; Café = 1)

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: FA-12 (agenda conjunta Café com Franqueados/Day Fusion) — decisão do champion 2026-10-09 09:34
- Sinal: o alcance de uma reunião de agenda conjunta NÃO é uniforme entre tipos — Day Fusion cobre as DUAS empresas (Acuidar + Dona Help, 1 ocorrência em cada) e Café com Franqueados cobre SÓ a empresa selecionada (1 ocorrência). A primeira implementação (09:19) inverteu os dois (Café = 2 empresas, Day Fusion = 1) e o champion corrigiu na 09:34.
- Evidência: correção aplicada na Skip v0.0.101 (QA ✓) — NovaReuniao.tsx usa `duasEmpresas = tipo === 'Day Fusion'` para decidir as empresas do registro; hook `ocorrencias_criar.js` inclui a empresa na idempotency_key de agenda conjunta (sem isso, as 2 ocorrências de um Day Fusion colidiriam). Champion aprovou ("ok tudo certo" 09:36).
- Regra reutilizável: ao modelar "agenda conjunta" ou qualquer regra que varia por TIPO de reunião, validar com o champion o alcance por tipo (quais empresas/unidades cada tipo cobre) ANTES de implementar — e, quando a regra criar N registros (um por empresa), incluir a empresa na chave de idempotência.
- Quando aplicar: novas tasks com regras por tipo de reunião (ex.: multiempresa, cobertura, presença) e qualquer fluxo que crie múltiplos registros por empresa.
- Quando não aplicar: regras uniformes para todos os tipos, ou quando o champion já declarou o alcance por tipo na análise.
- Confiança: alta — decisão explícita do champion, correção de regressão real observada e aprovada.
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto