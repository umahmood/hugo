+++
date = "2017-08-27T18:31:37+01:00"
title = "Piping Commands into Go Binaries"
hideToc = true
draft = false
+++

In this post we will see how we can pipe commands into our Go applications. We will write a basic command line program which demonstrates the functionality. Before we get started, it's worth going over the basics of piping commands.

The modular nature of the UNIX operating system. Allows the user to use basic commands to build up new commands. By allowing the output of one command to be used as the input for the next command. The general form of the pipe command is:

```
command1 | command2 | command3
```

Here, _command1_ is piping its output into _command2_, which transforms the incoming data, and then pipes it into _command3_.

Some examples:

```
ls -l | grep "ninja" | less
```

```
who | sort
```

```
file * | grep text
```

Piping commands into your Go applications is quite easy. Thanks to the excellent standard library. We will write a very basic command line program _'change'_. Which takes input, and replaces any occurrence of word A with word B. Here is an example of the the command in action:

```
$ echo "I hate Brussel sprouts!" | change "hate" "love"
I love Brussel sprouts!
```

Below is the code for the full program:

```
// change.go
package main

import (
	"bytes"
	"fmt"
	"io"
	"log"
	"os"
)

func main() {
	buf := &bytes.Buffer{}
	n, err := io.Copy(buf, os.Stdin)
	if err != nil {
		log.Fatalln(err)
	} else if n <= 1 { // buffer always contains '\n'
		log.Fatalln("no input provided")
	}
	if len(os.Args) != 3 {
		log.Fatalln("usage: echo \"hello world\" | change hello bye")
	}
	oldWord := os.Args[1]
	newWord := os.Args[2]
	r := bytes.Replace(buf.Bytes(), []byte(oldWord), []byte(newWord), -1)
	fmt.Println(string(r))
}
```

```
$ go build change.go
```

Note: this code needs more work to make it more robust, if it were to be used in production. But it is okay for demonstration purposes.

And that is how you pipe commands into your Go applications. Another command could pipe in the output of our command, as follows:

```
$ echo "I hate hate Brussel sprouts!" | change "hate" "love" | grep -o love | wc -l
2
```

I hope you enjoyed this blog post.

Fin.
