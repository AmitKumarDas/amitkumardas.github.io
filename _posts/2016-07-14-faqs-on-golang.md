---
layout: post
title: FAQs on golang
---

# Why this article ?

> This helps me in learning or referencing golang quickly.

<br />

## Here are the questions:

- **semicolon**
  - Is it required ?

- **code block**
  - How is it done ?

- **multi-valued return**
  - Is it possible ?

- **return nil, err**
  - What is the utility of such combination ?

- **:= vs. =** 
  - What is the difference ?

- **if statement**
  - Is it the same usage as other languages ?

- **variable name & data-type**
  - How is the placement done ?

- **method naming convention**
  - Are there any ?

- **variable naming convention**
  - Are there any ?

- **io/ioutil vs. bufio**
  - When to use one ?

- **struct vs class**
  - When to use one ?

- **func signature - I**
  - func f(x int)

- **func signature - II**
  - func avg(nums []float64) float64

- **func signature - III**
  - func f() (int, int)

- **func signature - IV**
  - func add(args ...int) int

- **func signature - V**
  - func Println(a ...interface{}) (n int, err error)

- **func signature - VI**
  - func (c *SSHClient) getShell() error

- **func signature - VII**
  - add := func(x, y int) int

- **defer**
  - What is it? 

- **recover**
  - What is it?

- **formatting & style**
  - Any specific styling ?

- **Code Review Comments**
  - Any guide ?

- **Naming conventions**
  - Are there any ?

- **dot import**
  - What are these ?

- **Flags**
  - What are these ?

- **path settings, workspaces, etc.**
  - What are these ?

- **install golang on CentOS**
  - Any steps ?

- **golang & creation of workspace for development** 
  - Any steps ?

<br />

## Time for solutions:

- **semicolon**
  - No need.

- **code block**
  - Use ```{}```.

- **multi-valued return**
  - It is possible & used heavily for error handling.

- **return nil, err**
  - nil may or may not indicate an error.
  - Alternative to throwing exceptions.

- **:= vs. =**

- **if**
  - It does not mandate use of ```()```.
  - e.g. ```if err != nil && err != io.EOF {..}```

- **all-in-one if statement**
  - if err := c.sess.Start(`echo "$SHELL"`); err != nil {..}

- **variable name & data-type**
  - first comes the name & then the data-type.

- **method naming convention**
  - CapitalCase

- **variable naming convention**
  - camelCase

- **func signature - IV**
  - it refers to a variadic function

- **func signature - III**
  - it refers to a multi return function

- **func signature - V**
  - variadic values of any type

- **func signature - VII**
  - it refers to a closure function

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
