# Rust!

Decided to learn Rust as several people I find really smart liked the language. And it is one of 2 languages loved more than Clojure percentagewise.

Using https://doc.rust-lang.org/book/ch02-00-guessing-game-tutorial.html as my tutorial.

# Associated Function

Implemented on a type

```txt
The :: syntax in the ::new line indicates that new is an associated function of the String type. An associated function is a function that’s implemented on a type, in this case String.
```

Don't quite have the background to undrstand this, but it seems quite important.

# Reference

The & indicates that this argument is a reference, which gives you a way to let multiple parts of your code access one piece of data without needing to copy that data into memory multiple times.

# STD and Types

If we hadn’t imported the io library with use std::io; at the beginning of the program, we could still use the function by writing this function call as std::io::stdin. The stdin function returns an instance of std::io::Stdin, which is a type that represents a handle to the standard input for your terminal.

# DOCUMENTATION <($.$<) (>$.$)>

```txt
cargo doc --open
```

Note: You won’t just know which traits to use and which methods and functions to call from a crate, so each crate has documentation with instructions for using it. Another neat feature of Cargo is that running the cargo doc --open command will build documentation provided by all your dependencies locally and open it in your browser. If you’re interested in other functionality in the rand crate, for example, run cargo doc --open and click rand in the sidebar on the left.

# Shadowing 

We create a variable named guess. But wait, doesn’t the program already have a variable named guess? It does, but helpfully Rust allows us to shadow the previous value of guess with a new one. Shadowing lets us reuse the guess variable name rather than forcing us to create two unique variables, such as guess_str and guess,


