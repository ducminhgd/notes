---
type: go
---
# Go common mistakes

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