+++
date = "2015-07-26T18:45:28+01:00"
draft = false
title = "Go and String Concatenation"
hideToc = true

+++

When writing Go code you should try to stay away from concatenating strings using the '+' and '+='' operators.

Strings in Go, like many other languages (Java, C#, etc...) are immutable, this means after a string has been created it is impossible to change. Here is what the [Go Programming Language Specification](https://golang.org/ref/spec#String_types) has to say about the string type:

_"A string type represents the set of string values. A string value is a (possibly empty) sequence of bytes. Strings are immutable: once created, it is impossible to change the contents of a string. The predeclared string type is string."_

Lets look at an example:

    var town string = "Spring"
    town += "field"

When you write the above code the compiler actually creates a new sequence of bytes, and assigns it to the variable 'town'. The string "Spring" is then eligible for garbage collection. Here is what the heap would look like, after the above code was run:

               "Spring"

    town ----> "Springfield"

Now if we concatenate the string 'town' with the another string:

    town = "742 Evergreen Terrace " + town

The heap would look like this:

               "Spring"

               "Springfield"

    town ----> "742 Evergreen Terrace Springfield"

If you concatenate a lot of strings using '+' and '+=' you will be generating a lot of garbage. This will make the garbage collector work harder as all those potential dead strings will need to be analyzed and freed.

There are two ways to concatenate string more efficiently:

##### 1. strings.Join()

    town := strings.Join([]string{"Spring", "field"}, ""))

##### 2. bytes.Buffer

    func concat(vals ...string) string {
        var buffer bytes.Buffer
        for _, s := range vals {
            buffer.WriteString(s)
        }
        return buffer.String()
    }

    func main() {
        town := concat("Spring", "field")

        names := []string{"Homer", "Moe", "Barney", "Carl", "Lenny"}

        friends := concat(names...)
        ...
    }

You can use either, I prefer using the 'concat' method as it's easier to read.

These two methods are much more efficient as behind the scenes they allocate a variable size buffer of bytes. Which can be modified over and over again with out leaving behind a lot of unused strings.
