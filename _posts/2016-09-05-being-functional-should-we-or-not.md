---

layout: post

title: Being Functional - Should we or not
---



##  Being Functional - Should we or not ?

It has been hard to convince my team on coding the functional way irrespective of its advantages against 
the imperative techniques. Having said that, I really do not believe we require a functional language to write functional 
code. I have used functional paradigms in Java 8 using java.util.Function interfaces & its lambda offerings. 
The end result made me happy if not ecstatic. ( Well, this led to interface explosion. The limited scope of Java Generics 
was exposed, sooner than I had imagined.)  However, I was able to eliminate lots of boilerplate code. The business logic 
could be reasoned by glancing at the code. In short, this made me realize the way we should program to build a well 
maintained & robust software .


## Java vs. Golang

In fact, I have been pushing functional code in a golang repository called [openebs](https://github.com/openebs/openebs).
I have begun to love golang for its ability to achieve so much in so less effort & lines of code. Way to go golang !! Compare this 
with Java, and you will find lots of libraries (functional java, javaslang, etc to full fledged languages like scala) trying to 
simplify functional programming for the developers. 


## Fucntional vs. Idiomatic

I would prefer DRY, readable & testable code to idiomatic code. Further, I would like to emphasize the fact that 
if idiomatic can not achieve above three characteristics, without being functional, then we should dilute the 
idiomatic stuff with some functional toppings. 

## Summary

Combine this with literate programming & we get a code that is maintainable. Literate programming becomes 
more important when we try to write functional code in imperative languages. I would like to see a world full of 
repositories like [corrode](https://github.com/jameysharp/corrode).
