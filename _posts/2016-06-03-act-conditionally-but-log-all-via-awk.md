---
layout: post
title: Act conditionally but log all via awk
---

Act conditionally but log all via awk

```bash
#!/bin/bash

# refer : http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
# the entire credit goes to above link & its author
# this gist takes one command at a time & shows its power
# the power of functional awk vs. all those imperative languages.
# you might use this tip to cover up your important info in your log files.

# remember awk syntax : awk 'condition {action} condition2 {action2}'
# the first sequence's condition is always true & has a substring action
# the second sequence's condition is 1 === true & default action === print $0
awk '{sub(/pattern/,"****")}1'
```
