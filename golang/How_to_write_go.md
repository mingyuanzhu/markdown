# How to Write Go Code
##Code organization
### Overview
* Go programmers typically keep all their go code in a single workspace.
* A workspace contains many version control _repositories_.
* Each repository contains one or more pakages
* Each package consists of one or more Go source files in a _single directory_.
* The path to a package's directory determins its _import path_.

### Workspaces
A workspace is a directory hierachy with three directories at its root:

* __src__ contains Go source file
* __pkg__ contains package objects
* __bin__ contains executable commands

```go
bin/
    hello                          # command executable
    outyet                         # command executable
pkg/
    linux_amd64/
        github.com/golang/example/
            stringutil.a           # package object
src/
    github.com/golang/example/
        .git/                      # Git repository metadata
	hello/
	    hello.go               # command source
	outyet/
	    main.go                # command source
	    main_test.go           # test source
	stringutil/
	    reverse.go             # package source
	    reverse_test.go        # test source
    golang.org/x/image/
        .git/                      # Git repository metadata
	bmp/
	    reader.go              # package source
	    writer.go              # package source
    ... (many more repositories and packages omitted) ...
```
### The GOPATH environment variable
The __GOPATH__ environment variable specifies the location of the workspace. It's likely the only environment variable you'll need to set when developing Go code.

### Package names
The first statement in a Go source file must be

```go
package name
```
Executable commands must always use __package main__.

### Testing
Go has a lightweight test framework composed of the `go test` command  and the `testing` package.

Write a __XXX\_test.go__ that contains the functions name __TestXXX__
with signature func(t *testing.T)

if the function calls a failure function such as t.Error or t.Fail, the test is considered to have failed.

```go
go test xxx/xxx/xxx
```

### Remote packages
An import path can describe how to obtain the package source code using a revision control system such as git.

```
$ go get github.com/golang/example
```
