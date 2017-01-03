# Golang CodingStyle

## First reads

A lot of talks from the Go community (Cloudflare, SoundClound, Golang
maintainers, etc.) are distilling good practices and patterns that are shared
across contributors to Go projects. To avoid watching them all, here's a quick
list of good practices that are found nearly everywhere and that benefit from a
broad consensus across the communitity i.e. they are followed by many projects.

 - https://golang.org/doc/effective_go.html
 - https://github.com/golang/go/wiki/CodeReviewComments
 - https://go-proverbs.github.io/

## Formatting

### What

Install `goimports` (with `go get golang.org/x/tools/cmd/goimports`) and
configure your editor to automatically call it on save for your Golang files.
It automatically reformats your code, like `gofmt` but also clean and reorder
your imports.

For **vim**, use [vim-go](https://github.com/fatih/vim-go) with
`let g:go_fmt_command = "goimports"` (see
[Settigs](https://github.com/fatih/vim-go#settings)).

For **IntelliJ Idea**, follow
[this guide](https://rootpd.com/2016/02/04/setting-up-intellij-idea-for-your-first-golang-project/).

### Why

Automatic formatting eliminates all arguments around styling and avoid having
noise in commits because developpers would have different formatting
conventions otherwise. For instance, we don't care about "spaces/tabs are
better than tabs/spaces" flamewar. What's important is to have spaces XOR tabs
in the repo.

## Document your code

### What

https://blog.golang.org/godoc-documenting-go-code

**TL;DR** Prepend a comment to your interfaces, structures, functions, etc.
starting with its name and describe how it should be used more than what it
does.

### Why

It's convention shared across many Go projects. It makes the code easier to
maintain and to be read by multiple contributors. Big plus, by following those
guidelines, the `go doc` tool is able to automatically generate the
documentation for you.

## Early returns

### What

Prefer this:

```go
// Do some stuff
res1, err1 := func1()
if err1 != nil {
	return nil, err1
}

// Do some other stuff
res2, err2 := func2()
if err2 != nil {
	return nil, err2
}

// Do something else
return something, nil
```

To this:

```go
// Do some stuff
res1, err := func1()
if err1 == nil {
  // Do some other stuff
  res2, err2 := func2()
    if err2 == nil {
      // Do something elScriptIdxe
      return something, nil
    } else {
      return nil, err2
    }
} else {
  return nil, err1
}
```

### Why

Easiest to read (no nested code and more explicit)

## Use Table Driven Tests when appropariate

### What

https://github.com/golang/go/wiki/TableDrivenTests

### Why

It's compact, easy to change and to add tests.

## Handle errors when they happen

### What

When you get an `error` from the standard library or from an external package,
rename it (optional), log it (with as local context as possible) and return it
(by early returning).

When you get an `error` from your own function calls, do the expected
processing and return it as it is (logging is optional).

### Why

By following this, you guarantee that the error has been logged depending on
where you are in the code. It also avoids to loose context if you log the error
at the moment it occurs.
