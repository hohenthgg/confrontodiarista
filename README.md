# Confronto de Diaristas — Período · Fonte A × Fonte B

Painel web (arquivo único, roda 100% no navegador) para confrontar duas bases de diaristas **por filial, dia a dia**:

- **Fonte A — Resumos do dia (previsto):** cole o texto dos "📊 Resumo do dia — dd/mm" do WhatsApp, de vários dias de uma vez. O app extrai por filial: volume previsto, HC (base S&OP), HC (produtividade × volume), %ABS e diaristas MELI/ID.
- **Fonte B — Base efetiva (solicitações):** envie a planilha `.xlsx` no modelo padrão — cada aba é uma filial (Pouso SVC, Pouso XD, Poços, Varginha, Divinópolis, Patos) com as colunas `DATA SOLICITAÇÃO · SOLICITANTE · EMPRESA DIARISTA · GROOT ID · NOME · CARGO · ESCALA`.

## O que o painel entrega

- **Matriz dia a dia × filial** com cada célula em formato `A→B` (verde = bate, vermelho = Fonte B acima do previsto/custo adicional, laranja = abaixo do previsto), alternando entre MELI e ID Logistics. Clique no dia para abrir o detalhe (volume, HC, %ABS, deltas e nomes da Fonte B).
- **Consolidado do período por filial** e KPIs (totais MELI/ID A→B, diárias excedentes, diárias previstas sem registro).
- **Alertas de custo adicional** por dia/filial.
- **Irregularidades e insights automáticos:** GROOT ID duplicado no mesmo dia (inclusive mesmo ID com nomes diferentes), registros sem GROOT ID ou com ID fora do padrão, solicitante em branco, dias previstos sem registro na base, dias na base sem resumo, dias/blocos duplicados no texto colado, linha TOTAL que não fecha com a soma dos sites, diaristas mais recorrentes e registros fora do período.
- **Exportação CSV** do confronto completo.

## Estrutura

| Arquivo      | Função                                              |
|--------------|-----------------------------------------------------|
| `index.html` | Aplicação completa (HTML + CSS + JS, sem backend)   |
| `CNAME`      | Domínio do GitHub Pages (`confrontodiaristas.com`)  |

## Observações

- As datas são casadas por **dia/mês**; registros de meses fora do período da Fonte A são ignorados (e informados nos insights).
- As chaves dos `{GROOT ID}` e prefixos numéricos nos nomes (`1. Fulano`) são limpos automaticamente.
- Nenhum dado sai do navegador.
