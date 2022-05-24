Basic idea behind this was to help understand the kelly criterion through simulations.

The kelly criterion is a useful tool for determining an optimal bet size in a game of chance with positive expected returns values. It is not applicable to losing expected returns or net 0 expected returns.

The basic idea behind the Kelly criterion is that for any bet there is a level of aggressiveness which in the long term given infinitely many bets would lead to the highest overall value.

Any bet higher than this optimal value is too agressive and while it still may lead to gains, the chances of going bankrupt or having massive losses will lead to a wrose outcome.

Any bet lower than this optimal value will experience less volatility in betting pool at the expense of a slightly slower rate of growth.

Christian Aichinger created a tremendous explanation and demo of the kelly criterion which I highly recommend checking out!

* https://www.caichinger.com/blog/2018/04/16/kelly_criterion1/
** https://web.archive.org/web/20220524022525/https://www.caichinger.com/blog/2018/04/16/kelly_criterion1/
* https://www.caichinger.com/blog/2018/04/17/kelly_criterion2/
** https://web.archive.org/web/20220524022613/https://www.caichinger.com/blog/2018/04/17/kelly_criterion2/
* https://www.caichinger.com/blog/2018/04/18/kelly_criterion3/
** https://web.archive.org/web/20220524022658/https://www.caichinger.com/blog/2018/04/18/kelly_criterion3/
* https://www.caichinger.com/blog/2018/04/19/kelly_criterion4/
** https://web.archive.org/web/20220524022732/https://www.caichinger.com/blog/2018/04/19/kelly_criterion4/

---

The goal of this project is to make interacting with sets of probabilities easier and give us an easy way calculate the optimal growth rate bet size.

---

There are 2 ways of calculating optimal growth rate.

Using simulations with a large enough sample size.

Calculating values via a formula 

For the first pass the simulation method was implemented primarily because I didn't have access to the internet when starting this project, and had no clue what the proper formula to calculate growth rate was. In practice this is because there are some neat ideas involved like being able to simplifiy an exponential growth function (based on constant bet size) into a linear growth function by taking the log of the expontential. Which is only reasonable to do because the optimal bet size is going to be the same in both cases.

Without reading the articles linked above (and I will still need additional readings for sure) I don't think I would've came up with these tricks for calculating growth.
