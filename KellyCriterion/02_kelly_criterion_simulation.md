Kelly Criterion Simulation

The basic flow for what I wanted here.

Goal:
Give a good enough simluation of the growth function that I can get a general sense of how big of a bet is prudent. I don't care about absolute precision, but a short run time + accurate enough results will give a general sense of good bet sizes.

Pre-reqs:
* Set of all odds for the game. (Note: Game must have a postive expected return)

Steps for how I thought a good simulation would be run:

Create tools to simulate a single bet. This takes the odds and selects one outcome and multiplies the bet by that outcome.

Create tools to simulate a chain of bets using a starting balance and the strategy of betting a fixed percent of current assets.

Create tools to run the above chain of bets simulation multiple times to reduce outliers.

Create tools that will run the above chain of bets from each outcome of 0-100%. Giving us 100 different bet percent values and how profitable they were.

Create tools to either save the above results to a CSV file, or calculate a polynomial equation which gives a good-enough fit for the results. That way we can calculate an optimal growth bet size.
