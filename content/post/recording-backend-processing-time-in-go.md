+++
date        = "2015-03-08T18:58:00-04:00"
title       = "Recording backend processing time in Golang"
description = ""
tags        = [ "code", "golang" ]
topics      = [ "code", "golang" ]
slug        = "recording-backend-processing-time-in-go"
+++

I recently wrote a RESTful web service in Golang, and wanted to pull out some stats on how my handlers were doing in terms of performance.

<!--more-->

A simplified version of where I started would be:

```go
package main

import (
	"github.com/gorilla/mux"
	"log"
	"net/http"
)

// Some handlers here

func main() {
	router := mux.NewRouter().StrictSlash(true)
	router.HandleFunc("/api/something", SomethingIndexHandler).Methods("GET")
	router.HandleFunc("/api/something", SomethingCreateHandler).Methods("POST")

	port := ":9000"
	log.Printf("Starting web server on " + port)
	err := http.ListenAndServe(port, router)
	if err != nil {
		log.Fatal(err)
	}
}
```

This is actually pretty simple, we just need something to wrap all our routes, and emit logs containing start and end times. That would look something like this:

```go
func DefaultWrapper(handler http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		start := time.Now()
		handler.ServeHTTP(w, r)
		log.Printf("%s %s %s %s", r.RemoteAddr, time.Since(start), r.Method, r.URL)
	})
}
```

You would then wrap your router with this wrapper:

```go
err := http.ListenAndServe(port, DefaultWrapper(router))
```

We would then see logs like:

```console
go-app git:master ❯ go run *.go
2015/03/08 17:34:55 Starting web server on :9000
2015/03/08 17:34:57 [::1]:49868 824µs GET /api/something?limit=10
2015/03/08 17:34:58 [::1]:49868 7.954824ms POST /api/something
2015/03/08 17:34:59 [::1]:49868 34.745µs GET /
```

#### Final code

```go
package main

import (
   "fmt"
   "github.com/gorilla/mux"
   "log"
   "net/http"
   "time"
)

// Some handlers here

func DefaultWrapper(handler http.Handler) http.Handler {
   return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
      start := time.Now()
      handler.ServeHTTP(w, r)
      log.Printf("%s %s %s %s", r.RemoteAddr, time.Since(start), r.Method, r.URL)
   })
}

func main() {
   router := mux.NewRouter().StrictSlash(true)
   router.HandleFunc("/api/something", SomethingIndexHandler).Methods("GET")
   router.HandleFunc("/api/something", SomethingCreateHandler).Methods("POST")

   port := ":9000"
   log.Printf("Starting web server on " + port)
   err := http.ListenAndServe(port, DefaultWrapper(router))
   if err != nil {
      log.Fatal(err)
   }
}
```
