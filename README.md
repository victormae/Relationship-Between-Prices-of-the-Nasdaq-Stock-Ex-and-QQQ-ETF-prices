QQQ ETF & Nasdaq 100 Stock Predictor 

Overview

In the ever-evolving landscape of financial markets, understanding the relationships between individual stocks and ETFs is crucial for making informed decisions. This project investigates the relationships between the stock prices of the top 10 and bottom 10 companies on the Nasdaq 100 index and the corresponding price of the QQQ ETF - and builds machine learning models to predict and classify those relationships.
The core questions driving this project:
Do individual Nasdaq 100 stocks have a predictable relationship with the QQQ ETF price?
•	Can we build a model that predicts QQQ price movements based on those stocks?
•	Which stocks rise and fall with the QQQ, and which ones move against it?
___________________________________________________________________________________
Data
Historical stock price data was sourced using the Alpaca API, covering a significant time span to capture a wide range of market conditions:
•	Start Date: January 1, 2012
•	End Date: November 1, 2023
Stocks analyzed include the top 10 and bottom 10 companies listed on the Nasdaq 100 index: Apple (AAPL), Amazon (AMZN), Meta (META), Alphabet (GOOGL), Tesla (TSLA), Adobe (ADBE), ANSYS (ANSS), eBay (EBAY), Walgreens Boots Alliance (WBA), Sirius XM (SIRI), Illumina (ILMN), Zoom (ZM), Align Technology (ALGN), JD.com (JD), VanEck Semiconductor ETF (SMH), iShares 20+ Year Treasury Bond ETF (TLT), iShares Ultra Short-Term Bond ETF (SGOV), and Lucid Group (LCID).
__________________________________________________________________________________
Methodology
Correlation Analysis
Initial Pearson correlation analysis was run between each stock and the QQQ close price to understand the directional relationships. Key findings:
•	Positively correlated with QQQ: META, GOOGL, AMZN, ZM, TLT, SGOV, EBAY
•	Negatively correlated with QQQ: AAPL, WBA, SIRI, LCID
Point Biserial Correlation
Biserial correlation was used to examine the relationship between each stock and QQQ movement direction (up or down) over 55 and 60-day percentage change windows -essentially asking: when a stock moves, does the QQQ move with it?

Regression Models
1. OLS Linear Regression The initial regression model produced high R-squared values on the training data -suspiciously high. Comparing RMSE and MAE between train and test sets confirmed overfitting. The model learned the training data too well and performed worse on unseen data.
2. Lasso Regression Lasso was applied for feature selection and regularization to penalize the weights and reduce overfitting. Grid search identified an optimal alpha of 0.004. While Lasso improved on OLS, it didn't totally eliminate overfitting, and R-squared values on both train and test sets remained low (Train: 0.43, Test: -0.54) - indicating a weak regression model overall.
3. PCA + Linear Regression PCA was used to transform the data and address multicollinearity before running another regression. VIF confirmed no multicollinearity in the transformed data. The first 5 principal components explained ~86% of the variance. While the R-squared remained low, the gap between cross-validation RMSE and model RMSE was small - suggesting this model generalized better than Lasso despite the low R-squared.
Model	RMSE	R-squared
OLS (Train)	0.038	High (overfit)
Lasso (Test)	 0.0949	-0.54
PCA + Linear Reg	0.109	-1.03
Cross-Val RMSE	0.092

Classification Models
Since regression struggled to pin down the exact price, I pivoted to classification - predicting the direction of movement (up or down) rather than the exact price.
KNN Classifier (n=3, weights='distance') Performed well with high accuracy and precision. Strong potential for classifying whether a new trade instance will go up (1) or down (0) with the QQQ.
AdaBoost Classifier AdaBoost was the star of the show - achieving perfect accuracy and precision on the test set. When predicted vs. actual labels are plotted on the time series, the model fits the test data almost perfectly. It cleanly identifies which stocks will rise or fall with the QQQ as it moves.
___________________________________________________________________________________
Key Findings
•	Regression models struggled to predict the exact QQQ price from individual stock prices: overfitting was a recurring challenge across all three regression approaches.
•	Classification flipped the script. AdaBoost nailed it - perfectly classifying stock movement direction relative to the QQQ on the test set.
•	The KNN classifier also showed strong performance and could serve as a solid secondary model for identifying stocks that move in tandem with the QQQ.
•	Stocks like META, GOOGL, ZM, and TLT consistently showed positive directional alignment with the QQQ, while AAPL, WBA, and SIRI moved against it.
____________________________________________________________________________________
Tech Stack
•	Python
•	Alpaca API (data collection)
•	Pandas, NumPy
•	Scikit-learn (OLS, Lasso, KNN, AdaBoost, PCA, Grid Search)
•	Statsmodels (OLS, VIF)
•	Matplotlib, Seaborn
•	Jupyter Lab
___________________________________________________________________________________
Future Directions
The AdaBoost classifier opens the door to a practical trading signal tool, flagging which stocks are likely to rise or fall with the QQQ at any given time. With more feature engineering and potentially
incorporating macroeconomic indicators, this could evolve into a robust signal layer for a broader trading strategy. I'd also be curious to test this on more recent data post-2023 to see how the model holds up in current market conditions.
___________________________________________________________________________________
Data sourced via the Alpaca API.
Data Collection
The script used to pull the stock data from the Alpaca API is included in the repo. You can pull your own data by adding your Alpaca credentials to a .env file in the following format:
ALPACA_API_KEY=your_api_key
ALPACA_SECRET_KEY=your_secret_key

You can get your credentials by creating an account on the Alpaca Developer Dashboard.

