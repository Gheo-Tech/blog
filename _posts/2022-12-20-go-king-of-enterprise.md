---
title: Go, The King of Enterprise
date: 2022-12-20 15:34:14 +0200
categories: [Coding, Go]
tags: [coding, go]
author: ghe0
image: /assets/img/gopher-king-1.jpg
---

# The Growth

![Desktop View](/assets/img/go-taking-over.jpg){: .w-50 .right}
Go has taken over backend programming and is slowly taking over enterprise. Go jobs are more and more popular, with salaries ranging between $60k and $180k per year. The ascendance of the language can be observed by analyzing github projects via [languish](https://tjpalmer.github.io/languish/). This is happening because of the language is ergonomic and exceptionally performant, especially considering its simplicity.

Being funded and designed by Google, the Go language benefitted from huge budget and implicitly had enough money to hire two of the most expensive brains in the industry: [Ken Thompson](https://en.wikipedia.org/wiki/Ken_Thompson) and [Rob Pike](https://en.wikipedia.org/wiki/Rob_Pike). In case the names don't ring a bell, these are the guys behind projects like C, Unix, UTF-8, grep. I've often been told that when I grow old I should quit engineering and just do management, but these people and the Go project are a testimony that you can achieve the magnum opus of your life in the later stages of your career. Seeing what they did with Go is an inspiration and encourages me to push hard and stay focused on technology.

# Explicit, not implicit

Go is a very verbose language. This makes the language easier to read for new developers. It also doesn't allow a lot of freedom when writing code, and this is a good thing. When working in big teams, you don't want to have dozens of coding styles. In the best case scenario, any developer can easily understand the code written by any other developer, which is hard to achieve in big companies. If you are used to the hidden magic from languages like Ruby or Rust, Go might seem weird to you for this specific reason.

Let's have a look at an example of how to [access a database](https://go.dev/doc/tutorial/database-access):


```go
var db *sql.DB

func main() {
    // Capture connection properties.
    cfg := mysql.Config{
        User:   os.Getenv("DBUSER"),
        Passwd: os.Getenv("DBPASS"),
        Net:    "tcp",
        Addr:   "127.0.0.1:3306",
        DBName: "recordings",
    }

    // Get a database handle.
    var err error
    db, err = sql.Open("mysql", cfg.FormatDSN())
    if err != nil {
        log.Fatal(err)
    }

    pingErr := db.Ping()
    if pingErr != nil {
        log.Fatal(pingErr)
    }
    fmt.Println("Connected!")
}
```

It is safe to assume that you don't need a lot of development experience in order to understand this code.

You will also notice that in go you will see A LOT of this:
```go
if err != nil {
    log.Fatal(err)
}    
```

I've been witnessing a lot of debates on what "idiomatic error handling" looks like in go, since people prefer different third party libraries and prefer different types of architecture, but one thing is clear: you will not find try-catch statements. You will find a lot of `if err != nil` in all the functions of each module, and at least 3 lines of code for each possible error case.
     
# The Compiler

## Comparison with Rust

To best explain the capabilities of the Go compiler is by comparing it with the Rust compiler, as the different capabilities of the two compilers are deeply rooted within the antithetic philosophies of the two languages. One project aimed to achieve the most productive language for modern software and other project aimed to create a modern language for optimal performance and enhanced security.

![Desktop View](/assets/img/sweet-gopher.jpg){: .w-25 .left}
> __The philosophy behind the Go language:__
> * the code must be easy to understand, even for ~~noobs~~ new devs
> * everything must be explicit; syntax sugar is the Devil
> * compile speed is more important than runtime performance
> * ... because hardware is cheaper than people
> * the language must empower people to develop complex apps
{: .prompt-info }

![Desktop View](/assets/img/evil-ferris-red-sun.jpg){: .w-25 .right}
> __The philosophy behind the Rust language:__
> * the dev must carefully read the docs before starting to code
> * syntax sugar is cool and stuff, and let's do implicit because yes
> * compile time is negligible; buy a better computer, you pleb
> * performance must be optimal, and the footprint must be low
> * the language must brutally enforce security and stability
{: .prompt-danger }

## Advantages of the Go compiler

Due to the above, a lot of decisions were made to make your life as a Go developer a lot easier. For example, you don't even need to know what [dynamic dispatch](https://en.wikipedia.org/wiki/Dynamic_dispatch) is. The language is fast enough in production, though it uses dynamic dispatch everywhere and it doesn't even give you the possibility opt out. In Rust, by comparison, you have to specify `dyn` every time you want to use dynamic dispatch. In other cases, rust will use [monomorphization](https://rustc-dev-guide.rust-lang.org/backend/monomorph.html#monomorphization) to increase runtime speed, which will cause compile time to take longer.

The short idea is that the Go compiler produces instant results. If you come from other compiled languages, you will enjoy working a bit faster.
     
# Behind Docker and Kubernetes ...

* 2009: GoLang gets released
* 2013: Docker gets released - user-friendly container engine written in Go
* 2014: Kubernetes gets released - container orchestration system written in Go

And from that point, the world started to change. Cloud native became the new thing. VMs slowly became history. Small docker containers became the norm. Kubernetes showed us how the future of technology will look. Cloud platforms took inspiration from it and created serverless platforms. In the background, Go was running the show the entire time.

![Desktop View](/assets/img/gopher-pirate.jpg){: }

Even today, new cloud software products are written in Go, since Go is only a bit slower than system's programming languages (like C, C++ and Rust) but a lot easier to understand. The bottleneck in most cases is the network anyway, so why go the extra mile to reduce CPU and memory utilization? Considering how well new cloud platforms scale, getting new resources is not an issue (as long as you have deep pockets). When a module starts eating too much, you can rewrite only that module in a faster language.

![Desktop View](/assets/img/docker-containers-1.jpg){: .w-25 .right}
## ... and on top of Docker and Kubernetes

Apart from the fact that most clouds are written in Go, the language is also ideal for running software on top of the cloud. This makes docker very happy, as it does not need to handle huge images. Kubernetes can scale your backend without issues. A web-server will be as small as 5 MB when compiled correctly. Your Dockerfile will be as simple as:

```Dockerfile
FROM scratch
COPY your_binary /your_binary
ENTRYPOINT ["/your_binary"]
```

And just run two commands to compile a statically linked binary and create a docker image:

```console
# compile the code
$ CGO_ENABLED=0 go build -trimpath -ldflags="-s -w"
# build your image
$ docker build -t my-image .
```

Your docker image will get compressed by cloud platforms to something like 4MB. Crazy, right?

## Running legacy code on Docker

![Desktop View](/assets/img/docker-legacy-apps.jpg){: .w-50 .left}
If you have PHP, Python, Java, Ruby or any other legacy code in your company, you can also run it on top of docker. The images will not be as lightweight as the ones created with Go, but docker will survive. It will cry, it will fight back a bit, but it will bite down on the mouthpiece and pull through.

But even if docker will survive, the developers are still bored with the old code and want to try out new stuff. All the reasons detailed above in the articled turned Go into the first choice when migrating away from legacy systems into new, modern, cloud native software. Even if you keep your old monolithic architecture, and you switch the language to Go without using a microservice architecture, the benefit would still be huge, as the modern tooling that the language provides will help your business grow.

# Multithreading in Go

Actually, they are [green threads](https://en.wikipedia.org/wiki/Green_thread), and they are called __goroutines__. In comparison with other languages, go does not give you control over the yield points of concurrent tasks. It just gives you one function, called `go`, that allows you to run any function concurrently and handles the yield points using a highly efficient algorithm. The point of the goroutines is to provide exceptionally simple mechanism that enable multi-threading apps, but you don't actually get control over the threads (go handles the threads on its own).

> This means it will be exceptionally easy to use, but you will not be able to import futures/promises from other languages (like C++, Swift, Javascript, Rust) through [FFI](https://en.wikipedia.org/wiki/Foreign_function_interface). By comparison, JavaScript and Rust can pass concurrent tasks from one to the other, through [wasm-bindgen](https://rustwasm.github.io/wasm-bindgen/reference/js-promises-and-rust-futures.html). If you are not planning to do hardcore stuff with Go, this is not an issue.
{: .prompt-tip }

For example, let's build some code that prints `hello world` 5 times by using goroutines. Check out how easy this is:
```go
package main

import (
	"fmt"
	"time"
)

func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}

func main() {
	go say("world")
	time.Sleep(50 * time.Millisecond)
	for i := 0; i < 5; i++ {
		fmt.Print("hello ")
		time.Sleep(100 * time.Millisecond)
	}
}
```
```console
❯ go run .
hello world
hello world
hello world
hello world
hello world
```

# But... I switched to Rust
![Desktop View](/assets/img/red-crab-1.jpg){: .w-50 .right}
Why? I like it more.

Despite all the benefits that Go has for a business, I had to face in 2022 a brutal truth: I got bored with Go and, at the same time, Rust is far more exciting for nerds passionate about technology. I am in a point of my career where I can afford to care less about the best language for the business and more about my pleasure and my productivity. We all get only one shot to enjoy life, so why make any other decision?

A lot of the decisions that made go exceptional for business, also made it no fun for nerds like me. Another article will follow soon detailing the differences between Rust and Go.
