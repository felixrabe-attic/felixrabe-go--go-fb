go-fb
=====

Firebird package for Go. Does not currently conform to database/sql/driver interfaces.

Docs: http://godoc.org/gopkg.in/felixrabe-go/go-fb.v0


About this fork
---------------

This fork is based on https://github.com/rowland/go-fb. This fork adds:

-   API versioning (by adding the `v0` branch): `go get gopkg.in/felixrabe-go/go-fb.v0`

-   support for embedded use by making username+password optional

-   documentation


On the horizon (see also https://github.com/rowland/go-fb/issues/1):

-   clarified license

-   portable test setup

-   test on Windows 7


License
-------

See https://github.com/rowland/go-fb/issues/1.


Example
-------

    package main

    import (
        "fmt"
        "log"
        "os"
        "path/filepath"

        "gopkg.in/felixrabe-go/go-fb.v0"
    )

    func main() {
        if len(os.Args) < 2 {
            fmt.Println("Usage: program path/to/database.fdb")
            os.Exit(1)
        }
        dbFilename := os.Args[1]
        var err error
        dbFilename, err = filepath.Abs(dbFilename)
        if err != nil {
            log.Fatal(err)
        }

        conn, err := fb.Connect("database=" + dbFilename)
        if err != nil {
            log.Fatal(err)
        }
        defer conn.Close()

        cu, err := conn.Execute("SELECT Count(*) FROM rdb$relations")
        if err != nil {
            log.Fatal(err)
        }
        if cu == nil {
            log.Fatal("empty cursor returned by conn.Execute()")
        }
        defer cu.Close()

        for cu.Next() {
            var n int
            if err := cu.Row().Scan(&n); err != nil {
                log.Fatal(err)
            }
            fmt.Println("Number of relations in DB:", n)
        }
    }
