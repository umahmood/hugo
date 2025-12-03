+++
date = "2015-12-14T01:40:39Z"
draft = false
title = "Go Channel Axioms"
hideToc = true

+++

A while ago I was watching a tech talk by Blake Caldwell on [Building Resilient Services with Go](https://www.youtube.com/watch?v=PyBJQA4clfc). In his presentation he had a slide which listed go channel axioms. I have listed his channel axioms here and provided some short code snippets to hopefully clarify them.

### A send to a nil channel blocks forever

```go
var ch chan bool
ch <- true // will always block
```

We declare a channel 'ch' but do not initialize it (with make), so it is a nil channel. We then attempt to **send** a value down the channel, this causes a blocking operation.

### A receive from a nil channel blocks forever

```go
var ch chan bool
v := <-ch // will always block
```

We declare a channel 'ch' but do not initialize it (with make), so it is a nil channel, We then attempt to **receive** a value from the channel, this causes a blocking operation.

### A send to a closed channel panics

```go
ch := make(chan bool)
...
close(ch)
...
ch <- true // panics!
```

We initialize a channel 'ch' then later close the channel, and then later attempt to **send** a value down the channel. This causes a panic, and we receive a stack trace. In this case, before we send a value down the channel, we may want check if the channel is open:

```go
if v, ok := <-ch; ok {
    // ch is open
}
```

### A receive from a closed channel returns the zero value immediately

```go
ch := make(chan int)
...
close(ch)
...
v := <-ch // v = 0
```

We initialize a channel 'ch' then later close the channel, and then later attempt to **receive** a value from the channel. This causes the zero value to be returned immediately, this is a non-blocking operation.
