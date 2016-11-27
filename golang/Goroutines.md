## Goroutines

A ___goroutine___ is a lightweight thread managed by runtime.

`go f(x, y, z)`   starts a new goroutine running.

The evaluation of `f, x, y, z` happens in the current goroutine and the execution of `f` happens in the new goroutine.

Goroutines run in the same address space, so access to shared memory must be used synchronized. The `sync` package provides useful primitives.

## Channels
Channels are a typed conduit through which you can send and receive values with the channle oprator `<-`

```go
ch <- v    // Send v to channel ch.
v := <-ch  // Receive from ch, and assign value to v.
```
Channels muse be create before use:

```go 
ch := make(chan int)
```
By default, sends and receives ___block___ until the other side is ready. This allows goroutines to synchronize without explicit locks or condition variables.

##Buffered Channels
Channels can be ___buffered___. Provide the buffer length as the second argument to `make` to initialize a buffer channel:

```go
ch := make(chan int, 100)
```

Sends to a buffered channel block only when the channel is full. Receives block when the channel is empty.

## Range and Close
A sender can `close` a channel to indicate that no more value will be sent. Receivers can test whether a channel has been closed by assign a second parameter to the receive experss:

```go
v, ok := <- channel  // ok == false when the channel is closed

for i := range c {
	fmt.Println(i)
}

close(ch)  // close channel
```

The loop `for i := range channel` receives value from the channel until the channel closed.

___Note:___ 

* Only the sender should close a channel. Sending on a closed channel will cause a panic.
* Channel is not like file, don't need explicit close them. Closing is necessary when the receiver must be told. such as a `range` loop.

## Select
The `select` statement lets goroutine wait on multiple communication operations.

A `select` ___blocks until one of its case can run___, then it executes that case. It chooses one random if multiple are ready.

```go
fun fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
			case c <- x:
				x, y = y, x + y
			case <-quit:
				fmt.Println("quit")
				return
		}
	}
}
```

* Default Selection

The `default` case in a `select` is run if no other case is ready.

Use a `default` case to try a send or receive without blocking

```go
select {
case i := <-c:
	// use i
default:
	// receiving from c would block
}
```
##sync.Mutex
Go lang provides mutual exclusion with `sync.Mutex` and its two method

```go
m := sync.Mutex
// Lock
m.Lock()
// Unlock, surrounding the code or use defer
m.Unlock()
defer m.Unlock()
```