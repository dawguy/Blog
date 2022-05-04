Finished reading through the clojure core.async page, and did a bit of playing around with it in previous 14 blog post.

Now going to go back into clojurescript since I was having fun with that.

---

https://github.com/r0man/cljs-http

This is the framework I will use.


---

Ends up I'm spending much more time on CORS than the front-end itself. As every request is failing until I add the header 

@CrossOrigin(origins = "http://localhost:8020")

I wasn't allowed to pass the header "Access=Control-Allow-Origin: http://localhost:8020/*" (or anything with a wildcard) as the browser security requirements prevent it by default. The solution needed to be allowing the requests server-side.

-

And now I'm going to try to make a config file which will automatically take care of this for me using a property file. As I don't want to do this everywhere.

https://stackoverflow.com/questions/42874351/spring-boot-enabling-cors-by-application-properties

-

Following the lambda island tutorials made me realize that the approach of swapping out the active page is not making use of React/Reagent particularly well.

Instead I'll just make one parent component which decides how to make everything else. This feels like it'll be more react/immutable data appropriate.

https://lambdaisland.com/episodes/reagent

