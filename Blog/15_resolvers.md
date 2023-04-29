# Resolvers

Noticed that the load speed was terribly slow from my phone while not on wifi.

Chose to solve this problem with resolvers (through the use of simulated slow 3g). It doesn't actually fix the loading speed, but requiring the data for the page to load first meant that there was no content flickering and felt way better to navigate.

The implementation was pretty straight forward of search for resolver on google and follow the top post stating how to implement them.

Had some concerns that it would be slightly hard to maintain as it separates the logic into another file, but it felt like a fair enough trade off.

---

Also improved load times by pre-fetching the next set of blog posts. Since these payloads are so small it ended up making it really snappy without much tradeoff.
