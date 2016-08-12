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

<br />

## Are heterogeneous elements allowed in a list ?

- No

<br />

## How are strings & lists related ?

- Strings are list of characters.
- list operations on Strings is possible.

<br />

## Do you understand [] vs. [[]] vs. [[], [], []] ?

- empty list vs. list containing an empty list vs. ...

<br />

## Good haskell project folder structure

```Haskell

-- refer - https://github.com/BardurArantsson/cqrs

-- project name - cqrs
-- folders:
--   cqrs/cqrs-core,
--   cqrs/cqrs-memory,
--   cqrs-postgresql,
--   cqrs-testkit,
--   cqrs/cqrs-example
```

<br />

## Quick Tips

```Haskell

-- load a script abc.hs in ghci via :l
$ :l abc

$ 'A':" DASHY BOY"
"A DASHY BOY"

$ head [1,2,3,4]
1

$ head []
**exception

$ null []
True

$ 1 `elem` [3,4,5]
False

$ 4 `elem` [3,4,5]
True

-- equality checks
$ 5 /= 5
False
```

<br />

## List Comprehension

```Haskell

$ [x*2 | x <- [1..10]]
[2,4,6,8,10,12,14,16,18,20]

$ [x*2 | x <- [1..10], x*2 >= 12]  
[12,14,16,18,20]

-- list comprehension as a function with arguments
$ boomBangs xs = [ if x < 10 then "BOOM!" else "BANG!" | x <- xs, odd x]
$ boomBangs [7..13]  
["BOOM!","BOOM!","BANG!","BANG!"]

$ let nouns = ["hobo","frog","pope"]  
$ let adjectives = ["lazy","grouchy","scheming"]  
$ [adjective ++ " " ++ noun | adjective <- adjectives, noun <- nouns]  
["lazy hobo","lazy frog","lazy pope","grouchy hobo","grouchy frog",  
"grouchy pope","scheming hobo","scheming frog","scheming pope"]

-- comprehension as a function
-- replace every element of the list with 1
$ length' xs = sum [1 | _ <- xs]

$ removeNonUppercase st = [ c | c <- st, c `elem` ['A'..'Z']]

-- advanced list comprehension
$ let xxs = [[1,3,5,2,3,1,2,4,5],[1,2,3,4,5,6,7,8,9],[1,2,4,2,1,6,3,1,3,2,3,6]]  
$ [ [ x | x <- xs, even x ] | xs <- xxs]  
[[2,2,4],[2,4,6,8],[2,4,2,6,2,6]]

```

<br />

## Types & Typeclasses

```Haskell

:t 'a'  
'a' :: Char

$ :t (True, 'a')  
(True, 'a') :: (Bool, Char)

$ :t "HELLO!"  
"HELLO!" :: [Char]

$ :t 4 == 5  
4 == 5 :: Bool

$ :t head  
head :: [a] -> a

-- tuple & fst
$ :t fst  
fst :: (a, b) -> a

-- == is a function
$ :t (==)  
(==) :: (Eq a) => a -> a -> Bool


-- explicit type declaration - a good practice
removeNonUppercase :: [Char] -> [Char]  
removeNonUppercase st = [ c | c <- st, c `elem` ['A'..'Z']]

addThree :: Int -> Int -> Int -> Int  
addThree x y z = x + y + z
```

## Wish I Knew

```Haskell

- https://github.com/sdiehl/wiwinwlh
```

<br />

## References

- [Learn you Haskell](http://learnyouahaskell.com/chapters)
