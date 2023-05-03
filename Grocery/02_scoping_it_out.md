# Scoping It Out
Trying Malli for this project. Finding it really easy to get my head around and really cool.

https://github.com/metosin/malli
https://www.youtube.com/watch?v=MR83MhWQ61E

```clj
(ns planogrammed-groceries-backend.planogram.spec
  (:require [planogrammed-groceries-backend.product.spec :as ps]))

(def Shelf
  [:map
   [:items [:vector {:gen/min 1, :gen/max 4} ps/Product]]
   [:position [:tuple [:int {:min 0 :gen/max 200}]
                      [:int {:min 0 :gen/max 100}]]]
   [:width [:int {:min 0, :gen/min 50, :gen/max 400}]]
   [:height [:int {:min 0, :gen/min 1, :gen/max 10}]]
   [:type [:enum "Shelf" "Box" "Board"]]
   ])
```

Other things. Reitit instead of compojure.
https://github.com/metosin/reitit
https://www.youtube.com/watch?v=cSntRGAjPiM

The data orientation in both of them probably won't help me understand the other frameworks much, but seems like a pleasant way to work and really will help structure some thinking + takeaways for the future.

# Struggles

What type of features should this have?

Things that I want it to be able to do.

Planogram Basic functionalities:
* Viewing
* Editing

Product Management Basic functionalities:
* Viewing
* Editing

Product Management Medium functionalities:
* Categorizing
* Similarity clustering
* Replacability clustering
* Purchased with clustering

Planogram Medium functionalities:
* Mark as available for replacement with product in similarity range
* Mark as multiple varities
* Mark as place most profitable for Instacart of product in similarity range
* Mark as place most profitable for retailer of product in similarity range
* Mark as place most loved by customers of product in similarity range

Basic Shopping Experience functionalities:
* Shopping cart
* Navigate to aisle (Or tag.)
** Basic idea here is to default to 1 planogram for the aisle which covers 95% of the bases. With the option of falling back to a fully planogrammed out offering. Ideally the planogrammed offering would match the local store's layout exactly.
* For my purposes connecting the shopping cart to a payment processor is unnecessary. But having a button that says "Checkout" could be useful.

Medium Shopping Experience functionalities:
* Swap between tag/aisle representative planogram and full aisle view covering everything you'd see in a real store.

Basic Authorization/Authentication functionalities:
* User accounts for shoppers
* User accounts for admins
* User accounts for plannogrammers

Medium Interface With Existing Website:
* Create API calls or custom UI's for adding all the products we'd want to other websites shopping carts.

# Basic Planning

To get this thing built its looking like I'll want to do the following. Starting with backend as having valid data would make the FE 10x easier to do.

1. Backend for product creation.
2. Backend for image upload for products.
3. Backend for planogram creation. Have routes for adding/removing shelves to planogram and products to shelves, but they don't need to do anything beyond that.
4. OAuth/User creation. Might create the front-end at this point as well.
5. Product editor and viewer. For admins.
6. Planogram viewer
    5.a. IMO planogram editor will be near last as its a lot of work and not what I want to focus on making work really well. As long as I have the patience to do 3 or so planograms through the backend/hand-coded it should be good enough for my cases.
7. Shopping cart
8. Shopping experience aisles in store

From here iterate the shopping experience. If it feels good continue, if it can't be made to work well abort project and use this as a resume piece.

9. Interface with existing website UI's.
10. With the shopping experience feeling crisp, up next is variable. 
    10.a. Planogram editing
    10.b. Product similarity clustering.
11. Auto replace/build parts of planograms for enhanced profitability.
