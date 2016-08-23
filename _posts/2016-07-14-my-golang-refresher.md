---
layout: post
title: My golang refresher
---

## Why this article ?

> This helps me in learning or referencing golang quickly.

<br />

### main - What is it ?

```go

Every standalone Go program contains a package called main and its main function,
after initialization, is where execution starts. The main.main function takes 
no arguments and returns no value.
```

<br />

### Cheatsheet

- [link](https://github.com/a8m/go-lang-cheat-sheet)
- [link 2](https://github.com/golang/go/wiki)

<br />

### Design References - Being Functional !!

```go

// refer - https://www.youtube.com/watch?v=xyDkyFjzFVc

// Single method interface
type Creator interface {
	Create(*types.Input) (*types.Output, error)
}

// A typed function that implements Creator interface
// NOTE - It is derived from signature !! No explicit stuff !!
type CreatorFunc func(*types.Input) (*types.Output, error)

// Meant to be used as a closure
func (f CreatorFunc) Create(ip *types.Input) (*types.Output, error) {
	// return the output of f
	return f(ip)
}

// A typed decorator - to add optional behavior
type CreateDecorator func(Creator) Creator

// Audit decorator to log the create requests
// NOTE - 3 return statements
func Auditing(l *log.Logger) CreateDecorator {
	return func(c Creator) Creator {
		return CreatorFunc(func (ip *types.Input) (*types.Output, error) {
			l.log("you are creating !!")
			return c.Create(ip)
		})
	}
}

// Instrumenting decorator to instrument the create requests
// NOTE - 3 return statements
func Instrumentation(requests Counter, latency Historgram) CreateDecorator {
	return func (c Creator) Creator {
		return CreatorFunc( func(ip *types.Input) (*types.Output, error) {
			defer func(start start.Time){
				latency.Observe(time.Since(start).Nanoseconds())
				requests.Add(1)
			}(time.Now())
			return c.Create(ip)
		})
	}
}
```

<br />

### Design References - Code Samples

```go

// Contracts - Interface based design
// https://github.com/vulcand/vulcand/blob/master/engine/engine.go

// Just Structs & ToString
// https://github.com/vulcand/vulcand/blob/master/engine/events.go

// Bound to Json
// https://github.com/vulcand/vulcand/blob/master/engine/json.go

// Business logic
// https://github.com/vulcand/vulcand/blob/master/engine/model.go

// Expose as Http service
// https://github.com/vulcand/vulcand/blob/master/api/api.go
```

<br />

### Design References - Code Samples - Non business logic approach

```go

// https://github.com/kanaka/mal/blob/master/go/src/types/types.go
// https://github.com/kanaka/mal/blob/master/go/src/env/env.go
// https://github.com/kanaka/mal/blob/master/go/src/core/core.go
```

<br />

### Design References - Others

```go

// https://husobee.github.io/golang/validation/2016/01/08/input-validation.html
// https://github.com/luciotato/golang-notes/blob/master/OOP.md
```

<br />

### Custom types & primitives

```go

type Year uint16

// Baked in validation & then unboxing !!
func AsUint16(val interface{}) uint16 {
	ref := reflect.ValueOf(val)
	if ref.Kind() != reflect.Uint16 {
		return 0
	}
	return uint16(ref.Uint())
}

func (y Year) ToUint16() uint16 { 
    return uint16(y)
}

func main() {
	fmt.Println(AsUint16(Year(2015)))
	fmt.Println(Year(2015).ToUint16())
}
```

<br />

### Arrays & Slices

```go

// We deal with an array when we create it with a size:
names := [2]string{"hello", "world"}
//or
names := [2]string{}

// These are slices
names := []string{"hello", "world"}
//or
names := make([]string, 2)
//or
var names []string
```

<br />

### Complex requirements yet simple implementation

```go

// refer - io/io.go

// If your requirements is going to be dangerously complex !
// Yet you do not want your implementation to spiral out of control !

///////////////
// Design Step 1 - Group various interfaces to produce a new interface type.
///////////////

type Writer interface {
        Write(p []byte) (n int, err error)
}
Writer is the interface that wraps the basic Write method.

type Reader interface {
        Read(p []byte) (n int, err error)
}
Reader is the interface that wraps the basic Read method.

type Seeker interface {
        Seek(offset int64, whence int) (int64, error)
}
Seeker is the interface that wraps the basic Seek method.

type Closer interface {
        Close() error
}
Closer is the interface that wraps the basic Close method.

type ReadWriter interface {
        Reader
        Writer
}
ReadWriter is the interface that groups the basic Read and Write methods.

type ReadWriteSeeker interface {
        Reader
        Writer
        Seeker
}
ReadWriteSeeker is the interface that groups the basic Read, Write and Seek methods.

///////////////
// Design Step 2 - Create interface that use existing interfaces to achieve its desired objective.
///////////////

type ReaderFrom interface {
        ReadFrom(r Reader) (n int64, err error)
}
ReaderFrom is the interface that wraps the ReadFrom method.
ReadFrom reads data from r until EOF or error. 

type WriterTo interface {
        WriteTo(w Writer) (n int64, err error)
}

type ReaderAt interface {
        ReadAt(p []byte, off int64) (n int, err error)
}

///////////////
// Design Usage  - part 1
///////////////

func LimitReader(r Reader, n int64) Reader { 
        return &LimitedReader{r, n} 
}

type LimitedReader struct {
        R Reader // underlying reader
        N int64  // max bytes remaining
}

func (l *LimitedReader) Read(p []byte) (n int, err error) {
        // ...
        return
}

///////////////
// Design Usage  - part 2
///////////////

func NewSectionReader(r ReaderAt, off int64, n int64) *SectionReader {
        return &SectionReader{r, off, off, off + n}
}

// SectionReader implements Read, Seek, and ReadAt on a section
// of an underlying ReaderAt.
type SectionReader struct {
        r     ReaderAt
        base  int64
        off   int64
        limit int64
}

func (s *SectionReader) Read(p []byte) (n int, err error) {
        if s.off >= s.limit {
                return 0, EOF
        }
        // ...
        return
}

func (s *SectionReader) ReadAt(p []byte, off int64) (n int, err error) {
        if off < 0 || off >= s.limit-s.base {
                return 0, EOF
        }
        off += s.base
        // ...
}

func (s *SectionReader) Seek(offset int64, whence int) (int64, error) {        
        // ...
}
```

```

- A simplified version can be found below:

- http://golangtutorials.blogspot.in/2011/06/polymorphism-in-go.html
- http://www.deangalvin.com/2015/08/04/polymorphism-in-golang/
```

<br />

### Types & Command pattern -- refer - conform library

```go

var patterns = map[string]*regexp.Regexp{
	"numbers":    regexp.MustCompile("[0-9]"),
	"nonNumbers": regexp.MustCompile("[^0-9]"),
	"alpha":      regexp.MustCompile("[\\pL]"),
	"nonAlpha":   regexp.MustCompile("[^\\pL]"),
	"name":       regexp.MustCompile("[\\p{L}]([\\p{L}|[:space:]|-]*[\\p{L}])*"),
}

// usage
func onlyNumbers(s string) string {
	return patterns["nonNumbers"].ReplaceAllLiteralString(s, "")
}

```

<br />

### Composable, Maintainable, Readable code in golang

```go

// Achieving DRY, testable, composable code in imperative languages is an uphill task.
// In Java, you might want to make use of java.util.function package
// You will be able to get most with lot of code verbosity

// However, it is simpler to understand & implement in golang
// This is derived out of Rob Pike's self referential functions & design article.
```

<br />

```go

package storage

// 1. Define a type
// Mark the case-sensitiveness of option
type option func(*Storage)

// 2. Implement a business function
// Mark the use of closure than direct setting of the property
// Notice, that the client invocation to this method will be very simple
// i.e. storage.Capacity(100)
func Capacity(cap int) option {
    return func(s *Storage) {
        s.capacity = cap
    }
}

// 3. Utility to set various options that are defined now or in future
// Mark the use of variadic arguments
func (s *Storage) Option(opts ...option) {
  for _, opt := range opts {
    opt(s)
  }
}
```

<br />

- **References**
  - [Functional Options](https://www.reddit.com/r/golang/comments/2jf63r/dotgo_functional_options_for_friendly_apis/)
  - [Self Referential Function](https://commandcenter.blogspot.in/2014/01/self-referential-functions-and-design.html?m=1)
  - [Config struct vs. Functional options](http://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis)

<br />

- **Chain of Responsibility - Pass through the filters**
 
```go

//--------------------------------------------------------------
// file - api/server/middleware/middleware.go
//--------------------------------------------------------------

// Middleware is an interface to allow the use of ordinary functions as OpenEBS API filters.
// Any struct that has the appropriate signature can be registered as a middleware.
type Middleware interface {
	WrapHandler(func(ctx context.Context) error) func(ctx context.Context) error
}

//--------------------------------------------------------------
// filter 1 -- file - api/server/middleware/debug.go
//--------------------------------------------------------------
func DebugMiddleware(handler func(ctx context.Context) error) func(ctx context.Context) error {
	return func(ctx context.Context) error {
		// ... ...
		return handler(ctx)
	}
}

//--------------------------------------------------------------
// filter 2 -- file - api/server/middleware/useragent.go
//--------------------------------------------------------------

type UAMiddleware struct {
	serverVersion string
}

func NewUAMiddleware(s string) UAMiddleware {
	return UAMiddleware{
		serverVersion: s,
	}
}
func (u UAMiddleware) WrapHandler(handler func(ctx context.Context) error) func(ctx context.Context) error {
	return func(ctx context.Context) error {
		// ... ...
		return handler(ctx)
	}
}

//--------------------------------------------------------------
// filter 2 -- file - api/server/middleware/version.go
//--------------------------------------------------------------

type badRequestError struct {
	error
}

func (badRequestError) HTTPErrorStatusCode() int {
	return http.StatusBadRequest
}

type VMiddleware struct {
	serverVersion  string
	defaultVersion string
	minVersion     string
}

// Returns a new handler function wrapping the previous one in the request chain.
func (v VMiddleware) WrapHandler(handler func(ctx context.Context) error) func(ctx context.Context) error {
	return func(ctx context.Context) error {
		// ... ...
		return handler(ctx)
	}
}

//-----------------------------------------
// usage of middleware filters
// file - api/server/middleware.go
//-----------------------------------------
func (s *Server) handleWithGlobalMiddlewares(handler httputils.APIFunc) httputils.APIFunc {
	next := handler

	for _, m := range s.middlewares {
		next = m.WrapHandler(next)
	}

	return next
}
```

<br />

### This or That - Case for defaults

```go

// BoolValue transforms a form value in different formats into a boolean type.
func BoolValue(r *http.Request, k string) bool {
	s := strings.ToLower(strings.TrimSpace(r.FormValue(k)))
	return !(s == "" || s == "0" || s == "no" || s == "false" || s == "none")
}

// BoolValueOrDefault returns the default bool passed if the query param is
// missing, otherwise it's just a proxy to boolValue above
func BoolValueOrDefault(r *http.Request, k string, d bool) bool {
	if _, ok := r.Form[k]; !ok {
		return d
	}
	return BoolValue(r, k)
}
```

<br />

### enumerated constants - What are these ?

```go

// use of keyword iota
// these are incremented by 1 starting from 0
const(
  a = iota // a == 0
  b = iota // b == 1
  c        // implicitly c == iota, c == 2
)
```

<br />

### := versus = 
 
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

### all-in-one if statement

```go

if err := c.sess.Start(`echo "$SHELL"`); err != nil {..}
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

### path settings, workspaces, etc.
  
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

### Flags

```bash

# refer http://bit.ly/GoFlags
```

<br />

### dot import

```bash

# Looks similar to static imports in Java
# Do not use it. Makes code unreadable.
# Is bad. Favor explicit than implicit.
```

<br />

### new(T)

```go

- new(type) -> allocates memory for the particular type & returns a pointer
- It does not initialize the memory, it zeros the memory
- Remember, new(type) returns a pointer to a newly allocated zero value of type T
- i.e. returns a *T
```

<br />

### What after new(T) ?

```go

# initialize the constructor via composite literals
# notice the verbose versus the terse option
# notice carefully the use of * and &

func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := new(File)
    f.fd = fd
    f.name = name
    f.dirinfo = nil
    f.nepipe = 0
    return f
}

// vs.

func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := File{fd, name, nil, 0}
    return &f
}

// unlike in C, it's perfectly OK to return the address of a local variable; 
// the storage associated with the variable survives after the function returns. 
// taking the address of a composite literal allocates a fresh instance each time 
// it is evaluated

// last 2 lines can be re-written as
return &File{fd, name, nil, 0}

// or
return &File{fd: fd, name: name}

// IMPORTANT - new(File) and &File{} are same !!!
// You need to set the fields separately now !!!
```

<br />

### Composite literals w.r.t ```arrays, slices, maps```

```go

// Either the field labels or map keys are indices 
// All of below work as long as values of Enone, Eio & Einval are distinct
a := [...]string   {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
s := []string      {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
m := map[int]string{Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
```

<br />

### make vs. new
 
```go

// make returns an initialized value of a type T
// i.e. it does not return a pointer i.e. *T
// make can only initialize a slice, map or channel
// to obtain a pointer explicitly, allocate with new 
// to obtain a pointer explicitly, take the address of a variable explicitly

var p *[]int = new([]int)       // allocates slice structure; *p == nil; rarely useful
var v  []int = make([]int, 100) // the slice v now refers to a new array of 100 ints

// Unnecessarily complex:
var p *[]int = new([]int)
*p = make([]int, 100, 100)

// Idiomatic:
v := make([]int, 100)
```

<br />

### arrays & pass-by-value

```go

// passing arrays do not pass pointers to the arrays
// it passes the copy of the array
// hence, non-performant

// If C like efficiency is required need to pass pointer of the array
// i.e. x := someFunc(&array)

// IMPORTANT - Above is not idiomatic go style !!!
// One should use slices instead.
```

<br />

### slices and efficiency
 
```go

// slices hold references to an underlying array
// so instead of passing pointers we pass slice argument

func (f *File) Read(buf []byte) (n int, err error)

// To read into the first 32 bytes of a larger buffer buf, 
// slice the buffer. It is efficient.
n, err := f.Read(buf[0:32])
```

<br />

### go keyword - What is it ?

```go

# A "go" statement starts the execution of a function call
# As an independent concurrent thread of control, or goroutine,
# All these are within the same address space.
```

<br />

### Concurrency - How to get it right ?

```bash

# use right tools
# when sharing memory between goroutines - use a mutex
# when orchestrating goroutines - use channels
# channels orchestrate; mutexes serialize.
# refer [go-proverbs.github.io](http://go-proverbs.github.io/)
  
# When to use channels ?
# complex behavior to model
# mode other low level primitives
# scatter & gather
# model the semaphore - a mutual exclusion primitive
# actor pattern

# Bad uses of channels ?
# Channels use a mutex internally
# Hence bad in performance
```

<br />

### Code Review Comments

```bash

# refer bit.ly/GoCodeReview
```

<br />

### Naming conventions

```bash

# refer bit.ly/GoNames
```

<br />

### install golang on CentOS

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

### golang & creation of workspace for development w.r.t CentOS

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

### creating a new golang project

```bash

# install govendor
# navigate to your project folder
$ pwd
/home/goworkspace/src/github.com/openebs/openebs
# initialize your project with govendor
$ govendor init
# install the 3rd party libraries
$ govendor fetch github.com/urfave/cli/@=v1
```

<br />

### govendor tips

```bash

# install or update govendor
$ go get -u github.com/kardianos/govendor
# this places the source code @ $GOPATH/src
# then compiles the package & places govendor binary in $GOPATH/bin

# get started
$ govendor init

# add a dependency
# inside your project...
$ govendor fetch github.com/amit/das

# update an existing dependency
# inside the your project..
$ govendor fetch github.com/openebs/openebs@master

# refer - https://github.com/kardianos/govendor/wiki/Govendor-CheatSheet
```

<br />

### git tips
 
```bash

# git set user name

git config --global user.name "AmitKumarDas"
git config --get user.name

# git set user email

git config --global user.email "amit.das@cloudbyte.com"
git config --get user.email

# git add existing project into git

# cd existing_folder
git init
git remote add origin http://20.11.1.101/root/openebs.git
git add .
git commit
git push -u origin master
git commit history
git log --pretty=oneline
git log --pretty=format:"%h - %an, %ar : %s"
git learn the origin
git config --get remote.origin.url
git learn the origin if referential integrity is intact
git remote show origin
```

<br />

### Referenced Links:

- [Effective golang](https://golang.org/doc/effective_go.html)
- [Learn golang](http://go-book.appspot.com/index.html)
- [Expressiveness & Runtime Performance](http://dave.cheney.net/2012/02/11/how-the-go-language-improves-expressiveness-without-sacrificing-runtime-performance)
- [Table driven tests](http://dave.cheney.net/2013/06/09/writing-table-driven-tests-in-go)
- [Benchmarks](http://dave.cheney.net/2013/06/30/how-to-write-benchmarks-in-go)
- [Arrays & Slices](http://openmymind.net/The-Minimum-You-Need-To-Know-About-Arrays-And-Slices-In-Go/)
