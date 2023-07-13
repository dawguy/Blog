# Connecting The Backend

I guess really the thing I want to try right now is how to create shelves + planograms.

Do shelves live on a planogram or do shelves live as unique objects. Or do shelves even exist in general? Should be pretty easy to just be given a planogram and make assumptions about the shelves so everything fits. The biggest downside of this approach is that I do think that pre-generating imgaes per shelf is going to be important.

Though now that I type it. Really the only thing that matters is pre-generating images per set of products. The shelf doesn't really need to exist.

Though for merchandising purposes more control would clearly be better.

I'll just make them all dumb and have routes that look like below.

```
createPlanogram(shelfCount, products)
createPlanogram(shelves)
```

```
createShelf(width, height, products)
```

```
createProduct(details)
```

Which can handle all the shelf creation and all the product placements. The shelves will live by themselves and be connected to planograms.

If I don't like the sharing of shelves, the easiest fix would be to make createPlanogram(sehlves) clone the shelves.


## Implementing It Anything That Bugs Me

Defining these things in code isn't super quick.

## Learned Why Coercion Is Nice

Just refactored a bunch of code and started working on the API endpoints. Ran into the first failure where "1" != 1. I now see why coercion would be useful for these routes.
