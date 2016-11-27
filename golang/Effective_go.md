#Effective Go
## Formatting
* gofmt
* Indentation:	Use tabs
* Line length:	Without line length limit.
* Parentheses:	Go needs fewer parenthese

## Commentary
* support /* XXXX */ or // XXXXXX
* Every package should have a _package comment_, for the multiple file packages, the package comment only needs to be present in one file.
* In side a package, any comment immediately preceding a top-level declaration serves as a doc comment for that declaration. Every exported (capitalized) name in a program should have a doc comment.

## Names
###Package name
* By convention, packages are given lower case, single-word names.
* The package name is the base name of directory.
</br> Such as `import encoding/base64`  `package base64`
* The package can help refer its contents.
</br> Such as `bufio.Reader` is better than `bufio.BufReader`

###Getters
Go does't provide automatic support for getter and setters.
By convention, getter `obj.XXX` setter`obj.SetXXX`

###Interface names
* Interface only contains one method, could use the method plus _er_ to name, such as Reader, Writer
* To avoid confusion, don't name your method as Write, Close, String and so on. But if yous method has the same meanig, please do it.

### MixedCaps
MixedCaps or mixedCaps

## Semicolons
The rule is this. If the last token before a newline is an indentifier  (which includes words like int and float64), a basic literal such as a number or string constant, or one of the tokens

```go
break continue fallthrough return ++ -- ) }
```	

## Control structors
### if
Writing simple statement on multiple lines. Especially when the body contains a control statement such as `break, return`

Omit the unnecessary `else`

``` go
f, err := os.Open(fileName)
if err != nil {
	return err
}
dosomething(f)
```

### Redeclaration and reassignment

```go
f, err := os.Open(fileName)
d, err := f.Stat()
```
Above code the err is redevlaration.

In a `:=` declaration a variable may appear even if it has been already declared, provided:

* The declaration is in the same scope as the existing declartion of `V`(If v is already declaration in an outer scoep, the declaration will create a new variable`$`)
* the corresponding type of the value in the initialization is assignable to `V`
* there is at least one other varable in the declaration that is being declared a new.

###For
GO unifies the `for` and `which`

```go
// for
for init; condition; post {
}
// while
for condition {
}
// dead while 
for {
}
```

Array, slice, string, or map, or reading from a channel:

```go
for key, value := range map {}

for key := range map {}

for _, value := range map {}
```

## Data

###Allocate with new

new(T) allocates zeroed storage for a new item of type T and returns its address, a value of type *T.
Arrange your data structures that the zero value of each type can be used without further initialization.

### Constructors and composite literals

Sometime a `Newxxx` function is necessary.

```go
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    return &File{fd, name, nil, 0}
}
```

### Allocation with make

The built-in function make(T, args), it creates _slices_, _maps_ and _channels_ only and then return the instance of T.

### Arrays
* Arrays are values. Assigning one array to another copies all the elements
* In particular, if you pass an array to a function, it will receive a __copy__ of the array, not a pointer to it.
* The size of an array is part of its type. The types[10]int and [20]int are distinct.

### Slices

Slices hold references to an underlying array.

The function uses the fact that len and cap are legal when applied to the nil slice, and return 0.

Slices hold references to an underlying array, and if you assign one slice to another, both refer to the same array. If a function takes a slice argument, changes it makes to the elements of the slice __will be visible to the caller__, 
__analogous to passing a pointer to the underlying array__ .

### Maps

The key can be of any type for which __the equality operator__ is defined, such as integers, floating point and complex number, strings, pointers, interfaces(as long as the dynamic type supports equlity), structs and arrays. __Slices__ can not be used as map keys.

If you pass a map to a function that changes the contents of the map, the changes will be visible in the caller.

Delete is safe when the key is already absent from the map.

### Printing

``` go
fmt.Printf()
fmt.Fprintf()  // first argument any object that implements the io.Writer interface.
fmt.Sprintf()
```

__%+v__ annotes the field of the structure with their names.
__%#v__ prints the value in full GO syntax (with type info and names)

__%q__ with backquotes like _"aaa"_
__%x__ generating a long hexadecimal string

* avoid to use fmt.Sprintf to implement the String() method, because it is easy to recur into your String method.

``` go
func (m MyString) String() string {
	return	fmt.Sprintf("MyString=%s", m)  // will recur fover
}
```

If want to print values of type T, the receiveer fror String must be of __value type__.

### Append

built-in function. The result needs to be returned because the underlying array may change.

If append is a slice, use ... like `append(sliceA, sliceB...)`

## Initailization

### Constatns
They are created at compile time, even when defined as locals in function, and can only be __numbers, characters(runes), strings or booleans__.

In Go, enumerated constants are created using the iota enumerator. Since iota can be part of an expression and expressions can be implicitly repeated, it is easy to build intricate sets of values.

```go
type ByteSize float64

const (
    _           = iota // ignore first value by assigning to blank identifier
    KB ByteSize = 1 << (10 * iota)
    MB
    GB
    TB
    PB
    EB
    ZB
    YB
)
```

### Variables

Variables can be a general expression computed at ___run time___.

### The init function

Each source file can define its ovwn niladic _init_ function to set up whatever state is required.(Each file can have multiple init functions)

Init fucntion is called after all the variable declarations in package have evaluated their initializers, and those are evaluated only __after all the import packages have been initialized__. A common use is to verify or repair correctness of the program state before real excution begins.

``` go
func init() {
    if user == "" {
        log.Fatal("$USER not set")
    }
    if home == "" {
        home = "/home/" + user
    }
    if gopath == "" {
        gopath = home + "/go"
    }
    // gopath may be overridden by --gopath flag on command line.
    flag.StringVar(&gopath, "gopath", gopath, "override default GOPATH")
}
```

## Methods

### Pointer vs. Values

* The pointer methods can modify the receiver.
* The value methods can be invoked on pointers and values, but pointer methods can only be invoked on pointers.(When the pointer implement some interface)
* When the value is addressable, the language takes care of the common case of invoking a pointer method on a value by inserting the address operator automatically.

## Interfaces and other types

### Interfaces 

Interfaces with only one or two methods are common in GO code, and are usually given a name derived from the method, such as `io.Writer` for something that impements `Write` method.

### Conversions

```go
type Sequence []int

fmt.Sprint([]int(s)) // It's legal to convert between them.
```
The convert does't create a new value.

It's an idiom in Go program to convert the type of an expression to access a different set of methods.

```go
func (s Sequence) String() string {
	sort.IntSlice(s).Sort()
}
```

### Interface conversions and type assertions

Type assertion: `value.(typeName)` and the result is a new value with the static type _typeName_. That type must either be the concrete type held by the interface or a second interface type that the value can be converted to(like the above conversions).

### Generality

If a type exists only to implement an interface and will never have exported methods beyond that interface, ___there is no need to export the type itself___.

In such cases, the constructor should return an interface value rather than the implementing type.

### Interface and methods

## The blank identifier

### The blank identifier in multiple assignment

### Unused imports and variables

The blank identifier provides a workaround to keep the unused imports and variables

```go
package main

import (
    "fmt"
    "io"
    "log"
    "os"
)

var _ = fmt.Printf // For debugging; delete when done.
var _ io.Reader    // For debugging; delete when done.

func main() {
    fd, err := os.Open("test.go")
    if err != nil {
        log.Fatal(err)
    }
    // TODO: use fd.
    _ = fd
}
```

By convention, the global declarations to slice the imprt errors should come right after the imports and be commented, both to make them easy to find and as a reminder to clean things up later.

### Import for side effect
Sometimes it is useful to import a package only for its side effects, without any explicit use. 

```go
import _ "net/http/pprof"
```

### Inteface checks

Some interface checks do happen at run-time.

`if _, ok := val.(TypeName); ok {}`

--

`var _ json.Marshaler = (*RawMessage)(nil)`

In this declaration, the assignment involving a conversion of a *RawMessage to a Marshaler requires that *RawMessage implements Marshaler, and that property will be checked at compile time. Should the json.Marshaler interface change, this package will no longer compile and we will be on notice that it needs to be updated.

The appearance of the blank identifier in this construct indicates that the declaration exists only for the type checking, not to create a variable. Don't do this for every type that satisfies an interface, though. By convention, such declarations are only used when there are no static conversions already present in the code, which is a rare event.

## Embedding

Interfaces can be embedded witin the interfaces.

```go
type ReadWriter interface {
	Reader
	Writer
}
```

The struct also can use the embeded.

```go
//
type Job struct {
    Command string
    *log.Logger
}
```
So use the embeded struct, you can call the embeded interface method and the receiver is the outer struct.

* Can't use the pointer of interface to be the embeded.

```go
job := &Job{command, log.New(os.Stderr, "Job: ", log.Ldate)}
job.Log("directly log")
```

```go
func (job *Job) Logf(format string, args ...interface{}) {
    job.Logger.Logf("%q: %s", job.Command, 	 fmt.Sprintf(format, args...))
}
```
Embedding types introduces the problem of name conflicts but the rules to resolve them are simple. First, a field or method X hides any other item X in a more deeply nested part of the type. If log.Logger contained a field or method called Command, the Command field of Job would dominate it.

If there are the same name methods in the same nesting level struct or the other embeded struct when you call the method will error. But if you never used it there are no problem.

## Concurrency

### Share by communicating

___Only one goroutine has access to the value at any given time.___

> Do not communicate by sharing memory; instead, share memory by communicating.

### Goroutines

It is a function executing concurrently with other goroutines in the same address space. It costing little more than the alllocation of stack space.

In Go, function literals are closures, the implementation makes sure the variables referred to by the function survice as long as they are active.

### Channels

Unbuffered channels or synchronous channel:
`c := make(chan int)`

A buffered channel can be used like a semaphore, for instance to limit throughput.

* In a Go for loop, the loop variable is reused for each iteration.

```go
var sem = make(chan int, MaxOutstanding)

for req := range queue {
        sem <- 1
        go func(req *Request) {
            process(req)
            <-sem
        }(req)
    }

```

### Parallelization
Get the cpu count
``` go
var numCPU = runtime.NumCPU()
// or, if the count is not 0 will set the runtime max use cpu num
var numCPU2 = runtime.GOMAXPROCS(0) 
```

__Go is a concurrent language, not a parallel one, and not all parallelization problems fit Go's model.__
    

## Errors

A simple built-in interface.

```go
type error interface {
	Error() string
}
```

### Panic
Built-in function `panic(str)` that in effect creates a run-time error that will stop the program. 
The real library functions should be avoid panic. If the library truly cannot set itself up, it might be reasonable to panic.

### Recover

Deferred functions can modify named return values. 

``` go
func test(i int) (err error) {

	defer func() {
	
		
		if p := recover(); p != nil {
		
			fmt.Println(p)
			
			err = errors.New("error")
			
		}
	}()
	
	if i % 2 == 0 {
		panic("panic")
	}
	return err
}
```

## A web server
