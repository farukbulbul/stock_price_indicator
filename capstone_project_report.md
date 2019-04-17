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

<img src="https://veribilimcisi.files.wordpress.com/2017/07/83buy.png"/>

<img src="https://cdn-images-1.medium.com/max/800/1*d7IVANCDovpXKP5N0rR2Yg.png"/>

We will look at the deviation by calculating the RMSE of the model. If the deviation is big RMSE score will be far away from 0. Simply put, the lower the value the better and 0 means the model is perfect. Since we do not want high deviation for our predictions we would like to have RMSE score near to 0.

For a regression with an intercept, 𝑅2 is between 0 and 1, and from its definition 𝑅2=1−𝑆𝑆𝐸/𝑇𝑆𝑆 we can find an interpretation: 𝑆𝑆𝐸/𝑇𝑆𝑆 is the sum of squared errors divided by the total sum of squares, so it is the fraction ot the total sum of squares that is contained in the error term. So one minus this is the fraction of the total sum of squares that is not in the error, or 𝑅2 is the fraction of the total sum of squares that is 'explained by' the regression.


## II. Analysis
_(approx. 2-4 pages)_

### Data Exploration
In this section, you will be expected to analyze the data you are using for the problem. This data can either be in the form of a dataset (or datasets), input data (or input files), or even an environment. The type of data should be thoroughly described and, if possible, have basic statistics and information presented (such as discussion of input features or defining characteristics about the input or environment). Any abnormalities or interesting qualities about the data that may need to be addressed have been identified (such as features that need to be transformed or the possibility of outliers). Questions to ask yourself when writing this section:
- _If a dataset is present for this problem, have you thoroughly discussed certain features about the dataset? Has a data sample been provided to the reader?_
- _If a dataset is present for this problem, are statistics about the dataset calculated and reported? Have any relevant results from this calculation been discussed?_
- _If a dataset is **not** present for this problem, has discussion been made about the input space or input data for your problem?_
- _Are there any abnormalities or characteristics about the input space or dataset that need to be addressed? (categorical variables, missing values, outliers, etc.)_

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

### Exploratory Visualization
In this section, you will need to provide some form of visualization that summarizes or extracts a relevant characteristic or feature about the data. The visualization should adequately support the data being used. Discuss why this visualization was chosen and how it is relevant. Questions to ask yourself when writing this section:
- _Have you visualized a relevant characteristic or feature about the dataset or input data?_
- _Is the visualization thoroughly analyzed and discussed?_
- _If a plot is provided, are the axes, title, and datum clearly defined?_

### Algorithms and Techniques
In this section, you will need to discuss the algorithms and techniques you intend to use for solving the problem. You should justify the use of each one based on the characteristics of the problem and the problem domain. Questions to ask yourself when writing this section:
- _Are the algorithms you will use, including any default variables/parameters in the project clearly defined?_
- _Are the techniques to be used thoroughly discussed and justified?_
- _Is it made clear how the input data or datasets will be handled by the algorithms and techniques chosen?_

### Benchmark
In this section, you will need to provide a clearly defined benchmark result or threshold for comparing across performances obtained by your solution. The reasoning behind the benchmark (in the case where it is not an established result) should be discussed. Questions to ask yourself when writing this section:
- _Has some result or value been provided that acts as a benchmark for measuring performance?_
- _Is it clear how this result or value was obtained (whether by data or by hypothesis)?_


## III. Methodology
_(approx. 3-5 pages)_

### Data Preprocessing
In this section, all of your preprocessing steps will need to be clearly documented, if any were necessary. From the previous section, any of the abnormalities or characteristics that you identified about the dataset will be addressed and corrected here. Questions to ask yourself when writing this section:
- _If the algorithms chosen require preprocessing steps like feature selection or feature transformations, have they been properly documented?_
- _Based on the **Data Exploration** section, if there were abnormalities or characteristics that needed to be addressed, have they been properly corrected?_
- _If no preprocessing is needed, has it been made clear why?_

### Implementation
In this section, the process for which metrics, algorithms, and techniques that you implemented for the given data will need to be clearly documented. It should be abundantly clear how the implementation was carried out, and discussion should be made regarding any complications that occurred during this process. Questions to ask yourself when writing this section:
- _Is it made clear how the algorithms and techniques were implemented with the given datasets or input data?_
- _Were there any complications with the original metrics or techniques that required changing prior to acquiring a solution?_
- _Was there any part of the coding process (e.g., writing complicated functions) that should be documented?_

### Refinement
In this section, you will need to discuss the process of improvement you made upon the algorithms and techniques you used in your implementation. For example, adjusting parameters for certain models to acquire improved solutions would fall under the refinement category. Your initial and final solutions should be reported, as well as any significant intermediate results as necessary. Questions to ask yourself when writing this section:
- _Has an initial solution been found and clearly reported?_
- _Is the process of improvement clearly documented, such as what techniques were used?_
- _Are intermediate and final solutions clearly reported as the process is improved?_


## IV. Results
_(approx. 2-3 pages)_

### Model Evaluation and Validation
In this section, the final model and any supporting qualities should be evaluated in detail. It should be clear how the final model was derived and why this model was chosen. In addition, some type of analysis should be used to validate the robustness of this model and its solution, such as manipulating the input data or environment to see how the model’s solution is affected (this is called sensitivity analysis). Questions to ask yourself when writing this section:
- _Is the final model reasonable and aligning with solution expectations? Are the final parameters of the model appropriate?_
- _Has the final model been tested with various inputs to evaluate whether the model generalizes well to unseen data?_
- _Is the model robust enough for the problem? Do small perturbations (changes) in training data or the input space greatly affect the results?_
- _Can results found from the model be trusted?_

### Justification
In this section, your model’s final solution and its results should be compared to the benchmark you established earlier in the project using some type of statistical analysis. You should also justify whether these results and the solution are significant enough to have solved the problem posed in the project. Questions to ask yourself when writing this section:
- _Are the final results found stronger than the benchmark result reported earlier?_
- _Have you thoroughly analyzed and discussed the final solution?_
- _Is the final solution significant enough to have solved the problem?_


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

linear_rmse	linear_r2	svm_linear_rmse	svm_linear_r2	svm_polynomial_rmse	svm_polynomial_r2	lstm_rmse	lstm_r2
2.192528	0.998134	2.004182	0.99844	0.839159	0.999727	6.9289	0.981359
7.805897	0.999726	5.369823	0.999871	6.121477	0.999832	25.304551	0.997126
2.037792	0.999393	1.878048	0.999484	1.024915	0.999846	4.122919	0.997514
0.373997	0.998415	0.318431	0.998851	0.180695	0.99963	0.946034	0.989856
0.922202	0.999698	0.537153	0.999898	0.381595	0.999948	1.961162	0.998634
0.501376	0.999621	0.354377	0.999811	0.268849	0.999891	1.408609	0.997009
1.319128	0.999684	0.274242	0.999986	0.400191	0.999971	3.076882	0.998283
0.70635	0.9982	0.659137	0.998432	0.438073	0.999308	6.199673	0.861317
0.261775	0.999008	0.242498	0.999149	0.174377	0.99956	2.330596	0.921378
0.781117	0.992585	0.72915	0.993539	0.483466	0.997159	4.308404	0.774404
0.757133	0.999201	0.565553	0.999554	0.402025	0.999775	1.444661	0.997091
0.594605	0.99975	0.413177	0.999879	0.258257	0.999953	1.486428	0.99844
0.846397	0.996935	0.77153	0.997453	0.383184	0.999372	2.377591	0.975812
							
linear_rmse	linear_r2	svm_linear_rmse	svm_linear_r2	svm_polynomial_rmse	svm_polynomial_r2	lstm_rmse	lstm_r2
2.120015	0.998255	2.004182	0.99844	0.839159	0.999727	7.67189	0.977147
8.308104	0.99969	5.369823	0.999871	6.121477	0.999832	22.541021	0.997719
2.027183	0.999399	1.878048	0.999484	1.024915	0.999846	4.654158	0.996832
0.368127	0.998464	0.318431	0.998851	0.180695	0.99963	1.20297	0.983598
0.963566	0.99967	0.537153	0.999898	0.381595	0.999948	3.019329	0.996763
0.502139	0.99962	0.354377	0.999811	0.268849	0.999891	1.064994	0.99829
1.31664	0.999686	0.274242	0.999986	0.400191	0.999971	2.965975	0.998404
0.652179	0.998465	0.659137	0.998432	0.438073	0.999308	6.336198	0.855142
0.263022	0.998999	0.242498	0.999149	0.174377	0.99956	3.31033	0.841382
0.841118	0.991402	0.72915	0.993539	0.483466	0.997159	3.840367	0.820756
0.703717	0.99931	0.565553	0.999554	0.402025	0.999775	1.228029	0.997898
0.59493	0.99975	0.413177	0.999879	0.258257	0.999953	1.60534	0.99818
0.848118	0.996922	0.77153	0.997453	0.383184	0.999372	1.98252	0.983183

