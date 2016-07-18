---
layout: post
title: FAQs on golang
---

# Why this article ?

> This helps me in learning or referencing golang quickly.

<br />

## Frequently asked question:

- **semicolon** Do we need them ?
  - No need.

- **code block** Do we have it ?
  - Use ```{}```.

- **multi-valued return** Is it possible ?
  - It is possible & used heavily for error handling.

- **return nil, err** What is the utility of such syntax ?
  - nil may or may not indicate an error.
  - alternative to throwing exceptions.
  - multi-valued return

- **:= vs. =** What is the difference ?

- **if** Does this have any variations from other languages ?
  - It does not mandate use of ```()```.
  - e.g. ```if err != nil && err != io.EOF {..}```

- **all-in-one if statement** Read the sample & understand !!

```
  if err := c.sess.Start(`echo "$SHELL"`); err != nil {..}
```

- **io/ioutil vs. bufio**
  - When to use one ?

- **struct vs class**
  - When to use one ?

- **variable name & data-type**
  - first comes the name & then the data-type.

- **method naming convention**
  - CapitalCase

- **variable naming convention**
  - camelCase

- **func signature - I**
  - func f(x int)

- **func signature - II**
  - func avg(nums []float64) float64

- **func signature - III**
  - func f() (int, int)
  - it refers to a multi return function

- **func signature - IV**
  - func add(args ...int) int
  - it refers to a variadic function

- **func signature - V**
  - func Println(a ...interface{}) (n int, err error)
  - variadic inputs of any type
  - multi-return

- **func signature - VI**
  - func (c *SSHClient) getShell() error

- **func signature - VII**
  - add := func(x, y int) int
  - it refers to a closure function
  - note: func is after the :=

- **defer**
  - moves the call to the end of the enclosing function
  - easy reading
  - handles multiple return statements inside the func
  - runs even when run-time panic occurs
  - i.e. equivalent to a big try .. finally block in a java method

```    
  f, _ := os.Open(filename)
  defer f.Close()
```

- **recover**
  - looks like the catch block as in java.
  - needs to be paired with defer

```
func main() {
  defer func() {
    str := recover()
    fmt.Println(str)
  }()
  panic("PANIC")
}
```

- **formatting & style**
  - gofmt on save - nothing else is accepted

- **path settings, workspaces, etc.**
  - refer [link](https://golang.org/doc/code.html)
  - below are Windows specific

```
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
```

- **Flags**
  - http://bit.ly/GoFlags

- **dot import**
  - Looks similar to static imports in Java
  - Do not use it. Makes code unreadable.
  - Is bad. Favor explicit than implicit.

- **Code Review Comments**
  - bit.ly/GoCodeReview

- **Naming conventions**
  - bit.ly/GoNames

- **install golang on CentOS**

```
curl -O https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz

tar -C /usr/local -xzf go1.6.2.linux-amd64.tar.gz

# update the PATH environment variable.
# export PATH=$PATH:/usr/local/go/bin   @
#      .. edit /etc/profile.d/golang.sh    (system-wide installation)
#      .. or $HOME/.profile                (local)

exit the session & re-login
```

- **golang & creation of workspace for development w.r.t CentOS**

```
$ pwd
/home/goworkspace

$ ls -ltr
drwxr-xr-x. 2 root root  6 Jul 18 10:29 bin
drwxr-xr-x. 2 root root  6 Jul 18 10:29 pkg
drwxr-xr-x. 3 root root 23 Jul 18 10:30 src

$ cat /etc/profile.d/golang.sh
export GOROOT=/usr/local/go
export GOPATH=/home/goworkspace
export PATH=$PATH:/usr/local/go/bin
export PATH=$PATH:$GOPATH/bin
```
