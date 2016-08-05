---
layout: post
title: My golang refresher
---

## Why this article ?

> This helps me in learning or referencing golang quickly.

<br />

## Frequently asked question:

- **main** - What is it ?

```go

Every standalone Go program contains a package called main and its main function,
after initialization, is where execution starts. The main.main function takes 
no arguments and returns no value.
```

<br />

- **Simply structs**
 
```go

package middleware

// VersionMiddleware is a middleware that
// validates the client and server versions.
type VersionMiddleware struct {
	serverVersion  string
	defaultVersion string
	minVersion     string
}

// NewVersionMiddleware creates a new VersionMiddleware
// with the default versions.
func NewVersionMiddleware(s, d, m string) VersionMiddleware {
	return VersionMiddleware{
		serverVersion:  s,
		defaultVersion: d,
		minVersion:     m,
	}
}

//-------------------------------------------------------------------------
// in some other file
//-------------------------------------------------------------------------
middleware.NewVersionMiddleware("0.1omega2", api.DefaultVersion, api.MinVersion)
```

<br />

- **Structs everywhere**

```go

// Config provides the configuration for the API server
type Config struct {
	Logging     bool
	Version     string
	SocketGroup string
	TLSConfig   *tls.Config
}

// HTTPServer contains an instance of http server and the listener.
// The instance srv *http.Server, contains configuration to 
//     - create a http server and a mux router with all api end points.
// l   net.Listener, is a TCP or Socket listener that dispatches incoming request to the router.
type HTTPServer struct {
	srv *http.Server
	l   net.Listener
}

// Server contains instance details for the server
type Server struct {
	cfg           *Config
	servers       []*HTTPServer
	routers       []router.Router
	routerSwapper *routerSwapper
	middlewares   []middleware.Middleware
}
```

<br />

- **Building logic... oops way !!!**

```go

// Config provides the configuration for the API server
type Config struct {
	Logging     bool
	Version     string
	SocketGroup string
	TLSConfig   *tls.Config
}

// Server contains instance details for the server
type Server struct {
	cfg           *Config
	servers       []*HTTPServer
	routers       []router.Router
	routerSwapper *routerSwapper
	middlewares   []middleware.Middleware
}

// New returns a new instance of the server based on the specified configuration.
// It allocates resources which will be needed for ServeAPI(ports, unix-sockets).
func New(cfg *Config) *Server {
	return &Server{
		cfg: cfg,
	}
}

// UseMiddleware appends a new middleware to the request chain.
// This needs to be called before the API routes are configured.
func (s *Server) UseMiddleware(m middleware.Middleware) {
	s.middlewares = append(s.middlewares, m)
}

//-----------------------------------------------------------------
// some other file e.g. test file
//-----------------------------------------------------------------

cfg := &Config{
	Version: "0.1omega2",
}
srv := &Server{
	cfg: cfg,
}

// An instance calling the instance method & then
// package.ExposedMethod() invocation
srv.UseMiddleware(middleware.NewVersionMiddleware("0.1omega2", api.DefaultVersion, api.MinVersion))
```

<br />

- **Terse loops in for**

```go

// HTTPServer contains an instance of http server and the listener.
// The instance srv *http.Server, contains configuration to 
//     - create a http server and a mux router with all api end points.
// l   net.Listener, is a TCP or Socket listener that dispatches incoming request to the router.
type HTTPServer struct {
	srv *http.Server
	l   net.Listener
}

// Server contains instance details for the server
type Server struct {
	servers       []*HTTPServer
}

// Accept sets a listener the server accepts connections into.
func (s *Server) Accept(addr string, listeners ...net.Listener) {
	for _, listener := range listeners {
		httpServer := &HTTPServer{
			srv: &http.Server{
				Addr: addr,
			},
			l: listener,
		}
		s.servers = append(s.servers, httpServer)
	}
}
```

<br />

- **This or That - Case for defaults**

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

- **enumerated constants** - What are these ?

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

- **all-in-one if statement** Read the sample & understand !!

```go

if err := c.sess.Start(`echo "$SHELL"`); err != nil {..}
```

<br />

- **Trying a functional approach via Closures**

```go

// RequiresMaxArgs returns an error if there is not at most max args
func RequiresMaxArgs(max int) cobra.PositionalArgs {
	return func(cmd *cobra.Command, args []string) error {
		if len(args) <= max {
			return nil
		}
		return fmt.Errorf(
			"\"%s\" requires at most %d argument(s).\n",
			cmd.CommandPath(),
			max,
		)
	}
}

// some other file

func NewLogoutCommand(dockerCli *client.DockerCli) *cobra.Command {
	cmd := &cobra.Command{
		Use:   "logout [SERVER]",
		Short: "Log out from a Docker registry.",
		Args:  cli.RequiresMaxArgs(1),
	}

	return cmd
}
```

<br />

- **Playing with Array & Struct**

```go

package cli

// Command is the struct containing the command name and description
type Command struct {
	Name        string
	Description string
}

// OpenEBSCommandUsage lists the top level openebs commands 
// and their short usage
var OpenEBSCommandUsage = []Command{
}

// OpenEBSCommands stores all the openebs command
var OpenEBSCommands = make(map[string]Command)

func init() {
	for _, cmd := range OpenEBSCommandUsage {
		OpenEBSCommands[cmd.Name] = cmd
	}
}
```

<br />

- **Transformations to struct**

```go

// ArchiveOptions stores archive information for different operations.
type ArchiveOptions struct {
	Name string
	Path string
}

// Parses form values and turns them into ArchiveOptions.
// It fails if the archive name and path are not in the request.
func ArchiveFormValues(r *http.Request, vars map[string]string) (ArchiveOptions, error) {
	if err := ParseForm(r); err != nil {
		return ArchiveOptions{}, err
	}

	name := vars["name"]
	path := filepath.FromSlash(r.Form.Get("path"))

	switch {
	case name == "":
		return ArchiveOptions{}, fmt.Errorf("bad parameter: 'name' cannot be empty")
	case path == "":
		return ArchiveOptions{}, fmt.Errorf("bad parameter: 'path' cannot be empty")
	}

	return ArchiveOptions{name, path}, nil
}
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

# refer http://bit.ly/GoFlags
```

<br />

- **dot import**

```bash

# Looks similar to static imports in Java
# Do not use it. Makes code unreadable.
# Is bad. Favor explicit than implicit.
```

<br />

- **Address** of a given variable

```go

// Address can be found for a string, int, float32, complex64, etc.
var (    
    hello string = "Hello world"
)

func main() {    
    fmt.Println("Hexadecimal address of hello is: ", &hello)
}
```

<br />

- Implicit definitions -- some examples

```go

// We know that pointer will be initialized with an address
// i.e. *myptr = &myvar

// In below example, cmdOutput is implicitly of type *bytes.Buffer
// In addition, *cmdOutput will have the value of bytes.Buffer

cmdOutput := &bytes.Buffer{}
```

<br />

- new(T)

```go

- new(type) -> allocates memory for the particular type & returns a pointer
- It does not initialize the memory, it zeros the memory
- Remember, new(type) returns a pointer to a newly allocated zero value of type T
- i.e. returns a *T

```

<br />

- What after new(T) ?

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

- Composite literals w.r.t ```arrays, slices, maps```

```go

// Either the field labels or map keys are indices 
// All of below work as long as values of Enone, Eio & Einval are distinct
a := [...]string   {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
s := []string      {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
m := map[int]string{Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
```

<br />

- make vs. new
 
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

- arrays & pass-by-value

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

- slices and efficiency
 
```go

// slices hold references to an underlying array
// so instead of passing pointers we pass slice argument

func (f *File) Read(buf []byte) (n int, err error)

// To read into the first 32 bytes of a larger buffer buf, 
// slice the buffer. It is efficient.
n, err := f.Read(buf[0:32])
```

<br />

- **go keyword** - What is it ?

```go

# A "go" statement starts the execution of a function call
# As an independent concurrent thread of control, or goroutine,
# All these are within the same address space.
```

<br />

- **Concurrency** How to get it right ?

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

- **Code Review Comments**

```bash

# refer bit.ly/GoCodeReview
```

<br />

- **Naming conventions**

```bash

# refer bit.ly/GoNames
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

- **creating a new golang project**

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

- **govendor tips**

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

- **git tips**
 
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

## Referenced Links:

- [Effective golang](https://golang.org/doc/effective_go.html)
- [Learn golang](http://go-book.appspot.com/index.html)
