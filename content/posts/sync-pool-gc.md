+++
date = "2017-05-23T21:31:37+01:00"
draft = false
title = "Sync.Pool is Drained During Garbage Collection"
hideToc = true
+++

The Go sync.Pool type stores temporary objects, and provides get and put methods. Allowing you to cache allocated but unused items for later reuse. And relieving pressure on the garbage collector.

The purpose of the sync.Pool type is to reuse memory between garbage collections. Which is why sync.Pool is drained during garbage collection (GC).

Here is an example of how to use the sync pool:

```
package main

import (
	"bytes"
	"fmt"
	"sync"
)

var bufPool = sync.Pool{
	New: func() interface{} {
		return new(bytes.Buffer)
	},
}

func main() {
	b := bufPool.Get().(*bytes.Buffer)
	b.WriteString("What is past is prologue.")
	bufPool.Put(b)
	b = bufPool.Get().(*bytes.Buffer)
	fmt.Println(b.String())
}
```

Output:

```
What is past is prologue.
```

**Note**: When calling 'bufpool.Get' it is not guaranteed that we will get a specific buffer from the pool. The 'Get' method selects a random buffer from the pool, removes it from the pool and then returns it to the caller.

As stated before, the interesting thing to note when using sync.Pool, is that it is drained **during** GC. That is, all the objects within the pool are removed. Lets look at an example of this in action:

```
func main() {
    b := bufPool.Get().(*bytes.Buffer)
	b.WriteString("What is past is prologue.")
	bufPool.Put(b)

	// never actually call runtime.GC() in your program :)
	runtime.GC()

	b = bufPool.Get().(*bytes.Buffer)
	fmt.Println(b.String())
}
```

The program will no longer output "What is past is prologue." because the call to runtime.GC() drained 'bufpool'. And when we make the call to 'bufPool.Get()', we get a brand new buffer and not a recycled one.

If we look at the Go source code repository on Github. We see that within the file mgc.go, the [gcStart](https://github.com/golang/go/blob/3b5637ff2bd5c03479780995e7a35c48222157c1/src/runtime/mgc.go#L1190) function makes a call to the function [clearpools](https://github.com/golang/go/blob/3b5637ff2bd5c03479780995e7a35c48222157c1/src/runtime/mgc.go#L2046). This function drains all the sync.Pool types.

There are other techniques that can be used to drain the sync pool, such as:

- Sometime before a GC
- Sometime after a GC
- Clock based
- Using weak references/pointers

Each of these techniques have their advantages and disadvantages. However, Draining the pool during a GC is a good technique as it is simple and does not circumvent the garbage collector.

Fin.
