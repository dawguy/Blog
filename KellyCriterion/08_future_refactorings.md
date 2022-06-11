One thing that bugs me a bit is that I needed to move more and more functions in the views namespace, as I couldn't access the data from a bottom level component otherwise.

I think a potential route to change this would be to separate the app-db access code into its own namespace.

That way everything can reference it with no circular dependency chains.

However for now I'm very happy with the fact that it works! So any additional refactorings will be done in the future.
