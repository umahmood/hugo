+++
date = "2015-12-14T20:55:08+01:00"
draft = false
title = "The Defer Statement"
hideToc = true

+++

The Go programming language has a defer statement that allows for a function call to be run just before the currently running function returns. Here is how the defer statement is explained in the language specification:

_"A "defer" statement invokes a function whose execution is deferred to the moment the surrounding function returns, either because the surrounding function executed a return statement, reached the end of its function body, or because the corresponding goroutine is panicking."_

Here is example usage of the defer statement:

```go
func DoWork(f Foo) {
    defer f.CleanUp()
    f.DoTask()
}
```

The function call in the defer statement (CleanUp), happens just before the function 'DoWork' exits. Below I have listed some properties of the defer statement:

### Defer'd functions are executed in LIFO

Given the following program:

```go
package main

import "fmt"

func a() {
	defer fmt.Println("a0")
	defer fmt.Println("a1")
	b()
}

func b() {
	defer fmt.Println("b")
	c()
}


func c() {
	defer fmt.Println("c")
}

func main() {
	a()
}
```

Here is what the defer'd queue looks like:

Front : [ fmt.Println("c") ][ fmt.Println("b") ][ fmt.Println("a1") ][ fmt.Println("a0") ]

And the output:

```text
c
b
a1
a0
```

### Defer'd functions execute even if the function panics

In the following program the method 'Space' panics, but the defer'd function queued up still executes.

```go
func Space() {
    defer fmt.Println("I'm a rocket ship on my way to Mars")
    panic("On a collision course")
}
```

Output:

```text
I'm a rocket ship on my way to Mars
panic: On a collision course

goroutine 1 [running]:
main.Space()
	/tmp/sandbox062698335/main.go:46 +0x160
main.main()
	/tmp/sandbox062698335/main.go:51 +0x20
```

When a panic function executes, it begins to unwind the stack executing any defer statements as it goes.

### Arguments are evaluated when the defer statement is encountered

Take the following example:

```go
func Foo() {
    var x int
    defer fmt.Println("value of x =", x)
    x = x + 1
    fmt.Println("value of x =", x)
}
```

You may expect the program to output:

```text
value of x = 1
value of x = 1
```

But instead the output is:

```text
value of x = 1
value of x = 0  // defer'd function call output
```

The reason for this is before the defer'd function was queued, it's arguments were evaluated and saved (x = 0). When the defer'd function was executed, rather than seeing that x == 1, it instead output the value of x it saved previously. The defer section in the Go language specification states:

_"Each time a "defer" statement executes, the function value and parameters to the call are evaluated as usual and saved anew but the actual function is not invoked."_

If we would like to have the arguments evaluated when the defer executes, wrap the function in a closure:

```go
func Foo() {
    var x int
    defer func() {
        fmt.Println("value of x =", x)
    }
    x = x + 1
    fmt.Println("value of x =", x)
}
```

Output:

```text
value of x = 1
value of x = 1
```

### Defer'd functions can access named parameters

In the following example (taken from the defer section of the language specification):

```go
// f returns 1
func f() (result int) {
	defer func() {
		result++
	}()
	return 0
}
```

You can see that defer'd functions can access and modify named parameters. Notice how the defer in function f is a closure, otherwise we could not capture the most up to date value of 'result' or modify it.

Fin.
