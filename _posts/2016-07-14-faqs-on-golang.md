---
layout: post
title: FAQs on golang
---

# Why this article ?

> This helps me in learning or referencing golang quickly.

<br />

# Here are the questions:

```go
// [semicolon] Is it required ?

// [code block] How is it done ?

// [multi-valued return] Is it possible ?

// [return nil, err] What is the utility of such combination ?

// [:= vs. =] What is the difference ?

// [if statement] Is it the same usage as other languages ?

// [all-in-one if statement]

// [variable name & data-type] How is the placement done ?

// [method naming convention] ?

// [variable naming convention] ?

// [io/ioutil vs. bufio] ?

// [struct vs class] ?

// [func signature - I]
func f(x int)

// [func signature - II]
func avg(nums []float64) float64

// [func signature - III]
func f() (int, int)

// [func signature - IV]
func add(args ...int) int

// [func signature - V]
func Println(a ...interface{}) (n int, err error)

// [func signature - VI]
func (c *SSHClient) getShell() error

// [func signature - VII]
add := func(x, y int) int

// [defer] ?

// [recover] ?

// [formatting & style] Any specific styling ?

// [Code Review Comments] Any guide ?

// [Naming conventions] Are there any ?

// [dot import] What are these ?

// [Flags] What are these ?

// [path settings, workspaces, etc.] What are these ?

```

<br />

# Time for solutions:

```go
// [semicolon] No need.

// [code block] Use ```{}```.

// [multi-valued return] It is possible & used heavily for error handling.

// [return nil, err] nil may or may not indicate an error.
// Alternative to throwing exceptions.

// [:= vs. =]

// [if] It does not mandate a ```()```.
if err != nil && err != io.EOF {..}

// [variable name & data-type] first comes the name & then the data-type.

// [method naming convention] CapitalCase

// [variable naming convention] camelCase

// [func signature - IV] variadic function

// [func signature - III] multi return

// [func signature - V] variadic values of any type
// assumption - increment is a func here
fmt.Println(increment())

// [all-in-one if statement]
if err := c.sess.Start(`echo "$SHELL"`); err != nil {..}

// [func signature - VII] closure

// [defer] moves the call to the end of the enclosing function
// .. easy reading
// .. handles multiple return statements inside the func
// .. runs even when run-time panic occurs
// .. i.e. equivalent to a big try .. finally block in a java method
f, _ := os.Open(filename)
defer f.Close()

// [recover] Looks like the catch block as in java.
// .. needs to be paired with defer
func main() {
  defer func() {
    str := recover()
    fmt.Println(str)
  }()
  panic("PANIC")
}

// [formatting & style] gofmt on save - nothing else is accepted

// [path settings, workspaces, etc.] https://golang.org/doc/code.html
// [path settings, workspaces, etc.] Below are Windows specific
$ echo $GOPATH
C:\Users\amit\code_src\goworks

$ echo $PATH
C:\Users\amit\code_src\goworks\bin

$ go get github.com/golang/example/hello

$ pwd
/c/Users/amit/code_src/goworks/src

$ ls -ltr
total 0
drwxr-xr-x    2 amit     Administ        0 Jul 15 14:58 bin
drwxr-xr-x    2 amit     Administ        0 Jul 15 14:58 pkg
drwxr-xr-x    3 amit     Administ        0 Jul 15 14:59 src

$ go build github.com/AmitKumarDas/sup

$ cd src/github.com/AmitKumarDas/sup/

$ go build

// [Flags] http://bit.ly/GoFlags

// [dot import] Looks similar to static imports in Java
// .. Do not do it. Makes code unreadable.
// .. Is bad. Favor explicit than implicit.

// [Code Review Comments] bit.ly/GoCodeReview

// [Naming conventions] bit.ly/GoNames
```
