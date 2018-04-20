Building Web Apps with GO: File Server
======================================


The Problem
-----------

*  A lot of the time servers just need to serve static files.
*  Think simple web pages, images, css, etc.
*  Python's `SimpleHTTPServer` & Apache are old hat and slow, sadly.
*  This will be much faster and is **very** simple to setup.

### The Procedure

*  Change to the user root of the go workspace.
    *  `$GOPATH/src/github.com/YOUR_NAME`
    *  `mkdir fileserver && cd fileserver`
* Create a `main.go` as the typical go entry point for a program.
```go
package main

import "fmt"
import "net/http"

func main() {
    http.ListenAndServe(":8080", http.FileServer(http.Dir(".")))
}
```
*  Then build & run the server with `go build && ./fileserver`
*  Try opening up a browser, and point it to `localhost:8080/main.go`
*  The contents of the go program that was just written should be served.

### What's Going On?

*  The **very** useful `net` standard library is imported as its `http` package.
*  The `http` package is accessed using go's accessor operator `http.*`.
*  `ListenAndServe` is a function of the `http` package that starts a http listening server.
    *  The first argument specifies, using a string, what port to listen to.
    *  The second expects an **HTTP handler**, which is the action that takes place when the server receives an HTTP request.
*  `http.Handler` is a go **interface** that specifies valid ways for an HTTP server to be expected to handle a request.
*  In this case, `http.FileServer(http.Dir("."))`, specifies an HTTP handler as being a `FileServer`, of the *current directory*, `Dir(".")`.
*  Essentially this means that whenever that server is contacted, the route after the server root, is a filename for the server to send back to the requester.
*  So, if the file, `hello.txt` existed in the directory, `localhost:8080/hello.txt` would then return that file.

