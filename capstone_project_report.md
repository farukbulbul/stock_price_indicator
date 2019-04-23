# Machine Learning Engineer Nanodegree
## Capstone Project: Stock Price Indicator
Ömer Faruk BÜLBÜL  
June 22nd, 2019

## I. Definition

### Project Overview

Investment firms, hedge funds and even individuals have been using financial models to better understand market behaviour and make profitable investments and trades. Since investor behaviour often deviates from logic and reason, and investors display many behaviour biases that influence their investment decision-making processes[1], it is important to construct an investment strategy in terms of strict algorithmic rules. 

Financial modeling is the task of building an abstract representation (a model) of a real world financial situation.[2] This is a mathematical model designed to represent (a simplified version of) the performance of a financial asset or portfolio of a business, project, or any other investment. Realistic financial models which require great effort are than used by financial analysts to anticipate the impact of an economic policy change or any other event on a company's stock to predict the future price of the stock. In quantitative finance, financial modeling entails the development of a sophisticated mathematical model. Models here deal with asset prices, market movements, portfolio returns and the like.

There are so many factors involved in the prediction – physical factors vs. physhological, rational and irrational behaviour, etc. All these aspects combine to make share prices volatile and very difficult to predict with a high degree of accuracy. Since finance area dynamics are very complex and stock prices depend on so many factors, machine learning can be a good solution if a suitable subset of these factors is selected and a reasonable performance is targeted. A wealth of information is available in the form of historical stock prices and company performance data, suitable for machine learning algorithms to process for constructing a financial model predicting the stock price of companies. There are lots of websites and web services for gathering historical stock price and financial performance data including Yahoo Finance, Google Finance etc. 

There are many successful implementations to make an estimator for stock prices using machine learning methods like linear regression, svm, lstm, arima etc. one of which creates a framework with neural networks and decision forests.[3] In their "A machine learning based stock trading framework using technical and economic analysis" work they have managed to beat S&P500 Index by far according to charts provided.

Hedge fund research firm Eurekahedge has published some informative data. The chart below displays the performance of the Eurekahedge AI/Machine Learning Hedge Fund Index vs. traditional quant and hedge funds from 2010 to 2016. The Index tracks 23 funds in total, of which 12 continue to be live.

<img src="https://qph.fs.quoracdn.net/main-qimg-41c3e97fa1fb39c693d6970ea612c8bd" />

With following table provided, Eurekahedge notes that:

“AI/machine learning hedge funds have outperformed both traditional quants and the average hedge fund since 2010, delivering annualized returns of 8.44% over this period compared with 2.62%, 1.62% and 4.27% for CTA’s, trend-followers and the average global hedge fund respectively.”
Performance in numbers – AI/Machine Learning Hedge Fund Index vs. quants and traditional hedge funds

<img src="https://qph.fs.quoracdn.net/main-qimg-5f73a8506b883839c392403cb592f657-c" />

With the information provided by Eurekahedge it is clear that machine learning algorithms are very usefull for constructing successfull investment strategies. Similarly I would like to implement a stock price indicator with machine learning methodologies aiming to predict future stock price for a given stock. 


### Problem Statement

The problem is predicting the actual value of adjusted close price of a requested stock for a requested day with 5-10 days of previous data in the form of average values provided.

For this project, I will try to build a stock price predictor that takes daily trading data over 5 or 10 days as input of requested stock, and outputs projected estimates for given query dates. The predictor given open price(open), highest price(highest), volume, adjusted close price(adjusted close) will only predict Adjusted Close price for a given stock. The predicted value can easily be compared and a benchmark can easily be set up with actual adjusted price of the requested date for various dates upon request.

Since we are trying to predict a value this problem is a regression problem. We take historical stock market values and try to predict adjusted close price of the stock. We will be constructing several predictive models which investigates the relationship between a dependent (target) and independent variable (s) (predictor). We will try to forecast, time series modeling and finding the causal effect relationship between the variables.

### Metrics

I would like to use R2 and RMSE(root mean squared error) for evaluation metrics which are frequently used for estimation error calculations.
With the following formulas:

<img src="https://veribilimcisi.files.wordpress.com/2017/07/83buy.png" width="50%" height="50%"/>

<img src="https://cdn-images-1.medium.com/max/800/1*d7IVANCDovpXKP5N0rR2Yg.png" width="50%" height="50%"/>

We will look at the deviation by calculating the RMSE of the model. If the deviation is big RMSE score will be far away from 0. Simply put, the lower the value the better and 0 means the model is perfect. Since we do not want high deviation for our predictions we would like to have RMSE score near to 0.

For a regression with an intercept, 𝑅2 is between 0 and 1, and from its definition 𝑅2=1−𝑆𝑆𝐸/𝑇𝑆𝑆 we can find an interpretation: 𝑆𝑆𝐸/𝑇𝑆𝑆 is the sum of squared errors divided by the total sum of squares, so it is the fraction ot the total sum of squares that is contained in the error term. So one minus this is the fraction of the total sum of squares that is not in the error, or 𝑅2 is the fraction of the total sum of squares that is 'explained by' the regression.


## II. Analysis
_(approx. 2-4 pages)_

### Data Exploration

I used 13 different stocks' historical stock prices saved to "data" folder of this project. Each file is in the csv format and containing approximately 9 years of historical stock market data. Data saved are from Yahoo Finance website with python code and open to public use. The symbols of the stocks in alphabetical order is: AAPL, AMZN, AVGO, CSCO, MA, MSFT, NVDA, NVS, PFE, QCOM, TXN, V, WNT. Historical Data of stock prices can be easily gathered using https://pypi.org/project/yahoo-finance/ project or pandas data reader commented in first code cell of the project.

In the csv files columns are: 
1- Date: The date which market was open and stock is exchanged.
2- Open: The price which first transaction of the day was occured.
3- High: Highest price of the day
4- Low: Lowest price of the day
5- Close: The price which last transaction occured.
6- Adjusted Close: The adjusted closing price is a useful tool when examining historical returns because it gives analysts an accurate representation of the firm's equity value beyond the simple market price. It accounts for all corporate actions such as stock splits, dividends/distributions and rights offerings.
7- Volume: The number of shares that changed hands during a given day.  

In order to have good predictions we need to get trend of the stock market. If close, adjusted close price of a stock is greater than open price it clearly shows a bullish market rather than a bearish one. So relations between open price and close price will be very important for predicting the requested days adjusted close price. Also volume is very important since volume reflects the intensity (strength) of a stock. Volume also provides an indication of the quality of a price trend and the liquidity of a stock. Highest price and lowest price values when compared to open price and close price are used to interpret trend of the stock by financers.
A trend analysis can be done with volume and price like in the below table [4].
General Rules in Volume Analysis:

<table align="center"> 
  <th>Volume</th><th>Price</th><th>Interpretation</th>
  <tr><td>Increasing</td><td>Rising</td><td>bullish</td></tr>
  <tr><td>Decreasing</td><td>Falling</td><td>bullish</td></tr>
  <tr><td>Increasing</td><td>Falling</td><td>bearish</td></tr>
  <tr><td>Decreasing</td><td>Rising</td><td>bearish</td></tr>
</table>

Here is an example of data structure:

<table>
      <thead>
        <tr>
            <th>Date</th>
            <th>Open</th>
            <th>High</th>
            <th>Low</th>
            <th>Close</th>
            <th>Adj Close</th>
            <th>Volume</th>
        </tr>
      </thead>
      <tbody>
          <tr>
              <td>2019-01-28</td>
              <td>155.789993</td>
              <td>156.330002</td>
              <td>153.660004</td>
              <td>156.300003</td>
              <td>155.632523</td>
              <td>26192100</td>
          </tr>
        <tr>
              <td>2019-01-29</td>
              <td>156.250000</td>
              <td>158.130005</td>
              <td>154.110001</td>
              <td>154.679993</td>
              <td>154.019440</td>
              <td>41587200</td>
          </tr>
          <tr>
              <td>2019-01-30</td>
              <td>163.250000</td>
              <td>166.149994</td>
              <td>160.229996</td>
              <td>165.250000</td>
              <td>164.544296</td>
              <td>61109800</td>
          </tr>
  </tbody>
</table>

Typically every dataframe(stock historical file) have more than 2000 rows. In cell 3 I checked if a null value was existing in the data but i could not found any, so I did not need to do anything to cover null values.
<img src="https://github.com/farukbulbul/stock_price_indicator/blob/master/amzn_historicalPrice_statistics.png">

### Exploratory Visualization
In cell 4 I added frequetly used trend analysis formulas to the data as additional columns in order to have better results and in cell 5 I plotted original adjusted close price, bollinger bands and weighted moving average calculated. We can consider bollinder bands as standard deviation margins from current price of the stock. Also moving averages provide trend information for the data.

<img src="https://github.com/farukbulbul/stock_price_indicator/blob/master/microsoft_plot.png"/>

### Algorithms and Techniques

So we have timeseries data that we would like to have a regression and predict adjusted close price of the next market day.
So we have lots of methodologies we could apply to the problem. Namely, we can apply simple linear regression, support vector machines regression algorithms, logistic regression, multiple regression algortihm, long short term memory(LSTM) regression etc.

For the project I chose following techniques to tackle the problem:

1- SVM Linear/Polynomial Regression: 
This supervised machine learning algorithm has strong regularization and can be leveraged both for classification or regression challenges. They are characterized by usage of kernels, the sparseness of the solution and the capacity control gained by acting on the margin, or on number of support vectors, etc. The capacity of the system is controlled by parameters that do not depend on the dimensionality of feature space. Since the SVM algorithm operates natively on numeric attributes, it uses a z-score normalization on numeric attributes. In regression, Support Vector Machines algorithms use epsilon-insensitivity (margin of tolerance) loss function to solve regression problems.
Support vector machines regression algorithms has found several applications in the oil and gas industry, classification of images and text and hypertext categorization. In the oilfields, it is specifically leveraged for exploration to understand the position of layers of rocks and create 2D and 3D models as a representation of the subsoil.
I chose error penalty coefficient C as 100 for both linear and polynomial regressors. For the polynomial regressor I chose gamma as auto mode, polynomial degree as 3 and epsilon as 0.1.

2- LSTM: 
LSTM networks are well-suited to classifying, processing and making predictions based on time series data, since there can be lags of unknown duration between important events in a time series. LSTMs were developed to deal with the exploding and vanishing gradient problems that can be encountered when training traditional RNNs. Relative insensitivity to gap length is an advantage of LSTM over RNNs, hidden Markov models and other sequence learning methods in numerous applications
Since The Long Short-Term Memory recurrent neural network has the promise of learning long sequences of observations I chose it for using it as a regressor for a time period of approximately 9 years.
I chose 100 nodes with dropout rate of 20% with activation linear and optimizer as rmrsprop.

### Benchmark
As a benchmark for the problem, simple linear regression is a statistical method that enables users to summarise and study relationships between two continuous (quantitative) variables. Linear regression is a linear model wherein a model that assumes a linear relationship between the input variables (x) and the single output variable (y).
Some of the most popular applications of Linear regression algorithm are in financial portfolio prediction, salary forecasting, real estate predictions and in traffic in arriving at ETAs.
I have chosen simple linear regression method for benchmarking because it is the simplest method for regression. This benchmark is frequently used for regression problems. We know that R2 score will be very near to 1 when we have a good predictor for the problem. From the results very near to 1 at the end of the report we can say that a simple linear regression method is a challenging benchmark to be selected for the methods implemented. For the linear regression I chose learning rate as 0.2 having 10 nodes and 10 epochs.

## III. Methodology

### Data Preprocessing
For preprocessing data, I needed to eliminate NaN or null values, normalize, put additional frequently used trend analysis information methodologies in order to have a successfull learning. I have followed the following steps:

1- Download data from Yahoo Finance servers with pandas datareader into 13 different pandas dataframes.
2- Check for null values if there is any. I encountered no null values so I made no action for null values.
3- Add MACD (Moving Average Convergence Divergence) Data for column Adjusted Close for every data frame for 26, 12 day periods. This is a very frequently used trend information.
4- Add RSI(Relative Strength Index) for period 14 days.
5- Add Bollinger Bands namely standard deviation margins informations in column up and low.
6- Delete first 25 rows of every dataframe since we have added NaN values to newly added columns.
7- Plot data with newly added information in order to check for any mistakes not confirming with data
8- Normalize data with sklearn preprocessing minmaxscaler and multipy adjusted close with 100 to scale up to 100
(I also tried without normalizing the adjusted column and saved results for that case)
9- seperate y values column namely adjusted close price column from dataframes
10- Shift backward 1 days y values dataframes since we are trying to predict (n+1)th day with nth day input
11- Also for lstm network I needed to turn data into three dimensional array


### Implementation
In this section, the process for which metrics, algorithms, and techniques that you implemented for the given data will need to be clearly documented. It should be abundantly clear how the implementation was carried out, and discussion should be made regarding any complications that occurred during this process. Questions to ask yourself when writing this section:
- _Is it made clear how the algorithms and techniques were implemented with the given datasets or input data?_
- _Were there any complications with the original metrics or techniques that required changing prior to acquiring a solution?_
- _Was there any part of the coding process (e.g., writing complicated functions) that should be documented?_

I have used Jupyter Notebooks as development environment backed up with python 3.6 having numpy, scipy, pandas, pandas-datareader, matplotlib, scikit-learn, tensorflow, keras.

After preprocessing the data I have done the following steps:

1- Plan: 
I have planned implentation in the following order as stated between code cell 6 and 7: Benchmark Model, RMSE and R2 Validation, Solution Model, Create Test Flow

2- Create Benchmark Model: 
I have selected simple linear regression for benchmarking. With Keras: The Python Deep Learning library it is very easy to implement a simple linear regression by adding a dense layer consisting of only 1 node with activation type of linear. With Stochastic gradient descent as optimizer with learning rate 0.2.

3- Create Metrics
I have chosen to use two metrics for comparing the results of learning techniques R2 and RMSE. These metrics are already provided by sklearn and I added a little informative lines to print stating method and score.

4- Create Solution Models:
In order to use in test flow I have implemented 3 methods returning models created.
First method of support vector machine regressor is very straight forward with sklearn libraries having the option linear and penalty coefficient 100.
todo: take screenshots

Second method of SVM regressor has polynomial kernel and gamma is in auto mode with third degree polynomial fitting.


Thirdly, I have created an lstm model having 100 nodes with dropout rate of 0.2 followed by a flattening layer and a dense layer of one node for final prediction.

5- For test flow I wondered what will be the effect of using single model for 13 stocks and 13 diffreent models for each stock. So for the test flow I set a parameter for calling different models and single model.
I simply created models, split data fit models, get predictions and than keep the r2, rmse scores after calling the check score.




  

### Refinement
In this section, you will need to discuss the process of improvement you made upon the algorithms and techniques you used in your implementation. For example, adjusting parameters for certain models to acquire improved solutions would fall under the refinement category. Your initial and final solutions should be reported, as well as any significant intermediate results as necessary. Questions to ask yourself when writing this section:
- _Has an initial solution been found and clearly reported?_
- _Is the process of improvement clearly documented, such as what techniques were used?_
- _Are intermediate and final solutions clearly reported as the process is improved?_

Here is an initial solution result for the problem. It is clear that RMSE namely deviation of the error depends on the value of the price so I decided to normalize the adj. close value and than run the solution again. It is clear that stocks with heigher prices rmse scores reduced and rmse values approached to each other.
<table>
      <tbody>
          <tr>
            <td>
    <img src="https://github.com/farukbulbul/stock_price_indicator/blob/master/images/non-normalized-100-1000.png"                  width="50%" height="50%"/></td>
            <td>
    <img src="https://github.com/farukbulbul/stock_price_indicator/blob/master/images/normalized-100-1000.png" width="50%"          height="50%"/></td>
        </tr>
  </tbody>
</table>
I than tried having two layers of lstm of 10 nodes to have better reults but it ended in a slightly worse result. Also trying tanh and softmax functions ended in local minimas having poor results. Since benchmark and lstm results are very near I stopped trying to make lstm results better.


## IV. Results

### Model Evaluation and Validation
In this section, the final model and any supporting qualities should be evaluated in detail. It should be clear how the final model was derived and why this model was chosen. In addition, some type of analysis should be used to validate the robustness of this model and its solution, such as manipulating the input data or environment to see how the model’s solution is affected (this is called sensitivity analysis). Questions to ask yourself when writing this section:
- _Is the final model reasonable and aligning with solution expectations? Are the final parameters of the model appropriate?_
- _Has the final model been tested with various inputs to evaluate whether the model generalizes well to unseen data?_
- _Is the model robust enough for the problem? Do small perturbations (changes) in training data or the input space greatly affect the results?_
- _Can results found from the model be trusted?_

As far as the methods I tried best performing method is SVR polynomial. This method has very good R2 mean for stocks which is 
0.9995363077 and very near to 1. This indicates that this model can be definetly used as an estimator for stock prices given the supportive information extracted from the stock market prices.

I was expecting better results in LSTM technique but SVR polynomial was better in results. In the next few weeks I will try configure a better LSTM network which can outperform SVR polynomial.

For the SVR definetly polynomial kernel performs better than linear one and also it is better than the benchmark. I have added a grid search similar section at the end of bar charts for SMV polynomial solution configuration generating the following results. 
For the C Value C = 50, 100, 200 vales are tried and following mean values are gathered. C=200 can be selected

RMSE                    R2
default    0.404407     default    0.999515
c200       0.382911     c200       0.999563
c50        0.436590     c50        0.999446

For the gamma value gammma = 0.1, 0.2, 0.4, 0.8 values are tried and following results are gathered. gamma = 0.8 can be selected

RMSE                            R2
default            0.404407     default       0.999515
c200               0.382911     c200          0.999563
c50                0.436590     c50           0.999446
gamma_dot2         0.364122     gamma_dot2    0.999605
rmse_gamma_dot4    0.315261     gamma_dot4    0.999716


default            0.404407
c200               0.382911
c50                0.436590
gamma_dot2         0.364122
rmse_gamma_dot8    0.315261
degree4            0.280099

default       0.999515
c200          0.999563
c50           0.999446
gamma_dot2    0.999605
gamma_dot8    0.999716
degree4       0.999778

### Justification
In this section, your model’s final solution and its results should be compared to the benchmark you established earlier in the project using some type of statistical analysis. You should also justify whether these results and the solution are significant enough to have solved the problem posed in the project. Questions to ask yourself when writing this section:
- _Are the final results found stronger than the benchmark result reported earlier?_
- _Have you thoroughly analyzed and discussed the final solution?_
- _Is the final solution significant enough to have solved the problem?_
When we compare benchmark and optimized SVR results it is clear that the performance of the SVR method is better than the 
benchmark in terms of both R2 and RMSE metrics as seen below. There is a significant improvement at the RMSE metric which show the deviation of the solution is declined very much.

RMSE                      R2
best         0.280099     best         0.999778
benchmark    0.810680     benchmark    0.998444

## V. Conclusion
_(approx. 1-2 pages)_

### Free-Form Visualization
In this section, you will need to provide some form of visualization that emphasizes an important quality about the project. It is much more free-form, but should reasonably support a significant result or characteristic about the problem that you want to discuss. Questions to ask yourself when writing this section:
- _Have you visualized a relevant or important quality about the problem, dataset, input data, or results?_
- _Is the visualization thoroughly analyzed and discussed?_
- _If a plot is provided, are the axes, title, and datum clearly defined?_

### Reflection
In this section, you will summarize the entire end-to-end problem solution and discuss one or two particular aspects of the project you found interesting or difficult. You are expected to reflect on the project as a whole to show that you have a firm understanding of the entire process employed in your work. Questions to ask yourself when writing this section:
- _Have you thoroughly summarized the entire process you used for this project?_
- _Were there any interesting aspects of the project?_
- _Were there any difficult aspects of the project?_
- _Does the final model and solution fit your expectations for the problem, and should it be used in a general setting to solve these types of problems?_

### Improvement
In this section, you will need to provide discussion as to how one aspect of the implementation you designed could be improved. As an example, consider ways your implementation can be made more general, and what would need to be modified. You do not need to make this improvement, but the potential solutions resulting from these changes are considered and compared/contrasted to your current solution. Questions to ask yourself when writing this section:
- _Are there further improvements that could be made on the algorithms or techniques you used in this project?_
- _Were there algorithms or techniques you researched that you did not know how to implement, but would consider using if you knew how?_
- _If you used your final solution as the new benchmark, do you think an even better solution exists?_

-----------

**Before submitting, ask yourself. . .**

- Does the project report you’ve written follow a well-organized structure similar to that of the project template?
- Is each section (particularly **Analysis** and **Methodology**) written in a clear, concise and specific fashion? Are there any ambiguous terms or phrases that need clarification?
- Would the intended audience of your project be able to understand your analysis, methods, and results?
- Have you properly proof-read your project report to assure there are minimal grammatical and spelling mistakes?
- Are all the resources used for this project correctly cited and referenced?
- Is the code that implements your solution easily readable and properly commented?
- Does the code execute without error and produce results similar to those reported?



[1] The Effects of Psychology on Individual Investors’ Behaviors: Evidence from the Vietnam Stock Exchange
http://www.ccsenet.org/journal/index.php/jms/article/download/39897/22142
[2] http://www.investopedia.com/terms/f/financialmodeling.asp
[3] A machine learning based stock trading framework using technical and economic analysis
http://cs229.stanford.edu/proj2017/final-reports/5234854.pdf
[4] Trading volume: What it reveals about the market from https://www.rediff.com/money/special/trading-volume-what-it-reveals-about-the-market/20090703.htm



