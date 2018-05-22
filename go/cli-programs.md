Building CLI Programs in Go
===========================

Parsing Flags
-------------

* Going by [Neil's suggestions][01] these are some of the easier ways to use the `flags` package.
* The brunt of the work is done by the `flag.Parse()` function.
* Basically it's just a wrapper to `flag.CommandLine.Parse(os.Args[1:])`.
* To make use of it to parse arguments:
```go
func setupInputs(args []string) {
    a := os.Args[1:]
    if args != nil {
        a = args
    }
    flag.CommandLine.Parse(a)
}
```
* Not sure why they are grabbing args this way and then checking against a nil result
* To read and mock STDIN when that is required use `os.Stdin`:
```go
var stdin *os.File
func setupInputs(args []string, file *os.File) {
    // ...
    // Code from above function removed to make easier reading
    if file != nil {
        stdin = file
    } else {
        stdin = os.Stdin
    }
}
```

* Then a good way to test out the functions that use STDIN, just create a temporary file, write to it, then pass it into the setup function like this:

```go
// Create temp file
file, _ := ioutil.TempFile(os.TempDir(), "stdin")
defer os.Remove(file.Name())
// Write to it
file.WriteString("content from stdin")
// Pass it in
setupInputs([]string{}, file)
```
* There's a huge example of a testfile [here][02]

References
----------

1. [Rapid 7: Building a Simple CLI Tool with Golang][01]
2. [Github: nwjlyons/email/main_test.go][02]

[01]: https://blog.rapid7.com/2016/08/04/build-a-simple-cli-tool-with-golang/ "Rapid 7: Building a Simple CLI Tool with Golang"
[02]: https://github.com/nwjlyons/email/blob/master/main_test.go "Github: nwjlyons/email/main_test.go"
