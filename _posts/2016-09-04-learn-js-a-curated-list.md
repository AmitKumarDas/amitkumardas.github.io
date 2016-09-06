---

layout: post

title: Learn JavaScript - A curated list
---



### react stamp

refer - [react-stamp](https://github.com/stampit-org/react-stamp)

> Composition is the act of creating an object from a collection of other objects. Many will say this is 
> actually multiple inheritance, not composition. Well, in the classical sense of the word, they're right! 
> However, JavaScript favors prototypal inheritance, and composition is actually Prototypal OO's primary 
> mechanism. Composition encompasses differential inheritance, concatenative inheritance, and functional 
> inheritance.

### javascript langauge's 2 pillars

refer - [2 pillars](https://medium.com/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3#.wkx0wdbgf)

It popularized two paradigms which are extremely important for the evolution of programming:
- Prototypal Inheritance (objects without classes, and prototype delegation, aka OLOO — Objects Linking to Other Objects), 
- Functional Programming (enabled by lambdas with closure)


### Mixins vs Prototype chain

refer - [mixins vs prototype chain](https://davidwalsh.name/javascript-objects-distractions)

> Take any two independent objects, call them A and B (they don't have to be linked via [[Prototype]] at all), 
> and you can still mixin A's stuff into B. If that style of code works for your situation, use it! But just note 
> that it has nothing to do with [[Prototype]] or inheritance. Trying to think of them as related is just a distraction.

### Use of ```this``` in polymorphism

refer - [use of this in polymorphism](https://davidwalsh.name/javascript-objects-distractions)

> Polymorphism is the practice of having the same method or property name at different levels of your 
> "inheritance chain", and then using super-style relative references to access ancestor versions of the same.

> this is not relative, but absolute to the beginning of the call stack. If JS had a super or acurrentThis, 
> then those references would be relative to whatever the currently resolved link in the [[Prototype]] 
> chain was, which would allow you to make a relative reference to a link "above". 

### Objects & JavaScript

refer - [Objects & JavaScript](https://davidwalsh.name/javascript-objects-deconstruction)

> we normally tap into this object mechanism by following the "OO way": we create a function which we use 
> as a "constructor", and we call that function with new so that we can "instantiate" our "class", which we 
> specify with the constructor function together with its subsequent .prototype additions... but all that is like 
> a magician's sleight of hand that dazzles you over here to distract you from what's really going on over there.

