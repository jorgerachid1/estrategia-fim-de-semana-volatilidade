# Indicador de Compra/Venda Baseado em Sazonalidade Semanal e Volatilidade (AAPL)

Projeto de portfólio que testa estatisticamente o "efeito de fim de semana" em ações — a ideia de que investidores reduzem posições antes do fim de semana (vendendo na sexta e recomprando na segunda) por aversão à incerteza — e faz o backtest de estratégias simples que tentam se aproveitar (ou evitar) esse padrão.

Feito por **Jorge Rachid**, estudante de Estatística (UFF), unindo formação quantitativa com experiência prática em mercado financeiro (assessoria de investimentos e mesa proprietária).

Continuação do projeto [`analise-estatistica-acoes`](https://github.com/jorgerachid1/analise-estatistica-acoes).

## Hipótese

Em períodos de muita incerteza, investidores tendem a evitar ficar posicionados durante o fim de semana. Como não há pregão no sábado/domingo, o retorno observado numa segunda-feira mede exatamente essa variação (fechamento de sexta → fechamento de segunda) — o "gap de fim de semana".

## O que o notebook responde

1. O retorno de segunda-feira é, em média, diferente dos demais dias da semana?
2. Esse efeito é mais forte em semanas de alta volatilidade?
3. Uma estratégia que compra no fechamento de sexta e vende no fechamento de segunda supera o buy & hold?
4. Imitar o comportamento de manada (evitar ficar posicionado no fim de semana) funciona?

## Principais achados (AAPL, 17/02/2015–17/02/2017)

- Retorno médio de segunda-feira (+0,21%) maior que o dos demais dias (-0,02%), mas a diferença **não é estatisticamente significativa** (p ≈ 0,14) — sinal na direção esperada, amostra pequena para confirmar.
- Sem diferença significativa entre regimes de alta e baixa volatilidade para o retorno de segunda (p ≈ 0,82) nesta amostra.
- No backtest, a estratégia "compra sexta / vende segunda" teve retorno anualizado maior e volatilidade/drawdown muito menores que o buy & hold (melhor Sharpe). A estratégia que imita o comportamento de manada teve o pior desempenho, com retorno negativo.
- Ver a seção "Leitura crítica" no notebook: o resultado do backtest não deve ser confundido com significância estatística confirmada, e uma parte do ganho pode vir simplesmente de menor exposição ao mercado.

**Aviso:** projeto educacional/estatístico com um único ativo e ~2 anos de dados, sem custos de transação. Não é recomendação de investimento.

## Arquivos

- `estrategia_fim_de_semana_volatilidade.ipynb` — notebook com a análise completa
- `aapl_precos_historicos.csv` — dados históricos diários (OHLCV) da AAPL
- `requirements.txt`

## Como rodar

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook estrategia_fim_de_semana_volatilidade.ipynb
```

## Próximos passos

- Repetir a análise numa cesta de ações do Ibovespa/B3 e num período mais longo.
- Testar outros padrões de calendário (virada de mês, véspera de feriado).
- Incluir custos de transação realistas no backtest.

## Tecnologias

Python · pandas · numpy · matplotlib · scipy (teste t, estatística) · Jupyter Notebook
