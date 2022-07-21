# API Ideas

Right now I've been stalling a bit because while the basic structure exists, how I want to interact with the config manager is still a bit iffy.

Ideas:

ring rest server which takes a RPC type approach. Aka passed in the body is a vec that looks like [diff file-a file-b].

This is quite temptign because its pretty simple to implement and would be straightforward to reuse for a purely CLI interface as well.

https://clojuredocs.org/clojure.core/intern

Seems like clojure even makes this extremely easy to do.

---

Could turn this ino a more normal rest api as well.
