---
layout: post
title: Experienced programmer's guide to learn ELM - A curated list
---

## Experienced programmer's guide to learn ELM - A curated list

### Different thoughts to a function signature

```elm

-- function signature
add : Int -> Int -> Int
add x y =
  x + y

-- thought 1 : Add 2 integers & return another integer
-- thought 2 : A function that takes one integer as argument & returns a function.
--             This returned function takes an integer & returns an integer.
--             Passing state between functions does not warrant complex coding.
```
