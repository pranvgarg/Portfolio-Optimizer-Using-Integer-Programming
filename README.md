**Optimization Project II: Integer Programming**

** **


## <a id="_p2y0ixg4aqyb"></a>1. Introduction

### <a id="_4lezqvrdvxcu"></a>Objective

This report investigates the development of an optimized index fund portfolio using integer programming techniques to track the NASDAQ-100 index. Our objective is to construct a fund with a limited number of selected stocks, balancing representational fidelity to the index while minimizing the complexity and cost associated with frequent rebalancing. By leveraging a similarity matrix for stock selection, we aim to create a high-performing, simplified fund structure that maintains alignment with the broader market trends.


### <a id="_awbn89pmzp9d"></a>Problem Statement

In the realm of equity money management, creating a fund that mirrors a broad market index typically involves either high transaction costs or complex asset allocations. This project seeks to address these issues by formulating an integer programming approach that selects a subset of stocks to replicate index performance effectively. We construct an optimization model using daily return correlations among stocks, aiming to maximize similarity to the index while restricting the portfolio size to predefined levels. This report outlines our approach to building a scalable and efficient portfolio model, testing its effectiveness across different subset sizes, and assessing the long-term tracking performance of the optimized fund against the full NASDAQ-100 index.


## <a id="_7ujkgprghip2"></a>2. Data Preparation

The data preparation for this project involved several key steps to ensure the integrity and usability of the stock and index data for optimization modeling. This process included data loading, calculating daily returns, and constructing a correlation matrix as input for the integer programming model. These steps were as follows:


### <a id="_vh8mzbyshft8"></a>Data Loading

The dataset was loaded from a CSV file containing daily stock prices for the NASDAQ-100 index and its component stocks. The dataset was read into a pandas DataFrame with dates as the index, facilitating time-series analysis and making date-specific calculations straightforward. 


### <a id="_h1qqoq6vouar"></a>Handling Missing Data

Daily price data for stocks often contain missing values due to market closures or other data inconsistencies. To address this, linear interpolation was applied, filling gaps while preserving the overall trend. The remaining NaN values were addressed using forward-fill and back-fill methods to ensure data continuity for subsequent calculations. _For example, in the case of ARM, if the values above row 6 are zero and row 6 contains a value of 377, all preceding NaN values will be filled with 377, resulting in zero returns._


### <a id="_exm4anduwplv"></a>Calculating Daily Returns

Daily returns were calculated for each stock and the index by computing the percentage change from day to day. This conversion from price levels to returns allowed for a standardized comparison across stocks with different price ranges. Any infinite values resulting from large gaps in data were replaced with zero to prevent potential issues in optimization.

 

![](blob:https://gdoc2md.com/3e894aa5-9a6b-46ea-b466-a945437ba644)


### <a id="_rpodfnbxee4"></a>Constructing the Correlation Matrix

A correlation matrix was generated from the daily returns data to quantify the similarity between stock returns. Each element in this matrix represents the correlation between two stocks, serving as a key input for the integer programming model. By maximizing the overall similarity through correlation, the model aims to replicate the performance of the broader index with a subset of stocks.

 

This preparation stage ensured that the data was reliable, representative, and properly structured, laying the foundation for the portfolio optimization and tracking error minimization models.


## <a id="_qbzhysfwdr97"></a>3. Linear Programming Approach

### <a id="_wza7uhglixar"></a>Stock Selection

The stock selection process aimed to identify a subset of stocks from the NASDAQ-100 index that could collectively replicate the index’s behavior. This process was structured as an integer programming (IP) problem, where the objective was to maximize the similarity between selected stocks and the full index, thus minimizing the need for a large number of holdings while retaining representative accuracy.


### <a id="_h0f8zpwnkrio"></a>Model Formulation  

To achieve optimal stock selection, the correlation matrix of daily returns served as the basis for measuring similarity. The objective function maximized the total similarity between stocks by summing the correlation values of selected stocks and their closest representatives. This approach allowed for a targeted selection of stocks that are highly correlated, capturing broad market trends with fewer assets.


### <a id="_q8cf1r2gka7"></a>Decision Variables and Constraints 

Binary decision variables were employed to indicate stock selection. Specifically:

<!--[if !supportLists]-->●      <!--[endif]-->![](blob:https://gdoc2md.com/1391eb92-6769-4aa7-840b-ce9074de048c): indicates whether stock j is included in the portfolio (1 if selected, 0 otherwise).

<!--[if !supportLists]-->●      <!--[endif]-->![](blob:https://gdoc2md.com/bfceb6b1-ee60-450f-b2f0-28a9cf78c4d6): denotes whether stock j represents stock i in the index (1 if so, 0 otherwise).

The constraints guiding the selection process ensured:

<!--[if !supportLists]-->●      <!--[endif]-->__Subset Size:__ Exactly m stocks were selected, where m is a specified subset size that varies depending on performance goals.

<!--[if !supportLists]-->●      <!--[endif]-->__Unique Representation__: Each stock i in the index was required to have exactly one representative as stock j.

<!--[if !supportLists]-->●      <!--[endif]-->__Representative Condition__: Stocks could only represent others if they were included in the portfolio, preserving logical consistency in selection.![](blob:https://gdoc2md.com/0deb9948-ed2f-4c1e-8813-4cc131298113)


### <a id="_ipms6vfqd0gi"></a>Weight Selection (LP)

One major task was formulating an optimal weighting strategy for each of the portfolio's selected stocks. The objective was to minimize the deviation between the portfolio's weighted sum of stock returns and the index return across all periods. Two constraints needed to be followed which were: 

 

<!--[if !supportLists]-->_ I.     _<!--[endif]-->__The sum of weights ( ___𝑤_<sub>i </sub>) constraint:___ _This ensures that the portfolio is fully invested, with the total weight summing to 1

|   |                                                                    |
| - | ------------------------------------------------------------------ |
|   |                                                                    |
|   | ![](blob:https://gdoc2md.com/5767f172-f3d3-46d9-b76c-5a5cd2c784d2) |

\
__

_ _

<!--[if !supportLists]-->_II.     _<!--[endif]-->__Non-negativity constraint:_ _Each stock weight must be non-negative

|   |                                                                    |
| - | ------------------------------------------------------------------ |
|   |                                                                    |
|   | ![](blob:https://gdoc2md.com/09549daf-d947-4017-b48b-eb93edcdc639) |

\


 

Mathematically, the objective function is expressed as: 

|   |                                                                    |
| - | ------------------------------------------------------------------ |
|   |                                                                    |
|   | ![](blob:https://gdoc2md.com/372bad6e-186d-4f4d-b92b-740f2ef70c16) |

\


 

Since the absolute function is nonlinear, auxiliary variables 𝓏<sub>t </sub>had to be introduced to reformulate the problem to be solved as a linear programming problem. 

 

![](blob:https://gdoc2md.com/3e6e5e92-d705-437a-b077-75f0b8a0e24f)

 

This reformulates the objective function as: 

|   |                                                                    |
| - | ------------------------------------------------------------------ |
|   |                                                                    |
|   | ![](blob:https://gdoc2md.com/ccb86138-e885-46a7-a429-211b4d849e05) |

\


 

To implement the above, a function was written called ‘optimize\_portfolio\_weights’. This function returns a dictionary of optimized weights for the selected stocks that minimize the tracking error for the index. 

 

The salient features of the code covering the above: 

|   |                                                                    |
| - | ------------------------------------------------------------------ |
|   |                                                                    |
|   | ![](blob:https://gdoc2md.com/bdcd6e7c-086d-4745-9291-12c781a5532c) |

\


![](blob:https://gdoc2md.com/62b24f51-67a4-4a04-a088-981cbd4751f8)****


### <a id="_vuwqvanzmrtm"></a>m Value Analysis for Linear Programming **__**

In 2024, the portfolios track the index with varying degrees of alignment depending on the number of stocks selected (represented by "m").

<!--[if !supportLists]-->_I.       _<!--[endif]-->__Smaller Portfolios (e.g., m=5, m=10)__: These smaller values show greater divergence from the index, especially during periods of high volatility. The cumulative returns for these portfolios tend to underperform the index, indicating a less stable tracking ability due to the limited diversification. From m=5 to m=20, we also see a significant decrease in tracking error as more stocks are added.

<!--[if !supportLists]-->_II.       _<!--[endif]-->__Moderate Portfolios (e.g., m=20, m=30)__: These portfolios show improved tracking with less deviation from the index, but there are still noticeable divergences, especially during market downturns. However, after m=20, there is a noticeable plateau for tracking error, as the model struggles to improve until around m=70.

<!--[if !supportLists]-->_III.       _<!--[endif]-->__Larger Portfolios (e.g., m=70 up to m=100)_: _These portfolios track the index most closely, with minimal deviations in both up and down market trends. By m=70 and above, the portfolios almost mirror the index's performance, suggesting that the larger number of stocks provides enough information to approximate the index's behavior more effectively. This is to be expected, as the portfolio includes the majority of the index’s stocks.

Ultimately, there are diminishing returns in reducing tracking error as m increases beyond 20 stocks. The chart shows a rapid decline in both in-sample and out-of-sample tracking errors as m increases from 5 to around 20. After m=20, the rate of decrease in out-of-sample tracking error slows significantly. However, there is a sudden decrease in error from 60 to 80 stocks portfolio which is quite close to including all 100 stocks but in this case, we are also almost selecting all the stocks from the index which may not be optimal.

This suggests that increasing m beyond 20 provides diminishing returns in terms of tracking error reduction, meaning that 20 stocks might provide an optimal balance between tracking the index and portfolio complexity. 

![](blob:https://gdoc2md.com/065144f2-fbda-4d09-846a-c963516c7e0f)


### <a id="_lu4jniu6i6ps"></a>2023 Performance (In-Sample) vs. 2024 Performance (Out-of-Sample)

#### <a id="_fhid8almhpvy"></a><!--[if !supportLists]-->      I.         <!--[endif]-->__Tracking Error Patterns___:_

__In-Sample (2023)__: The in-sample tracking error decreases consistently as the number of stocks (m) increases. For m=5, the in-sample tracking error is high at 1.110995, but it drops significantly as m grows, reaching 0.088113 by m=100. This pattern suggests that adding more stocks improves tracking by capturing a broader representation of the index.

__Out-of-Sample (2024)__: The out-of-sample tracking error follows a similar downward trend, but interestingly, it is **lower than the in-sample tracking error** for smaller portfolio sizes (m<40m). For instance, at m=5, the out-of-sample tracking error is 1.023837 compared to the in-sample error of 1.110995, and this difference persists until m=40, where errors nearly equalize (0.607945 in-sample vs. 0.605632 out-of-sample).


#### <a id="_ej9bw8fsgp7"></a><!--[if !supportLists]-->_    II.         _<!--[endif]-->__Performance Divergence:__

__In-Sample (2023)__: Since the portfolios were constructed using 2023 data, the tracking errors align closely with the index as mm increases, achieving almost perfect tracking with larger portfolios (m≥50). This is expected, as more stocks allow for better diversification and closer alignment to the index's composition.

__Out-of-Sample (2024)__: In 2024, while tracking error decreases as m increases, the error reduction is less steep than in-sample, indicating that portfolios do not track the index as precisely. The presence of lower out-of-sample errors at smaller m values, however, suggests that these smaller portfolios captured elements of market trends that aligned better with 2024 than 2023.


### <a id="_3rq53sob0tcd"></a>Differences in Performance

1. __Overfitting and Robustness__: For m<40, portfolios seem to overfit specific trends in 2023, leading to higher out-of-sample errors. In 2024, these smaller portfolios exhibit better alignment with the market, possibly due to a more concentrated exposure to sectors or factors that performed well in 2024. This unintentional alignment suggests simpler portfolios captured broader market trends beneficial in 2024.
2. __Stabilization with Diversification__: For m≥40, the in-sample and out-of-sample tracking errors converge, indicating that the portfolios become robust and stable across both years. The higher diversification mitigates volatility, making them less sensitive to year-to-year market shifts, which is why both 2023 and 2024 performances are closely aligned as mm grows.
3. __Market Condition Shifts__: Differences in 2024’s market conditions compared to 2023, such as sector rotations, interest rate changes, or macroeconomic events, likely influenced these results. Portfolios constructed with fewer stocks in 2023 might have unintentionally aligned with dominant trends in 2024, resulting in lower out-of-sample tracking error for smaller mm values.


### <a id="_6xpt5wm0g632"></a>Conclusion for Linear Programming 

In 2023, the portfolios perform well in-sample, particularly with larger m, due to optimization with 2023 data. However, in 2024, out-of-sample performance deviates somewhat, especially at lower m values, where smaller portfolios achieve lower tracking errors than in-sample. This suggests that these simpler portfolios coincidentally matched 2024 trends. As m increases beyond 40, both in-sample and out-of-sample tracking errors converge, indicating that large, diversified portfolios provide stable performance across years.


## <a id="_sgl2542oflzk"></a>4. Multiple Linear Programming Approach

The Big M technique within the Mixed-Integer Programming (MIP) framework is a method often used in optimization problems to enforce constraints on specific decision variables. In this project, the Big M technique is used to limit the number of non-zero weights (i.e., stocks actively included in the portfolio) to a specified subset m of selected stocks from the NASDAQ-100 index. The Big M technique is introduced as an alternative approach to the IP model, specifically focusing on the weight selection process after stock selection. 


### <a id="_u3jfzjk2ccv5"></a>What’s the smallest value of Big M you could use?

The smallest value of Big M you could use is the maximum feasible weight that any stock might need in the portfolio. In this context, **Big M should be set large enough to allow feasible values for **![](blob:https://gdoc2md.com/b6ad1d58-6a83-4875-991e-14d66618fc2a)** when the corresponding binary variable **![](blob:https://gdoc2md.com/407f745e-bc13-4cae-a965-8160a8902ffb)**= 1** but should be minimized to reduce the search space and improve computational efficiency.****

To set Big M to the smallest feasible value:

1. __Sum of Weights Constraint__: Since all weights ![](blob:https://gdoc2md.com/b6ad1d58-6a83-4875-991e-14d66618fc2a)​ must sum up to 1, the largest feasible weight for a single stock occurs when that stock holds the entire weight of 1. Thus, Big M should be at least 1 to allow any feasible solution.
2. __Additional Constraints:__ In hypothesis, if there are constraints that limit the maximum allowable weight for any stock to less than 1 (e.g., a cap of 20%), then Big M could be set to that maximum weight cap instead. For example, if no single stock can exceed 0.2 of the total portfolio, Big M could be set to 0.2.
3. __Practical Recommendation:__ In this project context, assuming no specific weight cap, **Big M = 1** is the smallest practical value, as it ensures each stock ![](blob:https://gdoc2md.com/d1b679b7-db11-41a8-ac2c-6da8dbd1bd6b) can be assigned up to the full weight if necessary without exceeding the portfolio weight sum constraint.


### <a id="_5on3kr6adu6e"></a> 

### <a id="_mjm6fizd7j34"></a> 

### <a id="_ape1tk4p2zsg"></a> 

### <a id="_q5dqh1sdiqzl"></a> 

### <a id="_vjl23cborb1w"></a> 

### <a id="_dwfrpea20rth"></a> 

### <a id="_1fumhffupko0"></a> 

### <a id="_yahptm835sgo"></a> 

### <a id="_ozrvid8nbrd8"></a> 

### <a id="_afuil4j5px12"></a> 

### <a id="_a0kbixy8q393"></a>Constraints

The MIP model includes several constraints to ensure the portfolio is constructed within the desired limits:

1. __The sum of Weights Constraint: __Ensures that all weights in the portfolio sum up to 1, reflecting a fully invested portfolio.

   |   |       |
   | - | ----- |
   |   |       |
   |   | ![]() |

2. __Non-Negativity Constraint:__ Each weight ![](blob:https://gdoc2md.com/c21907cb-50f1-42a6-bede-55b1572bf621)must be non-negative, preventing short positions (which is being handled by Gurobi by itself)

3. __Big M Constraint:__ This constraint links the binary variable ![](blob:https://gdoc2md.com/cb5784c2-c0e3-40ea-870b-094e2632a378) with the weight ![](blob:https://gdoc2md.com/c21907cb-50f1-42a6-bede-55b1572bf621) for each stock. If![](blob:https://gdoc2md.com/64891724-c178-44b7-a037-12855209d9a8), then ![](blob:https://gdoc2md.com/c21907cb-50f1-42a6-bede-55b1572bf621) must also be zero, effectively excluding stock ![](blob:https://gdoc2md.com/306694be-4ffd-442b-9c05-200c8e270179) from the portfolio. This is achieved using the Big M technique:

   |   |                                                                    |
   | - | ------------------------------------------------------------------ |
   |   |                                                                    |
   |   | ![](blob:https://gdoc2md.com/37fa527d-13de-4a05-80ea-1326ba40f3d6) |

where M represents the maximum feasible weight any single stock can hold. For this project, M=1, allows each selected stock to potentially hold the entire portfolio weight if needed.

|   |       |
| - | ----- |
|   |       |
|   | ![]() |

\


4. __Subset Size Constraint:__ Limits the number of stocks in the portfolio by constraining the sum of ![](blob:https://gdoc2md.com/cb5784c2-c0e3-40ea-870b-094e2632a378) to exactly _m_, the desired subset size:

   |   |                                                                    |
   | - | ------------------------------------------------------------------ |
   |   |                                                                    |
   |   | ![](blob:https://gdoc2md.com/570380d8-1fe5-4502-8427-44cab334ff50) |


### <a id="_asqzdnkrfm79"></a>Objective Function

The primary objective of the MIP model is to minimize tracking errors. Tracking error is measured by the absolute difference between the portfolio return and the index return for each period _t_. Let:

<!--[if !supportLists]-->●      <!--[endif]-->![](blob:https://gdoc2md.com/5b7c99b3-2050-4570-9e2c-4af4f3d5b74b)represent the return of stock ![](blob:https://gdoc2md.com/f7d8c5f0-450d-4160-8eca-6dd3468dc37b) at time ![](blob:https://gdoc2md.com/d0da109b-632f-4453-824d-43993f7e2c71),

<!--[if !supportLists]-->●      <!--[endif]-->![](blob:https://gdoc2md.com/ac05a73b-51b6-43d5-b704-77bdb0f3cea5)​ represent the return of the NASDAQ-100 index at the time ![](blob:https://gdoc2md.com/d0da109b-632f-4453-824d-43993f7e2c71),

<!--[if !supportLists]-->●      <!--[endif]-->![](blob:https://gdoc2md.com/89c7667f-6c47-490a-9515-19fc5f358d4f)  represent the weight of the stock ![](blob:https://gdoc2md.com/f7d8c5f0-450d-4160-8eca-6dd3468dc37b) in the portfolio.

The tracking error for each period ![](blob:https://gdoc2md.com/d0da109b-632f-4453-824d-43993f7e2c71) is given by:

|   |                                                                    |
| - | ------------------------------------------------------------------ |
|   |                                                                    |
|   | ![](blob:https://gdoc2md.com/90520a66-0efc-4387-9962-5c5f2146beb0) |

\


The objective function minimizes the cumulative tracking error over all periods ![](blob:https://gdoc2md.com/47bc749a-f5be-40cf-bf46-313cce20f659):

|   |                                                                    |
| - | ------------------------------------------------------------------ |
|   |                                                                    |
|   | ![](blob:https://gdoc2md.com/cfa41650-78d2-4f4e-abf1-77b9138882d5) |

\


 

To convert this objective into a linear programming formulation, we introduce auxiliary variables for the absolute terms, allowing the solver to minimize tracking error as a linear function.

|   |                                                                    |
| - | ------------------------------------------------------------------ |
|   |                                                                    |
|   | ![](blob:https://gdoc2md.com/c3e8f35a-4744-42a9-8429-7c033ab64e50) |

\



### <a id="_c110dhnbwok9"></a> 

### <a id="_se7vsvm1inw0"></a>Conclusions for MIP

The MIP model demonstrates strong in-sample and out-of-sample performance, achieving notably higher accuracy in tracking the NASDAQ-100 index compared to the IP model. Even when computational time is limited, the MIP model’s precision in replicating index returns is evident and can be seen in the chart below, which illustrates the MIP model’s tracking performance across both in-sample and out-of-sample returns.

![](blob:https://gdoc2md.com/305ae8b0-3ae9-42e6-854f-fedc2a3fc600)

 

The tracking error in the MIP model follows a distinct pattern. For both in-sample and out-of-sample returns, the error declines sharply as the portfolio size grows from 5 to 30 stocks, after which it plateaus. Unlike the IP model—where the tracking error decreases partly at the start and then again as the portfolio approaches full replication at 100 stocks—the MIP model reaches a high level of accuracy with the first 30 stocks. This indicates that the MIP model effectively captures the broader index behavior with a smaller, well-selected portfolio.


## <a id="_wv1ezunlloiv"></a>5. Recommendations

### <a id="_b6p6n0rdqm8"></a>Which model should we use?

Both the Integer Programming (IP) and Mixed-Integer Programming (MIP) models have distinct advantages. The IP model offers a straightforward, cost-effective solution for approximating index performance using a carefully selected subset of stocks. Its approach, which maximizes similarity through highly correlated stock pairs, minimizes rebalancing needs, leading to lower transaction costs and simplified management. If computational efficiency is a priority, the IP model provides a reliable and accessible option.

 

However, if our primary goal is to replicate the NASDAQ-100 index’s movements as closely as possible, **the MIP model is the superior choice**. This model directly fine-tunes portfolio weights to minimize tracking error, achieving a closer match to the index. A comparison of out-of-sample tracking errors between the two models reveals that while both begin to plateau at around 20 to 30 stocks, the IP model’s tracking error stabilizes at approximately 0.6, whereas the MIP model stabilizes just below 0.3. The IP model cannot achieve the same level of precision as the MIP model without including nearly all index stocks. This is shown in the graph below.

 

However, it is important to consider the limitations of this model, specifically, the computational complexity. Mixed-Integer Programming (MIP) problems, especially those involving binary and continuous variables, are computationally intensive. Our current code iterates over multiple values of m, setting up and solving a new MIP model for each. This can lead to significant runtime, making it challenging to scale the approach for larger datasets or to experiment with numerous m values efficiently.

 

Despite this, we believe the pros of the MIP model outweigh the cons. Thus, we recommend the MIP model for this task due to its superior tracking accuracy, despite its higher computational demands. The MIP model achieves nearly half the tracking error of the IP model, producing results that more effectively meet our objective of close index replication.

 

![](blob:https://gdoc2md.com/cac595fd-fca8-4102-8f08-314eed135549)****


### <a id="_45sxo256ije9"></a>How many component stocks should we include in the fund?

As shown in our analysis, both models reach a plateau in out-of-sample tracking error around 20 to 30 stocks. With the MIP model, the lowest out-of-sample tracking error, at 0.279, occurs with 30 stocks, where the tracking error curve begins to flatten. While including fewer stocks might be ideal from a cost and simplicity perspective, reducing the portfolio from 30 to 20 stocks leads to a significant increase in tracking error—from 0.279 to 0.387—indicating that 30 stocks provide the best balance between accuracy and manageability. The ability of the portfolio to accurately track the NASDAQ 100’s return can be seen below.

 

![](blob:https://gdoc2md.com/e187cdbc-af03-4039-806d-cede8b0b2784)

 

On the other hand, if the IP model were selected instead, we recommend a portfolio of 20 stocks. This is the point where the IP model’s tracking error plateaus, and while adding more stocks marginally improves accuracy, it does so with diminishing returns across the next 40 stocks. Thus, 20 stocks in the IP model would minimize tracking error while maintaining a cost-effective, manageable portfolio size. The graph below depicts the accuracy of the IP model with 20 stocks in tracking the NASDAQ 100’s returns.

 

![](blob:https://gdoc2md.com/df43ab45-ccc1-45c8-93d2-e1443a1233b4)


### <a id="_hvq1ciu6qg7m"></a>Interesting Observations

Linear Integer Programming Stocks: 

The optimal solution found with objective value: 51.4450284090593

Portfolio weights calculated from In-Sample (2023) for m=5: {'HON': 0.13532789738415604, 'INTU': 0.22717045376089487, 'NXPI': 0.1719438705236885, 'PEP': 0.21077735222976027, 'SNPS': 0.2547804261015003}

 

Intuitively from the above, you would think stocks like Apple would be the first ones to be picked in your market portfolio due to their high market cap, but it does not show up for m=5 for Linear Integer Programming which is contradictory to our bias and even the general portfolio of popular holding companies. 

 

Multiple Integer Programming:

The optimal solution found with an objective value: 0.6546165504485314

Portfolio weights calculated from In-Sample (2023) for m=5: {'AMZN': 0.1299048109561683, 'AAPL': 0.2882989864253069, 'MSFT': 0.2761652558058142, 'TSLA': 0.06828108669632539, 'TXN': 0.2373498601163852}

 

However, for MIP, companies with higher market caps are included for m=5 and tries to minimize the tracking error compared to the index.


### <a id="_l4hyzka06bpj"></a>Summary

In conclusion, the MIP model with 30 stocks appears to offer the most efficient portfolio composition, balancing tracking accuracy and operational simplicity. This approach allows us to achieve our objective of close index tracking while retaining only a fraction of the NASDAQ-100's total holdings. 
