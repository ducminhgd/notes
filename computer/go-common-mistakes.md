---
type: go
---
# [[Go]] common mistakes

[[Sources]]:
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

### [[Bitwise]] [[NOT Operator]]

Most of other languages: `~`

Go: `^`

### Operator Precedence Differences

```go
package main

import "fmt"

func main() {  
    fmt.Printf("0x2 & 0x2 + 0x4 -> %#x\n",0x2 & 0x2 + 0x4)
    //prints: 0x2 & 0x2 + 0x4 -> 0x6
    //Go:    (0x2 & 0x2) + 0x4
    //C++:    0x2 & (0x2 + 0x4) -> 0x2

    fmt.Printf("0x2 + 0x2 << 0x1 -> %#x\n",0x2 + 0x2 << 0x1)
    //prints: 0x2 + 0x2 << 0x1 -> 0x6
    //Go:     0x2 + (0x2 << 0x1)
    //C++:   (0x2 + 0x2) << 0x1 -> 0x8

    fmt.Printf("0xf | 0x2 ^ 0x2 -> %#x\n",0xf | 0x2 ^ 0x2)
    //prints: 0xf | 0x2 ^ 0x2 -> 0xd
    //Go:    (0xf | 0x2) ^ 0x2
    //C++:    0xf | (0x2 ^ 0x2) -> 0xf
}
```

### Unexported Structure Fields Are Not Encoded

The struct fields *starting with lowercase letters **will not** be* (json, xml, gob, etc.) encoded, so when you decode the structure you'll end up with zero values in those unexported fields.

### App Exits With Active Goroutines

One of the most common solutions is to use a "WaitGroup" variable. It will allow the main goroutine to wait until all worker goroutines are done. If your app has long running workers with message processing loops you'll also need a way to signal those goroutines that it's time to exit. You can send a "kill" message to each worker. Another option is to close a channel all workers are receiving from. It's a simple way to signal all goroutines at once.

```go
package main

import (  
    "fmt"
    "sync"
)

func main() {  
    var wg sync.WaitGroup
    done := make(chan struct{})
    workerCount := 2

    for i := 0; i < workerCount; i++ {
        wg.Add(1)
        go doit(i,done,wg)
    }

    close(done)
    wg.Wait()
    fmt.Println("all done!")
}

func doit(workerId int,done <-chan struct{},wg sync.WaitGroup) {  
    fmt.Printf("[%v] is running\n",workerId)
    defer wg.Done()
    <- done
    fmt.Printf("[%v] is done\n",workerId)
}
```

### Sending to an [[Unbuffered]] [[Channel]] Returns As Soon As the Target Receiver Is Ready

The sender will not be blocked until your message is processed by the receiver. Depending on the machine where you are running the code, the receiver goroutine may or may not have enough time to process the message before the sender continues its execution.

```go
package main

import "fmt"

func main() {  
    ch := make(chan string)

    go func() {
        for m := range ch {
            fmt.Println("processed:",m)
        }
    }()

    ch <- "cmd.1"
    ch <- "cmd.2" //won't be processed
}
```

### Sending to an Closed [[Channel]] Causes a [[Panic]]

Error:

```go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    ch := make(chan int)
    for i := 0; i < 3; i++ {
        go func(idx int) {
            ch <- (idx + 1) * 2
        }(i)
    }

    //get the first result
    fmt.Println(<-ch)
    close(ch) //not ok (you still have other senders)
    //do other work
    time.Sleep(2 * time.Second)
}
```

Works

```go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    ch := make(chan int)
    done := make(chan struct{})
    for i := 0; i < 3; i++ {
        go func(idx int) {
            select {
            case ch <- (idx + 1) * 2: fmt.Println(idx,"sent result")
            case <- done: fmt.Println(idx,"exiting")
            }
        }(i)
    }

    //get first result
    fmt.Println("result:",<-ch)
    close(done)
    //do other work
    time.Sleep(3 * time.Second)
}
```

### Using "[[nil]]" [[Channel]]s

Send and receive operations on a nil channel block forver. It's a well documented behavior, but it can be a surprise for new Go developers.

## Intermediate

### Closing [[HTTP]] Response Body

```go
package main

import (  
    "fmt"
    "net/http"
    "io/ioutil"
)

func main() {  
    resp, err := http.Get("https://api.ipify.org?format=json")
    // defer resp.Body.Close() // not ok
    if err != nil {
        fmt.Println(err)
        return
    }

    defer resp.Body.Close() //ok, most of the time :-)
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Println(err)
        return
    }

    fmt.Println(string(body))
}
```

### Closing [[HTTP]] Connections

Some HTTP servers keep network connections open for a while (based on the [[HTTP 1.1]] spec and the server "[[keep-alive]]" configurations). By default, the standard http library will close the network connections only when the target HTTP server asks for it. This means your app may run out of sockets/file descriptors under certain conditions.

You can ask the http library to close the connection after your request is done by setting the `Close` field in the request variable to `true`.

Another option is to add a `Connection` request header and set it to `close`. The target HTTP server should respond with a `Connection: close` header too. When the http library sees this response header it will also close the connection.

```go
package main

import (  
    "fmt"
    "net/http"
    "io/ioutil"
)

func main() {  
    req, err := http.NewRequest("GET","http://golang.org",nil)
    if err != nil {
        fmt.Println(err)
        return
    }

    req.Close = true
    //or do this:
    //req.Header.Add("Connection", "close")

    resp, err := http.DefaultClient.Do(req)
    if resp != nil {
        defer resp.Body.Close()
    }

    if err != nil {
        fmt.Println(err)
        return
    }

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Println(err)
        return
    }

    fmt.Println(len(string(body)))
}
```

### [[JSON]] Encoder Adds a [[Newline]] Character

```go
import (
  "fmt"
  "encoding/json"
  "bytes"
)

func main() {
  data := map[string]int{"key": 1}
  
  var b bytes.Buffer
  json.NewEncoder(&b).Encode(data)

  raw,_ := json.Marshal(data)
  
  if b.String() == string(raw) {
    fmt.Println("same encoded data")
  } else {
    fmt.Printf("'%s' != '%s'\n",raw,b.String())
    //prints:
    //'{"key":1}' != '{"key":1}\n'
  }
}
```

### [[JSON]] Package Escapes Special [[HTML]] Characters in Keys and String Values

```go
package main

import (
  "fmt"
  "encoding/json"
  "bytes"
)

func main() {
  data := "x < y"
  
  raw,_ := json.Marshal(data)
  fmt.Println(string(raw))
  //prints: "x \u003c y" <- probably not what you expected
  
  var b1 bytes.Buffer
  json.NewEncoder(&b1).Encode(data)
  fmt.Println(b1.String())
  //prints: "x \u003c y" <- probably not what you expected
  
  var b2 bytes.Buffer
  enc := json.NewEncoder(&b2)
  enc.SetEscapeHTML(false)
  enc.Encode(data)
  fmt.Println(b2.String())
  //prints: "x < y" <- looks better
}
```

### [[Unmarshal]]ling [[JSON]] Numbers into Interface Values

By default, [[Go]] treats numeric values in [[JSON]] as [[float64]] numbers when you [[decode]]/[[unmarshal]] JSON data into an interface

```go
package main

import (  
  "encoding/json"
  "fmt"
)

func main() {  
  var data = []byte(`{"status": 200}`)

  var result map[string]interface{}
  if err := json.Unmarshal(data, &result); err != nil {
    fmt.Println("error:", err)
    return
  }

  var status = result["status"].(int) //error
  fmt.Println("status value:",status)
}
```

- Option one: use the float value as-is

- Option two: convert the float value to the integer type you need.

```go
package main

import (  
  "encoding/json"
  "fmt"
)

func main() {  
  var data = []byte(`{"status": 200}`)

  var result map[string]interface{}
  if err := json.Unmarshal(data, &result); err != nil {
    fmt.Println("error:", err)
    return
  }

  var status = uint64(result["status"].(float64)) //ok
  fmt.Println("status value:",status)
}
```

- Option three: use a [[Decoder]] type to unmarshal [[JSON]] and tell it to represent [[JSON]] numbers using the [[Number]] interface type.

```go
package main

import (  
  "encoding/json"
  "bytes"
  "fmt"
)

func main() {  
  var data = []byte(`{"status": 200}`)

  var result map[string]interface{}
  var decoder = json.NewDecoder(bytes.NewReader(data))
  decoder.UseNumber()

  if err := decoder.Decode(&result); err != nil {
    fmt.Println("error:", err)
    return
  }

  var status,_ = result["status"].(json.Number).Int64() //ok
  fmt.Println("status value:",status)
}
```

```go
package main

import (  
  "encoding/json"
  "bytes"
  "fmt"
)

func main() {  
  var data = []byte(`{"status": 200}`)

  var result map[string]interface{}
  var decoder = json.NewDecoder(bytes.NewReader(data))
  decoder.UseNumber()

  if err := decoder.Decode(&result); err != nil {
    fmt.Println("error:", err)
    return
  }

  var status uint64
  if err := json.Unmarshal([]byte(result["status"].(json.Number).String()), &status); err != nil {
    fmt.Println("error:", err)
    return
  }

  fmt.Println("status value:",status)
}
```

- Option four: use a `struct` type that maps your numeric value to the numeric type you need.

```go
package main

import (  
  "encoding/json"
  "bytes"
  "fmt"
)

func main() {  
  var data = []byte(`{"status": 200}`)

  var result struct {
    Status uint64 `json:"status"`
  }

  if err := json.NewDecoder(bytes.NewReader(data)).Decode(&result); err != nil {
    fmt.Println("error:", err)
    return
  }

  fmt.Printf("result => %+v",result)
  //prints: result => {Status:200}
}
```

- Option five: use a `struct` that maps your numeric value to the `json.RawMessage` type if you need to defer the value decoding.

```go
package main

import (  
  "encoding/json"
  "bytes"
  "fmt"
)

func main() {  
  records := [][]byte{
    []byte(`{"status": 200, "tag":"one"}`),
    []byte(`{"status":"ok", "tag":"two"}`),
  }

  for idx, record := range records {
    var result struct {
      StatusCode uint64
      StatusName string
      Status json.RawMessage `json:"status"`
      Tag string             `json:"tag"`
    }

    if err := json.NewDecoder(bytes.NewReader(record)).Decode(&result); err != nil {
      fmt.Println("error:", err)
      return
    }

    var sstatus string
    if err := json.Unmarshal(result.Status, &sstatus); err == nil {
      result.StatusName = sstatus
    }

    var nstatus uint64
    if err := json.Unmarshal(result.Status, &nstatus); err == nil {
      result.StatusCode = nstatus
    }

    fmt.Printf("[%v] result => %+v\n",idx,result)
  }
}
```

### [[JSON]] String Values Will Not Be Ok with Hex or Other non-[[UTF8]] Escape Sequences

Go expects string values to be UTF8 encoded. This means you can't have arbitrary hex escaped binary data in your JSON strings (and you also have to escape the backslash character). This is really a JSON gotcha Go inherited, but it happens often enough in Go apps that it makes sense to mention it anyways.

```go
package main

import (
  "fmt"
  "encoding/json"
)

type config struct {
  Data string `json:"data"`
}

func main() {
  raw := []byte(`{"data":"\xc2"}`)
  var decoded config

  if err := json.Unmarshal(raw, &decoded); err != nil {
     fmt.Println(err)
     //prints: invalid character 'x' in string escape code
  }
  
}
```

If you do need to have a backslash in you string make sure to escape it with another backlash. If you want to use hex encoded binary data you can escape the backslash and then do your own hex escaping with the decoded data in your JSON string.

```go
package main

import (
  "fmt"
  "encoding/json"
)

type config struct {
  Data string `json:"data"`
}

func main() {
  raw := []byte(`{"data":"\\xc2"}`)
  
  var decoded config
  
  json.Unmarshal(raw, &decoded)
  
  fmt.Printf("%#v",decoded) //prints: main.config{Data:"\\xc2"}
  //todo: do your own hex escape decoding for decoded.Data  
}
```

Another option is to use the byte array/slice data type in your JSON object, but the binary data will have to be [[base64]] encoded.

```go
package main

import (
  "fmt"
  "encoding/json"
)

type config struct {
  Data []byte `json:"data"`
}

func main() {
  raw := []byte(`{"data":"wg=="}`)
  var decoded config
  
  if err := json.Unmarshal(raw, &decoded); err != nil {
          fmt.Println(err)
      }
  
  fmt.Printf("%#v",decoded) //prints: main.config{Data:[]uint8{0xc2}}
}
```

### Comparing [[Struct]]s, [[Array]]s, [[Slice]]s, and [[Map]]s

```go
package main

import "fmt"

type data struct {  
    num int
    fp float32
    complex complex64
    str string
    char rune
    yes bool
    events <-chan string
    handler interface{}
    ref *byte
    raw [10]byte
}

func main() {  
    v1 := data{}
    v2 := data{}
    fmt.Println("v1 == v2:",v1 == v2) //prints: v1 == v2: true
}
```

If any of the struct fields are not comparable then using the equality operator will result in compile time errors. Note that arrays are comparable only if their data items are comparable.

```go
package main

import "fmt"

type data struct {  
    num int                //ok
    checks [10]func() bool //not comparable
    doit func() bool       //not comparable
    m map[string] string   //not comparable
    bytes []byte           //not comparable
}

func main() {  
    v1 := data{}
    v2 := data{}
    fmt.Println("v1 == v2:",v1 == v2)
}
```

Go does provide a number of helper functions to compare variables that can't be compared using the comparison operators.

The most generic solution is to use the `DeepEqual()` function in the `reflect` package.

`DeepEqual()` doesn't consider an empty slice to be equal to a `nil` slice. This behavior is different from the behavior you get using the `bytes.Equal()` function. `bytes.Equal()` considers `nil` and empty slices to be equal.

`DeepEqual()` isn't always perfect comparing slices.

> If your byte slices (or strings) contain text data you might be tempted to use ToUpper() or ToLower() from the "bytes" and "strings" packages when you need to compare values in a case insensitive manner (before using ==,bytes.Equal(), or bytes.Compare()). It will work for English text, but it will not work for text in many other languages. strings.EqualFold() and bytes.EqualFold() should be used instead.

> If your byte slices contain secrets (e.g., cryptographic hashes, tokens, etc.) that need to be validated against user-provided data, don't use reflect.DeepEqual(), bytes.Equal(), or bytes.Compare() because those functions will make your application vulnerable to timing attacks. To avoid leaking the timing information use the functions from the 'crypto/subtle' package (e.g., subtle.ConstantTimeCompare()).

### Recovering From a [[Panic]]

Incorrect

```go
package main

import "fmt"

func main() {  
    recover() //doesn't do anything
    panic("not good")
    recover() //won't be executed :)
    fmt.Println("ok")
}
```

Works

```go
package main

import "fmt"

func main() {  
    defer func() {
        fmt.Println("recovered:",recover())
    }()

    panic("not good")
}
```

The call to `recover()` works only if it's called directly in your deferred function.

### Updating and Referencing Item Values in Slice, Array, and Map "range" Clauses

```go
package main

import "fmt"

func main() {  
    data := []int{1,2,3}
    for _,v := range data { // data is generated by `range` is a copy
        v *= 10 //original item is not changed
    }

    fmt.Println("data:",data) //prints data: [1 2 3]
}
```

### "Hidden" Data in [[Slice]]s

When you reslice a slice, the new slice will reference the array of the original slice. If you forget about this behavior it can lead to unexpected memory usage if your application allocates large temporary slices creating new slices from them to refer to small sections of the original data.

```go
package main

import "fmt"

func get() []byte {  
    raw := make([]byte,10000)
    fmt.Println(len(raw),cap(raw),&raw[0]) //prints: 10000 10000 <byte_addr_x>
    return raw[:3]
}

func main() {  
    data := get()
    fmt.Println(len(data),cap(data),&data[0]) //prints: 3 10000 <byte_addr_x>
}
```

To avoid this trap make sure to copy the data you need from the temporary slice (instead of reslicing it).

### Slice Data "Corruption"

```go
package main

import (  
    "fmt"
    "bytes"
)

func main() {  
    path := []byte("AAAA/BBBBBBBBB")
    sepIndex := bytes.IndexByte(path,'/')
    dir1 := path[:sepIndex]
    dir2 := path[sepIndex+1:]
    fmt.Println("dir1 =>",string(dir1)) //prints: dir1 => AAAA
    fmt.Println("dir2 =>",string(dir2)) //prints: dir2 => BBBBBBBBB

    dir1 = append(dir1,"suffix"...)
    path = bytes.Join([][]byte{dir1,dir2},[]byte{'/'})

    fmt.Println("dir1 =>",string(dir1)) //prints: dir1 => AAAAsuffix
    fmt.Println("dir2 =>",string(dir2)) //prints: dir2 => uffixBBBB (not ok)

    fmt.Println("new path =>",string(path))
}
```

Fix:

```go
package main

import (  
    "fmt"
    "bytes"
)

func main() {  
    path := []byte("AAAA/BBBBBBBBB")
    sepIndex := bytes.IndexByte(path,'/')
    dir1 := path[:sepIndex:sepIndex] //full slice expression
    dir2 := path[sepIndex+1:]
    fmt.Println("dir1 =>",string(dir1)) //prints: dir1 => AAAA
    fmt.Println("dir2 =>",string(dir2)) //prints: dir2 => BBBBBBBBB

    dir1 = append(dir1,"suffix"...)
    path = bytes.Join([][]byte{dir1,dir2},[]byte{'/'})

    fmt.Println("dir1 =>",string(dir1)) //prints: dir1 => AAAAsuffix
    fmt.Println("dir2 =>",string(dir2)) //prints: dir2 => BBBBBBBBB (ok now)

    fmt.Println("new path =>",string(path))
}
```

### "Stale" Slices

Multiple slices can reference the same data. This can happen when you create a new slice from an existing slice, for example. If your application relies on this behavior to function properly then you'll need to worry about "stale" slices.

### Type Declarations and Methods

When you create a type declaration by defining a new type from an existing (non-interface) type, you don't inherit the methods defined for that existing type.

If you do need the methods from the original type you can define a new struct type embedding the original type as an anonymous field.

```go
package main

import "sync"

type myLocker struct {  
    sync.Mutex
}

func main() {  
    var lock myLocker
    lock.Lock() //ok
    lock.Unlock() //ok
}
```

Interface type declarations also retain their method sets.

```go
package main

import "sync"

type myLocker sync.Locker

func main() {  
    var lock myLocker = new(sync.Mutex)
    lock.Lock() //ok
    lock.Unlock() //ok
}
```

### Breaking Out of "for [[switch]]" and "for [[select]]" Code Blocks

A "break" statement without a label only gets you out of the inner switch/select block. If using a "return" statement is not an option then defining a label for the outer loop is the next best thing.

```go
package main

import "fmt"

func main() {  
    loop:
        for {
            switch {
            case true:
                fmt.Println("breaking out...")
                break loop
            }
        }

    fmt.Println("out!")
}
```

A "goto" statement will do the trick too...

### Iteration Variables and Closures in "for" Statements

This is the most common gotcha in Go. The iteration variables in `for` statements are reused in each iteration. This means that each closure (aka function literal) created in your `for` loop will reference the same variable (and they'll get that variable's value at the time those goroutines start executing).

Fail:

```go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    data := []string{"one","two","three"}

    for _,v := range data {
        go func() {
            fmt.Println(v)
        }()
    }

    time.Sleep(3 * time.Second)
    //goroutines print: three, three, three
}
```

Work:

```go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    data := []string{"one","two","three"}

    for _,v := range data {
        vcopy := v //
        go func() {
            fmt.Println(vcopy)
        }()
    }

    time.Sleep(3 * time.Second)
    //goroutines print: one, two, three
}
```

or

```go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    data := []string{"one","two","three"}

    for _,v := range data {
        go func(in string) {
            fmt.Println(in)
        }(v)
    }

    time.Sleep(3 * time.Second)
    //goroutines print: one, two, three
}
```

### Deferred Function Call Argument Evaluation

```go
package main

import "fmt"

func main() {  
    var i int = 1

    defer fmt.Println("result =>",func() int { return i * 2 }())
    i++
    //prints: result => 2 (not ok if you expected 4)
}
```

If you have pointer parameters it is possible to change the values they point to because only the pointer is saved when the `defer` statement is evaluated.

```go
package main

import (
  "fmt"
)

func main() {
  i := 1
  defer func (in *int) { fmt.Println("result =>", *in) }(&i)
  
  i = 2
  //prints: result => 2
}
```

### Deferred Function Call Execution

The deferred calls are executed at the end of the containing function (and in reverse order) and not at the end of the containing code block

```go
package main

import (  
    "fmt"
    "os"
    "path/filepath"
)

func main() {  
    if len(os.Args) != 2 {
        os.Exit(-1)
    }

    start, err := os.Stat(os.Args[1])
    if err != nil || !start.IsDir(){
        os.Exit(-1)
    }

    var targets []string
    filepath.Walk(os.Args[1], func(fpath string, fi os.FileInfo, err error) error {
        if err != nil {
            return err
        }

        if !fi.Mode().IsRegular() {
            return nil
        }

        targets = append(targets,fpath)
        return nil
    })

    for _,target := range targets {
        f, err := os.Open(target)
        if err != nil {
            fmt.Println("bad target:",target,"error:",err) //prints error: too many open files
            break
        }
        defer f.Close() //will not be closed at the end of this code block
        //do something with the file...
    }
}
```

Wrap the code block in a function will help

```go
package main

import (  
    "fmt"
    "os"
    "path/filepath"
)

func main() {  
    if len(os.Args) != 2 {
        os.Exit(-1)
    }

    start, err := os.Stat(os.Args[1])
    if err != nil || !start.IsDir(){
        os.Exit(-1)
    }

    var targets []string
    filepath.Walk(os.Args[1], func(fpath string, fi os.FileInfo, err error) error {
        if err != nil {
            return err
        }

        if !fi.Mode().IsRegular() {
            return nil
        }

        targets = append(targets,fpath)
        return nil
    })

    for _,target := range targets {
        func() {
            f, err := os.Open(target)
            if err != nil {
                fmt.Println("bad target:",target,"error:",err)
                return
            }
            defer f.Close() //ok
            //do something with the file...
        }()
    }
}
```

### Failed [[Type]] [[Assertion]]s

Incorrect

```go
package main

import "fmt"

func main() {  
    var data interface{} = "great"

    if data, ok := data.(int); ok {
        fmt.Println("[is an int] value =>",data)
    } else {
        fmt.Println("[not an int] value =>",data) 
        //prints: [not an int] value => 0 (not "great")
    }
}
```

Correct

```go
package main

import "fmt"

func main() {  
    var data interface{} = "great"

    if res, ok := data.(int); ok {
        fmt.Println("[is an int] value =>",res)
    } else {
        fmt.Println("[not an int] value =>",data) 
        //prints: [not an int] value => great (as expected)
    }
}
```

### Blocked [[Goroutine]]s and Resource [[Leak]]s

```go
func First(query string, replicas ...Search) Result {  
    c := make(chan Result)
    searchReplica := func(i int) { c <- replicas[i](query) }
    for i := range replicas {
        go searchReplica(i)
    }
    return <-c
}
```
The function starts a goroutines for each search replica. Each goroutine sends its search result to the result channel. The first value from the result channel is returned.

What about the results from the other goroutines? What about the goroutines themselves?

The result channel in the `First()` function is unbuffered. This means that only the first goroutine returns. All other goroutines are stuck trying to send their results. This means if you have more than one replica each call will leak resources.

To avoid the leaks you need to make sure all goroutines exit. One potential solution is to use a buffered result channel big enough to hold all results.

```go
func First(query string, replicas ...Search) Result {  
    c := make(chan Result,len(replicas))
    searchReplica := func(i int) { c <- replicas[i](query) }
    for i := range replicas {
        go searchReplica(i)
    }
    return <-c
}
```

Using `select` with `default` case


```go
func First(query string, replicas ...Search) Result {  
    c := make(chan Result,1)
    searchReplica := func(i int) { 
        select {
        case c <- replicas[i](query):
        default:
        }
    }
    for i := range replicas {
        go searchReplica(i)
    }
    return <-c
}
```

Or using a special cancellation

```go
func First(query string, replicas ...Search) Result {  
    c := make(chan Result)
    done := make(chan struct{})
    defer close(done)
    searchReplica := func(i int) { 
        select {
        case c <- replicas[i](query):
        case <- done:
        }
    }
    for i := range replicas {
        go searchReplica(i)
    }

    return <-c
}
```

### Same [[Address]] for Different Zero-sized Variables

If you have two different variables shouldn't they have different addresses? Well, it's not the case with Go. If you have zero-sized variables they might share the exact same address in memory.

```go
package main

import (
  "fmt"
)

type data struct {
}

func main() {
  a := &data{}
  b := &data{}
  
  if a == b {
    fmt.Printf("same address - a=%p b=%p\n",a,b)
    //prints: same address - a=0x1953e4 b=0x1953e4
  }
}
```

### The First Use of [[iota]] Doesn't Always Start with Zero

```go
package main

import (
  "fmt"
)

const (
  azero = iota
  aone  = iota
)

const (
  info  = "processing"
  bzero = iota
  bone  = iota
)

func main() {
  fmt.Println(azero,aone) //prints: 0 1
  fmt.Println(bzero,bone) //prints: 1 2
}
```

## Advanced

### Using [[Pointer]] Receiver Methods On Value Instances

It's OK to call a pointer receiver method on a value as long as the value is addressable. In other words, you don't need to have a value receiver version of the method in some cases.

```go
package main

import "fmt"

type data struct {  
    name string
}

func (p *data) print() {  
    fmt.Println("name:",p.name)
}

type printer interface {  
    print()
}

func main() {  
    d1 := data{"one"}
    d1.print() //ok

    var in printer = data{"two"} //error
    in.print()

    m := map[string]data {"x":data{"three"}}
    m["x"].print() //error
}
```

### Updating [[Map]] Value Fields

If you have a map of struct values you can't update individual struct fields.

```go
package main

type data struct {  
    name string
}

func main() {  
    m := map[string]data {"x":{"one"}}
    m["x"].name = "two" //error
}
```

It doesn't work because map elements are not addressable.

What can be extra confusing for new Go devs is the fact that slice elements are addressable.

```go
package main

import "fmt"

type data struct {  
    name string
}

func main() {  
    s := []data {{"one"}}
    s[0].name = "two" //ok
    fmt.Println(s)    //prints: [{two}]
}
```

- The first work around is to use a temporary variable.

```go
package main

import "fmt"

type data struct {  
    name string
}

func main() {  
    m := map[string]data {"x":{"one"}}
    r := m["x"]
    r.name = "two"
    m["x"] = r
    fmt.Printf("%v",m) //prints: map[x:{two}]
}
```

- Another workaround is to use a map of pointers.

```go
package main

import "fmt"

type data struct {  
    name string
}

func main() {  
    m := map[string]*data {"x":{"one"}}
    m["x"].name = "two" //ok
    fmt.Println(m["x"]) //prints: &{two}
}
```

### [[nil]] Interfaces and nil Interfaces Values

```go
package main

import "fmt"

func main() {  
    var data *byte
    var in interface{}

    fmt.Println(data,data == nil) //prints: <nil> true
    fmt.Println(in,in == nil)     //prints: <nil> true

    in = data
    fmt.Println(in,in == nil)     //prints: <nil> false
    //'data' is 'nil', but 'in' is not 'nil'
}
```

### [[Stack]] and [[Heap]] Variables

You don't always know if your variable is allocated on the stack or heap. In C++ creating variables using the `new` operator always means that you have a heap variable. In Go the compiler decides where the variable will be allocated even if the `new()` or `make()` functions are used. The compiler picks the location to store the variable based on its size and the result of "escape analysis". This also means that it's ok to return references to local variables, which is not ok in other languages like C or C++.

If you need to know where your variables are allocated pass the "-m" gc flag to "go build" or "go run" (e.g., `go run -gcflags -m app.go`).

### [[GOMAXPROCS]], [[Concurrency]], and [[Parallelism]]

Go 1.4 and below uses only one execution context / OS thread. This means that only one goroutine can execute at any given time. Starting with 1.5 Go sets the number of execution contexts to the number of logical CPU cores returned by `runtime.NumCPU()`. That number may or may not match the total number of logical CPU cores on your system depending on the CPU affinity settings of your process. You can adjust this number by changing the `GOMAXPROCS` environment variable or by calling the `runtime.GOMAXPROCS()` function.

There's a common misconception that `GOMAXPROCS` represents the number of CPUs Go will use to run goroutines. The `runtime.GOMAXPROCS()` function documentation adds more to the confusion. The `GOMAXPROCS` variable description (https://golang.org/pkg/runtime/) does a better job talking about OS threads.

You can set `GOMAXPROCS` to more than the number of your CPUs. As of 1.10 there's no longer a limit for `GOMAXPROCS`. The max value for `GOMAXPROCS` used to be 256 and it was later increased to 1024 in 1.9.

### Read and Write Operation Reordering

Go may reorder some operations, but it ensures that the overall behavior in the goroutine where it happens doesn't change. However, it doesn't guarantee the order of execution across multiple goroutines.

### [[Preemptive]] Scheduling

It's possible to have a rogue goroutine that prevents other goroutines from running. It can happen if you have a `for` loop that doesn't allow the scheduler to run.

```go
package main

import "fmt"

func main() {  
    done := false

    go func(){
        done = true
    }()

    for !done {
    }
    fmt.Println("done!")
}
```

The `for` loop doesn't have to be empty. It'll be a problem as long as it contains code that doesn't trigger the scheduler execution.

The scheduler will run after GC, "go" statements, blocking channel operations, blocking system calls, and lock operations. It may also run when a non-inlined function is called.

```go
package main

import "fmt"

func main() {  
    done := false

    go func(){
        done = true
    }()

    for !done {
        fmt.Println("not done!") //not inlined
    }
    fmt.Println("done!")
}
```

To find out if the function you call in the for loop is inlined pass the "-m" gc flag to "go build" or "go run" (e.g., `go build -gcflags -m`).

Another option is to invoke the scheduler explicitly. You can do it with the `Gosched()` function from the "runtime" package.

```go
package main

import (  
    "fmt"
    "runtime"
)

func main() {  
    done := false

    go func(){
        done = true
    }()

    for !done {
        runtime.Gosched()
    }
    fmt.Println("done!")
}
```

## [[Cgo]]

### Import C and Multiline Import Blocks

You need to import the "C" package to use `Cgo`. You can do that with a single line `import` or you can do it with an `import` block.

```go
package main

/*
#include <stdlib.h>
*/
import (
  "C"
)

import (
  "unsafe"
)

func main() {
  cs := C.CString("my go string")
  C.free(unsafe.Pointer(cs))
}
```

### No blank lines Between Import C and [[Cgo]] Comments

```go
package main

/*
#include <stdlib.h>
*/

import "C"

import (
  "unsafe"
)

func main() {
  cs := C.CString("my go string")
  C.free(unsafe.Pointer(cs))
}

// ./main.go:15:2: could not determine kind of name for C.free
```

### Can't Call C Functions with Variable Arguments

Fail:

```go
package main

/*
#include <stdio.h>
#include <stdlib.h>
*/
import "C"

import (
  "unsafe"
)

func main() {
  cstr := C.CString("go")
  C.printf("%s\n",cstr) //not ok
  C.free(unsafe.Pointer(cstr))
}
```

Works:

```go
package main

/*
#include <stdio.h>
#include <stdlib.h>

void out(char* in) {
  printf("%s\n", in);
}
*/
import "C"

import (
  "unsafe"
)

func main() {
  cstr := C.CString("go")
  C.out(cstr) //ok
  C.free(unsafe.Pointer(cstr))
}
```

[//begin]: # "Autogenerated link references for markdown compatibility"
[Sources]: sources "Sources"
[//end]: # "Autogenerated link references"