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

### Object.create vs. new operator

refer - [object.create vs new operator](https://davidwalsh.name/javascript-objects-deconstruction)

```javascript

function Foo(who) {
    this.me = who;
}

function Bar(who) {
    Foo.call(this,who);
}

Bar.prototype = Object.create(Foo.prototype);
```

> Some people write Bar.prototype = Object.create(Foo.prototype); as Bar.prototype = new Foo();. 
> Both approaches end up with the same linked objects, 

<br />

> where Bar.prototype is an object linked via its[[Prototype]] to Foo.prototype. 
> The only real difference is whether or not the Foo function is called during the creation of Bar.prototype. 
> Depending on your circumstances and intent, you may or may not want that to happen, so 
> let's consider them roughly interchangable but with different purposes.

- We have an object labelled Foo.prototype
- Another object labelled Bar.prototype
- Bar.prototype is [[Prototype]]-linked to Foo.prototype

> I have to manually call (aka, "borrow") the Foo() function (it's not a constructor here, just a normal function call!) 
> from inside ofBar(), and to make sure the this is bound correctly, I have to do the slightly more awkward 
> .call(this) style of code. 

<br />

> the Foo() function isnot related in any useful/practical way to the Bar() function. 
> The Foo() function does not even appear in the "inheritance" (aka "delegation") chain of Bar.prototype object.

<br />

> Any function at one level of the [[Prototype]] chain that wants to call up to an ancestor with the same name must 
> do so via this manual implicit mixin approach just like we did inside of Bar() above. We have no effective way of 
> making relative references up the chain.

<br />

> the only time a .constructor property is added to an object is when that object is the default .prototype attached 
> to a declared function, as is the case of Foo(). When objects are created via new Fn() or Object.create(..) calls, 
> those objects don't get a .constructor added to them. 

<br />

> So if you reference b1.constructor for instance, then you're 
> actually going to delegate up the chain a few links, to Foo.prototype. Of course, Foo.prototype has a .constructor 
> property and it's pointing at Foo like you'd expect. If you plan to rely on the .constructor property (which many do), 
> you need to perform an extra step

```javascript

Bar.prototype = Object.create(Foo.prototype);
Bar.prototype.constructor = Bar;
```

<br />

> Foo() had nothing to do with creating the default Foo.prototype. Foo.prototype defaults to an empty object that 
> was actually constructed by the built-in Object() constructor.
