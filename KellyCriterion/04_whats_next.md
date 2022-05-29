Short answer.

I want a client side clojurescript application which could allow anybody to very easily explore the kelly criteria.
---
Parts I want done in Clojure and pulled in as a dependency.

- Simulation code
- Growth rate from formula calculation for a given bet size
- Optimal growth rate from formula calculation

---
Rough outline of what I want done in Clojurescript

* Graph showing bet size vs growth rate. The goal for this is to demonstrate how more aggressive bets lead to fewer gains as the number of bets reaches towards the law of large numbers.
* Table showing currently selected probabilities
* Graph showing currently selected probabilities. 
** I want percentage to be x-axis while payoff is y-axis. 
** The idea behind the interface is that the boundaries of each probability have a draggable anchor. You can increase the anchor by dragging it rightwards and decrease it by dragging leftwards. 
** As of right now unsure if I want to allow payoff of bet as the UI experince would be rough with such a finicky number. 
** NOTE: Draggable interface only will affect the probability of the left element. Will not touch right element.
** NOTE: Graph will be allowed to go over 100%, with a warning that values will be normalized.
* Add probability section
* For ease of making it, having the table be editable before implementing the interface is probably a good idea.


