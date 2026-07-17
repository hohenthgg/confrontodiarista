# Confronto de Diaristas — Fonte A × Fonte B

Aplicação **estática de arquivo único** (`index.html`) que confronta o **previsto (Fonte A)** contra o **realizado (Fonte B)** de diaristas, por **período**, na unidade fundamental **data × operação × filial × solicitante**, comparando **MELI** e **ID Logistics** separadamente. Roda 100% no navegador — nenhum dado sai da máquina — e publica direto no GitHub Pages.

## Fontes aceitas

Ambas as fontes aceitam **texto colado, TXT, CSV, XLS e XLSX**:

- **Fonte A (previsto):** os "📊 Resumo do dia — dd/mm" do WhatsApp (vários dias de uma vez) **ou** uma base tabular no mesmo padrão da Fonte B.
- **Fonte B (realizado):** planilha com uma aba por filial (Pouso SVC, Pouso XD, Poços, Varginha, Divinópolis, Patos), colunas `DATA · SOLICITANTE · EMPRESA DIARISTA · GROOT ID · NOME · CARGO · ESCALA`.

## Modelo de dados normalizado

Toda fonte tabular é convertida para um registro único e todas as visualizações consomem essa estrutura:

```js
{ data:"2026-07-01", filial:"SMG3 - Pouso Alegre", filialKey:"SMG3",
  operacao:"SVC", turno:"AM", solicitante:"MELI", empresaDiarista:"MOURA",
  grootId:"2463312", nome:"Douglas David da Silva", cargo:"Diarista",
  origem:"Fonte B", arquivo:"Diaristas - Julho v1.xlsx", aba:"Pouso SVC", linha:25 }
```

**Filial, operação e turno são dimensões independentes.** Filiais são unidades geográficas (SMG3, SMG5, SMG9, SMG10, SMG11); operações são `SVC / XD / SD / Full`; turnos (AM, PM, faixas de horário, sábado, domingo) nunca são tratados como operação.

## Principais recursos

- **Parser de resumo robusto** por fila de cabeçalhos pendentes: blocos fecham na linha `TOTAL`, cabeçalhos consecutivos (ex.: 02/07 e 03/07) recebem o 1º e o 2º bloco corretamente, e duplicatas idênticas (ex.: 16/07 colado duas vezes) são ignoradas sem contagem em dobro. Valores divergentes na mesma combinação viram **conflito sinalizado**, nunca soma silenciosa.
- **Período por datas ISO** (inicial/final) com atalhos (dia, 1ª/2ª quinzena, mês, todo o período detectado). Todos os dias do intervalo aparecem, mesmo sem dados em uma ou nas duas fontes.
- **Classificação centralizada** de operação (`detectOperation`) e turno (`detectShift`) por prioridade explícita, com mapeamento aba→filial×operação **corrigível manualmente** na tela de auditoria.
- **Tela de auditoria da importação** com diagnóstico por fonte — cada número é clicável e abre os registros.
- **A × B por dia**: MELI/ID/Total com Gap = B − A, status textual (Conferido, Excedente, Déficit, Sem Fonte A/B, Sem dados, Operação não classificada, Conflito de importação) e drill-down operação → filial → nomes.
- **Acumulado** por operação/filial/solicitante e **comparação nominal** por GROOT ID (presente em ambas, só A, só B, duplicado, mesmo ID com nomes diferentes, etc.).
- **Três modos de contagem**: linhas brutas, pessoas únicas por GROOT ID, saneada (sem duplicidade exata).
- **Inteligência financeira** com custo por solicitante/operação/dia da semana e adicional noturno. Exposição calculada **célula a célula** (`max(B − A, 0) × custo`) — excessos e déficits **nunca se compensam** entre dias, operações, filiais ou solicitantes. Déficit é tratado como *déficit de cobertura*, não economia.
- **Storytelling executivo** (o fato / por que importa / decisão recomendada) baseado nos números calculados.
- **Filtros** independentes e **exportações**: CSV (UTF-8 com BOM, escape correto), Excel com múltiplas abas e resumos copiáveis.
- **Segurança**: todo valor vindo de planilha/texto passa por `escapeHtml()` antes de ir ao DOM.
- **Persistência**: salvar/limpar sessão no navegador e exportar/importar sessão em JSON.

## Estrutura

| Arquivo      | Função                                              |
|--------------|-----------------------------------------------------|
| `index.html` | Aplicação completa (HTML + CSS + JS, sem backend)   |
| `README.md`  | Esta documentação                                   |
| `CNAME`      | Domínio do GitHub Pages (`confrontodiaristas.com`)  |

## Publicação no GitHub Pages

1. Faça merge desta branch na `main` (o `index.html` fica na raiz).
2. Em **Settings → Pages**, mantenha *Source: Deploy from a branch*, branch `main`, pasta `/ (root)`.
3. O `CNAME` já aponta para `confrontodiaristas.com`; após o deploy o painel fica disponível nesse domínio.

Como é 100% estático e sem dependências de servidor, também funciona abrindo o `index.html` diretamente no navegador.

## Limitações conhecidas

- O resumo da Fonte A não possui nomes/GROOT ID — a comparação nominal usa a Fonte B (e a Fonte A apenas quando ela é uma base tabular).
- Quando a operação de um resumo não vem no escopo do cabeçalho `(SVC)`, ela fica como "Não classificado" até ajuste.
- A biblioteca de leitura de planilhas (SheetJS) é carregada via CDN; sem internet, use CSV/TXT ou baixe a lib localmente.
- Custos são parametrizáveis por solicitante/operação/dia; regras por empresa fornecedora específica usam os mesmos campos base.
