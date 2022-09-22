# Testing

Test definitions seem pretty straight forward

are: allows multiple data sets to run against a single test case.
is: explicity tests a single case. Can be chained together.

(deftest secrets-test
  (testing "Secrets are replaced"
    (are [s secrets target] (= (replace-secrets s secrets) target )
      "${dbUser} doesn't change ${dbPassword}" db-secrets "DB-USER-SUCCESS doesn't change DB-PASSWORD-SUCCESS"
      "${dbUser} doesn't change ${dbPassword}" empty-secrets "${dbUser} doesn't change ${dbPassword}"
      "dbUser doesn't change dbPassword" db-secrets "dbUser doesn't change dbPassword"
      "dbUser doesn't change dbPassword" empty-secrets "dbUser doesn't change dbPassword"
    )))


(deftest assoc-property-data-test
  (testing "Properties are assoc"
    (is (= (assoc-property-data {} [:a :b :c :world] "hello") {:prop "a.b.c.world" :ks [:a :b :c :world] :val "hello"} )
    (is (= (assoc-property-data {} [:a] "hello") {:prop "a" :ks [:a] :val "hello"} )
    )



