Setting Up Go Developer Environment
===================================

Workspaces
----------

*  Go code is kept in **workspaces**.
*  The Go tool understands the layout of a workspace.
*  No need for `Makefile`!!!
*  The layout of the **workspace** is everything.
*  Changing the layout of files, changes the build.

```
PATH/
    src/
        github.com/user/repo/
            mypkg/
                mysrc1.go
                mysrc2.go
            cmd/mycmd/
                main.go
    bin/
        mycmd
```

Create a Workspace
------------------

```
mkdir /tmp/gows
GOPATH=/tmp/gows
```

*  The `GOPATH` env variable tells the Go tool where the workspace is located.

```
go get github.com/dsymonds/fixhub/cmd/fixhub
```

*  The `go get` command fetches the source repositories from the web & places them in the workspace.
*  Package paths matter in go.
*  Using `github.com/...` means the tool knows how to fetch the directory.

```
go install github.com/dsymonds/fixhub/cmd/fixhub
```

*  The `go install` command builds a binary & places it in `$GOPATH/bin/fixhub`


Our Workspace
-------------

```
$GOPATH/
    bin/fixhub                              # installed binary
    pkg/darwin_amd64/                       # compiled archives
        code.google.com/p/goauth2/oauth.a
        github.com/...
    src/                                    # source repositories
        code.google.com/p/goauth2/
            .hg
            oauth                           # used by package go-github
            ...
        github.com/
            golang/lint/...                 # used by package fixhub
                .git
            google/go-github/...            # used by package fixhub
                .git
            dsymonds/fixhub/
                .git
                client.go
                cmd/fixhub/fixhub.go        # package main
```

*  `go get` fetched a lot of repositories to make this project work.
*  `go install` built a binary out of them.

### Why Not Prescribe Layouts?

*  No configuration.
*  No `Makefile`.
*  No `build.xml`.
*  Less time with configurations, means more programming.
*  Everyone in the community will use the same layout.
    *  Thus sharing is also easier.

### Where's the Workspace?

*  It's possible to have multiple workspaces.
*  *Most* just use one workspace.
*  So where to point `GOPATH`?
    *  Some suggest `$HOME`
    *  Some say `$HOME/go`
    *  I'd like it to be in my personal `$HOME/bin/go`
