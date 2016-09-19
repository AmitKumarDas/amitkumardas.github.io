---
layout: post
title: Experienced programmer's guide to learn JavaScript - A curated list
---

## Experienced programmer's guide to learn JavaScript - A curated list

### javascript langauge's 2 pillars

refer - [2 pillars](https://medium.com/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3#.wkx0wdbgf)

It popularized two paradigms which are extremely important for the evolution of programming:
- Prototypal Inheritance
  - objects without classes, &
  - prototype delegation, aka OLOO ~ Objects Linking to Other Objects), 
- Functional Programming (enabled by lambdas with closure)


### Mixins vs Prototype chain

refer - [mixins vs prototype chain](https://davidwalsh.name/javascript-objects-distractions)

- Take any two independent objects, call them A and B (they don't have to be linked via [[Prototype]] at all)
- You can still mixin A's stuff into B. If that style of code works for your situation, use it! 
- However, this has nothing to do with [[Prototype]] or inheritance. 
- Trying to think of them as related is just a distraction.

### Use of ```this``` in polymorphism

refer - [use of this in polymorphism](https://davidwalsh.name/javascript-objects-distractions)

- The practice of having the same method or property name at different levels of "inheritance chain", &
- then using super-style relative references to access ancestor versions of the same.
- This is not relative, but absolute to the beginning of the call stack. 
- If JS had a super then those references would be relative to whatever the currently resolved link in the [[Prototype]] 
  - chain was, which would allow you to make a relative reference to a link "above". 

### Objects & JavaScript

refer - [Objects & JavaScript](https://davidwalsh.name/javascript-objects-deconstruction)

- We normally tap into this object mechanism by following the "OO way": 
  - we create a function which we use as a "constructor", &
  - we call that function with new so that we can "instantiate" our "class", 
  - Finally we specify with the constructor function together with its subsequent .prototype additions...
- but what's really going on !!!

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

- Some people write 
  - Bar.prototype = Object.create(Foo.prototype); 
  - as 
  - Bar.prototype = new Foo();
- Both approaches end up with the same linked objects where 
  - Bar.prototype is an object linked via its[[Prototype]] to Foo.prototype. 
- The only real difference is:
  - whether or not the Foo function is called during the creation of Bar.prototype. 
- Depending on your circumstances and intent, you may or may not want that to happen, so 
  - let's consider them roughly interchangable but with different purposes.

#### Few observations

- NOTE - We have an **object** labelled Foo.prototype
- NOTE - Another object labelled Bar.prototype
- NOTE - Bar.prototype is [[Prototype]]-linked to Foo.prototype

#### Did we get inheritance ?

- I have to manually call the Foo() function (it's not a constructor here, just a normal function call!)
  - from inside of Bar(), &
  - to make sure the this is bound correctly, I have to do ```.call(this)``` style of code. 
- Foo() function is not related in any useful/practical way to the Bar() function. 
- Foo() function does not even appear in the "inheritance" (aka "delegation") chain of Bar.prototype object.
- Any function at one level of the [[Prototype]] chain that wants to call up to an ancestor with the same name must 
  - do so via this manual implicit mixin approach just like we did inside of Bar() above. 
- We have no effective way of making relative references up the chain.

#### What about .constructor ?

- The only time a .constructor property is added to an object is:
  - when that object is the default .prototype attached to a declared function, as is the case of Foo(). 
- When objects are created via new Fn() or Object.create(..) calls, 
  - those objects don't get a .constructor added to them. 
- So if you reference b1.constructor for instance, then you're 
  - actually going to delegate up the chain a few links, to Foo.prototype. 
  - Of course, Foo.prototype has a .constructor property and it's pointing at Foo.
  - If you plan to rely on the .constructor property, you need to perform an extra step

<br />

```javascript

Bar.prototype = Object.create(Foo.prototype);
Bar.prototype.constructor = Bar; // extra step
```

#### What does executing Foo() signify ?

- NOTE - Foo() had nothing to do with creating the default Foo.prototype. 
- Foo.prototype defaults to an empty object constructed by the built-in Object() constructor.

### Just Objects - No functions - No constructors

refer - [just objects](https://davidwalsh.name/javascript-objects-deconstruction)

```javascript

var Foo = {
    init: function(who) {
        this.me = who;
    },
    identify: function() {
        return "I am " + this.me;
    }
};

var Bar = Object.create(Foo);

var b1 = Object.create(Bar);
```

#### Points to note:

- Bar & Foo are just objects
- They were linked via Object.create(...)
- I feel, the primary advantage is code readability even if I am from a different prog background.
 
### Functions & Constructors vs Objects - Through lens of Reflection

refer - [lens of reflection](https://davidwalsh.name/javascript-objects-deconstruction)

```javascript

// If functions & constructors approach then:
b1 instanceof Bar; // true
b2 instanceof Bar; // true
b1 instanceof Foo; // true
b2 instanceof Foo; // true
Bar.prototype instanceof Foo; // true
Object.getPrototypeOf(b1) === Bar.prototype; // true
Object.getPrototypeOf(b2) === Bar.prototype; // true
Object.getPrototypeOf(Bar.prototype) === Foo.prototype; // true

// If pure objects approach then:
Bar.isPrototypeOf(b1); // true
Bar.isPrototypeOf(b2); // true
Foo.isPrototypeOf(b1); // true
Foo.isPrototypeOf(b2); // true
Foo.isPrototypeOf(Bar); // true
Object.getPrototypeOf(b1) === Bar; // true
Object.getPrototypeOf(b2) === Bar; // true
Object.getPrototypeOf(Bar) === Foo; // true
```

### Concatenative Inheritance

refer - [concantenative inheritance](https://medium.com/javascript-scene/common-misconceptions-about-inheritance-in-javascript-d5d9bab29b0a#.omzsiww6m)

```javascript

// I'm not sure Object.assign() is available (ES6)
// so this time I'll use Lodash. It's like Underscore,
// with 200% more awesome. You could also use
// jQuery.extend() or Underscore's _.extend()
var assign = require('lodash/object/assign');

var skydiving = require('skydiving');
var ninja = require('ninja');
var mouse = require('mouse');
var wingsuit = require('wingsuit');

// The amount of awesome in this next bit might be too much
// for seniors with heart conditions or young children.
var skydivingNinjaMouseWithWingsuit = assign({}, // create a new object
  skydiving, ninja, mouse, wingsuit); // copy all the awesome to it.
```

<br />

- The prototypes that this technique inherits from are referred to as **exemplar prototypes**
- exemplar differ from delegate prototypes where
  - former **copies from prototypes** & latter **delegates to prototypes**.

### Do you rely on instanceof ?

refer - [relying on instanceof](https://medium.com/javascript-scene/common-misconceptions-about-inheritance-in-javascript-d5d9bab29b0a#.omzsiww6m)

- `instanceof` does not do type checking we are used to in strongly typed languages
- it does an identity check on the prototype object

### Do you need constructors for data privacy !!

refer - [constructors & data privacy](https://medium.com/javascript-scene/common-misconceptions-about-inheritance-in-javascript-d5d9bab29b0a#.omzsiww6m)

- It is not constructors rather closures that are used for data privacy
- Any function can have a closure embedded in it for data privacy
 
### No classes - show me code

refer - [no classes](https://medium.com/javascript-scene/common-misconceptions-about-inheritance-in-javascript-d5d9bab29b0a#.omzsiww6m)

```javascript

// no more var, but let in ES6
let animal = {
  animalType: 'animal',
 
  // method shortcut, in ES6
  describe () {
    // templating available only in ES6
    return `An ${this.animalType} with ${this.furColor} fur, 
      ${this.legs} legs, and a ${this.tail} tail.`;
  }
};
 
let mouseFactory = function mouseFactory () {
  let secret = 'secret agent';
 
  // Object.assign provides the concatenative inheritance via exemplar prototypes
  // Object.create allows attaching delegate prototypes
  return Object.assign(Object.create(animal), {
    animalType: 'mouse',
    furColor: 'brown',
    legs: 4,
    tail: 'long, skinny',
    // closure for data privacy
    profession () {
      return secret;
    }
  });
};
 
let james = mouseFactory();
```

### Constructors - ES2015 vs Stampit vs Golang

```javascript
// ES2015 way
class Qos {
	constructor({iops, throughput}){
		this.iops = iops;
		this.throughput = throughput;
	}
}

const qos = new Qos({1000, 4000});

// Stampit v3
const Qos = overrides('iops', 'throughput');

const qos = Qos({iops: 1000, throughput: 4000});

// Golang
type struct Qos {
  Iops uint64
  Throughput uint64
}

type NewQos(iops uint64, tput uint64) *Qos{
  return &Qos{
    Iops: iops, 
    Throughput: tput,
  }
}
```

### How was stampit made ?

refer - [making of stampit](https://medium.com/@koresar/fun-with-stamps-episode-4-implementing-stamps-in-30-loc-e52f5c17dcfe#.qvq2w2hzq)

> This might hold true when designing any micro library that allows for intuitive compositional strategies.

- A composable object i.e. POJO
- A compose function that composes variadic number of composables
- A create stamp function that creates a object of above merged composables
- A create factory function that handles internals of JS e.g. Object.create({...})

### White-box reuse vs. black-box reuse

refer - [composition over inheritance](https://medium.com/@FennNaten/composition-over-inheritance-the-importance-of-context-d8916f041a7e#.yraotkgtn)

- white-box === visibility. inheritance === internal of parent is visible
- black-box === opaque. composition === internals are not visible

### Composition all the way down

refer - [composition over inheritance](https://medium.com/@FennNaten/composition-over-inheritance-the-importance-of-context-d8916f041a7e#.yraotkgtn)

```javascript

// Imagine this as a behavorial interface w.r.t Golang/Java
// Single Responsibility Principle
const reader = (delay) => ({
    lastRead: null,
    delay: delay,
    canRead: function(now) {
        //...
    }
    read: function(){
        //...
    }
});

// Imagine this as a behavorial interface w.r.t Golang/Java
// Single Responsibility Principle
const writer = (delay) => ({
    lastWrite: null,
    delay: delay,
    canWrite: function(now) {
        //...
    }
    write: function(bytes){
        //...
    }
});

// Issue with concatenative inheritance
const file = (readDelay, writeDelay, reader, writer) => Object.assign({}, reader(readDelay), writer(writeDelay));
let abcFile = file(4, 8, reader, writer);
console.log(abcFile.delay) //8

// Alternative compositional style
const readWriterFile = (readDelay, writeDelay, reader, writer) =>({
 readerImpl: reader(readDelay),
 writerImpl: writer(writeDelay),
 read: () => this.readerImpl.read(),
 write: (bytes) => this.writerImpl.write(bytes)
})

// Yet another style
const reader = (delay) => {
  return {
    getReadDelay(){
      //...
    },
    read: function(){
      //...
    }
  }
}
// Similarly writer will have getWriteDelay()
// & concatenative inheritance will work well.
```

### Separation, Composition, Property Hiding, Logic Placement - Puzzles

refer - [separation & composition](https://medium.com/@koresar/fun-with-stamps-episode-5-composition-design-pattern-fe55721c571a#.r1f5fzwu4)

- A separate file that will hold the behavior implementations
- A separate file that will hold the composed behaviors
- Make use of const objects
- Minimize using `this`
- Use of const will reduce the use of `this`
- make use of stampit configuration property for hiding purposes
- The best way to verify is via code testability

<br />

### JavaScript syntax

- refer - [JS Queries](https://github.com/nishant8BITS/123-Essential-JavaScript-Interview-Question)

<br />

#### null check

```javascript

function GetName(context) {
  return context && context.name;
}
```

#### check the type

```javascript

if(Object.prototype.toString.call(someobj) === '[object Array]'){...}
```

<br />

#### run-time definition vs. parse-time definition

```javascript
<script>
  hi(); // error
  var hi = function () {};
</script>

<script>
  hi(); //ok
  function hi() {};
</script>
```

### Reverse Engineering st2 web interface design

refer - [st2web](https://github.com/StackStorm/st2web/)

#### st2-history-child

- a folder/component that consists of
  - template.html, style.less, index.js, & history-child.directive.js
- index registers the directive & exports it as a angular module
  - export follows a namespace
  - e.g. ```main.modules.st2HistoryChild```
  - registration is done via name of the directive
- directive does the following:
  - sets certain well understood properties
  - sets task name in a ```clever way```

#### queries in st2web

- $event.stopPropagation()
- this.$apply() & bind(this)
