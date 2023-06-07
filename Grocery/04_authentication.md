# Authentication

Next challenge will be user account creation and anything else that goes along with this.

I feel quite comfortable with how the Angular app's oauth flow works at a client side level, but am quite nervous as I'll now need to handle secrets, the oauth side, and the client side.

# Server Side
https://github.com/funcool/buddy-sign/blob/master/doc/00-intro.md
https://github.com/funcool/buddy-core
https://github.com/funcool/buddy-auth
https://github.com/funcool/buddy-hashers

https://github.com/r0man/oauth-clj/tree/master

https://developers.google.com/identity/protocols/oauth2

From what I've read it seems like

session: stored on backend. Client sends sessionId that lookup is based on.
token: JWT/JWS/JWE 
https://stackoverflow.com/a/62012045

To put it simply, JWT (JSON Web Token) is a way of representing claims, which are name-value pairs, into a JSON object. The JWT specification defines a set of standard claims to be used or transferred between two parties.

On the other hand, JWS (JSON Web Signature) is a mechanism for transferring a JWT payload between two parties with a guarantee for integrity. The JWS specification defines multiple ways of signing (for example, HMAC or digital signature) the payload and multiple ways of serializing the content to transfer across the network.

(JWS = JSON Web Signature, JWE = JSON Web Encryption)
https://sherryhsu.medium.com/session-vs-token-based-authentication-11a6c5ac45e4

# Maybe This Will Guide Me?
https://www.oauth.com/oauth2-servers/signing-in-with-google/

I am starting to feel like the best approach regarldess is going to be just to create the user database table and implement something custom at first then try to bolt something on with any required refactorings after. (Note: This is what I'm going to do). Also of note is that I'll definitely want to create a second email address just in case I screw something up and leak my secret.

https://x-team.com/blog/storing-secure-passwords-with-postgresql/


# Another one!
https://12factor.net/

https://12factor.net/logs

Logging to stdout, managed from there entirely by the execution environment.

# STARTREK SAVES THE DAY

https://github.com/dharrigan/startrek

This repo is a complete example with all types of auth. Will likely be learning a lot from this as I try to implement my own auth/login experience.

# JWT Implementation

```clj
(ns decode-jwt
  (:require [cheshire.core :as json])
  (:import [org.apache.commons.codec.binary Base64]))

(-> returned-jwt ;; returned-jwt is your full jwt string
    (clojure.string/split #"\.") ;; split into the 3 parts of a jwt, header, body, signature
    second ;; get the body
    Base64/decodeBase64 ;; read it into a byte array
    String. ;; byte array to string
    json/decode ;; make it into a sensible clojure map
    )
```

JWT Parsing libraries

https://github.com/kelveden/ring-jwt : Ring wrapper for below Java implementation
https://github.com/auth0/java-jwt : In Java

# JWT Parsing

```clj
(ns planogrammed-groceries-backend.auth.auth
  (:require [buddy.sign.jwt :as jwt]
            [environ.core :refer [env]]))

(defn create-jwt [username]
  (jwt/sign (get-user username) (env :auth-secret)))
(defn create-invalid-jwt [username]
  (jwt/sign (get-user username) (env :another-auth-secret)))
(jwt/unsign (create-jwt "dawguy") (env :auth-secret))
(jwt/unsign (create-invalid-jwt "dawguy") (env :auth-secret))
```

Procues output 
```clj
=> {:username "dawguy", :email "example@gmail.com"}
Execution error (ExceptionInfo) at buddy.sign.jws/unsign (jws.clj:171).
Message seems corrupt or manipulated.
```

# Profiling

For future me https://clojurians.slack.com/archives/C03L9H1FBM4/p1681223084938609

A bug where the profiling was much slower than expected.

# Hashing

Buddy makes it super simple.

```clj
(ns planogrammed-groceries-backend.auth.auth
  (:require [buddy.hashers :as hashers]))

(hashers/derive "aB^4Rd^zgwAQQ#OD")
; Produces "bcrypt+sha512$18b64e558fe9c8450f17b546dcc39d9c$12$1046fef9194c6443f53f997393b1b8640aaff8c685f6d54d"
; Algo bcrypt+sha512
; Salt 18b64e558fe9c8450f17b546dcc39d9c
; Iterations 12
; Password 1046fef9194c6443f53f997393b1b8640aaff8c685f6d54d
(hashers/check "aB^4Rd^zgwAQQ#OD" "bcrypt+sha512$18b64e558fe9c8450f17b546dcc39d9c$12$1046fef9194c6443f53f997393b1b8640aaff8c685f6d54d")
; True
(hashers/check "password" "bcrypt+sha512$18b64e558fe9c8450f17b546dcc39d9c$12$1046fef9194c6443f53f997393b1b8640aaff8c685f6d54d")
; False
```
