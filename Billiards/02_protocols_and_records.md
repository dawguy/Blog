# Protocols and Records

Note: I recommend https://www.manning.com/books/the-well-grounded-java-developer-second-edition for anyone working on Clojure/JVM. This book really helped me grasp how and what is happening behind the scenes of the JVM and some basic ideas on how Clojure code is converted into something runnable. No amounts of clojure based videos really helped clarify this more clearly than this book.

## Protocols
(defprotocol)
Defines something that looks like an interface that defines methods.

## Records
(defrecord)
Defines somethign that looks like a class. Has state, for functionality you must give it a protocol and then the methods of that protocol that you want to implement. The non-stated methods of that interface will be ignored/treated as UnsupportedMethods at runtime.

### Creating a record

(map->RecordName [value-a-from-map value-b-from-map]])

I don't quite understand yet the left-hand of this arrow syntax and how we might be able to define transforms.

But (map->RecordName {}) or (->RecordName {}) both essentialy say to create an object of class RecordName filling in the fields with the matching map's keys.

# Sample Code

```clojure
(defprotocol Table "A representation of a billiards table"
  (start [this player-a player-b])
  (stop [this])
  (step [this])
)
(defprotocol NamedObj "An object that can say its name"
  (say-my-name [this]))
(defrecord PoolTable [id name]
  Table
  (start [this player-a player-b] (do (prn player-a) (prn player-b) (prn this)))
  NamedObj
  (say-my-name [this] (prn (:name this)))
  )

(def tables (atom []))
(swap! tables conj
      (map->PoolTable {:id "Hello" :name "David"}))

(say-my-name (first @tables))
(start (first @tables) "A" "B")
```


# Actual Strucutres

```clojure
(-> Table
    .getClass)
=> clojure.lang.PersistentArrayMap
(-> PoolTable
    .getClass)
=> java.lang.Class
(-> (first @tables)
    .getClass)
(-> PoolTable
    .getPackageName)
=> "clj_board_game.core"=> clj_board_game.core.PoolTable
(-> (first @tables)
    .getPackageName)
Execution error (IllegalArgumentException) at clj-board-game.core/eval2826 (core.clj:27).
No matching field found: getPackageName for class clj_board_game.core.PoolTable
```
