+++
date = "2015-09-02T18:22:06+07:00"
draft = false
title = "Things to do before committing Go code"
hideToc = true

+++

This blog post will list some of the basic things you should really do before committing Go code into your repository.

**1)** Run gofmt/goimports

_gofmt_ is probably the most popular Go tool amongst gophers. The job of _gofmt_ is to
format Go packages, your code will be formatted to be consistent across your code base. For example if we have the following _if_ statement:

```go
if x == 42 { fmt.Println("The answer to everything") }
```

Then running _gofmt_ on this will format the code to:

```go
if x == 42 {
    fmt.Println("The answer to everything")
}
```

_goimports_ is a tool that does exactly what _gofmt_ does, but takes it a step further and adds/removes packages. For example if you had the following code:

```go
package main

import (
    "log"
)

func main() {
    fmt.Println("Hello")
}
```

As you can see from this code, package 'log' is not used anywhere and package 'fmt' has not been imported. Running _goimports_ on this code transforms it to:

```go
package main

import (
    "fmt"
)

func main() {
    fmt.Println("Hello")
}
```

Running either of these tools on your code is a must, its great to see a code base using common idioms and a consistent format/style. And to me, it's one of main things that makes Go code bases so more approachable than code bases in other languages.

**2)** Run golint

_golint_ will lint your source code and make suggestions concerning coding style.

see [golint on github](https://github.com/golang/lint).

**3)** Run go vet

Go _vet_ is another important tool, it's perfectly summed up by it's documentation:

_"Vet examines Go source code and reports suspicious constructs, such as Printf calls whose arguments do not align with the format string. Vet uses heuristics that do not guarantee all reports are genuine problems, but it can find errors not caught by the compilers."_

If we run go _vet_ on the following code:

```go
fmt.Printf("%s", 42)
```

We would get the following error:

```
test.go:6: arg 42 for printf verb %s of wrong type: int
exit status 1
```

Go _vet_ differs from _golint_, go _vet_ is concerned with correctness and _golint_ is concerned with coding style.

see [here](https://godoc.org/golang.org/x/tools/cmd/vet) for more information on _vet_.

**4)** Run build/install/run with -race flag:

There is a fully integrated race detector in the go tool chain. The race detector contains complex race detection and deadlock algorithms, to help you hunt down those hard to find concurrency related bugs.

To enable the race detector in your code, add the -race flag on the command line:

```
$ go test -race mypkg    // test the package
$ go run -race mysrc.go  // compile and run the program
$ go build -race mycmd   // build the command
$ go install -race mypkg // install the package
```

Note: Your code will run slower when you enable this flag, as the race detector is busy doing its thing :)

**5)** Run a dependency management tool

It's very likely that your project will make use of 3rd party libraries. If you need to capture there versions, run a dependency management tool. Below is a list of some popular ones:

- [gb on github](https://github.com/constabulary/gb)
- [godeps on github](https://github.com/tools/godep)
- [glide on github](https://github.com/Masterminds/glide)

I have no real experience with the above tools but instead use the go 1.5 vendor experiment feature to capture dependencies.

I hope this lists helps you in some way in managing your Go projects. Remember, this is just a basic list, Go has numerous tools that you probably run which are essential to you or your project. The ones listed above are ones which I believe are the basics.
