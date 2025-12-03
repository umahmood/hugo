+++
date = "2015-08-04T23:26:39+01:00"
draft = false
title = "Gofmt and Rewrite Rules"
hideToc = true

+++

One thing I absolutely love about Go is its tooling support. Whenever I use the
numerous tools I always discover something new. In this short post I will be
showing off gofmt's -r flag, this flag allows you to apply a rewrite rule to your
source before formatting.

A rewrite rule is a string in the following format:

```
pattern -> replacement
```

Both pattern and replacement must be valid Go expressions (more on this later), lets
apply a simple rewrite to the following code:

```go
// test1.go
package main

import (
    "fmt"
)

func main() {
    foo := "Hello World"

    fmt.Println(foo)
}
```

The following rewrite rule changes the variable name from 'foo' to 'bar':

```
$ gofmt -r='foo -> bar' test1.go
```

Output:

```go
// test1.go
package main

import (
    "fmt"
)

func main() {
    bar := "Hello World"

    fmt.Println(bar)
}
```

We will now apply a more powerful rule to the below code:

```go
// test2.go
package main

func main() {
    vals := make([]int, 0)

    vals = append(vals, 15)
    vals = append(vals, 17)
    vals = append(vals, 23)

    slice := vals[1:len(vals)]

    _ = slice
}
```

The line:

```go
slice := vals[1:len(vals)]
```

Is not very idiomatic Go so lets change this:

```
$ gofmt -r='a[b:len(a)] -> a[b:]' test2.go
```

Output:

```go
// test2.go
package main

func main() {
    vals := make([]int, 0)

    vals = append(vals, 15)
    vals = append(vals, 17)
    vals = append(vals, 23)

    slice := vals[1:]

    _ = slice
}
```

As you can see the code was correctly transformed. Notice how the rule used the
characters 'a' and 'b'. If your rule uses single-character lowercase identifiers,
then these will serve as wild-cards matching arbitrary sub-expressions; these
expressions will be substituted for the same identifiers in the replacement. So
the rule:

```
-r='a[b:len(a)] -> a[b:]'
```

Would match:

```go
x := vals[1:len(vals)] // vals[1:]
y := nums[5:len(nums)] // nums[5:]
```

Were on the first match:

```
'a' would be substituted for 'val' <br/>
'b' would be substituted for '1' <br/>
```

And on the second match:

```
'a' would be substituted for 'nums' <br/>
'b' would be substituted for '5' <br/>
```

An important thing to remember when using the -r flag is that the resulting
transformation must be a syntactically valid declaration list, statement list,
or expression. So the following rule:

```
-r='a[b:len(a)] -> a[const]'
```

Would be syntactically incorrect (const is a reserved keyword), and you would get
the error:

    parsing replacement a[const] at 1:4: expected operand, found 'const'

If you would like to learn more about rewrite rules then run:

```
$ godoc gofmt
```

**Note**: godoc gofmt reports the following at the end:

```
BUGS

The implementation of -r is a bit slow.
```

:)

**Note**: I am using Go version 1.4.2
