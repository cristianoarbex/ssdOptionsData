# SSD options data

This is the dataset used in the paper _Enhanced indexation using both equity assets and index options_.

The dataset comprises the period from 17/03/2017 until 01/08/2025. It is composed of the following files:

 - **benchmarks.csv**: Data for three benchmarks, the S&P500, the VIX index and the IRX. All three are used as input in the Black-Scholes formula for calculating the prices of options.
 - **equitiesAndETF.csv**: Data for all assets in the S&P500 during this period. Negative prices mean that the corresponding asset was not part of the S&P500 at the time. Empty prices mean that the prices are missing, e.g. did not exist for that asset at that time.
 - **option_OPT0XX.txt**: Data for each one of the 12 option strategies. For each option, we include:
    - A table containing the price of the underlying, the assumed risk-free rate and volatility, and the option strategy returns. The option strategy returns is made of a mix of the returns of each option component, plus the underlying and the risk-free rate.
    - One table for each option component. Each option as six components:
         - an ATM put,
         - an ATM call,
         - a 3% OTM put,
         - a 3% OTM call,
         - a 3% ITM put,
         - a 3% ITM call.
       
       Data is repeated in all twelve options. An option strategy is made of a combination of individual option components. For each option component, we include the current applicable expiration date, price, the option-implied forward price (used for calculating ATM prices), the option exercise, the price of the currently held option, the price of the option held yesterday, and the individual component returns. The prices of each component are calculated using Black-Scholes.
    - A final table with the proportions invested in each component, in each day. The proportions include a proportion in the underlying, then in each component, then in the risk-free rate. In this particular paper, the proportion in the underlying is always zero.
 

