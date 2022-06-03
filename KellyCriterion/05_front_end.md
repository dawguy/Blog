Most of the front-end stuff that I've worked on up to this point was covered in my rowing blog posts.

Essentially creating components and laying them out.

I will say that CSS is both trickier and more fun than I expected. Only real challenge I feel like I've had so far is with a flex-box interaction where I put a container div around an SVG and made them both the same max-h and max-w. The issue which popped up is that because the SVG isn't at the top of the flex div, due to a header, the SVG silently would expand past the bottom causing padding calculations for the Insights section (next top level component group) to be messed up.

The solution was to just shrink the SVG's max dimenions to be smaller than its container.

---

Other than that, for the most part everything has gone really smoothly and I'm really liking the look of it all.

---

Had some issues with axis in D3. Ends up that because D3 and react use such different ways of thinking about the world, the 'xAxis(range)' call most D3 tutorials used wasn't particularly helpful.

I found a different walkthrough which introduced me to the '(.ticks xDomain)', which made it incredibly easy to manually create x and y axis tick marks + labels. It wasn't a clojurescript tutorial, but everything ported over quite well https://wattenberger.com/blog/react-and-d3 .

---

Other assorted stuff.

Added some green/red colored regions to the charts. These in my opinion really make it easy to tell what you're looking at.

---

Insights

After getting the view version of everything working, I felt like I was missing something. So an insights section was added which gives key stats about the given probability set. With this introduced I think that it pretty much contains all the information I was interested in!

---

Front-end work left:

* Adding editability
* Adding working links to the S&P data that I'll discuss further in part 6.
* Unsure about this (probably worth if I'm putting this online) Adding a footer giving links to resources you can learn more about the Kelly criterion and a "This is not financial advice" disclaimer.
