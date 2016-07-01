---
layout: post
title: Multi line vs. single line coding in shell
---

Multi line vs. single line coding in shell

Alternatively, terse coding vs. readable coding in shell

```bash
echo -e "hello\nchalo" |
  while read var;
    do
      echo $var;        
    done

# alternatively - terse but less readable & error prone
some command | while read var; do some_command $var; some_other_command $var; done
```
