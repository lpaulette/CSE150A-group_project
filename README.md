# Abstract

Our probabilistic agent will decide whether to trade an Amazon stock after estimating the probability of the stock price increasing over a given time period. The agent we are trying to implement will be a utility based agent. 
Performance measure is to maximize expected utility, balancing profit and risk.
Environment the agent operated in is the stock market, where prices fluctuate over time. The key environment factors include:
The stock price movement parameters, i.e. open, high, low, close.
Volume
Volatility
Date, i.e. day of the week, month.
Actuators include the agents actions, such as:
Buy: purchase stock at the start of the given time period
Sell: sell stock at the end of the time period
Hold: do nothing if expected utility is low
*Potentially Position Sizing: adjusting trade size based on confidence.
Sensors:
Historical price and volume data
Probability of increase estimated by the agent

P(Increase | X) = P(X | Increase)  P(Increase)P(X)
Each feature of the data is independent, so:

P(X | Increase) = P(Day | Increase) P(Volume | Increase)  P(Volatility | Increase) ...
Above is the probability of observing X given the stock increases.

P(Increase) : The probability that the stock increased based on the historical data.

This is the evidence:
P(X) = P(X | Increase) P(Increase) + P(X | Decrease)P(Decrease) 


The utility function can be defined as
U(A) = P(Increase | X) * ExpectedProfit - P(Decrease | X) * ExpectedLoss)

and represents the agent’s understanding of how the stock is changing. When the probability of an increase in the market is expected, it prompts the agent to buy or hold. On the other hand when there’s a lower probability of an increase with a high probability of a decrease, the agent factors into selling or holding. We can define a Threshold in which U(A) has to be greater than in order to trade


