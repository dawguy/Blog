# Concurrency is hard

Really well written guide.
http://martintrojer.github.io/clojure/2013/07/07/coreasync-and-blocking-io

Helpful functions for styling core async. After reading above guide I undersatnd why the callbacks are useful.
https://ericnormand.me/mini-guide/core-async-code-style

Neat website for Go version of chans https://divan.dev/posts/go_concurrency_visualize/

## Where I'm at now

Created a worker to take items between machines. This works really well but has a high chance of causing timeouts for items waiting at earlier steps as we prioritize whatever is closest to being done.

I chose this way to solve the problem of workers attempting to put every order in before moving onto the next step. It generally works, but maybe the solution is to prioritize the steps which are blocking the machines from working.

Was this:
```clj
(a/go (loop [i 0]
(let [[v port] (a/alts! (vector (a/timeout timeout-length)
                                (:brewers-out tables)
                                (:rf-brewers-table tables)
                                (:grinders-out tables)
                                (:rf-grinders-table tables)
                                (:registers-out tables))
                        {:priority true})]
```

Moving to this didn't really do anything.

I think what I'm now realizing is that essentially the timeouts need to at least depend a little bit on table sizes and time for the operations to occur. For example if we allow 10 ground cups to be on the brewing table. With 1 machine we need to ensure the timeout is `(< (* 10 brew-time) timeout)` otherwise the table will time out 100% of the time the table is full.

## Does small table size break things?

Not really.

## Reasoning behind timeouts.

Could not debug this. It was more challenging than expected.

