---
layout: post
title: Views on Imperative vs. Functional programming
---

Views on Imperative vs. Functional programming

**The four quadrants**:

|                |    Imperative     |  |      Functional                      |
| :------------- |  ----------------:|:--:| --------------------------:        |
| **Dynamic**    | {JS, Py, Ruby}    || {Erlang, Elixir, Clojure, awk}       |
| **Static**     | {Java, C#, Golang}|| {OCaml, Haskell, Elm}                |


**How does programming languages matter w.r.t an architecture ?**

- Imperative vs Functional boils down to:
  - Design Patterns vs. Mathematical functions
  - Discipline vs Behavior
- Have you ever wondered why we were not taught of patterns in our schools ?
- Have you observed the difference between the Maths taught at schools vs engineering Maths?


**With above thoughts in mind, I tend to have below conclusions:**

- Note - Some may find it harsh.
- However, we need to eliminate the factors leading to issues in a project's lifecycle.
- Refer to the conclusion quadrants.


**Conclusion quadrants:**

|             | Imperative             |  | Functional                                 |
| :-----------| ----------------------:|:--:| ---------------------------------------:   |
| **Dynamic** | {buggy, logic =~ bugs}    || {terse code, less bugs, fix minus effects} |
| **Static**  | {buggy, fix injects bugs} || {terse code, difficult to introduce bugs}  |


**Strongly opinionated views on programming any new feature:**

```
- Whatever you decide, it should belong to the right hand column of above quadrant.

- If the language does not have a REPL, then dump that language.

- Erlang/Elixir   can effectively replace Java & Scala
- Erlang/Elixir   can effectively replace Python
- OCaml           can effectively replace Python
- OCaml           can effectively replace GoLang
- Erlang          can effectively replace GoLang
- Erlang          can be a scripting choice
- Ocaml           can be a faster scripting choice
- Elm             can effectively replace JavaScript
- Ocaml           can effectively replace JavaScript
- If JVM          then use type safe Clojure & nothing else
- Scala           should be avoided.
  - Year 1 - I want to learn scala
  - Year 2 - I want to do learn scala extensions (read functional extensions)
  - Year 3 - I want to move to java 8 lambdas & functions
- Groovy          is good for scripting but there are better alternatives
- Kotlin          same as Groovy, & there are better alternatives
- Unix tools      can effectively replace dynamic languages for scripting purposes.
  - In short most Unix tools are dynamic i.e. not type safe.
  - Issues with Unix tools will be similar in nature to dynamic functional languages.
- Most important of all, the architecture should provide enough flexibility:
  - In other words the architecture should be programming language agnostic.
```


**References:**

- [Erlang/OTP vs. Others](https://news.ycombinator.com/item?id=9907310)
- [GoLang vs. Erlang](http://blog.erlware.org/some-thoughts-on-go-and-erlang/)

> Go is mostly gaining developers from Python and Ruby, not C++.
> Go's concurrency is better than Ruby, Python or C++.
> Go's concurrency is broken due to shared state.
> Erlang has better pre-emptive scheduling via reduction counting.
> Erlang's dirty schedulers for improved integration with C is a considerable improvement.

- [Everyday-Hassles-In-Go](http://crufter.com/2014/12/01/everyday-hassles-in-go/)
- http://yager.io/programming/go.html
- http://bravenewgeek.com/go-is-unapologetically-flawed-heres-why-we-use-it/
- [You-Should-Master-Awk](https://news.ycombinator.com/item?id=1738688)
- [Reason](http://facebook.github.io/reason/index.html)


**Passing Comments:**

> Dogs, cats & birds can talk together thanks to distribution built in xyz language
> itself. We need to get it right while choosing our weapon (read programming
> language)


**Quotes:**

```
- Complex systems fail in complex ways -- Bellovin
- Premature optimization is root of all evil --Knuth
- Worse is better --Gabriel
- Complexity increases non-linearly --Brooks
- Complexity is the enemy of security --Schneier
```
