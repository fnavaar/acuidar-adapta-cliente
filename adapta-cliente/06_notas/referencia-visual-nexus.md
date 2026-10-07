# Referência visual — NEXUS CONSULTORIA (projeto Skip 48835)

**Registrado em:** 2026-10-07 · **Origem:** Champion (Luis Carlos) — "eu ainda não gostei da parte do
frontend, eu disse que queria que o front fosse parecido com o de outro projeto que já foi feito no
skip, o nexus consultoria".

## O projeto de referência

- **NEXUS CONSULTORIA** — Skip project **48835**, hostname `portal-acuidar-60b71` (org luiscarlos-9b8dd).
  "Sistema web corporativo para gestão de consultoras, unidades, reuniões, eventos e performance
  das franquias ACUIDAR." Tem Farol, PECAF/PEDHE, agenda, reuniões, unidades das duas marcas
  (tema por marca via `useBrandTheme`).
- **Código lido (fonte de verdade do estilo):** `src/components/AdminLayout.tsx`,
  `PageHeader.tsx`, `FarolSummaryCards.tsx`, `FarolBadge.tsx`, `FarolTransparencyPanel.tsx`,
  `src/pages/admin/AdminFarol.tsx`, `AdminUnidadeDetail.tsx`, `src/main.css`, `tailwind.config.ts`.
- **Screenshots capturados:** `artifacts/referencia-nexus/nexus-login.png`,
  `nexus-farol.png`, `nexus-dashboard.png`, `nexus-unidades.png` (workspace ETHOS).

## O estilo NEXUS em concreto (o que "parecido" significa)

1. **Layout admin com sidebar escura fixa:** fundo `#0c0c0c` (página) e `#141614` (sidebar/header),
   borda `#343834`, texto claro `#bec6ba`; logo da marca + "NEXUS ADMIN" no topo; nav com ícones
   (lucide) e item ativo destacado (`bg-[#545955]`); card do usuário + sair no rodapé da sidebar;
   header superior translúcido "Portal NEXUS • Painel de Controle" + dropdown do usuário.
2. **Conteúdo claro sobre a moldura escura:** área principal `bg-slate-50`; cards brancos
   `rounded-xl border border-slate-200 shadow-subtle`; tipografia Inter/SF Pro.
3. **Brand primary teal `#0F4C5C`** (botões, links, nomes de unidade em bold, sublinhado do título).
4. **PageHeader:** título grande com sublinhado curto colorido embaixo + subtítulo cinza + botão de
   ação à direita (ex.: "Recalcular Farol").
5. **Farol — resumo clicável (FarolSummaryCards):** grid de 5 cards — Total · Verdes · Amarelas ·
   Vermelhas · Sem dados — cada um com contagem grande, borda colorida suave, hover com elevação e
   anel quando selecionado; clicar FILTRA a lista.
6. **Farol — lista agrupada por cor (AdminFarol):** seções "🟢 VERDES (n) / 🟡 AMARELAS (n) /
   🔴 VERMELHAS (n) / ⚪ SEM DADOS (n)", cada linha com FarolBadge (pill com emoji), nome da unidade
   em bold teal, cidade/UF • franqueado, pontuação "55 pts", tag RANQUEADA/NÃO RANQUEADA,
   consultora e link "Ver Análise →".
7. **Detalhe da unidade (AdminUnidadeDetail + FarolTransparencyPanel):** PageHeader com badge grande
   do farol; grid de info (Franqueado · Consultora responsável · CNPJ); painel "Por que esta unidade
   está em X?" — resultado geral `/102` com barra de progresso colorida + barras por categoria
   (Avaliação qualitativa 0-62, Faturamento 0-20, Contratos 0-20, Cliente Oculto 0-20) + blocos
   "Pontos fortes" / "Oportunidades de melhoria"; tabela de reuniões da unidade (Data/Tipo/Assunto/Status).
8. **Login:** fundo gradiente escuro, card translúcido, botão verde vibrante.

## Diferença para a intranet atual (Adapta Cliente, 51740)

A intranet hoje usa `Layout.tsx` de header simples (links em linha, sem sidebar, sem marca, sem
PageHeader) e a tela `/farol` é uma tabela shadcn padrão com cards de contagem — funcional, mas
visualmente distante do NEXUS. O retrabalho proposto reutiliza TODOS os hooks/regras já provados
(farol, painel, transicao, avaliacoes) e muda a pele/layout.

## Login de referência do NEXUS

Usuário demo criado pelas seed migrations do próprio projeto 48835 (ex.: `ceo@acuidarbr.com.br`,
role `ceo`). Senha demo fica nas migrations do projeto — não registrar aqui (regra de segredos).
