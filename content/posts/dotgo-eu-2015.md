+++
date = "2015-11-12T13:15:12+01:00"
draft = false
title = "dotGo 2015"
hideToc = true

+++

![Dot conf. logo](https://i.imgur.com/uiLTeK2.png)

On Monday the 9th of November I was in Paris attending dotGo, the European Go conference. This blog post is a summary of my time there.

### Pre-Conference

The day before the conference, the Paris tech talks group organized a pre-conference meetup/party. There were a large number of delegates who turned up and the meet up consisted of about six or seven talks. The talks were about how individuals at there respective companies were using Go. All the projects demoed and talked about were network related i.e. using Go to write a load balancer which solved a particular problem. After the talks, there was a chance to socialize and eat pizza with other gophers :).

### Venue

On the day of the conference I made my way down to the venue Théâtre de Paris. This venue was absolutely beautiful, the high ceilings, the seating, the theatre boxes and stage. Made this venue truly great, and I was very grateful that the building owners allowed the conference to take place there.

### Talks

dotGo is a single track conference which I prefer, as I always have a hard time making my mind up about which talks to attend. Here is a summary of the talks:

#### Microservices

This talk was given by Peter Bourgon the creator of Gokit. The talk centered around his thoughts, opinions and efforts of getting people to adopt microservices in organizations.

[gokit on github](https://github.com/go-kit/kit)

#### Tools for working with Go code

This talk was given by Fatih Arslan the creator of Vim-go. This was a great talk were you were introduced to a wide range of tools that exist in the Go ecosystem. Tools such as _gorename_, _generate_ and _oracle_ were presented along with examples on how to use them.

#### The Docker Trail

This talk was given by Jessica Frazelle who is a core team member at Docker. She talked about three odd things the team at Docker noticed and how they went about debugging and fixing them.

#### Applied Concurrency in Go

A talk given by Matt Aimonetti who is the co-founder and CTO of Splice. Matt was running Go code (which used concurrency) on an Arduino which made some LED's blink according to some rules. He ran various versions of the code all of which contained concurrency related bugs, he fixed the bugs as he went along. Showing all the the mistakes we usually make when writing concurrent code.

#### Functional Go?

A talk given by the excellent Francesc Campoy Flores a member of the Google Go team. This talk concentrated on his efforts to use Go in a functional manner, after his experiences with Haskell. The functional code he wrote for the problem he was trying solve was scary, he really was actively working against the language (something he admitted). It was a fun talk though.

#### The Other Side of Go: Programming Pictures

This talk was given by Anthony Starks the creator of SVGo. It was great to see what people were doing with Go outside the network related projects we always see. Anthony's talk was great, he was using Go to generate SVG for all sorts of things. One important point that stayed with me about his talk, was about dissecting a complex image into just lines and arcs, this allowed you to then build up a replica image (using SVGo) using just these primitives. He also spoke about his project with great passion.

#### Gomobile

David Crawshaw the creator of gomobile spoke about the challenges of getting Go up and running on mobile platforms. Some of the topics he discussed were Go's calling convention and threading (goroutines, OS threads and CPUs).

[gombile on github](https://github.com/golang/mobile)

#### A Tour of the Bleve

Marty Schoch the creator of Bleve gave an excellent talk on the open-source full-text search library for Go. Marty presented great code examples along side his talk, which really helped clarify the points he was trying to get across. He also spoke about how members of the community have contributed to the project. This was probably my favorite talk, and I also learned how to pronounce Bleve :)

[bleve homepage](http://www.blevesearch.com/)

#### Simplicity is Complicated

A talk given by the excellent Rob Pike co-creator of Go. He stated that even though Go is a very simple language, a lot of the complexity is hidden behind the scenes. He talked about how simplicity and complexity are part of the design and finding the right balance is a challenging task. This was good talk, and when listening I thought about the _go_ keyword.

```go
go someFunction()
```

This in Go is a simple way to get concurrency into your program and all the complexity of scheduling is hidden behind the scenes.

There were also a series of ten minute lightning talks, one of these was given by Brad Fitz about http2 in Go. http2 will be available in go 1.6.

Full list of dotGo videos can be found [here](http://www.thedotpost.com/conference/dotgo-2015).

Overall I really enjoyed my time at dotGo, and Paris is an amazing and buzzing city. I will never forget standing on my Hotel balcony and listening to a trumpeter playing for tips, with the noise of the city in the background.

Fin.
