# Input

```clojure
(defn c-sim [max-steps k in-chan out-chan]
  (go (loop [i k
             ct 0]
        (let [input (poll! in-chan)]
          (if (= 0 (mod ct 100)) (prn ct))
          (if (some? input) (prn (str "***********" input)))
          (if (= ct max-steps)
            (>! out-chan i)
            (recur (+ i 2) (inc ct)))))))

(c-sim 100000 0 c-in c-out)
(go (>! c-in "**********IN**********"))

(go (prn (str "C-OUT " (<! c-out))))
```

From a short exploration of channels, looks like the idea I had will work. One big change which makes it possible is that the go macro causes (c-sim ...) to be ran asynchronously. Meaning that I can send multiple REPL commands and they will all be ran.

One thing to note during this investigation is that I'll likely want to play with Thread/sleep or something similar as its nearly impossible to trigger the c-in channels input. Maybe this will be less difficult when the loop contains more work like the simulation does.

## Key points

<! blocks
>! puts and blocks (TODO: Look up if there is a >! that replaces value if one exists)
poll! does not block. This is exactly what I was looking for in the case where a simulation is on-going. Not sure how useful, as ideally we'd only allow new shots and players to be added when the game is in-progress.
