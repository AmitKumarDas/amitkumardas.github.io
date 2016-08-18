---
layout: post
title: My lisp refresher
---

# Why this article ?

> This helps me in learning or referencing clojure, lfe, etc quickly.

<br />

## emacs - often used shortcuts

- C-/
  - undo changes
- C-k
  - kill line after the pointer
- C-g
  - abort the current emacs action
  - will not close emacs
- C-x C-c
  - exit emacs
- C-y
  - yank
- C-x C-f
  - open a file
- C-x C-s
  - save a file
- C-a
  - move to **beginning**
- C-e
  - move to **end**
- M-g g
  - go to line
- M-x
  - will expose you to all the **commands**
- M-f
  - forward by word
- M-b
  - back by word
- C-x b
  - to change buffer
- C-x k
  - closing a buffer, rather killing it

<br />

## emacs as clojure editor

- http://www.braveclojure.com/basic-emacs/

<br />

## emacs as go editor

- http://tleyden.github.io/blog/2014/05/22/configure-emacs-as-a-go-editor-from-scratch/
- http://tleyden.github.io/blog/2014/05/27/configure-emacs-as-a-go-editor-from-scratch-part-2/
- M-x godoc
  - to autocomplete paths of packages
- M-.
  - get inside the function definition
- M-*
  - go back
  - refer - https://github.com/dominikh/go-mode.el
- Autocompletion
  - https://github.com/nsf/gocode
- Call hierarchy
  - M-x go-oracle-set-scope
  - github.com/openebs/openebs/cmd/openebsd
  - you always need to give it a main package
  - M-x go-oracle-callers
  - or
  - C-c C-o <

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
