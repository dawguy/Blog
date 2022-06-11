Next up on the list is getting editing working.

The things I expect to need:
* Editing versions of the Chance component
* Way to refer to what is being changed
* On-change and on-event listeners

A while back I saw these tutorials from lamdaisland.com:
* https://lambdaisland.com/episodes/reagent
* https://lambdaisland.com/episodes/reagent-2-cursors
* https://lambdaisland.com/episodes/reagent-3-keys-lifecycle-methods

For editing the cursors is most important. (With some basic knowledge of how react works from earlier projects in jsx) 

Essentially what needed to be done was creating cursors, snippets of an atom which can be viewed and swapped nearly the same was an atom, from the atoms. And then add the editing + delete buttons to the side.

---

Something interesting I found was that type-hints for the key are needed rather than applying a key property to the top-level div of Chance.

```clojure
(for [idx (range (count (:unnormalized-chances @data)))]
 (let [chance-cur (r/cursor data [:unnormalized-chances idx])]
   ^{:key (random-uuid)} [Chance chance-cur]
 ))
```

---

NOTE: Had a weird error occur where any change caused a loss of focus on first change when I was using the type hint and explicitly defining the key attribute on the [:tr] itself like below.

```clojure
[:tr {:key (str (:prob @chance) "-chance-" (:payout @chance))} ...
```

The solution was to remove the key attribute from the [:tr] element and then everything started working as expected.

---

The type system of clojurescript is throwing me a bit for a loop.

Normally something like "250" < 251 does the comparison after converting both to be the same type giving values that made sense as if you were comparing "250" < "251". However I'm getting errors.

Payoff is easy. Just swap it over to (int (:value data)) which is good enough for my use case. (Exception handling code to come if this gets used by more than just me, but as this is pretty much going to be a personal website only I'm not too too worried about it)

In particular there is a bigger challenge with the double casting of :prob. 

Doh <slaps head>

js/parseFloat and js/parseDouble strike again. Completely forgot that if I wanted to use javascript I needed to use the js/ module.

---

One thing that is biting me right now though is that essentially by saving the parsed input stuff like 0. is impossible to type out. I think we're going to have to allow these fields to all be saved as text, and only cast them to floats/ints when doing comparisons or math.

Not too bad but was not expected given how loose javascript types typically are.

---


Hit another interesting roadblock

I was working on a delete function which looked like this

```clojure
(defn remove-at-index [arr idx] (concat (subvec arr 0 idx) (subvec arr (inc idx))))
```

However when setting the unnormalized-chances to this functions return value I was getting nil for the cursors being created.

I believe what was happening was that I was assigning lazy-seqs to the app-db. And for some reason my cusor creation wasn't being persisted after a delete.

Simple solution is to place a fully evaluated list into the atom with mapv identity.

This is definitely something I want to investigate further, since I found it a bit unintuitive.
