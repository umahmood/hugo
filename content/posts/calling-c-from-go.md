+++
date = "2015-07-25T20:18:15+01:00"
draft = false
title = "Calling C from Go"
hideToc = true

+++

This post will show you the basics of how to call C code from a Go package.

Lets get started with an example:

Create a header file "add.h" with a function prototype:

    #ifndef _ADD_H_
    #define _ADD_H_
    int add(int, int);
    #endif

Create the source file "add.c" containing the definition for add:

    #include "add.h"

    int add(int a, int b)
    {
       return a + b;
    }

Create a Go package "main.go":

    package main

    // #include "add.c"
    import "C"

    import (
        "fmt"
    )

    func main() {
        r := C.add(40, 2)
        fmt.Println("result = ", r)
    }

Ouptut:

    $ go build main.go
    $ ./main
    result = 42

Notice the comment above the import "C" statement we include add.c not add.h.

If import "C" is immediately preceded by comments, then those comments become apart of the compilation process. If there are any spaces between the comments, then those are seen as normal go comments, for example:

    // #include <math.h>

    // #include <stdio.h>
    // #include <errno.h>
    import "C"

The header files stdio.h and errno.h are included as part of the compilation process, math.h is **not**.

## Inline C

You can also write C code directly in the comments, here is an example:

    package main

    /*
    int fortytwo()
    {
        return 42;
    }
    */
    import "C"

    import (
        "fmt"
    )

    func main() {
        fmt.Println(C.fortytwo)     // address
        fmt.Println(C.fortytwo())   // invocation
    }

Output:

    $ go build inline.go
    $ ./inline
    0x40014a0
    42

## Accessing C structs

Assume we have the below C struct defined in a .h or .c file:

    struct point {
        int x;
        int y;
    };

In order to access this from a Go package, you simple prefix the type name "point" with "C.struct\_":

    func main() {
        p := C.struct_point{}
        p.x = 99
        p.y = 42
        fmt.Printf("type:   %T\n", p)
        fmt.Printf("struct: %+v\n", p)
    }

Output:

    $ go build cstruct.go
    $ ./cstruct
    type:   main._Ctype_struct_point
    struct: {x:99 y:42}

## Controlling the behaviour of the C compiler

You can pass flags to the C compiler to control its behaviour. This is done by defining a CFLAGS with a pseudo #cgo directive in the comments. In the example below, the -H flag will be passed to the C compiler (clang in my case) when it's invoked. The -H flag tells clang to show the header includes and nesting depth.

    package main

    // #cgo CFLAGS: -H
    // #include "add.c"
    import "C"

    import (
        "fmt"
    )

    func main() {
        r := C.add(40, 2)
        fmt.Println("result = ", r)
    }

Ouptut:

    $ go build main.go
    !! this output is from clang writing to stdout because we passed the -H flag !!
    . ./add.c
    .. ./add.h
    . /usr/include/errno.h
    .. /usr/include/sys/errno.h
    ... /usr/include/sys/cdefs.h
    .... /usr/include/sys/_symbol_aliasing.h
    .... /usr/include/sys/_posix_availability.h
    ...

    $ ./main
    result = 42

## Peeking behind the scenes

What is happening when we build a Go package that includes an import "C" statement.

Firstly import "C" is a pseudo-package it is not listed in the standard library. When the go compiler sees the pseudo-package import "C" it runs the **cgo** command, this generates all the supporting infrastructure. It transforms main.go outputting some .h and .c files, these are then passed to clang to compile.

**Note:** the cgo command actually invokes the gcc compiler, however on my machine gcc is an alias for clang, so it's clang that is doing the compiling.

The output from the C compiler is an object file named \_cgo\_.o, which contains the compiled C code. This object code (\_cgo\_.o) is then linked into the rest of the go binary.

Lets runs the cgo command directly to get a better understanding:

    $ go tool cgo main.go

This command will output a directory called \_obj:

    $ ls _obj/

    _cgo_.o
    _cgo_defun.c
    _cgo_export.c
    _cgo_export.h
    _cgo_flags
    _cgo_gotypes.go
    _cgo_main.c
    main.cgo1.go
    main.cgo2.c

As stated \_cgo\_.o contains the compiled C code, one interesting file to browse is main.cgo2.c:

    $ cat main.cgo2.c

    #line 10 "/go/src/samples/main.go"

     #include "add.c"

    // Usual nonsense: if x and y are not equal, the type will be invalid
    // (have a negative array count) and an inscrutable error will come
    // out of the compiler and hopefully mention "name".
    #define __cgo_compile_assert_eq(x, y, name) typedef char name[(x-y)*(x-y)*-2+1];

    // Check at compile time that the sizes we use match our expectations.
    #define __cgo_size_assert(t, n) __cgo_compile_assert_eq(sizeof(t), n, _cgo_sizeof_##t##_is_not_##n)

    __cgo_size_assert(char, 1)
    __cgo_size_assert(short, 2)
    __cgo_size_assert(int, 4)
    typedef long long __cgo_long_long;
    __cgo_size_assert(__cgo_long_long, 8)
    __cgo_size_assert(float, 4)
    __cgo_size_assert(double, 8)

    extern char* _cgo_topofstack(void);

    #include <errno.h>
    #include <string.h>

    void
    _cgo_7474d4d504ba_Cfunc_add(void *v)
    {
        struct {
            int p0;
            int p1;
            int r;
            char __pad12[4];
        } __attribute__((__packed__)) *a = v;
        char *stktop = _cgo_topofstack();
        __typeof__(a->r) r = add(a->p0, a->p1);
        a = (void*)((char*)a + (_cgo_topofstack() - stktop));
        a->r = r;
    }

Take a while to browse the other files, they are quite interesting.

I hope you enjoyed this post.

**Note:** I am on OSX 10.10.4 64 bit using go version 1.4.2 and clang version 602.0.53.
