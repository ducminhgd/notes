---
type: go
---
# Go

The programming language.

## Arrays, Slices and Strings

It took a year to answer these questions to design [[Array]]s, [[Slice]]s in Go:

- fixed-size or variable-size?
- is the size part of the type?
- what do multidimensional arrays look like?
- does the empty array have meaning?

The key step was the introduction of slices, which built on fixed-size arrays to give a flexible, extensible data structure

The declaration `var buffer [256]byte` declares the variable `buffer`, which holds `256 bytes`. The type of `buffer` includes its size, `[256]byte`. An array with 512 bytes would be of the distinct type `[512]byte`.

Arrays have their place—they are a good representation of a transformation matrix for instance—but their most common purpose in Go is to hold storage for a slice.

## Slices: The slice header

A slice is a data structure describing a contiguous section of an array stored separately from the slice variable itself. *A slice is not an array*. A slice *describes* a piece of an array.

It's important to understand that even though a slice contains a pointer, it is itself a value. Under the covers, it is a struct value holding a *pointer* and a length. *It is not a pointer to a struct*.

```go
type sliceHeader struct {
    Length        int
    Capacity      int
    ZerothElement *byte
}
```

The `Capacity` field records how much space the underlying array actually has; it is the maximum value the `Length` can reach

`make` function allocates a new array and creates a slice header to describe it, all at once. The `make` function takes three arguments: the type of the slice, its initial length, and its capacity, which is the length of the array that make allocates to hold the slice data

## [[Context]]

1. Can use `WithTimeout` to set [[Timeout]] for a job.

## [[Channel]]

- A send to a nil channel blocks forever

```go
package main

func main() {
    var c chan string
    c <- "let's get started" // deadlock
}
```

- A receive from a nil channel blocks forever

```go
package main

import "fmt"

func main() {
        var c chan string
        fmt.Println(<-c) // deadlock
}
```

- A send to a closed channel panics

```go
package main

import "fmt"

func main() {
    var c = make(chan int, 100)
    for i := 0; i < 10; i++ {
        go func() {
            for j := 0; j < 10; j++ {
                c <- j
            }
            close(c)
        }()
    }
    for i := range c {
        fmt.Println(i)
    }
}

// check if channel is isClosed
// if !isClosed(c) {
//         // c isn't closed, send the value
//         c <- v
// }
```

- A receive from a closed channel returns the zero value immediately

```go
package main

import "fmt"

func main() {
    c := make(chan int, 3)
    c <- 1
    c <- 2
    c <- 3
    close(c)
    for i := 0; i < 4; i++ {
        fmt.Printf("%d ", <-c) // prints 1 2 3 0
    }
}

// Solution for range loop
for v := range c {
    // do something with v
}

for v, ok := <- c; ok ; v, ok = <- c {
    // do something with v
}
```

## Others

- Go does not have reference variables
- Maps and channels are not references. If they were this program would print `false`.

  ```go
  package main

    import "fmt"

    func fn(m map[int]int) {
        m = make(map[int]int)
    }

    func main() {
        var m map[int]int
        fn(m)
        fmt.Println(m == nil)
    }
  ```

- When you write the statement `m := make(map[int]int)` the compiler replaces it with a call to `runtime.makemap`.
  > // makemap implements a Go map creation make(map[k]v, hint)\
  > // If the compiler has determined that the map or the first bucket\
  > // can be created on the stack, h and/or bucket may be non-nil.\
  > // If h != nil, the map can be created directly in h.\
  > // If bucket != nil, bucket can be used as the first bucket.\
  > func makemap(t *maptype, hint int64, h*hmap, bucket unsafe.Pointer) *hmap
  
  As you see, the type of the value returned from `runtime.makemap` is a pointer to a `runtime.hmap` structure. We cannot see this from normal Go code, but we can confirm that a map value is the same size as a [[uintptr]]–one machine word.

  ```go
  package main

  import (
    "fmt"
    "unsafe"
  )

  func main() {
    var m map[int]int
    var p uintptr
    fmt.Println(unsafe.Sizeof(m), unsafe.Sizeof(p)) // 8 8 (linux/amd64)
  }
  ```

  If maps are pointers, shouldn't they be *map[key]value?

  > "In the very early days what we call maps now were written as pointers, so you wrote *map[int]int. We moved away from that when we realized that no one ever wrote `map` without writing `*map`." - Ian Taylor

  **Maps, like channels, but unlike slices, are just pointers to `runtime` types**

## [[Logging]]

- With `log` package:
  Go logger [flags](https://pkg.go.dev/log#pkg-constants)

  Using `log.SetFlags(log.LstdFlags | log.Lshortfile | log.Lmicroseconds)`

- With `runtime.Caller`

    ```go
    func HandleError(err error) (b bool) {
        if err != nil {
            // notice that we're using 1, so it will actually log where
            // the error happened, 0 = this function, we don't want that.
            _, fn, line, _ := runtime.Caller(1)
            log.Printf("[error] %s:%d %v", fn, line, err)
            b = true
        }
        return
    }

    //this logs the function name as well.
    func FancyHandleError(err error) (b bool) {
        if err != nil {
            // notice that we're using 1, so it will actually log the where
            // the error happened, 0 = this function, we don't want that.
            pc, fn, line, _ := runtime.Caller(1)

            log.Printf("[error] in %s[%s:%d] %v", runtime.FuncForPC(pc).Name(), fn, line, err)
            b = true
        }
        return
    }

    func main() {
        if FancyHandleError(fmt.Errorf("it's the end of the world")) {
            log.Print("stuff")
        }
    }
    ```

## Atomic types

From Go 1.19, `Bool`, `Int32`, `Int64`, `Uint32`, `Uint64`, `Uintptr` and `Pointer` are new atomic types under `sync/atomic`. From [Go docs](https://docs.studygolang.com/pkg/sync/atomic/):

> Package atomic provides low-level atomic memory primitives useful for implementing synchronization algorithms.
>
> These functions require great care to be used correctly. Except for special, low-level applications, synchronization is better done with channels or the facilities of the sync package. **Share memory by communicating; don't communicate by sharing memory**.

## Go vs other languages

- [[Rust]] will almost always beat Go in run-time benchmarks due to its fine-grained control over how threads behave and how resources are shared between threads. [Link](https://www.getclockwise.com/blog/rust-vs-go)

## Word size and alignment

```go
// common architecture word sizes and alignments
var gcArchSizes = map[string]*StdSizes{
    "386":      {4, 4},
    "amd64":    {8, 8},
    "amd64p32": {4, 8},
    "arm":      {4, 4},
    "arm64":    {8, 8},
    "loong64":  {8, 8},
    "mips":     {4, 4},
    "mipsle":   {4, 4},
    "mips64":   {8, 8},
    "mips64le": {8, 8},
    "ppc64":    {8, 8},
    "ppc64le":  {8, 8},
    "riscv64":  {8, 8},
    "s390x":    {8, 8},
    "sparc64":  {8, 8},
    "wasm":     {8, 8},
    // When adding more architectures here,
    // update the doc string of SizesFor below.
}
```

This may relevant to [[cache line]]

Read more: [A go alignment](https://go-review.googlesource.com/c/go/+/414214)

## Variables allocated

Go compilers will allocate variables that are local to a function in that function’s [[stack]] frame. However, if the compiler cannot prove that the variable is not referenced after the function returns, then the compiler must allocate the variable on the garbage-collected [[heap]] to avoid dangling pointer errors. Also, if a local variable is very large, it might make more sense to store it on the heap rather than the stack.

If a variable has its address taken, that variable is a candidate for allocation on the heap. However, a basic escape analysis recognizes some cases when such variables will not live past the return from the function and can reside on the stack.

Collecting stack memory blocks is also much cheaper than collecting heap memory blocks. In fact, stack memory blocks don't need to be collected. The stack of a goroutine could be actually viewed as a single memory block, and it will be collected as a whole when the goroutine exits.

## Read more

1. [Length and capacity](https://riptutorial.com/go/example/3561/length-and-capacity)
2. [Understanding Allocations in Go](https://medium.com/eureka-engineering/understanding-allocations-in-go-stack-heap-memory-9a2631b5035d)
3. [Memory Allocations](https://go101.org/optimizations/0.3-memory-allocations.html)
