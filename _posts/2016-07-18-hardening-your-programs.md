---
layout: post
title: Hardening your programs
---

# Hardening your programs

## [Destroy all ifs](http://degoes.net/articles/destroy-all-ifs)

- Replace boolean parameters with custom algebraic data types
- Issues arise when there are multiple booleans passed to a function
- Replacing these with ADT pro-actively will be good.

```
type Case = CaseInsensitive | CaseSensitive
type Predicate = Contains | Equals
```
