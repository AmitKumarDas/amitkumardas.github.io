---
layout: post
title: Surround your variable and positional parameters with curly braces
---

Why should you surround your variable and positional parameters with curly braces ?

```bash
#!/bin/bash

# refer : http://stackoverflow.com/questions/8748831/

# When part is a variable & part is a constant
# e.g. in below snippet foo is the variable & bar is atom i.e. a string

"${foo}bar"

# positional parameters beyond 9
# "${10} ${11}"

# using arrays
# ${array[12]}
```
