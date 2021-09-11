---
type: go
---
# [[Go]] common mistakes

Source:
- [50 Shades of Go: Traps, Gotchas, and Common Mistakes for New Golang Devs](http://devs.cloudimmunity.com/gotchas-and-common-mistakes-in-go-golang/)

## Beginner

### Opening Brace Can't Be Placed on a Separate Line

```go
package main

import "fmt"

func main()  
{ //error, can't have the opening brace on a separate line
    fmt.Println("hello there!")
}
```

### Unused variables

Global variables can be unused.

### Unused imports

```go
package main

import (  
    _ "fmt"
    "log"
    "time"
)

var _ = log.Println

func main() {  
    _ = time.Now
}
```

### Short Variable Declarations Can Be Used Only Inside Functions

Failed

```go
package main

myvar := 1 //error

func main() {  
}
```

### Redeclaring Variables Using Short Variable Declarations

You can't redeclare a variable in a standalone statement, but it is allowed in multi-variable declarations where at least one new variable is also declared.

The redeclared variable has to be in the same block or you'll end up with a shadowed variable.

Fails:

```go
package main

func main() {  
    one := 0
    one := 1 //error
}
```

Works:

```go
package main

func main() {  
    one := 0
    one, two := 1,2

    one,two = two,one
}
```

### Can't Use Short Variable Declarations to Set Field Values

Fails:

```go
package main

import (  
  "fmt"
)

type info struct {  
  result int
}

func work() (int,error) {  
    return 13,nil  
  }

func main() {  
  var data info

  data.result, err := work() //error
  fmt.Printf("info: %+v\n",data)
}
```

Works:

```go
package main

import (  
  "fmt"
)

type info struct {  
  result int
}

func work() (int,error) {  
    return 13,nil  
  }

func main() {  
  var data info

  var err error
  data.result, err = work() //ok
  if err != nil {
    fmt.Println(err)
    return
  }

  fmt.Printf("info: %+v\n",data) //prints: info: {result:13}
}
```

### Accidental Variable Shadowing

```go
package main

import "fmt"

func main() {  
    x := 1
    fmt.Println(x)     //prints 1
    {
        fmt.Println(x) //prints 1
        x := 2
        fmt.Println(x) //prints 2
    }
    fmt.Println(x)     //prints 1 (bad if you need 2)
}
```

Using `vet` command in [[go tool]] to dectect this kind of issue: `go tool vet -shadow your_file.go`

### Can't Use [[nil]] to Initialize a Variable Without an Explicit Type

```go
package main

func main() {  
    var x = nil // error

    _ = x

    var y interface{} = nil // ok

    _ = y
}
```

### Using [[nil]] [[Slice]] and [[Map]]

It's OK to add items to a [[nil]] slice, but doing the same with a map will produce a runtime panic.

### [[Map]] Capacity

You can specify the map capacity when it's created, but you can't use the `cap()` function on maps.

### Strings Can't Be "nil"

`var x string //defaults to "" (zero value)`


### Unexpected Values in [[Slice]] and [[Array]] "range" Clauses

```go
package main

import "fmt"

func main() {  
    x := []string{"a","b","c"}

    for v := range x {
        fmt.Println(v) //prints 0, 1, 2
    }

    for _, v := range x {
        fmt.Println(v) //prints a, b, c
    }
}
```

### [[String]]s Are [[Immutable]]

### Conversions Between [[String]]s and [[Byte]] Slices

When you convert a string to a byte slice (and vice versa) you get a complete copy of the orginal data. It's not like a cast operation in other languages and it's not like reslicing where the new slice variable points to the same underlying array used by the original byte slice.

### [[String]]s and Index Operator

```go
package main

import "fmt"

func main() {  
    x := "text"
    fmt.Println(x[0]) //print 116
    fmt.Printf("%T",x[0]) //prints uint8
}
```

### [[String]]s Are Not Always [[UTF8]] Text

```go
package main

import (  
    "fmt"
    "unicode/utf8"
)

func main() {  
    data1 := "ABC"
    fmt.Println(utf8.ValidString(data1)) //prints: true

    data2 := "A\xfeC"
    fmt.Println(utf8.ValidString(data2)) //prints: false
}
```

### [[String]] Length

The built-in `len()`` function returns the number of bytes instead of the number of characters like it's done for unicode strings in [[Python]].

To get the same results in Go use the `RuneCountInString()` function from the `"unicode/utf8"` package.

### Missing Comma In Multi-Line [[Slice]], [[Array]], and [[Map]] Literals

### `log.Fatal` and `log.Panic` Do More Than Log

Logging libraries often provide different log levels. Unlike those logging libraries, the log package in Go does more than log if you call its `Fatal*()` and `Panic*()` functions. When your app calls those functions *[[Go]] will also terminate your app*

### Built-in Data Structure Operations Are Not [[Sync]]hronized

### Iteration Values For [[String]]s in "range" Clauses

The index value (the first value returned by the "range" operation) is the index of the first byte for the current "character" (unicode code point/rune) returned in the second value.

Make sure to check out the "norm" package (golang.org/x/text/unicode/norm) if you need to work with characters.

### Iterating Through a [[Map]] Using a "for range" Clause

### Fallthrough Behavior in "[[switch]]" Statements

The "case" blocks in "switch" statements break by default. This is different from other languages where the default behavior is to fall through to the next "case" block.

```go
package main

import "fmt"

func main_err() {  
    isSpace := func(ch byte) bool {
        switch(ch) {
        case ' ': //error
        case '\t':
            return true
        }
        return false
    }

    fmt.Println(isSpace('\t')) //prints true (ok)
    fmt.Println(isSpace(' '))  //prints false (not ok)
}

func main() {  
    isSpace := func(ch byte) bool {
        switch(ch) {
        case ' ', '\t':
            return true
        }
        return false
    }

    fmt.Println(isSpace('\t')) //prints true (ok)
    fmt.Println(isSpace(' '))  //prints true (ok)
```

### Increments and Decrements

Many languages have increment and decrement operators. Unlike other languages, Go doesn't support the prefix version of the operations. You also can't use these two operators in expressions.

```go
package main

import "fmt"

func main() {  
    data := []int{1,2,3}
    i := 0
    ++i //error
    fmt.Println(data[i++]) //error
}
```