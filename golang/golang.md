# Go Lang

## Basics
### Packages
Programs start running in package main.

```go
// multi import
import (
	"fmt"
	"math"
)
```

### Exported names
In Go, a name is exported if it begins with a __capital letter__. For example, Pizza is an exported name, as is Pi, which is exported from the math package.

### Functions
type comes ___after___ the varible name

```go
package main

import "fmt"

func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```
If two or more consecutive named function parameters share a type, can omit the type from all but the last.

```go
x, y int
```

#### Mutiple result
A function can return any number results

```go
func multipleResult(a, b, c int) (a, b, c int) {
	return a,b,c
}

x, y, z := multipleResult(1, 2, 3)
```

#### Name retured value
return naked and should be used only in short function. They be harm readability in long fucntions.

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```

### Varibales
`var` declase a list of variables, as in function arguments lists, the type is last. Can be use in package or function.
#### Initailizers
`var i, j int = 1, 2` or __implicit__ type is `var i, j = 1, 2`
If an initializer is present, the type can be omitted; the variable will take the type of the initializer.
#### short variable declarations
Inside a __function__ the short `:=` can used in place of var declaration with implicit type. 

`k := 3` equals `var k int = 3`

#### basic type
```go
bool 
string
int int8 int16 int32 int64
uint uint8 uint16 uint32 uint64
byte // alias for uint8
rune // alias for int32, represents a Unicode code point
float32 float64
complex64 complex128
```
The __int__, __uint__, and __uintptr__ types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems. When you need an integer value you should use int unless you have a specific reason to use a sized or unsigned integer type.

#### Zero value
The __zero__ value will be given variables wihout an explicit init value

* 0 for numeric types
* false for bool types
* "" (the empty string) for strings

#### Type conversions
Need a explicit conversion in go lang.

```go
i := 42
f := float64(i)
u := uint(f)
```

#### Type inference

int, float64, or complex128 depending on the __precision__ of the constant

###Constants
Constants are declared like variables, but with the const keyword.

Constants can be character, string, boolean, or numeric values.

Constants cannot be declared using the := syntax.

Constants value cannot to be modify value.

```go
const Pi = 3.14

func main() {
	const World = "世界"
}
```

#### Numeric Constants
Numeric constants are high-precision values.

An untyped constant takes the type needed by its context.

```go
const Big = 1 << 100
func needInt(num int) int {
	return i * 10 + 1
}

needInt(Big) throws exception overflows int
```



## Flow control

### For

`for init statement(optional); condition express; post statement(optional)`

```go
for i := 0; i < 10;i++ sum--  {
		sum += i
}
```
* The init statement often a short variables declared.
* The variables scope is for statement

#### For is Go's while
You can drop the semicolons, equals while like `for (i < 100)`

#### Forever
```go
for {
}
```

### If

```go
if condition {

} else if condition {

} else {

}
```
if can start with a short statement and the variable scope is if scope, like 

```go
if a:= 10; condition {
}
```

### Switch
```go
switch (init statement, optional) variable {
case xxx:
	action
case xxx1:
	action
	fallthrough
default:
	action
}
```
__Stoppting__ when a case succeeds.
If use `fallthrough` will go next case.

#### switch with no condition

With no condition the same as `switch true {}`

```go
switch {
	case 1 < 2:
	case 1 > 2:
}
```

### Defer

`defer fmt.Println("hello world")`

A defer statement defers the execution of a function until the surrounding function returns.

The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.
The `defer` will be push into stack, and last-in-first-out.

```go
for i := 0; i < 10; i++ {
	defer fmt.Println(i)
}
// sysout, 9,8,7...0
```
* use to close opened file 

## More types
###Pointer
The type *T is a pointer to T value. Its zero value is nil

``` go
var p *int
```
The & operator generates a pointer to its operand

```go
i := 42
p = &i
```
The * operator denotes the pointer's underlying value.

```go
fmt.Println(*p) // read i through the pointer p
*p = 21 		   // set i throgh the pointer p
```
###Structs
A struct is a collection fields.

``` go
type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.println(Vertex{1, 2})
}
```

* ####Structs field

	Access by dot.

	``` go
	func main() {
		v := Vertex{1, 2}
		v.X = 4
	}
	```

* ####Pointers to stucts

	Struct field can be access by a struct pointer
	
	```go
	func main() {
		v := Vertex{1, 2}
		p := &v
		p.X = 6
	}
	```

* ####Struct Literal
	
	Use the `name: syntax` list just a fields.
	
	```go
	v := Vertex{X: 1, Y: 2}
	// get pointer value
	p = &Vertex{X: 1}
	```

###Arrays
The type [n]T is an arrya of n values of type T.

The size is can not be resize after inititialize.

```go
var a [10]int
a[0] = 1

b := [3]string{"hello", "world", "go"}

```
### Slice
A dynamic size, flexible view into the elements of array.
The type []T is a slice of type T.

```go
// init
var s1 []int

// an array first element to 5 element
s := array[0, 5]
```
* #### Slice are like reference of array

	A slice does not store any data.Change the elements of a slice, will modify corresponding the underlying array.
	
	Other slices that share the same underlying array will see those changes.
* #### Slice literal
	like array `slice := []int{1, 2, 3}`
* #### Slice defaults
	Can omit the low or high bounds to use the default value.The low default value is 0. The high default value is the length of array.

	```go
	a := [5]int{1, 2, 3, 4, 5}
	s := a[:]
	s := a[1:]
	s != a[:5]
	```
* ####Slice length and capacity

	The __length__ of slice is the number of elements it contains.
	
	The __capacity__ of a slice is the number of elements in the underlying array, counting from the first element in the slice.
	
	If the boudary is out of underlying array, `throw bound out of range`
* #### Nil slices
	The zero value of a slice is __nil__.
	
	The length and capacity is zero, without the underlying array.
* #### Creating a slice with make
	Slice can create with a built-in __make__ function.
	
	```go
	a := make([]int, 5) 	 // len(5)
	a := make([]int, 0, 5)   // len(0)  cap(5)
	```		
* #### Slices of slices
	Slices can contains any type, including other slices.
	
	```go
	slics := [][]string{
		[]string{"aa", "bb"}
		[]string{"cc", "dd"}
	}
	```
* #### Appending to slice
	Built-in function __`func append(s []T, element ... T)`__
	If append the element exceed the length of the underlying array, will create a new underlying array.
	
	```go
	func main() {
	
		a := []int{1, 2, 3}
		sl := a[0: 3]
		sl[0] = -1 	// a[0] = -1 
		sl = append(sl, 4, 5)  // create a new underlying array
		sl[2] = 100		// a[2] still is 3
	}
	```
	
### Range
The __range__ form of the __for__ loop iterator over a slice or map.

The range over a slice, return two valuce: index and ___copy___ of the value

```go
var slice = []int{1, 2, 3, 4}
for i, v := range slice {
	fmt.Printf("%d = %d", i, e);
}
```
* #### Range continue
	
	Skip the index __`for _,value := range slice {}`__
	
	Only use the index __`for index := range slice {}`__
	
### Map
A map maps key to value. The zero value of a map is __nil__
The `make` function can returns a map of the given type.

```go
m := make(map[string]string)
```
* #### Map literals
	
	Map literals like structure but keys are required.
	
	```go
	m := map[string]string{"test" : "value"}
	```
* #### Mutating Map

```go
// insert
m[key] = value
// retrieve
elem := m[key]
// delete
delete(m, key)
// test, if m contains key, ok is ture and elem is value
// otherwise ok is false and elem is the value type zero value
elem, ok := m[key]
```
### Function Values
Functions can be used as arguments and return values.

```go
func(fn func(float64, float64) float64) float64 {
	return fn(3, 4);
}
```
* #### Function closures

	Go functions may be closures. A closure is a function value that references variables from outside its body. The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.
	
	```go
	package main

	import "fmt"
	
	// fibonacci is a function that returns
	// a function that returns an int.
	func fibonacci() func() int {
		index := -1
		i := 0
		j := 1
		k := 0
		return func() int {
			index++
			switch index {
			case 0:
				return 0
			case 1:
				return 1
			default: 
				k = i + j
				i = j
				j = k
				return k
			}
		}
	}
	
	func main() {
		f := fibonacci()
		for i := 0; i < 10; i++ {
			fmt.Println(f())
		}
	}

	```
	
## Methods and interfaces
### Methods
Go does not have classes.

A method is a function with a special __receiver__ argument.

The receiver appears in its own argument list between the func keyword and the method name.

```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs())
}
```
* #### Methods are functions

	_Remember:_ a mthod is jush a function with a receiver argument.
* #### Methods continued
	Can declare a method on the types which defined in ___the same package___.
	
	```go
	type MyFloat float64

	func (f MyFloat) Abs() float64 {
		if f < 0 {
			return float64(-f)
		}
		return float64(f)
	}
	
	func main() {
		f := MyFloat(-math.Sqrt2)
		fmt.Println(f.Abs())
	}
	```
* #### Pointer receiver
	You can declare the method receiver as a pointer with a literal syntas `*`
	
	The send ___value___ and send ___pointer___ are the same with other program. If you send value, method operate a copy value of the receiver. 
	
	```go
	func (v *Vertex) Scale(f float64) {
		v.X = v.X * f
		v.Y = v.Y * f
	}
	```
* #### Pointer and functions
	The __functions__ are as same as __methods__ when send value or send pointer.
	
* #### Methods and pointer indirector
	* Comparing the previous two programs, you might notice that functions with a pointer argument must take a pointer:

		```go
		var v Vertex
		Scale(v) // Complie error
		Scale(&v) // OK
		```
		While methods with pointer receivers take a either a __value__ or a __pointer__
		
		```go
		var v Vertex
		v.Scale(5)  // OK
		p := &v
		p.Scale(10) // OK
		```
		___Go will interprets the statement convert a value to pointer.___
	
	* The equivalent thing happens in reverse direction.

		Function that take a value argument must take a value of that specific type.
		
		```go
		var v Verext
		fmt.Println(Abs(V))  // OK
		fmt.Println(Abs(&V)) // Compile error
		```
		
		While methods with value receiver take either a __value__ or a __pointer__ as a receiver.
		
		```go
		var v Vertex
		fmt.Println(v.Abs())  // OK
		p := &v
		fmt.Println(p.Abs())  // OK
		```
* #### Choosing a pointer or value receiver
	There are two reasons to use a pointer receiver:
	1. The method can modify the value that the receiver points to.
	2. To avoid copy the value. This can be efficient if the recevier is a large structure.
	
### Interfaces
An ___interface type___ is defined a set of method signatures.

A  value of interface type can hold any value that implements those methods.

``` go
type Abser interface {
	Abs() float64
}

type Myfloat float64 

func (f Myfloat) Abs() float64{
	if f < 0 {
		return float64(-f)
	} else {
		return float64(f)
	}
}

type Vertex struct {
	X, Y float
}

func(v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*V.X + v.Y*v.Y)
}

func main() {
	var a Abser
	f := Myfloat(-5)
	v := Vertex{3, 4}
	
	a = f
	a = &v
	a = v // this is error, because the v is a Vertex(not *Vertex) and does NOT implement Abser
}

``` 
#### Interface are implemented implicitly
A type implements an interface without the explicitly declare.

Implicit interfaces decouple the definition of an interface from its implemention, which could then appear in any package without prearrangement.

#### Interface value with _nil_ underlying value

If ther concrete value inside the interface itself is nil, the method will be called with a nil receiver.

_In some language this would trigger a null pointer exception, but in Go it is common to write methods that gracefully handle being called with a nil receiver_

```go
type I interface {
	M()
}

type s struct {
	S string
}

func s *S M() {
	if s == nil {
		return null
	}
	fmt.Println(s.S)
}

```

#### Nil interface values
A nil interface value holds neither value nor concrete type.

Calling a method on nil interface is a run-time error.

#### The empty interface
The interface type that specifies zero method is known as ___empty___ interface:

`interface {}`

An empty inreface may hold values of any type.

```go
func main() {
	var i inteface{}
	describe(i)
	
	i = 5
	describe(i)
}

func describe(i interface{}) {
	fmt.Println("(%V, %T)", i, i)
}
```
### Type assertions

A type ___assertion___ provides access to an interface value's underlying concrete value.
`t := i.(T)`

If i does not hold a T, the statement will trigger a panic.

`t, ok := i.(T)` But if you test the value holds a specific type. If _i_ holds _T_ the ok will be _true_ and t will be the underlying value, otherwise the ok will be false and the t will be the zero value of type T.

```go
func main() {
	var i interface{} = "string"
	
	s := i.(string)
	s, ok := i.(string)
	
	f, ok := i.(float)
	f = i.(float)  // panic
}
```
	
* #### Type switches
	
	A ___type switch___ is a contruct than permits several type assertions in series.
	
	```go
	// notice: the type is a keyword and only be used inside type switch 
	switch v := i.(type) {
		case int:
			fmt.Println("type is int")
		case string:
			fmt.Println("type is string")
		default:
			fmt.Println("unknown the type")
	}
	
	```	
	
### Stringers
One of the ubiquitous interface is `Stringer` defined by the fmt package.(like java toString())

```go
type Stringer interface {
	String() string
}
```

A `Stringer` is a type that can describe itself as a string.

``` go
type Person struct {
	Name string
	Age  int
}

func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}
```
### Errors
The error type is a built-in interface similar to fmt.Stringer:

```go
type error interface {
	Error() string
}
```

```go
type MyError struct {
	When time.Time
	What string
}

func (e *MyError) Error() String {
	return fmt.Sprintf("at %v, %s", e.When, e.What)
}

func main() {
	if err := run(), err != nil {
		fmt.Println(err)
	}
}
```

### Readers

The `io` package specifies the `io.Reader` interface.

```go
func (T) Read(b []byte) (n int, err error) 
```

`Read` populates the given byte slice with data and retures the number of bytes populated and an error value.
It returns an `io.EOF` error when the streams ends.

```go
func main() {
	r := strings.NewReader("Hello, Reader!")

	b := make([]byte, 32)
	for {
		n, err := r.Read(b)
		fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
		fmt.Printf("b[:n] = %q\n", b[:n])
		if err == io.EOF {
			break
		}
	}
}
```
