+++
date = "2016-12-08T17:12:39Z"
draft = false
title = "Runtime and User Level Panics in Go"
hideToc = true

+++

How can our application code detect the difference between a runtime panic and a user level panic? Before answering this question. Let's take a look at the difference between a runtime panic, and user level panic.

A runtime panic is one thrown by the Go runtime, there are many things that can trigger a runtime panic. An example of the the most common runtime panic would be attempting to index an array out of bounds. The Go runtime would detect the illegal access and call the built-in panic function.

A user level panic would be were code outside the runtime i.e. your code, 3rd partly library, etc... makes a call to the built-in panic function:

```
panic("he's dead jim")
```

In Go 1.7 a change was made to the panic values thrown by the runtime. From the [1.7 release notes ](https://golang.org/doc/go1.7#runtime) it states:

_All panics started by the runtime now use panic values that implement both the builtin error, and runtime.Error, as required by the language specification._

When the runtime throws a panic it will call the built-in panic function. With a value which implements the _runtime.Error_ interface. When user level code calls the built-in panic function. It will provide a value which does not implement the _runtime.Error_ interface.

Lets look at a code example:

```
package main

import (
	"errors"
	"fmt"
	"runtime"
)

func runtimePanic() {
	var a []int
	// index out of range, will trigger a runtime panic
	b := a[99]
	_ = b
}

func nonRuntimePanic() {
	panic(errors.New(":("))
}

func main() {
	defer func() {
		if x := recover(); x != nil {
			if _, ok := x.(runtime.Error); ok {
				fmt.Println("this is a runtime panic error")
			} else {
				fmt.Println("this is a non-runtime panic error")
			}
		}
	}()
	// nonRuntimePanic()
	runtimePanic()
}

```

In our defer closure we call the built-in recover function. And then use the return value, to see if the type satisfies the _runtime.Error_ interface.

If we run this code, we will get the output:

```
this is a runtime panic error
```

If we uncomment the call to _nonRuntimePanic_, and then run the code again, we will get the output:

```
this is a non-runtime panic error
```

Now we can distinguish between runtime and user level panics. This information may be useful if your are determining whether to rescue your application from a panic, or to let it crash.

Useful links:

[runtime.Error Documentation](https://golang.org/pkg/runtime/#Error)

[Run-time panics](https://golang.org/ref/spec#Run_time_panics)

[Go 1.7 Release Notes](https://golang.org/doc/go1.7#runtime)

Fin.
