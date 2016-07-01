---
layout: post
title: Verify via sh if something is installed and running
---

Verify via sh if something is installed and running

```bash
#!/bin/bash

# NOTE - Check mysql is installed and the server running
MYSQL="/usr/bin/mysql"
MYSQL_OPTS="-uroot" #"-prootpassword"

[ -x "$MYSQL" ] && "$MYSQL" $MYSQL_OPTS < /dev/null || exit 0
```
