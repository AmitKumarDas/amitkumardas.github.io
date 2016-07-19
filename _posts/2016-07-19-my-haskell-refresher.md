---
layout: post
title: My Haskell refresher
---

# My Haskell refresher

Hopefully this will keep my Haskell knowledge fresh.

<br />

## Why is Haskell lazy ?

```Haskell
  > xs = [1,2,3,4,5,6,7,8]
  > doubleMe(doubleMe(doubleMe(xs)))

  -- There will not be any **redundant** iterations like in other languages
```

<br />

## How does Haskell use colons ?

```Haskell
  -- Haskell uses colons in so many ways

  [:] :t, :l, :r, ::, :set prompt "ghci> ", cons operator
```

<br />

## What is the output of 5 * -3 ?

- compilation error

<br />

## What is the output of 5 + "das" ?

- compilation error

<br />

## Can you think of a simplest function composition ?

```Haskell
doubleUs x y = doubleMe x + doubleMe y
```

<br />

## Is there any specialty in if then else statements ?

```Haskell
-- else is mandatory
```

## Are heterogeneous elements allowed in a list ?

- No

## How are strings & lists related ?

- Strings are list of characters.
- list operations on Strings is possible.

## Do you understand [] vs. [[]] vs. [[], [], []] ?

- empty list vs. list containing an empty list vs. ...

- [string & list] How are they related ?
- [!!] When is !! used ?
- [: vs. ++] operator wars. Do you understand the usage difference ?
- [square brackets]
- [list index] index in a list start at ?

<br />

## References

- [Learn you Haskell](http://learnyouahaskell.com/chapters)
