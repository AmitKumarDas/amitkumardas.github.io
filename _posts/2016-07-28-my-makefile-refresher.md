---
layout: post
title: My makefile refresher
---

# Why this article ?

> This helps me in learning or referencing makefile quickly.


<br />

- All for make's **love for files**

```bash

# $@ represents the file that is being made right now
# $< is the first input file
# $^ is the list of all input files
# $? represents all the input files that are newer than target
# In short you can build files using $< and $@ vars
```

<br />

- A bit of **shell scripting** in makefile

```bash

srcfiles := $(shell echo src/{00..99}.txt)
```

- **Tricky** stuff

```bash

# Query - When will one need to use $$< and $$@ ?
```

- Call other targets from within a target

```bash

all-target:
    $(MAKE) a-target # means the make currently in use
    $(MAKE) some-other-target
```

<br />

- variable assignment ```:=``` vs ```=```

```make

# lazy assignment
# will re-assign every time `objects` variable is used
objects = main.o kbd.o command.o display.o \
          insert.o search.o

# immediate assignment
# will consider the older assignment
# variable's value can be mutated
immediate := some_value
```

<br />

- defining prerequisites

```make

# define.h is the prerequisite for entire list of `$(objects)`
$(objects) : define.h

# common.h is the prerequisite for specific .o files
abc.o bef.o zef.o: common.h
```

<br />

- Include other makefiles

```make

include another-makefile
```

<br />

## References

- https://gist.github.com/isaacs/62a2d1825d04437c6f08
- http://makefiletutorial.com/
- http://electric-cloud.com/blog/2009/03/makefile-performance-shell/
- http://electric-cloud.com/blog/2009/08/makefile-performance-built-in-rules/
- http://electric-cloud.com/blog/2009/04/makefile-performance-pattern-specific-variables/
- https://github.com/teracyhq/devops/blob/master/docs/Makefile
- https://bost.ocks.org/mike/make/
- https://github.com/ndmitchell/build-shootout
- https://hackage.haskell.org/package/turtle-1.2.8/docs/Turtle-Tutorial.html
- https://github.com/buildsome/buildsome/blob/master/doc/Presentation.pdf
