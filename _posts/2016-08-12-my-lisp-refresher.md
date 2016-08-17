---
layout: post
title: My lisp refresher
---

# Why this article ?

> This helps me in learning or referencing clojure, lfe, etc quickly.

<br />

### S-Expressions

```clojure

;; f is the function & rest are arguments
(f arg1, arg2, ... argN )

(+ 1 2 3)

(list 1 2 3)

;; symbolize & then later eval it
'(1 2 3)

'(+ 1 2 3)

(eval '(+ 1 2 3))
```

<br />

### Homoiconic Language

```clojure

".. the primary representation of programs is also a data structure in a primitive
type of the language itself."
```

<br />

### Macros

```clojure

;; a compile time transformation
;; a scoped syntax
```

<br />

### Why use Macros ?

```clojure

;; syntax has semantics, data does not
;; only way to create **control-flow abstractions**
;; solving problems at compile time is always better than at runtime

;; GO macro
;; a library & no compiler changes are required
;; works in JVM & JavaScript environments
;; identical flow of execution, just a different way to "park"

;; ztellman/riddley
;; ztellman/proteus
```
