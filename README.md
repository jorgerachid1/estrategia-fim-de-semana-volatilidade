# Indicador de Compra/Venda Baseado em Sazonalidade Semanal e Volatilidade (AAPL)

**Ativo analisado:** AAPL (Apple Inc., NASDAQ) · **Período:** 02/01/2015 a 29/11/2024 (~10 anos, 2.494 pregões diários)

Projeto de portfólio que testa estatisticamente o "efeito de fim de semana" em ações — a ideia de que investidores reduzem posições antes do fim de semana (vendendo na sexta e recomprando na segunda) por aversão à incerteza — e faz o backtest de estratégias que tentam se aproveitar (ou evitar) esse padrão, incluindo uma estratégia **contrária ao efeito manada** (ficar sempre comprado no gap do fim de semana) e uma estratégia que combina buy & hold com **opções**.

Feito por **Jorge Rachid**, estudante de Estatística (UFF), unindo formação quantitativa com experiência prática em mercado financeiro (assessoria de investimentos e mesa proprietária).

Continuação do projeto [`analise-estatistica-acoes`](https://github.com/jorgerachid1/analise-estatistica-acoes).

## Hipótese

Em períodos de muita incerteza, investidores tendem a evitar ficar posicionados durante o fim de semana. Como não há pregão no sábado/domingo, o retorno observado numa segunda-feira mede exatamente essa variação (fechamento de sexta → fechamento de segunda) — o "gap de fim de semana".

## O que o notebook responde

1. O retorno de segunda-feira é, em média, diferente dos demais dias da semana?
2. Esse efeito é mais forte em regimes de alta volatilidade? (medido pelo **VIX**, o índice de volatilidade implícita da CBOE — usado em vez de um corte arbitrário na volatilidade realizada da própria ação, por ser uma referência padronizada de mercado)
3. Uma estratégia que compra no fechamento de sexta e vende no fechamento de segunda supera o buy & hold?
4. Uma estratégia **contrária ao efeito manada** (ficar comprado no gap do fim de semana, sistematicamente, todo fim de semana — o oposto do que a maioria faz) funciona melhor em determinados níveis de VIX?
5. É possível combinar o buy & hold com uma proteção via **opções** especificamente para o risco do fim de semana?
6. Aplicando a mesma lógica ao **S&P 500** (via SPY): um Buy & Hold que só usa opções como "booster" (comprando uma put) nos fins de semana em que o VIX está muito alto — vale a pena?

## Como o VIX é usado

O VIX de cada sexta-feira (sem look-ahead) classifica o regime de volatilidade em 4 faixas, seguindo a leitura convencional de mercado: **Baixa** (<15), **Moderada** (15–20), **Alta** (20–30), **Muito alta** (>30).

## Principais achados (AAPL, 02/01/2015–29/11/2024 · ~10 anos, 2.494 pregões)

- Retorno médio de segunda-feira (+0,21%) maior que o dos demais dias (+0,06%), mas a diferença **não é estatisticamente significativa** mesmo com 10 anos de dados (t=1,484; p ≈ 0,138).
- Com o VIX como medida de volatilidade, também não há diferença estatisticamente significativa entre o retorno de segunda em regime de VIX≥20 vs. VIX<20 (t=0,753; p≈0,452). A quebra por faixa mostra um padrão **não linear**: o retorno médio do trade sexta→segunda foi mais forte nas faixas "Baixa" (VIX<15, +0,26% médio) e "Alta" (20-30, +0,37% médio), e mais fraco na faixa "Moderada" (15-20, +0,03%) — ou seja, "quanto maior o VIX, melhor" não é uma regra que se confirma nesta amostra.
- No backtest de 10 anos, o **Buy & Hold** teve o melhor retorno anualizado (25,9% a.a.) e o melhor Sharpe (0,81), puxado pela forte valorização da AAPL no período.
- A estratégia **"contra o efeito manada"** (comprado sistematicamente no gap sexta→segunda, todo fim de semana) rendeu menos em termos absolutos (10,3% a.a.), mas com volatilidade e drawdown muito menores (-22,0% vs. -38,5%) e Sharpe próximo do buy & hold (0,75) — um perfil de risco mais eficiente por dia exposto ao mercado.
- **A versão filtrada por VIX alto (só entra quando VIX≥20) teve desempenho pior, não melhor**, que a versão irrestrita (5,0% a.a. e Sharpe 0,48, contra 10,3% a.a. e Sharpe 0,75) — porque o filtro descarta a faixa "Baixa", que também contribuiu bastante, sem descartar a faixa "Moderada", que foi a mais fraca. Um bom lembrete de que filtros de volatilidade "óbvios" precisam ser testados, não assumidos.
- **Estratégia combinada (Buy & Hold + collar de opções no fim de semana):** fica sempre posicionada na ação (como o buy & hold), mas a cada sexta-feira monta um *collar* (compra put + vende call, ambas ~3% fora do dinheiro, expirando na segunda) para limitar o risco especificamente no gap do fim de semana. Resultado: 24,6% a.a. (vs. 25,9% do buy & hold puro), volatilidade um pouco menor (27,4% vs. 28,6%) e drawdown máximo um pouco menor (-38,1% vs. -38,5%) — mantém quase todo o retorno de longo prazo e reduz uma fatia real do risco de cauda, sem precisar sair do mercado. Os prêmios das opções foram **estimados via Black-Scholes** (não há dados reais de opções disponíveis neste ambiente) — ver aviso abaixo.
- Ver a seção "Leitura crítica" no notebook: com uma janela maior de dados, fica claro que ficar fora do mercado (mesmo que só nos fins de semana) tem custo de oportunidade alto quando o ativo sobe muito no longo prazo — o resultado do backtest não deve ser confundido com significância estatística confirmada.

### S&P 500 (via SPY): Buy & Hold + "booster" de put em VIX muito alto

Estratégia diferente da anterior: em vez de um collar toda semana, aqui a base é **Buy & Hold puro** do S&P 500, e a única intervenção é comprar uma **put** (sem vender call — upside não é limitado) nos fins de semana em que o VIX da sexta anterior estava acima de 30 ("Muito alta"). Resultado real (2015–2024):

| Estratégia | Retorno anualizado | Volatilidade anualizada | Sharpe | Max drawdown |
|---|---|---|---|---|
| S&P 500 — Buy & Hold | 11,7% a.a. | 17,8% | 0,62 | -34,1% |
| S&P 500 — Buy & Hold + booster de put (VIX>30) | 10,4% a.a. | 17,4% | 0,57 | -30,5% |

- O booster foi acionado em 27 dos 516 fins de semana do período (5,2%), concentrado sobretudo na crise de março-junho de 2020.
- Nesses 27 fins de semana, o retorno médio do S&P 500 puro foi -0,16%; com o booster, -0,04% — a put ajudou exatamente quando foi usada. Em 13 das 27 vezes (48%), a put pagou mais do que seu prêmio.
- No agregado dos 10 anos, o booster **custou retorno** (10,4% vs. 11,7% a.a.) — o prêmio pago nas vezes sem crash pesou mais que o ganho nas vezes com crash — mas **reduziu o drawdown máximo em ~3,6 pontos percentuais** (-30,5% vs. -34,1%), com boa parte do benefício vindo da proteção real durante o crash de março de 2020. Funciona como um seguro: custo pequeno e recorrente, benefício concentrado nos eventos de cauda.

**Aviso:** projeto educacional/estatístico com um único ativo e ~10 anos de dados, sem custos de transação, corretagem ou impostos. Os prêmios de opções são **teóricos** (modelo Black-Scholes com volatilidade realizada como proxy), não preços de mercado realmente negociados. O VIX mede a volatilidade esperada do S&P 500, não da AAPL especificamente. Não é recomendação de investimento.

## Arquivos

- `estrategia_fim_de_semana_volatilidade.ipynb` — notebook com a análise completa
- `aapl_precos_historicos.csv` — dados históricos diários (OHLCV) da AAPL
- `sp500_precos_historicos.csv` — dados históricos diários (OHLCV) do S&P 500 (via SPY)
- `vix_historico.csv` — histórico diário do índice VIX (CBOE)
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
- Incluir custos de transação e prêmios de opções reais no backtest.

## Tecnologias

Python · pandas · numpy · matplotlib · scipy (teste t, estatística, Black-Scholes) · Jupyter Notebook
