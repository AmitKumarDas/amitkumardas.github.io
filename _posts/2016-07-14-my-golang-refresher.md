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

<br>

- **Composable, Maintainable, Readable code in golang**

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

```go

// A bit of state management & still have composable, maintainable & readable code.
// What if we want to return the previous value when an update is being made to
// an option ?

// 1. Define the type
// Mark the option type can return a value i.e. previous state
// Notice returning of empty interface value
type option func(*Storage) interface{}

// 2. Implement a business function
// Mark the use of closure than direct setting of the property
// Notice, that the client invocation to this method is still simple
// i.e. storage.Capacity(100)
// However, the client side resetting of this option will be verbose
func Capacity(cap int) option {
    return func(s *Storage) interface{}{
        previous := s.capacity
        s.capacity = cap
        return previous
    }
}

// 3. Utility to set various options that are defined now or in future
// Mark the use of variadic arguments
// Notice the previous value of last option is returned
func (s *Storage) Option(opts ...option) (previous interface{}){
  for _, opt := range opts {
    previous = opt(s)
  }

  return previous
}
```

<br />

```go

// A state machine implementation.
// What if the feature encountered some issue & now needs to revert back to old
// settings.
// What if a feature needs to turn on a flag for 5 minutes & finally reset to
// old flag. Probably, a profiling requirement.

// 1. Define the type
// Mark the option type returns another option
// Otherwise known as a self referential function.
// The return option can implement the inverse logic.
// i.e. closures to our rescue yet again !!!
type option func(*Storage) option

// 2. Implement a business function
// Mark the use of closure than direct setting of the property
// Notice, that the client invocation to this method is still simple
// i.e. storage.Capacity(100)
// However, the client side resetting of this option will be verbose
func Capacity(cap int) option {
    return func(s *Storage) option{
        previous := s.capacity
        s.capacity = cap
        return Capacity(previous)
    }
}

// 3. Utility to set various options that are defined now or in future
// Mark the use of variadic arguments
// Notice the previous value of last option is returned
func (s *Storage) Option(opts ...option) (previous option){
  for _, opt := range opts {
    previous = opt(s)
  }

  return previous
}
```

<br />

- **References**
  - [Functional Options](https://www.reddit.com/r/golang/comments/2jf63r/dotgo_functional_options_for_friendly_apis/)
  - [Self Referential Function](https://commandcenter.blogspot.in/2014/01/self-referential-functions-and-design.html?m=1)
  - [Config struct vs. Functional options](http://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis)

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

- **Nouns as Structs' properties and Verbs as Interfaces' methods - I**

```go

// a simple built-in interface
type error interface {
    Error() string
}

// A library writer is free to implement this interface with a richer model
// under the covers, making it possible not only to see the error 
// but also to provide some context.
// PathError records an error and the operation and
// file path that caused it.
type PathError struct {
    Op string    // "open", "unlink", etc.
    Path string  // The associated file.
    Err error    // Returned by the system call.
}

func (e *PathError) Error() string {
    return e.Op + " " + e.Path + ": " + e.Err.Error()
}
```

<br />

- **Nouns as Structs' properties and Verbs as Interfaces' methods - II**

```go

// refer - http://www.lshift.net/blog/2013/10/25/going-monad-with-parser-combinators/

// Verbs i.e. behavior encapsulated in interface
type Parser interface {
    // Parse consumes some input and returns ParseTrees. If the
    // receiver failed to parse anything, Parse returns an empty
    // slice. A slice containing a single element represents an
    // unambiguous parse. Multiple results mean multiple parse
    // trees for the given consumed input.
    Parse(s string) ParseTrees
}

// Struct i.e. the Noun -- consists of the properties
type ParseTree struct{
    Value interface{}
    Remaining string
}

// Collection of Structs i.e Nouns
type ParseTrees []ParseTree

// type declaration for a specific implementation of ParseTree
// This will do nothing
type Zero struct {}

// type declaration for a specific implementation of ParseTree
// This will do some work
type Item struct {}

// Actual implementation - polymorphism
func (p Zero) Parse(s string) (ParseTrees) {
    return ParseTrees{}
}

// Actual implementation - polymorphism
func (p Item) Parse(s string) (results ParseTrees) {
    if len(s) == 0 {
        results = ParseTrees{}
    } else {
        reader := strings.NewReader(s)
        // ReadRune replaces an invalid rune with U+FFFD!
        // Err is _never_ set!
        first, size, _ := reader.ReadRune()
        if first == 'uFFFD' {
            results = ParseTrees{}
        } else {
            rest := s[size:]
            results = ParseTrees{ParseTree{first, rest}}
        }
    }
    return
}
```

<br />

- **Interfaces exposes method signature(s) implemented as struct(s) properties**

```go

// refer - https://gist.github.com/Rubentxu/c353cac8f18321dbd96d/

// Interface with methods returning the Interface
// What can be the reason for above ?
// Maybe method chaining !!!

type Maybe interface {
    Return(value interface{}) Maybe
    Bind(func(interface{}) Maybe) Maybe
}

type Just struct {
    Value interface{}
}

type Nothing struct {}

func (j Just) Return(value interface{}) Maybe {
    return Just{ value }
}

func (j Just) Bind(fn func(interface{}) Maybe) Maybe {
    return fn(j.Value)
}

func (n Nothing) Return(value interface{}) Maybe {
    return Nothing{}
}

func (n Nothing) Bind(fn func(interface{}) Maybe) Maybe {
    return Nothing{}
}
```

<br />

- **Building logic... functional way !!!**

```go

// Steps
// 0. Get some Monad implemenations in some package

// Steps for MayBe monad 
// i.e. error handling in a functional way
// 1. Implement any logic inside a function
// 2. The function should accept a MayBe type & return a MayBe type
// 3. This function will return another function that returns MayBe i.e. closure
// 4. You achieve 3rd point -via- Bind & Just calls

func MaybeDouble(a Maybe) Maybe {
    return a.Bind(func (v interface{}) Maybe { 
        return Just { 2 * v.(int) }
    })
}

// Your client will use syntax similar to lisp
// i.e. yourFn2(yourFn1(Just{someval}))
// __OR__
// Client can use method chaining 
// i.e. Just{someval}.Bind(yourFn1).Bind(yourFn2)
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

- **Terse for loops**

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

- **Here comes the goroutine - Your own http server !!**
 
```go

// HTTPServer contains an instance of http server and the listener.
// srv *http.Server, contains configuration to create a http server and a mux router with all api end points.
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

// Serve starts listening for inbound requests.
func (s *HTTPServer) Serve() error {
	return s.srv.Serve(s.l)
}

// serveAPI loops through all initialized servers and spawns goroutine
// with Server method for each. It sets createMux() as Handler also.
func (s *Server) serveAPI() error {
	var chErrors = make(chan error, len(s.servers))
	for _, srv := range s.servers {
		srv.srv.Handler = s.routerSwapper
		go func(srv *HTTPServer) {
			var err error
			logrus.Infof("API listen on %s", srv.l.Addr())
			if err = srv.Serve(); err != nil && strings.Contains(err.Error(), "use of closed network connection") {
				err = nil
			}
			chErrors <- err
		}(srv)
	}

	for i := 0; i < len(s.servers); i++ {
		err := <-chErrors
		if err != nil {
			return err
		}
	}

	return nil
}

// Wait blocks the server goroutine until it exits.
// It sends an error message if there is any error during
// the API execution.
func (s *Server) Wait(waitChan chan error) {
	if err := s.serveAPI(); err != nil {
		logrus.Errorf("ServeAPI error: %v", err)
		waitChan <- err
		return
	}
	waitChan <- nil
}

//------------------------------------------------------------
// In the daemon code
//------------------------------------------------------------
func (cli *DaemonCli) start() (err error) {
	stopc := make(chan bool)
	defer close(stopc)

	flags := flag.CommandLine
	cli.commonFlags.PostParse()

	cliConfig, err := loadDaemonCliConfig(cli.Config, flags, cli.commonFlags, *cli.configFile)
	if err != nil {
		return err
	}
	cli.Config = cliConfig

	if cli.Pidfile != "" {
		pf, err := pidfile.New(cli.Pidfile)
		if err != nil {
			return fmt.Errorf("Error starting daemon: %v", err)
		}
		defer func() {
			if err := pf.Remove(); err != nil {
				logrus.Error(err)
			}
		}()
	}

	serverConfig := &apiserver.Config{
		// blah blah blah ...
	}

	api := apiserver.New(serverConfig)
	cli.api = api

	for i := 0; i < len(cli.Config.Hosts); i++ {
		var err error
		
		protoAddr := cli.Config.Hosts[i]
		protoAddrParts := strings.SplitN(protoAddr, "://", 2)
		if len(protoAddrParts) != 2 {
			return fmt.Errorf("bad format %s, expected PROTO://ADDR", protoAddr)
		}

		proto := protoAddrParts[0]
		addr := protoAddrParts[1]

		// It's a bad idea to bind to TCP without tlsverify.
		if proto == "tcp" {
			logrus.Warn("[!] DON'T BIND ON ANY IP ADDRESS WITHOUT setting -tlsverify")
		}
		
		ls, err := listeners.Init(proto, addr, serverConfig.SocketGroup, serverConfig.TLSConfig)
		if err != nil {
			return err
		}
		
		ls = wrapListeners(proto, ls)
		// If we're binding to a TCP port, make sure that a container doesn't try to use it.
		if proto == "tcp" {
			if err := allocateDaemonPort(addr); err != nil {
				return err
			}
		}
		logrus.Debugf("Listener created for HTTP on %s (%s)", protoAddrParts[0], protoAddrParts[1])
		
		api.Accept(protoAddrParts[1], ls...)
	}

	signal.Trap(func() {
		cli.stop()
		<-stopc // wait for daemonCli.start() to return
	})

	d, err := daemon.NewDaemon(cli.Config)
	if err != nil {
		return fmt.Errorf("Error starting daemon: %v", err)
	}

	cli.initMiddlewares(api, serverConfig)
	initRouter(api, d)

	cli.d = d
	cli.setupConfigReloadTrap()

	// The serve API routine never exits unless an error occurs
	// We need to start it as a goroutine and wait on it so
	// daemon doesn't exit
	serveAPIWait := make(chan error)
	go api.Wait(serveAPIWait)

	// after the daemon is done setting up we can notify systemd api
	notifySystem()

	// Daemon is fully initialized and handling API traffic
	// Wait for serve API to complete
	errAPI := <-serveAPIWait
	shutdownDaemon(d, 15)
	if errAPI != nil {
		return fmt.Errorf("Shutting down due to ServeAPI error: %v", errAPI)
	}

	return nil
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
- [Expressiveness & Runtime Performance](http://dave.cheney.net/2012/02/11/how-the-go-language-improves-expressiveness-without-sacrificing-runtime-performance)
- [Table driven tests](http://dave.cheney.net/2013/06/09/writing-table-driven-tests-in-go)
- [Benchmarks](http://dave.cheney.net/2013/06/30/how-to-write-benchmarks-in-go)
