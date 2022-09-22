# Replacing Secrets In Strings

First thought was to use a regex and go through every match.

New idea is to create a function which parses the characters 1 by one and manages to do in place string replace.

-

Ends up that it wasn't bad at all to just do a character match.

But seems like concat produces a stack overflow exception due to the reasons outlined in the below post.

https://stackoverflow.com/questions/24958907/why-does-reduce-give-a-stackoverflowerror-in-clojure

Thunks, delayed function calls.

When you concat the result of a concat you have nested a thunk within another thunk. So it goes 3500 (length of string) deep.
