---
---

Hello, 2021!

I've been experimenting with Rust for a while and I really like the
language. I like how it forces me to check for all the possible values
when I'm matching on something or checking a condition. Compile time
checks, although quite annoying at times (because of my inexperience
obviously) is so.. so much better than a dynamically typed language. I
no longer mistakenly get variable undefined errors or a missing
comma/semi-colon error at runtime only if the code gets inside a
certain condition! There are so much things to like about the language
honestly. Some of my favorites are:

- `struct`s and `impl`s blocks. I like how the `impl` blocks are
  separate from the actual `struct` definition. This allows for a
  cleaner code and lets me see what the actual data is about without
  having to see the method definitions. You can also have multiple
  `impl` blocks for each category of features that you want to
  support. For example, one block for only initializing the struct
  (maybe two to three different types of initialization methods).
  Implementing `trait`s also happen in different blocks. This keeps
  the code really clean and readable.

- Everything is by default immutable. You cannot assign to any
  variable unless it is explicitly marked as mutable. The same goes
  for function calls and definitions. You must mark a parameter as
  mutable to mutate it inside a function. When you call that function,
  you are also required to use the `&mut` keyword to pass as a mutable
  reference. Immutable references are represented by `&`. This makes
  for a code that is very explicit in that, you don't have to think
  whether the variable that you just passed will be mutated by the
  function, or just read value from it, or maybe own it completely.

- Error messages! I think this is one of the most underrated and
  underappreciated features of Rust that many people just take for
  granted. This is by far the most friendly language I've seen in
  terms of error messages. Although there are cases where the error
  messages can go out of hand just like other languages, it generally
  highlights where you made a mistake, what the possible fix might be
  and sometimes an additional error code to let you know more about
  how it originates and the possible solutions.

- Very explicit about type conversions. Unlike many other languages,
  Rust does not auto convert from one type to another unless you
  explicitly ask it to do so. You are then also forced to check if the
  conversion succeeded (unless you do away with `if let` or do
  `unwrap()`/`expect(...)`).

- `enum`s! They can hold data! You can easily match on a particular
  enum variant and extract data from it. The `match` keyword will
  match exhaustively, meaning, the compiler will not let you go away
  with just by checking for one variant and ignoring the other ones
  (although you can with `if let` or using `_ => ()` in match blocks).

- Cargo! Although not directly part of the language itself, Cargo is
  really friendly for a compiled language and the dependency
  management is nice (I've only tried "Rust only" simple projects, not
  the ones where you have to link to other languages/libs at
  compile/runtime). My experience with cargo is very limited, by so
  far I love it!

- Docs and Tests! The whole rust community is obsessed with extensive
  documentation and tests. As a result, almost all the popular crates
  (what you call modules in python or external libraries in other
  languages) have really good documentation with example code. The
  standard library contains code blocks which let you run the code
  right in the browser. The documentation site for every rust crate is
  of the same style, so you don't have to figure how docs are laid out
  in the different projects. They even have [docs.rs](https://docs.rs)
  which hosts the documentation for all the uploaded crates on
  [crates.io](https://crates.io).

- High quality libraries. Rust libraries are some of the most highest
  quality libraries out there. Of course this is not true for every
  library but for the most popular ones, this is true. They're usually
  battle tested and runs in production in many organisations. The
  popular library maintainers usually strive for correctness, safe and
  fast code.

There are obviously many more, but tbh I'm not really that much of an
expert in Rust yet so I can't say more. There are also challenges that
I have not faced yet that many experienced and big projects face. I
also haven't really grasped the lifetimes concept to the core.

With this limited knowledge, I set out to build a Websocket server
that lets us display logs in real time. The client software that runs
on one machine must be able to send logs to a browser that is possibly
running on another machine. The client machine cannot host a server
directly because of security reasons. So, the flow of messages will
be: `Client -> Server -> Browser`. I was able to successfully build
this system, however its yet to be put into production and test. Maybe
I'll write about this more about this in another post?
