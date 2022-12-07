# Seting Up A Common Reader

The end result is super simple:

`lein install` - Builds and puts pom into the `.m2` as expected for usage by other repositories.

One thing that tripped me up longer than it should is that the dependency code is ran upon being required. The original version of the package had statements that looked like below. This caused a failure, which I initially believed was a misunderstanding of package management. But ended up just being that if the day-1.txt file didn't exist then the dependency crashed upon being loading (making it look like it didn't work)

```clj
(def txt (slurp "./resources/day-1.txt"))
```

DON'T DO THE ABOVE.
