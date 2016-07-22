---
layout: post
title: My golang refresher
---

# Why this article ?

> This helps me in learning or referencing golang quickly.

<br />

## Frequently asked question:

- **main** - What is it ?

> Every standalone Go program contains a package called main and its main function,
> after initialization, is where execution starts. The main.main function takes 
> no arguments and returns no value.

<br />

- **semicolon** - Do we need them ?
  - No need.

<br />

- **code block** - Do we have it ?
  - Use ```{}```.

<br />

- **multi-valued return** Is it possible ?
  - It is possible & used heavily for error handling.

<br />

- **return nil, err** What is the utility of such syntax ?
  - nil may or may not indicate an error.
  - alternative to throwing exceptions.
  - multi-valued return

<br />

- **constants** - Are there any restrictions ?
  - String, numbers or boolean can be constants.

<br />

- **(  )** - Does it signify anything special ?
  - It is used for grouping:
  - NOTE - Do not mix them up.

```go
import ( 
)

var(  
)

const(  
)
```

<br />

- **enumerated constants** - What are these ?
  - these are incremented by 1 starting from 0

```go
// use of keyword iota

const(
  a = iota // a == 0
  b = iota // b == 1
  c        // implicitly c == iota, c == 2
)
```

<br />

- **:= vs. =** What is the difference ?
 
```go

var var1, var2, var3 type = value1, value2, value3

// above is same as below without any type
var var1, var2, var3  = value1, value2, value3

// omit var and type, 
// and use ':=' instead of '=' inside the function body
func test(){
    var1, var2, var3 := value1, value2, value3
}
```

<br />

- **if** Does this have any variations from other languages ?
  - It does not mandate use of ```()```.
  - e.g. ```if err != nil && err != io.EOF {..}```

<br />

- **all-in-one if statement** Read the sample & understand !!

```go
  if err := c.sess.Start(`echo "$SHELL"`); err != nil {..}
```

<br />

- **io/ioutil vs. bufio**
  - When to use one ?

<br />

- **struct vs class**
  - When to use one ?

<br />

- **variable name & data-type**
  - first comes the name & then the data-type.

<br />

- **method naming convention**
  - CapitalCase

<br />

- **variable naming convention**
  - camelCase

<br />

- **func signatures**

```go

	func f(x int)
	
	func avg(nums []float64) float64

	// it refers to a multi return function  
	func f() (int, int)

	// it refers to a variadic function
	func add(args ...int) int
  
	// variadic inputs of any type
  // multi-return
  func Println(a ...interface{}) (n int, err error)
  
  func (c *SSHClient) getShell() error
  
  // it refers to a closure function
  // note: func is after the :=
  add := func(x, y int) int
```

<br />

- **defer**

```go  

  // moves the call to the end of the enclosing function
  // easy reading
  // handles multiple return statements inside the func
  // runs even when run-time panic occurs
  // i.e. equivalent to a big try .. finally block in a java method
  
  f, _ := os.Open(filename)
  defer f.Close()
```

<br />

- **Error Handling**

```go

// May use errors package to create new errors.
// err = errors.New("an error")
// The returned error can be treated as a string by either:
// accessing err.Error(), or 
// using the fmt package functions (e.g. fmt.Println(err)).

var (
	ErrNoDefault = fmt.Errorf("Error: No %q machine exists.", defaultMachineName)
	ErrTooManyArguments = errors.New("Error: Too many arguments given")

	osExit = func(code int) { os.Exit(code) }
)

// usage
osExit(3)
return

// usage
return "", ErrNoDefault

// usage
fmt.Fprintln(os.Stderr, err)
os.Exit(1)
```

<br />

- **recover**

```go

// looks like the catch block as in java.
// needs to be paired with defer
func main() {
  defer func() {
    str := recover()
    fmt.Println(str)
  }()
  panic("PANIC")
}
```

<br />

- **formatting & style**

```bash

  - gofmt on save - nothing else is accepted
```

<br />

- **path settings, workspaces, etc.**
  
```bash

- refer https://golang.org/doc/code.html
- below are Windows specific
 
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

<br />

- **Flags**

```bash

  - refer http://bit.ly/GoFlags
```

<br />

- **dot import**

```bash

  - Looks similar to static imports in Java
  - Do not use it. Makes code unreadable.
  - Is bad. Favor explicit than implicit.
```

<br />

- **Code Review Comments**

```bash

	- refer bit.ly/GoCodeReview
```

<br />

- **Naming conventions**

```bash

  - refer bit.ly/GoNames
```

<br />

- **install golang on CentOS**

```bash

curl -O https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz

tar -C /usr/local -xzf go1.6.2.linux-amd64.tar.gz

# update the PATH environment variable.
# export PATH=$PATH:/usr/local/go/bin   @
#      .. edit /etc/profile.d/golang.sh    (system-wide installation)
#      .. or $HOME/.profile                (local)

exit the session & re-login
```

<br />

- **golang & creation of workspace for development w.r.t CentOS**

```bash

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

<br />

- **3rd party lib management**

```bash

  - May use govendor
  - go get -u github.com/kardianos/govendor
```

<br />

- **creating a new golang project**

```bash

  - install govendor
  - navigate to your project folder
    - e.g. pwd
    - /home/goworkspace/src/github.com/openebs/openebs
  - initialize your project with govendor
    - govendor init
  - install the 3rd party libraries
    - govendor fetch github.com/urfave/cli/@=v1
```

<br />

- **go keyword** - What is it ?
  - A "go" statement starts the execution of a function call
  - As an independent concurrent thread of control, or goroutine,
  - All these are within the same address space.

```go
  go Server()
```

<br />

- **Concurrency** How to get it right ?

```bash

  - use right tools
  - when sharing memory between goroutines - use a mutex
  - when orchestrating goroutines - use channels
  - channels orchestrate; mutexes serialize.
  - refer [go-proverbs.github.io](http://go-proverbs.github.io/)
```

<br />

- **govendor tips**

```bash

- install or update govendor
  - go get -u github.com/kardianos/govendor
  - this places the source code @ $GOPATH/src
  - then compiles the package & places govendor binary in $GOPATH/bin

- get started
  - govendor init

- add a dependency
  - inside your project...
  - govendor fetch github.com/amit/das

- update an existing dependency
  - inside the your project..
  - govendor fetch github.com/openebs/openebs@master

- more
  - https://github.com/kardianos/govendor/wiki/Govendor-CheatSheet
```

<br />

- **git tips**
 
```bash
- git set user name
  - git config --global user.name "AmitKumarDas"
  - git config --get user.name
- git set user email
  - git config --global user.email "amit.das@cloudbyte.com"
  - git config --get user.email
- git add existing project into git
  - cd existing_folder
  - git init
  - git remote add origin http://20.11.1.101/root/openebs.git
  - git add .
  - git commit
  - git push -u origin master
- git commit history
  - git log --pretty=oneline
  - git log --pretty=format:"%h - %an, %ar : %s"
- git learn the origin
  - git config --get remote.origin.url
- git learn the origin if referential integrity is intact
  - git remote show origin
- git update a repository
  - git pull origin master
```

<br />

## Other useful links:

- [golang & use of shell commands](http://nathanleclaire.com/blog/2014/12/29/shelled-out-commands-in-golang/)
- [govendor](https://github.com/kardianos/govendor)
- [Let us learn go](http://go-book.appspot.com/index.html)
