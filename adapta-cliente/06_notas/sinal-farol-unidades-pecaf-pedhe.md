# Sinal — Farol das Unidades (PECAF / PEDHE)

**Registrado em:** 2026-10-06
**Origem:** Champion (Luis Carlos - CTO), conversa ETHOS
**Status:** sinal completo — análise profunda autorizada ("pode", 2026-10-06 12:20)

## O que o champion pediu

**Farol das Unidades** — acompanhamento completo centralizado por unidade: reuniões, relatos,
ocorrências, situação PECAF/PEDHE, mapa de acompanhamento e status de atividade. Duas empresas,
dois programas: **Acuidar → PECAF** · **Dona Help → PEDHE** (mesma lógica, parâmetros próprios).

## Decisões fechadas pelo champion (2026-10-06)

1. **Status de atividade** (ativa/treinada/suspensa/fechada): já existe no Portal Acuidar;
   virá pela **API** — o campo será criado no Portal, **mas não agora** (dependência futura).
2. **Dados do PECAF/PEDHE:** campos viram **formulário dentro da intranet**, preenchido pelo
   champion/consultoria; o **sistema calcula** o resultado geral e o semáforo automaticamente.
3. **Cálculo do semáforo:** lógica dos PDFs com **cálculo padrão** proposto pelo Ethos e
   aprovado pelo champion.
4. **Periodicidade:** PECAF/PEDHE **anual** (avaliação por convenção; referência 2026 = jun/jul).
5. **Mapa de acompanhamento:** **continua**, alimentado por dados de reuniões (em dia, em
   atraso, programada, próximo a atraso, não retorna tentativas); o **semáforo geral vem do
   PECAF/PEDHE**. **Cadência aprovada (12:24): mensal** — 30 ou 31 dias conforme o calendário
   (alinhada à janela mensal RN-1-16).
6. **Escopo:** **evolução imediata da fase 1** (não fase 2).

## Estrutura dos parâmetros (dos PDFs)

### PECAF (Acuidar) — 3 anexos
- **Regulamento:** mínimos por tempo de franquia — contratos mensais + faturamento + mínimo:
  3m→2/R$10k/R$8k · 4m→3/15k/12k · 6m→4/20k/16k · 1a→8/60k/48k · 2a→15/120k/96k · 3a→25/200k/160k
  · 4a→40/260k/208k · 5a→50/300k/240k
- **Avaliação:** 50 unidades (48 elegíveis; Palmas e Camaçari "não se aplica" — 1 mês), tempo de
  unidade, 20 perguntas qualitativas (0/1/2): financeiro (1-5), marketing (6-9), operação APP
  (10-13), equipe (14-19), padrões da marca (20); faturamento bruto por unidade; contratos
  mensais fixos; resultado geral (43-102); RANQUEADA SIM/NÃO
- **Resultado 2026:** 32 unidades ranqueadas, referência junho/julho

### PEDHE (Dona Help) — 3 anexos
- **Regulamento:** mínimos **só de faturamento** por tempo de unidade: 3m→R$10k · 6m→20k ·
  8m→30k · 10m→40k · 12m→60k · 14m→70k · 16m→80k · 18m→100k · 2a→120k · 3a→200k
- **Avaliação:** 13 unidades, 20 perguntas adaptadas ao negócio (Web Help, Web Helper, Helpers;
  Help Cast em vez de Café com Franqueados), faturamento bruto, resultado geral (41-48),
  RANQUEADA SIM/NÃO
- **Resultado 2026:** 9 unidades ranqueadas, referência junho/julho

### Diferenças materiais PECAF ↔ PEDHE
- Mínimos: PECAF tem contratos+faturamento+mínimo; PEDHE só faturamento
- Escala de resultado: PECAF 43-102; PEDHE 41-48 → **cortes do semáforo parametrizados por
  programa**
- Perguntas adaptadas por marca (mesma estrutura 0/1/2, 20 perguntas)

## Semáforo (proposta a aprovar na análise)

- 🟢 Verde: RANQUEADA = SIM
- 🟡 Amarelo: não ranqueada mas atinge os mínimos do regulamento para seu tempo de franquia
- 🔴 Vermelho: abaixo dos mínimos ou sem dados
- (alternativa: cortes por faixa de pontuação — a decidir pelo champion na análise)

## Dependências e pendências

- **Campo status de atividade na API do Portal** — será criado, mas não agora (farol nasce sem
  ele ou com cadastro provisório na intranet)
- **Carga inicial:** pontuações 2026 (32 PECAF + 9 PEDHE) como histórico da avaliação anual
- O farol respeita RLS por empresa (consultora vê só a sua) e RLS por perfil (leitura para
  todos autenticados; edição do formulário = champion/consultoria)

## Método

Evolução da fase 1 pelo mesmo padrão: sinal registrado → análise profunda → emenda de SPEC /
tasks novas aprovadas pelo champion → implementação com provas → teste humano. Nada implementado
antes da autorização.