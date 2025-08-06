# SSD options data

This is the dataset used in the paper _Enhanced indexation using both equity assets and index options_.

The dataset comprises the period from 17/03/2017 until 01/08/2025. It is composed of the files below.

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
       
 

