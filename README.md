# SSD options data

This is the dataset used in the paper _Enhanced indexation using both equity assets and index options_.

The dataset comprises the period from 17/03/2017 until 01/08/2025. It is composed of the files below.

___
# Construction of an option strategy  

### Illustration in ```Option strategy.xlsx```

Before describing the dataset, it is important to explain how an option strategy is constructed. The idea is that, from options that either exist or existed in the past, we build a long-term time series of returns. This time series can be interpreted as a __synthetic asset__ which mirrors returns obtained by investing continuously in options following a given policy.

In order to explain how we construct an option strategy, we will make use of an illustration using data actually used in the paper above. The illustration is given in the spreadsheet ```Option strategy.xlsx```, included in this repository. The spreadsheet was built with Google Sheets, hence it might not be entirely compatible with other spreadsheet software. It can also be accessed via this link:

https://docs.google.com/spreadsheets/d/1ejGgL8SAfKnR7vxi8WDHrjzuEAUksXU8sEHWWSJXuuE/edit?usp=sharing

We are going to build the following option strategy (OS): _buy or keep a position in 3% Out-of-the-Money (OTM) puts if the return of the S&P500 over the past 30 business days is less than -5%_. 

In the spreadsheet, we construct the returns of the option strategy above from 03/10/2022 until 25/10/2022. A step-by-step explanation of the spreadsheet is given below:

  1. Columns __A__ and __B__ are the S&P500 prices 30 days prior to the range above. This information is used to calculate the condition used to create the OS as described above.
  2. Columns __C-F__ are the main data from which option returns are built. These include S&P500 price, the risk-free rate and implied volatility (using VIX as a proxy).
  3. Columns __G__ and __H__ indicate whether the condition established for the OS is true or false.
  4. Columns __J-R__ build a continuous time series for a PUT OTM, with a $3$% moneyness target. __This is not yet the OS__, rather it is a component that will later be used to build the OS. The continuous time series (continuous meaning without interruptions) assumes automatic rollover to a different option whenever one of these two conditions are met:
     1. The current date is less than 20 days before the current expiration,
     2. The current exercise $E$ has deviated $\pm 3$% from the option-implied forward price $F$ of the S&P500, meaning $|\frac{F - E}{F}| \geq 3\\%$.

### Deciding the expiration

  5. In __Row 3__, the current date is 2022-10-03. Following the rules described in the paper for the most liquid S&P500 options, the next option available has its expiration date on the third Friday of the month, which happened on 2022-10-21. This is only 18 days to expiration, however, so we assume that the valid option to buy/hold is the one expiring at the last day of the month, 2022-10-31.
  6. In __Row 10__, we change the expiration date because 2022-10-31 is less than 20 days after 2022-10-12. The interpretation here is that if we were to buy an option on 2022-10-03, we would buy the one with expiration on 2022-10-31.
  7. With this decision we can calculate $L = 0.0767$ (time to expiration, in years), column __J__.
  8. Say $S$ is the current S&P500 price (column __D__) and $r$ is the annualised risk-free rate (column __E__). Having $L$, we calculate $F$ in column $L$ with the formula: $S\times e^{rL}$.

### Deciding the exercise
  
  9. In __Row 3__, the exercise is straightforward given the rule decided in Step 4. above. We find the multiple of 5 that matches $F' = F \times 0.97$ (meaning a $3$% target moneyness, column __M__) as close as possible.
  10. In __Row 4__, notice that the exercise $3575$ has deviated from $F'$ by more than $3$% (the deviation is shown in column __N__). Here we then have to change the option from exercise 3575 to 3685 to bring it back to the target moneyness.
  11. This change does not need to happen in __Row 5__ since $F'$ did not deviate enough from 3685. Overall the exercise is changed the exercise more often than the expiration date. In practical terms this would liquidating the position in the currently held options and buying the new one. This incurs costs and liquidity penalties, but for simplicity in this paper we ignore these.

### Calculating the PUT OTM prices and returns

  12. On 2022-10-03, we bought the option with the expiration/exercise given in __Row 3__ at the end of the day. According to Black-Scholes, we paid $\$72.158$ per unit of that option (Column __Q__).
      1. On 2022-10-04, __Row 4__, we held the option from __Row 3__ until the end of the day. We sold it for $\$35.813$ (Column __P__), a return of approximately -50%.
      2. On the same day, we used the proceedings from selling the __Row 3__ option to buy the __Row 4__ option for $\$68.018$ per unit.
      3. We did not need to change the option held in __Row 5__, hence the prices in Columns __P__ and __Q__ are identical.
  
  13. With the logic above, we construct a long-term series of returns for a PUT OTM 3% in column __R__.

### Constructing our option strategy

  14. Columns __T__ and __U__ indicate whether we should buy the option or invest in the risk-free rate, given the condition in column __H__.
  15. On 2022-10-18 (__Row 14__) the condition is false, hence we should hold the risk-free rate.
      1. On that day, we had kept the option from the day before (__Row 13__), and sold it on __Row 14__. Hence the return at the end of the day is -23%, the option return.
      2. At the end of the day, we "bought" the risk-free rate.
      3. The return on 2022-10-19 (__Row 15__) is the risk-free return.
      4. On 2022-10-19, the condition is true. At the end of the day (__Row 15__) we buy the option again (with expiration 2022-11-18 and exercise 3620).
      5. The return on __Row 16__ is the option return, 8.96%.
  16. With this logic, we construct a long-term series of returns for the OS, which alternates between risk-free investment and the PUT OTM 3%.

___
# Data

The data is located inside folder ```dataFiles``` and is comprised of the following.

## Equities and benchmarks

### benchmarks.csv 

Data for three benchmarks, the S&P500, the VIX index and the IRX. All three are used as input in the Black-Scholes formula for calculating the prices of options.

### equitiesAndETF.csv

Data for all assets in the S&P500 during this period. Negative prices mean that the corresponding asset was not part of the S&P500 at the time. Empty prices mean that the prices are missing, e.g. did not exist for that asset at that time.

## Options data

For the 12 option strategies used in the paper, the following files are necessary:

### optionComponent_XXX.txt

In order to generate the time series of each option, we assume the following rules:
 - Options exist at strike prices that are multiples of 5,
 - Options expire either on the third Friday of the month, or in the last business day of the month.

The option **moneyness** is calculated with a forward price assuming no dividends, a constant risk-free rate and a frictionless market, and is given by:

$$F = S e^{r\tau}$$

where $F$ is the option-implied forward price, $S$ is the price of the underlying, $r$ is the annualised risk-free rate and $\tau$ is the time (in years) till expiration.

An exercise price $E$ equal or close to $F$ means that the option is at-the-money (ATM). A put (call) option where $E < F$ ($E > F$) is out-of-the-money (OTM). A put (call) option with $E > F$ ($E < F$) is in-the-money (ITM). The moneyness is how far (in relative terms) the option exercise is from $F$, if $F = 100$  and $E = 97$, a put option is $3$% OTM.

As an option strategy needs a continuous time series, we simulate the rollover from one option to the next based on the following rules:
  - Rollover to the next expiration when the currently held option reaches 20 days to expiration.
  - Rollover to a new exercise price when the exercise price $E$ of the currently held option deviates $\pm 3$% of the moneyness target.

An option strategy may combine investiment in one or more individual options, the underlying asset and in an asset mimicking the risk-free rate (in our paper we did not employ option strategies that invest in the underlying). Our 12 option strategies make use of one or two of four individual components, each having its own time series of returns: put and call options ATM and put/call options OTM, with a moneyness target of 3%.

For each file, we include everything necessary to calculate the prices with Black-Scholes, as well as the formula results:
  - the price of the underlying (SP500),
  - the annualised risk-free rate, equivalent to symbol IRX,
  - an approximation for the implied volatility (given by the VIX index, regardless of moneyness),
  - The expiration of the option to be held at the end of the current day,
  - The forward price,
  - The exercise price of the option to be held at the end of the current day,
  - The price, according to Black-Scholes, of the option to be held at the end of the current day,
  - The price today, according to Black-Scholes, of the option held at the end of the previous day (same if the exercise and expiration are the same),
  - The return of the component, calculated as (PreviousPrice - Price of the day before) / (Price of the day before).

Each component file provides a continuous time series for a singular option strategy based on a moneyness target and assuming periodic rollover when the necessary conditions are met. With each component, we define the 12 option strategies used in the paper.

### option_OPT0XX.txt: 

Data for each one of the 12 option strategies (OS). For each OS, we include:
  - The OS returns, calculated as a weighted sum of the proportions in each individual component,
  - ```w_SP500``` as the weight of the OS invested in the underlying at each day (zero every day in all 12 OS),
  - ```w_c1``` as the weight of the OS invested in the first component, the PUT ATM.
  - ```w_c2``` as the weight of the OS invested in the second component, the CALL ATM.
  - ```w_c3``` as the weight of the OS invested in the third component, the PUT OTM.
  - ```w_c4``` as the weight of the OS invested in the fourth component, the CALL OTM.
  - ```w_RFR``` as the weight of the OS invested in an asset mimicking the IRX (daily) returns.
       
 

