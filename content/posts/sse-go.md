+++
title = "Server-Sent Events (SSE) With Go"
date =  2020-05-08T13:13:23+01:00
draft = false
hideToc = true
+++

Server-Sent Events (SSE) technology is best explained by this article on [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events):

> The EventSource interface is used to receive Server-Sent Events. It connects to a server over HTTP and receives events in text/event-stream format without closing the connection.

The connection is persistent and communication is one-way, from the server to the client, the client cannot push data to the server. This is unlike WebSockets where communication is bi-directional.

Some unique characteristics of SSE compared to WebSockets or long polling are:

- Communication is one-way from server to client and read-only.
- Requests are regular HTTP requests.
- The client will attempt auto-reconnect if the connection drops.
- Event messages can contain id's, so if a client connection drops. On reconnect, it can send the last id it saw, and the server can work out the number of messages the client has missed. And push those message to the client on reconnect.

There is one important fact that you should understand about SSE when used over HTTP/1 and HTTP/2, from the MDN article above:

> When not used over HTTP/2, SSE suffers from a limitation to the maximum number of open connections, which can be specially painful when opening various tabs as the limit is per browser and set to a very low number (6). The issue has been marked as "Won't fix" in Chrome and Firefox. This limit is per browser + domain, so that means that you can open 6 SSE connections across all of the tabs to www.example1.com and another 6 SSE connections to www.example2.com. (from Stackoverflow). When using HTTP/2, the maximum number of simultaneous HTTP streams is negotiated between the server and the client (defaults to 100).

#### SSE Server

Here is a basic example of an SSE server in Go using the _eventsource_ package:

```
// sse_server.go
package main

import (
    "fmt"
    "net/http"
    "strconv"
    "time"

    "github.com/bernerdschaefer/eventsource"
)

func main() {
    es := eventsource.Handler(func(lastID string, e *eventsource.Encoder, stop <-chan bool) {
        var id int64
        for {
            select {
            case <-time.After(3 * time.Second):
                fmt.Println("sending event...")
                id += 1
                e.Encode(eventsource.Event{ID: strconv.FormatInt(id, 10),
                    Type: "add",
                    Data: []byte("some data")})
            case <-stop:
                return
            }
        }
    })
    http.HandleFunc("/events", func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "text/event-stream")
        w.Header().Set("Cache-Control", "no-cache")
        w.Header().Set("Connection", "keep-alive")
        es.ServeHTTP(w, r)
    })
    if e := http.ListenAndServe(":9090", nil); e != nil {
        fmt.Println(e)
    }
}
```

The above code sets up an endpoint _/events_ and pushes events to clients every three seconds.

Start the server:

```
$ go run sse_server.go
```

#### SSE Client

To view events pushed by the server, we will use the excellent [HTTPie](https://httpie.org/) tool:

```
$ http --stream http://localhost:9090/events
HTTP/1.1 200 OK
Cache-Control: no-cache
Connection: keep-alive
Content-Type: text/event-stream
Transfer-Encoding: chunked
Vary: Accept

id: 1
event: add
data: some data

id: 2
event: add
data: some data

id: 3
event: add
data: some data
```

An example using HTML/Javascript:

```
<!DOCTYPE html lang="en">
<html>
  <body>
    <h1>Getting server updates</h1>
    <div id="result"></div>
    <script>
      if(typeof(EventSource) !== "undefined") {
        var source = new EventSource("http://localhost:9090/events");
        source.addEventListener("add", (message) => {
                  console.log(message)
                  document.getElementById('result').innerHTML += message.data + "<br>";
              });
      } else {
        document.getElementById("result").innerHTML = "Sorry, your browser does not support server-sent events...";
      }
    </script>
  </body>
</html>
```

Save the above code into file sse_client.html, and then open it in your web browser.

you will see the event "some data" being output on the page, if you check the console you will see much more detailed information about the events:

```
add { target: EventSource, isTrusted: true, data: "some data", origin: "http://localhost:9090", lastEventId: "1", ports: Restricted, srcElement: EventSource, currentTarget: EventSource, eventPhase: 2, bubbles: false, … }
```

I hope you found this post helpful.

Fin
