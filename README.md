# [StockGram : An Intelligent Portfolio Manager](https://aircconline.com/ijaia/V11N6/11620ijaia02.pdf)

## Research Paper

Please find my research paper on [Intelligent Portfolio Management via NLP Analysis of Financial 10-K Statements](https://aircconline.com/ijaia/V11N6/11620ijaia02.pdf), published in the November issue of [International Journal of Artificial Intelligence and Applications](http://www.airccse.org/journal/ijaia/ijaia.html)


## Overview
The project attempts to analyze if the sentiment stability of financial 10-K reports over time can determine
the company’s future mean returns. A diverse portfolio of stocks was selected to test this hypothesis. The
proposed framework downloads 10-K reports of the companies from SEC’s EDGAR database. It passes
them through the preprocessing pipeline to extract critical sections of the filings to perform NLP analysis.
Using [Loughran and McDonald](https://github.com/purvasingh96/StockGram-Intelligent-Portfolio-Manager/blob/main/code/LoughranMcDonald_MasterDictionary_2018.csv) sentiment word list, the framework generates sentiment TF-IDF from
the 10-K documents to calculate the cosine similarity between two consecutive 10-K reports and proposes
to leverage this cosine similarity as the alpha factor. For analyzing the effectiveness of our alpha factor
at predicting future returns, the framework uses the alphalens library to perform factor return analysis,
turnover analysis, and for comparing the Sharpe ratio of potential alpha factors. The results show that
there exists a strong correlation between the sentiment stability of our portfolio’s 10-K statements and its
future mean returns.

## Quandl Dataset

[Quandl end of day US Stock Prices database, Accessed: 2020-10](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices)

### How to use Quandl data?

```python
!pip install quandl

import quandl

quandl.ApiConfig.api_key = "YOURAPIKEY"

data = quandl.get(['EOD/AMZN', 'EOD/NKE'])

data.head()

```
## Portfolio

We test our hypothesis: *Sentiment stability of financial 10-K report can be a potential trading signal*, on a diverse portfolio of 7 stocks as below:<br>
<img src="./images/1. portfolio table.png" width="70%" height="50%"></img>  

## The SEC EDGAR Database
In order to extract financial 10-K reports of the stocks in our universe, we leverage a pre-defined SEC API and the CIK number of the stock. Details on how to extract the 10-K report from SEC EDGAR database and pre-process it can be found in [this notebook](https://github.com/purvasingh96/StockGram-Intelligent-Portfolio-Manager/blob/main/code/extracting-financial-10-k-reports-via-sec-edgar-db.ipynb).  

## Loughran McDonald Sentiment Word List
Loughran McDonald word lists contains 6 different sentiments (negative, positive, uncertainty, litigious, strong modal, and weak modal) which are curated by examining word usage in at least 5% of 10-Ks (i.e., annual reports) during 1994-2008. It is a relatively exhaustive list of words that makes avoidance much more challenging. The sentiment lists are based on the most likely interpretation of a word in a business context. The Loughran and McDonald (LM) word lists are quite extensive: it contains 354 positive and 2,329 negative words. You can find the csv version used in this project [here](https://github.com/purvasingh96/StockGram-Intelligent-Portfolio-Manager/blob/main/code/LoughranMcDonald_MasterDictionary_2018.csv)

## Code

You can find the PyTorch implementation of the framework [here](https://github.com/purvasingh96/StockGram-Intelligent-Portfolio-Manager/tree/main/code)

## Evaluation and Results

### Factor Returns

Factor returns are a way to directly measure the returns of our portfolio if their weights were
determined purely by the alpha factor. Alphalens requires two mandatory arguments to
predict future mean returns: factors and prices. In this project, we consider cosine
similarity between two consecutive 10-K reports as factor data and year-end adjusted closing prices of the stocks in our portfolio as
pricing data to run against our factor data.<br><br>

After generating the factor data frame and setting the pricing data, we pass both the arguments
in the alphalens’ method called get_clean_factor_and_forward_returns, which accepts factor
data, pricing data, quantiles, bins, and periods. This function generates a multi-indexed
merged data frame that is indexed by date at level 0 and followed by stock/asset at level 1. This
data frame contains the values for a single alpha factor, forward returns for each period, and
quantile/bin in which the signal belongs.<br><br>

<img src="./images/factor_return_analysis.png" width="80%" height="60%"></img><br><br>

Figure 1 shows a plot between factor returns and time. As we can see from the graph, 10-K
financial reports expressing the sentiment interesting and positive, yield the maximum returns.
On the other hand, the forms that convey constraining , negative , and litigious resulted in the
lowest returns. The following observation aligns with our hypothesis that performing NLP
analysis on financial 10-K statements could predict future mean returns.

### Turnover Analysis

Since liquidity and transaction costs are dependent upon market conditions at the time of the
trade, it is challenging to simulate actual transaction costs when evaluating an Alpha factor. So a
useful proxy for these real-world constraints is to measure the turnover. The turnover
analysis estimates the fraction of the portfolio's total value getting traded in a period. One of
the ways to measure turnover is factor rank autocorrelation . Factor rank autocorrelation is a
way to measure how stable are the ranked alpha factors. In this case, stability refers to the fact that alpha ranks do not change much from period to period. Since trading is costly, we would
always prefer other factors to be the same, i.e., the alpha factor’s ranks do not change
significantly per period. A high factor rank autocorrelation is an indication that the turnover
is lower. A low or even a negative autocorrelation is a proxy to indicate a higher turnover. If
two alpha factors have similar quintile performance and similar factor returns, we would prefer
the one with lower turnover.<br><br>

<img src="./images/factor_rank_autocorrelation.png" width="80%" height="60%"></img><br><br>

The reason for choosing alpha factor with lower turnover is that it makes it possible for us to
execute trades if we have liquid stocks and reduce transaction costs. Excessive turnover could
imply that our Alpha factor is only catching noise.

### Sharpe Ratio
The Sharpe Ratio or risk-adjusted return is a critical metric in evaluating alpha factors. It is the
measure of excess portfolio return over the risk-free rate relative to its standard deviation.
Sharpe ratio helps us to compare the relative performance of alpha factors. One important thing
to note is that the Sharpe ratio is the key and not the magnitude of factor returns. Table 1 shows the Sharpe ratio of our alpha factors. <br>

Usually, a ratio under 1.0 is considered sub-optimal. Sharpe ratio greater than 1.0 is acceptable to good by investors. A Sharpe ratio
higher than 2.0 is good, and investors deem a 3.0 or higher Sharpe ratio excellent.
Looking at the Sharpe ratio of our Alpha factor, we can see that the 10-K filing reports that
convey the sentiment interesting have the highest Sharpe ratio of 4.10, followed by the 10-K
documents that express a positive view with a Sharpe ratio of 1.02.<br><br>

<img src="./images/sharpe_ratio.png" width="40%" height="60%"></img><br><br>

## Contributor
- [Purva Singh](https://purvasingh96.github.io)

## Contributing

Please feel free to open a Pull Request to contribute towards this repository. Also, if you think there's any section that requires more/better explanation, please use the issue tracker to let me know about the same.

## Support 

If you like this repo and find it useful, please consider (★) starring it (on top right of the page) so that it can reach a broader audience.



