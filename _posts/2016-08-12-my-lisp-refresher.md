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
- C-x u
  - redo
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
- C-M-f
  - forward to opening/closing parentheses
- C-M-b
  - backward to opening/closing parentheses
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

### Lisp & its special syntax forms

```lisp

;; Lisp defines a couple of special syntax forms. 
;; The quote (') indicates the next token is a literal. 
;; The quasiquote or backtick (`) indicates the next token is a literal with escapes. 

;; Escapes are indicated by the comma operator.

;; The literal '(1 2 3) is the equivalent of Python's [1, 2, 3]. 
;; You can assign it to another variable or use it in place. 
;; `(1 2 ,x) is the equivalent of Python's [1, 2, x] 
;; where x is a variable previously defined. 
;; This list notation is part of the magic that goes into macros. 
```

<br />

### Basic Types

```clojure

;; Nested Maps - A map literal syntax
{:name {:first "John" :middle "Jacob" :last "Jingleheimerschmidt"}}

;; Map - via - hash-map
(hash-map :a 1 :b 2)
; => {:a 1 :b 2}

;; multiple ways to lookup - use idiomatic way
;; keywords can be used as function - loved by Clojurists

;; vector is a 0-indexed array collection
;; a vector can contain heterogenous types

;; list is a linear collection of values
;; list uses () plus '
;; i.e. '() 

;; read more on performance implications of retrieving from a list vs. vector
```

<br />

### keywords vs. functions

```clojure

;; via keyword
(:a #{:a :b})
; => :a

;; via get
(get #{:a :b} :a)
; => :a

;; via contains?
(contains? #{:a :b} :a)
; => true
```

<br />

### inverted thinking

```clojure

;; selecting a function based on predicate
((and (= 1 1) +) 1 2 3)
; => 6

;; function == data, data == function
((first [+ 0]) 1 2 3)
; => 6

;; clojure functions can take any expression as arguments
;; including other functions
```

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

### Macros are so special - Why ?

```lisp

;; So let us define a macro called lcomp (short for list comprehension). 
;; It's syntax will be exactly like the python 
;; e.g. [x for x in range(10) if x % 2 == 0]

;; vs. lisp's
;; (lcomp x for x in (range 10) if (= (% x 2) 0))
```

```lisp

(defmacro lcomp (expression for var in list conditional conditional-test)
  ;; create a unique variable name for the result
  (let ((result (gensym)))
    ;; the arguments are really code so we can substitute them 
    ;; store nil in the unique variable name generated above
    `(let ((,result nil))
       ;; var is a variable name
       ;; list is the list literal we are suppose to iterate over
       (loop for ,var in ,list
            ;; conditional is if or unless
            ;; conditioanl-test is (= (mod x 2) 0) in our examples
            ,conditional ,conditional-test
            ;; and this is the action from the earlier lisp example
            ;; result = result + [x] in python
            do (setq ,result (append ,result (list ,expression))))
           ;; return the result 
       ,result)))
```

```lisp

;; using above macro in command line

(lcomp x for x in (range 10) if (= (mod x 2) 0))
(0 2 4 6 8)
```

<br />

### Clojure - Protocols for polymorphic dispatch

```clojure

;; http://clojure.org/protocols
```

<br />

### Clojure - Keywords as functions

```clojure

;; http://stackoverflow.com/questions/6915531/why-does-using-keywords-or-symbols-as-functions-to-lookup-values-from-maps-work
```

<br />

### Clojure - Transducers - Functions applied to collections

```clojure

;; http://clojure.org/transducers
```

<br />

### Pixie - Native lisp on RPython

```lisp

;; Pixie from likes of Timothy Baldridge. 
;; Tim is also a key part of Clojure's core.async.

;; Pixie has all the good attributes of Clojure, but has a 10MB runtime. 
;; It's small and fast. How? It's written in RPython and compiles down to native code. 
;; It's got a tracing JIT and full garbage collection. 
;; It's got very fast start-up time and Tim has some benchmarks that demonstrate that it runs reasonably fast.

;; Pixie interoperates with the outside world pretty much the same way Python does... via libffi. 
;; This means your Pixie code can call to C code... to native .so libraries.
```

<br />

### References

```lisp

;; http://www.braveclojure.com/
;; https://notamonadtutorial.com/how-to-earn-your-clojure-white-belt-7e7db68a71e5#.wuff9cdwu
```
