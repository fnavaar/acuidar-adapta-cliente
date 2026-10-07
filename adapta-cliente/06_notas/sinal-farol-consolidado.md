# Sinal — Farol consolidado (tela única por unidade)

**Registrado em:** 2026-10-06 13:39 · **Origem:** Champion (Luis Carlos), conversa ETHOS (com print do menu da intranet)
**Status:** sinal completo — entendimento confirmado e autorização dada (13:42)

## O que o champion pediu (texto dele)

> "que que você utilize a interface, o frontend do projeto skip NEXUS CONSULTORIA e quero que
> reuna as informações que estão em todas essas abas, menos o de registrar reunião, e coloque
> num só farol das unidades. eu quero que você use de maneira esperta o frontend de um projeto
> que já existe e que você me coloque essas informações de forma que o usuário possa mexer nelas."

Print anexado: menu da intranet com as abas **Farol das unidades · Avaliação PECAF/PEDHE ·
Registrar reunião · Fila de ocorrências · Painel de cobertura · Importar da Agenda**.

## Entendimento confirmado pelo champion (13:42)

- A aba **Farol das Unidades** vira a **tela única e central**: por unidade, reunir o que hoje
  está espalhado em 5 abas — mapa de acompanhamento + status de atividade (Farol), avaliação
  PECAF/PEDHE editável, ocorrências da unidade (com as ações da fila conforme perfil),
  cobertura mensal (painel) e reuniões (histórico + programadas da agenda).
- **"Registrar reunião" fica fora** da consolidação (continua aba própria).
- **"Usar de maneira esperta o frontend que já existe"** = reutilizar os hooks e componentes
  existentes (farol, avaliacoes, transicao, painel, unidades_info) — nada refeito do zero.
- **"Mexer nelas"** = editar onde a regra já permite: status de atividade e avaliação
  (gestor/admin), ações de ocorrência conforme perfil da fila; cobertura e reuniões são leitura.

## Decisões fechadas pelo champion (2026-10-06 13:42, formulário)

1. **Entendimento confirmado:** "Sim, é isso".
2. **Tabela de conferência (carga 2026):** champion quer CONFERIR a tabela antes — os 7
   vínculos especiais nome↔código serão exibidos para aprovação; a carga só roda depois.
3. **Cliente oculto 2027+:** ADICIONAR campo no formulário de avaliação (0-20), como nos PDFs.
4. **q1..q20 da carga 2026:** gravar SOMA CONSOLIDADA (sem respostas individuais) — o semáforo
   não usa as perguntas, só resultado/ranqueada/mínimos.
5. **Autorização:** "Pode implementar o farol consolidado" — FA-3 + FA-4 unificados, uma task
   por vez com provas e teste humano.

## Encaixe no plano FAROL-1

- Consolida **FA-3 (semáforo + carga 2026) + FA-4 (detalhe da unidade)** unificados em uma tela.
- Análise da FA-3 concluída (06_notas/analise-fa3-semaforo-carga.md).
