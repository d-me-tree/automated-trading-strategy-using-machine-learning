# Engineering and evaluating predictive features

## Objective

Create new variables ("features") by transforming the original stock price data that will inform the return predictions made by the machine learning model.  

## Workflow

For this milestone, we will work with a subset of the Quandl Wiki data to create a range of features that capture information about future returns. In a second step, we will evaluate the quality of these features before using them as inputs to the machine learning models that we will build in the next milestone.

This requires the following steps:
1. Select the adjusted open, high, low, and close prices as well as the volume for all tickers from the Quandl Wiki data that you downloaded and simplified for the last milestone for the 2006-2016 time period. Looking ahead, we will use 2014-2016 as our 'out-of-sample' period to test the performance of a strategy based on a machine learning model selected using data from preceding periods. We're including some earlier years to ensure all indicators (some of which are computed over longer windows) have data for the period of interest.
2. Compute the dollar volume as the product of closing price and trading volume; then select the stocks with at least eight years of data and the lowest average daily rank for this metric.
    - We are simplifying here a little bit: in practice, you would want to identify the universe as a rolling average (e.g. on monthly or quarterly basis, depending on our train/test parameters in the next steps) of the dollar volume to avoid including information 'from the future' that could introduce a lookahead bias. Feel free to select the universe in this methodologically more robust yet computationally a bit more intensive way.    
3. Compute daily returns and keep only 'inliers' with values between -100% and + 100% as a basic check against data error.
4. Now we're ready to compute financial features. The Alpha Factory Library listed among the resources below illustrates how to compute a broad range of those using pandas and TA-Lib. We will list a few examples; feel free to explore and evaluate the various TA-Lib indicators.
    - Compute **historical returns** for various time ranges such as 1, 3, 5, 10, 21 trading days, as well as longer periods like 2, 3, 6 and 12 months.
    - Use TA-Lib's **Bollinger Band** indicator to create features that anticipate **mean-reversion**.
    - Select some indicators from TA-Lib's **momentum** indicators family such as
        - the Average Directional Movement Index (ADX), 
        - the Moving Average Convergence Divergence (MACD), 
        - the Relative Strength Index (RSI), 
        - the Balance of Power (BOP) indicator, or 
        - the Money Flow Index (MFI).
    - Compute TA-Lib **volume** indicators like On Balance Volume (OBV) or the Chaikin A/D Oscillator (ADOSC)
    - Create volatility metrics such as the Normalized Average True Range (NATR).
    - Compute rolling factor betas using the five Fama-French risk factors for different rolling windows of three and 12 months (see resources below).
    - *Optional*: the above technical indicators are a useful starting point because they are quite familiar to traders (and straightforward to compute). If you are already experienced in the trading domain, feel free to develop more sophisticated (and more promising) alternatives (see resources, e.g. the book by [Marcos Lopez de Prado](http://www.quantresearch.org/index.html) that summarizes fairly recent research in the area, some of which also available from the linked website.
    - Compute the outcome variable that we will aim to predict, namely the 1-day forward returns.
    - Evaluate the predictive content of your financial features with respect to the 1-day forward returns using several metrics, including:
        - the information coefficient (i.e., the Spearman rank correlation)
        - the mutual information
        - the LightGBM feature importance, computed by training a gradient boosting model with default settings on the first nine years of data 
        - SHAP values computed from the LightGBM model (see resources)
        - Alphalens quantile-based return spreads (see resources)
    - These different metrics will yield different and even conflicting answers. Take some time to think about why this is the case, and which approach(es) would likely be most effective when aiming to select the most predictive features?  

## Importance to project

If you are already familiar with ML, you may know that feature engineering is a key ingredient for successful model predictions. This is no different in finance and trading. Financial features aim to capture patterns in the data that contain information, or signal, about future price movements and returns.

## Resources

- [Alpha factor library](https://github.com/stefan-jansen/machine-learning-for-trading/blob/master/24_alpha_factor_library/02_common_alpha_factors.ipynb)
- [TA-Lib](https://mrjbq7.github.io/ta-lib/) documentation
- [statsmodels](https://www.statsmodels.org/dev/examples/notebooks/generated/rolling_ls.html) rolling regression with Fama-French data example 
- [Mutual information-based feature selection](https://thuijskens.github.io/2017/10/07/feature-selection/)
- scikit-learn's [mutual_info_regression](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.mutual_info_regression.html)
- [SHAP values repository](https://github.com/slundberg/shap)
- [Explain Your Model with the SHAP Values](https://towardsdatascience.com/explain-your-model-with-the-shap-values-bc36aac4de3d)
- [Alphlens docs](https://quantopian.github.io/alphalens/)
- [Advances in Financial Machine Learning](https://www.wiley.com/en-us/Advances+in+Financial+Machine+Learning-p-9781119482109), Marcos Lopez de Prado, 2018