---
layout: post
title: My makefile refresher
---

## Why this article ?

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

<br />

- **Tricky** stuff

```bash

# Query - When will one need to use $$< and $$@ ?
```

<br />

- Call other targets from within a target

```bash

all-target:
    $(MAKE) a-target # means the make currently in use
    $(MAKE) some-other-target
```

<br />

- variable assignment ```:=``` vs ```=```

```bash

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

```bash

# define.h is the prerequisite for entire list of `$(objects)`
$(objects) : define.h

# common.h is the prerequisite for specific .o files
abc.o bef.o zef.o: common.h
```

<br />

- Include other makefiles

```bash

include another-makefile
```

<br />

## References

- [A make gist](https://gist.github.com/isaacs/62a2d1825d04437c6f08)
- [Make tutorial](http://makefiletutorial.com/)
- [Make blog - 1](http://electric-cloud.com/blog/2009/03/makefile-performance-shell/)
- [Make blog - 2](http://electric-cloud.com/blog/2009/08/makefile-performance-built-in-rules/)
- [Make blog - 3](http://electric-cloud.com/blog/2009/04/makefile-performance-pattern-specific-variables/)
- [A sample makefile](https://github.com/teracyhq/devops/blob/master/docs/Makefile)
- [Make blog - 4](https://bost.ocks.org/mike/make/)
- [Build shootout](https://github.com/ndmitchell/build-shootout)
- [Turtle](https://hackage.haskell.org/package/turtle-1.2.8/docs/Turtle-Tutorial.html)
- [Buildsome](https://github.com/buildsome/buildsome/blob/master/doc/Presentation.pdf)
