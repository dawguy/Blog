# Physics

Didn't take many notes during this portion of the game making process.

Biggest note was that these collisions are elastic 2D collisions. For now not going to worry about spin. When I eventually do get that far it'll probably be purely graphical and always look like its spinning in the direction the ball is moving.

## Racking

Think that this works pretty well and will be able to be used no matter the ball radius.

```clojure
; cos 45 == .525321988818
; sin 45 == .850903524534
;            [0,0]
;         (-1,1)[1,1]
;      [-2,2]<0,2>(2,2)
;   (-3,3)[-1,3](1,3)[3,3]
; [-4,4](-2,4)(0,4)[2,4](4,4)
(defn racked-positions [radius vertical-stack offset]
  (let [r (* 1.05 radius)
        w (* r (Math/cos 45))
        h (* r (Math/sin 45))
        pos (if vertical-stack
              (fn [x y] {:x (+ (:x offset) (* x w)) :y (+ (:y offset) (* y h))})
              (fn [y x] {:x (+ (:x offset) (* x h)) :y (+ (:y offset) (* y w))}))]
    {
     :solids  (shuffle [(pos 0 0) (pos 1 1) (pos -2 2) (pos -1 3)
                        (pos 3 3) (pos -4 4) (pos 2 4)])
     :stripes (shuffle [(pos -1 1) (pos 1 2) (pos -3 3) (pos 1 3)
                        (pos -2 4) (pos 0 4) (pos 4 4)])
     :eight   [(pos 0 2)]
     :cue     [(pos 0 -10)]
     }))
```

## Physics

https://en.wikipedia.org/wiki/Elastic_collision implemented the angle free collision from this page. Required some basic vector operations to be implemented as well.

Collision detection itself for now is pretty simple, but will get slightly harder when the pockets are implemented. But I'm leaving that as a challenge after getting user input to work (and a visualization of the table)

## Simulations

* step-ball
* step-table
* sim-table

With the combination of these 3 things I believe that the simulation is working. Essentially step-ball will move the ball 1 iteration ahead. 

Step-table updates all balls, checks collisions and if a collision occured updates the velocities for the next iteration. My assumption for right now is that no mid-collision fixing needs to occur during each step as the balls should naturally move apart based on updated velocities.

Sim-table updates the table until everything stops moving.

## Next up

Taking in user input for hitting the cue ball.
