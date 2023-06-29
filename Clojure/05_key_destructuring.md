# Key Destructuring

```clj
(defn abc [{:keys [user pass] :as credentials}]
  (prn (str "user: " user ", pass: " pass))
  (prn credentials)
)
```

```clj
(abc {:user 123
      :pass "abc"
      :other "DEF"})
"user: 123, pass: abc"
{:user 123, :pass "abc", :other "DEF"}
```

```clj
(abc {:user 123
      :other "DEF"})
"user: 123, pass: "
{:user 123, :other "DEF"}
```
