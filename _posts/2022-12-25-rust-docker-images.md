---
title: Rust Docker Images
date: 2022-12-25 01:32:51 +0200
categories: [Coding, Rust]
tags: [coding, rust]
author: ghe0
image: /assets/img/ferris-peaceful-container.jpg
---
# Docker images for Rust

There is a high chance you will need to run your Rust apps on cloud. In this case, you want the docker image to be as small as possible. The examples you will find will allow you to create images that are 50 MB or even 80 MB big, but you can get that down to 10 MB or less for simple web servers or gRPC servers.


# x86_64-unknown-linux-musl

![Desktop View](/assets/img/ferris-in-a-container.jpg){: .w-25 .right }
Normally Ferris does not play nice and tries to escape and touch your s#&!…  What you need to keep him in check is a bit of “muscle”…

If you just compile for release, you will produce a dynamically linked binary. `musl` will allow you to make statically linked binaries, which will ultimately allow you to use smaller base images in prod. If that sounds confusing, just keep reading.

This is what you need to do:
```console
$ rustup target add x86_64-unknown-linux-musl
# … bla bla installing stuff…
$ cargo build --release --target=x86_64-unknown-linux-musl
# … bla bla compiling stuff…
```

This will compile a static binary that will get saved to `target/x86_64-unknown-linux-musl/release/BINARY_NAME`. In comparison with the normal binary you get when compiling just without musl, you will see there are no extra dependencies:

```console
# binary compiled with cargo build --release
$ ldd target/release/my_crab
    linux-vdso.so.1 (0x00007ffed3df7000)
    libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007fd2ee7ae000)
    libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fd2ee6c7000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fd2ede00000)
    /lib64/ld-linux-x86-64.so.2 (0x00007fd2ee7e4000)

# binary compiled by adding --target=x86_64-unknown-linux-musl
$ ldd target/x86_64-unknown-linux-musl/release/my_crab
    statically linked
``` 

> You can choose to build this on your system or withing a docker container. Experiment with both to see what you like more. Details are provided [below](#building-a-docker-image).
{: .prompt-tip }

## Compiling on Mac

Compiling on Mac has some extra challenges and you might want to compile within docker. If you are looking to compile on your system without docker, you will probably need these environment variables:

```console
$ export TARGET_CC=x86_64-linux-musl-gcc
$ export RUSTFLAGS="-C linker=x86_64-linux-musl-gcc"
```

# reduce binary size

![Desktop View](/assets/img/rick-runs-from-big-crab.jpg){: .w-50 }
<center><b>QUICK, MORTY, ADD THIS AT THE END OF YOUR CARGO TOML!!!</b></center>

```ini
[profile.release]
strip = true
codegen-units = 1
```

Now let’s check the size of your binary, rebuild, and check the size again:
```console
$ du -h target/x86_64-unknown-linux-musl/release/my_crab
9,8M    target/x86_64-unknown-linux-musl/release/my_crab

$ cargo build --release --target=x86_64-unknown-linux-musl
# CPU fan going crazy as usual

$ du -h target/x86_64-unknown-linux-musl/release/my_crab
3,7M    target/x86_64-unknown-linux-musl/release/my_crab
```

![Desktop View](/assets/img/rick-runs-after-crab.jpg){: .w-50 .right }


What you just did there is you removed debug symbols and also forced the compiler to reduce parallelism during compilation in order to increase runtime performance. This means the new binary is not only smaller, but also faster than the old one.

> If you are hunting a random bug, you might actually need debug symbols. This is however not something that you should do in production. It’s best to perform testing and debugging on your local machine, using tools and binaries optimized for this purpose.
{: .prompt-warning } 

If you are serious about rust, you should read more about [rustc](https://doc.rust-lang.org/rustc/what-is-rustc.html). If you don't, you will end up like Rick: either desperately running from a crab that is too big or desperately running after a crab that is too small. Rust gives you a choise. Use it wisely!

# building a docker image

Now that you have a small binary that is statically, you can just use `from scratch` as your base docker image and you will get a very small image. This is how your docker file can look if you like to compile on the host:

```Dockerfile
from scratch
copy my_crab /my_crab
cmd ["/my_crab"]
```

In order to build fast, you will also need a bash script that looks something like this:
```bash
#!/bin/bash

mkdir -p .tmp
cargo build --release --target=x86_64-unknown-linux-musl

mv target/x86_64-unknown-linux-musl/release/my_crab .tmp/my_crab

docker build \
  -t my_crab_image \
  -f Dockerfile \
  .tmp/
```

## compiling in docker

Building in docker will have some delays, since you must choose one of these two poisons:
1. add `~/.cargo` and `./target` to the build image OR
2. just download and compile all of the crates on every build

Neither will be very fun, but leave usualy love their [multi-stage builds](https://docs.docker.com/build/building/multi-stage/) so here is an example:
```Dockerfile
FROM rust:latest AS builder
WORKDIR /code
COPY . .

RUN apt update
RUN apt install musl-dev musl-tools -y
RUN rustup target add x86_64-unknown-linux-musl
RUN cargo build --release --target=x86_64-unknown-linux-musl

FROM scratch
COPY --from=builder /code/target/x86_64-unknown-linux-musl/release/my_crab /my_crab
CMD [ "/my_crab" ]
```

No matter if compiling on your machine or in docker, the image of a simple actix web server should not be bigger than 4 MB. Speaking of actix, let’s look at a small detail…

# [actix-rs](https://actix.rs/) and [tonic](https://docs.rs/tonic/latest/tonic/)

![Desktop View](/assets/img/docker-carries-ship.jpg){: .w-50 .left }

There is a high chance you got here after you tried [the actix docker example](https://github.com/actix/examples/tree/master/docker). That is not what you want, because the image is too big. Their binary is 9.6 MB and the image they are using for runtime is `debian:bullseye-slim`, which has 80.5 MB. At the end you will end up with an image bigger than 90 MB.

That is not a whale carrying a small container that contains a crab. That is a whale that carries an entire ship that hunts for crabs in the ocean. Lucky enough, we can apply what we learned in previous chapters to create a smaller image and successfully fail.

Since actix by default uses the OpenSSL library of the OS, you will need to add the `rustls` feature to Cargo.toml in order to allow it to build to a statically linked binary. If you don't your binary will work on your system but will crash with `from scratch` image and it will also fail `with alpine:edge`.

```toml
actix-web = { version = "4.2.1", features = ["rustls"] }
```

Tonic uses [rustls](https://docs.rs/rustls/latest/rustls/) by default so your MUSL binary will just work.

As long as you are sure your binary uses rustls, you should not have any issues running actix or tonic in containers.
