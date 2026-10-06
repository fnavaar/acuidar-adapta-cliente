# Análise FA-3 — Semáforo + Carga inicial 2026

**Task:** FAROL-1 / FA-3 · **Data:** 2026-10-06 · **Estado:** análise concluída — aguardando autorização
**Fontes:** 6 PDFs anexos (Regulamento PECAF/PEDHE, Avaliação PECAF/PEDHE 2026, Resultado 2026 PECAF/PEDHE) + cadastro oficial das APIs (174 Acuidar / 55 Dona Help)

## 1. O que a análise provou (com evidência)

### Regulamentos (mínimos por tempo de franquia)
- **PECAF** (contratos + faturamento + mínimo): 3m→2/R$10k/R$8k · 4m→3/15k/12k · 6m→4/20k/16k · 1a→8/60k/48k · 2a→15/120k/96k · 3a→25/200k/160k · 4a→40/260k/208k · 5a→50/300k/240k
- **PEDHE** (só faturamento): 3m→10k · 6m→20k · 8m→30k · 10m→40k · 12m→60k · 14m→70k · 16m→80k · 18m→100k · 2a→120k · 3a→200k

### ACHADO — estrutura do resultado (validada contra os 2 PDFs de Avaliação)
- A **contagem qualitativa** dos PDFs = soma das perguntas + **CLIENTE OCULTO (+20 fixo para todas as unidades)** — nos dois programas.
- **Resultado geral = contagem qualitativa + pontuação faturamento + pontuação contratos** (PECAF) / contagem + pontuação faturamento (PEDHE, contratos = 0).
- Prova: PECAF ABC 62+20+20 = **102** ✓ (48/48 unidades); PEDHE BSB 28+20+0 = **48** ✓ (13/13).
- **Implicação para o formulário FA-2:** o formulário não tem campo "cliente oculto". A carga 2026 replica o PDF (soma = contagem incluindo os 20). Para avaliações 2027+ o champion decide: (a) adicionar campo cliente oculto, ou (b) manter o formulário como está.

### Carga 2026 extraída e validada
- **63 linhas**: 50 PECAF (48 elegíveis + Palmas e Camaçari "não se aplica" — 1 mês de abertura) + 13 PEDHE.
- **Fórmula do resultado: 48/48 PECAF + 13/13 PEDHE ✓** (soma + pont. faturamento + pont. contratos = resultado geral).
- **Ranqueadas: 32 PECAF + 9 PEDHE = 41 — 100% iguais à lista oficial do Resultado 2026 (0 divergências).**
- Dados por unidade: soma_perguntas, pontuacao_faturamento, faturamento_bruto, contratos_fixos, pontuacao_contratos, resultado_geral, ranqueada, tempo de franquia, referência "Junho e Julho" (arquivos `carga-2026-pecaf-final.json` / `carga-2026-pedhe-final.json`).

### Vínculo nome (PDF) ↔ código oficial (RN-1-19)
- 54 casamentos exatos + **7 especiais** (nome no PDF ≠ nome no cadastro — conferir abaixo) + 2 não elegíveis.
- Nenhuma unidade ficou sem código.

## 2. Os 7 vínculos especiais (aprovação do champion)

| # | Nome no PDF | Código | Nome no cadastro oficial |
|---|---|---|---|
| 1 | Acuidar Natal / Dh Natal Tirol | 77 | Acuidar Natal |
| 2 | Acuidar Presidente Prudente/Bauru | 155 | Acuidar Presidente Prudente |
| 3 | Acuidar SP Carrão/Anália Franco | 268 | Acuidar SP Anália Franco |
| 4 | Acuidar SP Jabaquara | 91 | Acuidar SP Ipiranga Jabaquara |
| 5 | Acuidar Taboão da Serra e Sp Santo Amaro | 295 | Acuidar Taboão da Serra |
| 6 | DH BSB Águas Claras DF | 153 | Dona Help BSB Águas Claras |
| 7 | Dona Help SBC Nova Petrópolis | 146 | Dona Help São Bernardo do Campo Nova Petrópolis |

Tabela completa das 63 linhas: `06_notas/tabela-conferencia-carga-2026.md`

## 3. Decisões pendentes do champion (gates da FA-3)

1. **Tabela de conferência** — aprovar os 7 vínculos especiais acima (RN-1-19: nunca adivinhar; sem aprovação não grava).
2. **Cliente oculto (+20)** — campo ausente no formulário FA-2; decidir o tratamento para 2027+ (a carga 2026 replica o PDF independentemente desta decisão).
3. **q1..q20 individuais da carga** — os PDFs não expõem as respostas individuais de forma confiável (cabeçalhos repetidos; extração com incerteza ±2 em 2 unidades). Opções: (a) champion fornece os **xlsx originais** → extração exata; (b) gravar a carga com **soma consolidada** e q1..q20 vazios + flag `carga_historica` (o semáforo usa só resultado/ranqueada/mínimos — não usa q1..q20).
4. **Autorização para implementar** o plano abaixo.

## 4. Plano de implementação (após autorização)

1. **Hook farol** (`GET /backend/v1/farol`): join com `avaliacoes` do ano corrente → semáforo por unidade + contagens (verde/amarelo/vermelho/⚪); mínimos dos regulamentos parametrizados por programa no código; tempo de franquia da avaliação (carga) ou `unidades_info` (formulário).
2. **Tela /farol**: bolinha colorida por unidade + filtro por cor + legenda com a regra aprovada.
3. **Carga 2026**: script idempotente (UNIQUE programa+unidade+ano) com as 63 avaliações — só após aprovação da tabela de conferência.
4. **Provas**: ranqueada → verde; abaixo do mínimo → vermelho; sem avaliação → vermelho (⚪ só < 3 meses); RLS por empresa; idempotência da carga; regressão FA-1/FA-2.

## 5. Regra do semáforo (aprovada no sinal — 06_notas/sinal-farol-unidades-pecaf-pedhe.md)

- 🟢 **Verde:** ranqueada = SIM
- 🟡 **Amarelo:** não ranqueada, mas atinge os mínimos do regulamento para o tempo de franquia
- 🔴 **Vermelho:** abaixo dos mínimos ou sem avaliação no ano
- ⚪ **Sem classificação:** unidade com menos de 3 meses (não se aplica — ex.: Palmas e Camaçari em 2026)
