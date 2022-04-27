It appears that to get API calls sent out I could either blindly import a library which uses core async. Or I could learn what core.async does, how to think about it and how to use it.

Decided to go with the later, with the expectation that I'm going to just use the library anyways.

Note: This is going to make the future simulations pretty fun to write as I fully expect to be using channels when implementing the game aspect of this.

---

https://www.braveclojure.com/core-async/

This is where I started. Great book.

---

Doing some random experimentations in this repo
https://github.com/dawguy/learning-clojure

---

Overall the idea seems pretty straight-forward

<! >! park

<!! >!! block

go is how you define a parking channel

(go (loop [] (do (let [val (<! chanel-name)] (recur))))) is how you get a repeating channel.

However the above method doesn't close, meaning it'll continue reading from the channel forever.

---

Current exploration topic is how to close out a channel as I haven't aactually read through the whole brave-clojure tutorial yet.

---

Also worth noting is that clojure will use JVM thread-maximum for channels, so there are concurrency issues if you need more than the default (8).

https://eli.thegreenplace.net/2017/clojure-concurrency-and-blocking-with-coreasync/

---

