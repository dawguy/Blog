# List of things that I believe are important before releasing this

## UI Quality Improvements For Planogram Tab

* Animating the zooms
* Capping the zoom-out to a certain level
* Tooltip showing what is being hovered
* Showing flavor variations when a product is selected
* Double click prevention
* Animation showing add to cart was successful
* Better rounding of corners
* Adding prices below the products
* Image load testing. If its quicker on slow connections to do 1 big image I'd probably be able to figure some type of spriting system out.

# Shopping Cart Implementation

* Add To Cart should actually do something.
* UI should show cart somewhere

# Navigation To Other Categories

* Should be possible to navigate to other categories

# Planogram Expansion

* Should be able to expand past the "most likely to be purchased" default view. Idea here is essentially to have 1 view which should be good enough for the user to pick out everything they'd want. But in the case they want more variety I would like to be able to do a "True to store" layout which shows bay by bay.

# Product Images

* Thought of a pretty fun way to do this. Run stable diffusion on my desktop with a huge list of words + combos to try. Then write a quick filtering program (clojure?) which pops up the images allows me to click the one I want to save it.
* This will be essentially free, pretty high quality (I hope), and low man hour way to do this. The alternative would be to create some type of partially automated workflow involving purchasing the products. This might still be needed if I wanted to really get customers using this, but for the most part would be too much for right now.

# Connecting To Server

* Doing everything with front-end generated products works, but will need to be connected eventually.

# Shelf + Planogram Generation

* For now I'm okay making 3 planograms by hand, but after those I'd like to create something that auto generates shelves.
