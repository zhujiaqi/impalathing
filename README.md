Impalathing is a small Go wrapper library the thrift interface go Impala

It's based on [hivething](https://github.com/derekgr/hivething)

Working on this you quickly realize that having strings deliminated by tabs is a ugly API... (That's the thrift
side of things)

## Usage

in order to use kerberos, you need an extra dependency

`
    go get -tags kerberos github.com/beltran/gosasl
`

then

`
    go build --tags=kerberos
`

```go
package main

import (
    "log"
    "fmt"
    "time"
    "github.com/zhujiaqi/impalathing"
)

func main() {
    host := "impala-host"
    port := 21000

    useKerberos := true
    con, err := impalathing.Connect(host, port, impalathing.DefaultOptions, useKerberos)

    if err != nil {
        log.Fatal("Error connecting", err)
        return
    }

    query, err := con.Query("SELECT user_id, action, yyyymm FROM engagements LIMIT 10000")

    startTime := time.Now()
    total := 0
    for query.Next() {
        var (
            user_id     string
            action      string
            yyyymm      int
        )

        query.Scan(&user_id, &action, &yyyymm)
        total += 1

        fmt.Println(user_id, action)
    }

    log.Printf("Fetch %d rows(s) in %.2fs", total, time.Duration(time.Since(startTime)).Seconds())
}

```
