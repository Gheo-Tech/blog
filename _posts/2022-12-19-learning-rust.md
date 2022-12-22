---
title: Learning Rust
date: 2022-12-19 07:17:09 +0200
categories: [Coding, Rust]
tags: [coding, rust]
author: ghe0
image: /assets/img/evil-ferris-on-rocks.jpg
---

# Touch the crab

## A language for masochists

I've personally had an amazing experience coding in Rust. It is by far the most exciting language I worked with. This does not mean that your experience will be similar. First, Rust should not be the first programming language you learn. If you are new to coding, [try Go instead](/posts/go-king-of-enterprise). 

Before starting, it's important to know that Rust mandates a specific degree of masochism. Working with Rust is like playing Dark Souls: painful but rewarding. If you don't know what masochism is, or have never played Dark Souls, think about Rust as a new dimension of life that you are about to experience...

Assuming you don't have a life, this is how long it will take to learn the basics:
  * 3 days: take the rust tour
  * 1 month: read the rust book
  * 2 weeks: play rustlings
  * 1 month: try popular libraries

If you do have a life, there is a high chance you will never be a Rust developer. I prefer to let you know now, then to allow you to experience disappointment after 1 year of failures.

## A tour of rust...

Assuming you already know another programming language (doesn't matter which), you should start by taking a [tour of rust](https://tourofrust.com/). This will take you through the features of the language and allow you to see really fast if you like it or not. If you don't like it, the best thing is to just do something else with your life.

> If you are a true masochist, and you get a real kick out of hurting yourself, learning the language is encouraged even if you don't like it, since this path is likely to produce an even higher degree of pain.
{:.prompt-tip}

# Learn the Crab

## The rust book

If you got this far, it means you decided that you like Rust or that you like my blog. Either way, you have my respect. From now on, [the rust book](https://doc.rust-lang.org/stable/book/) shall be your Bible. You have to live and breathe this book. Start your first read, which should be a confusing but enjoyable experience. You will have to read this book a lot of times...

![Desktop View](/assets/img/ferris-eating-ps.jpg){: .w-50 .left}
In the meantime, set up an IDE that you can use to actually test the code. The language as an exceptionally powerful compiler (it is truly a miracle), so make sure you take advantage of it. You can use VS Code, Vim, Emacs or whatever else you prefer, just make sure the rust-analyzer works and your errors get highlighted properly.

> Without the compiler, you will experience repeated brutal death. Coding in Rust without the compiler is like playing Dark Souls with a broken controller.
{:.prompt-danger}

If you like vim, here is [my IDE](https://github.com/ghe0/nvim-rust-ide).

## Find friends!

One very important aspect of software is the community. You will not get too far on your own. You should find a community where you feel at home and share your opinions with other people. At this point, Rust communities can be found on every social platform.

The most active platform for Rust is Discord. There are multiple servers (with lots of channels each) and voice chat + scharescreen is also popular in some of the Discord communities. Overall, Discord is a very good place to start to make new crab friends.

## Rustlings

![Desktop View](/assets/img/happy-ferris.jpg){: .w-50 .right}
[Rustlings](https://github.com/rust-lang/rustlings) is an amazing Rust game that will take you through all the essentials. It's a mandatory tutorial for anybody that gets into the language. Remember how I said you are dead without a compiler? Your compiler will not work with Rustlings, but in this case it is fine. It will force you to read through the docs and understand what is actually happening. The compiler gives you so much good advice that it actually prevents you from learning Rust, so just go through Rustlings. You will enjoy it.

If you got so far, it means you already started to tame the beast... At this point, the Crab will appear a lot friendlier than before. Now it's time to use the Crab to your advantage.

# Use the crab

## Actix Back-End

There is a very small chance you will exist as a developer without writing web-servers. It just happens. It's there. POOF. Weekend gone, web-server appears. Done. This is why you need a good library that allows you to write servers fast and also have the tooling you need for connecting to a database and testing your code.

For these use cases, I recommend having a look at [Actix](https://actix.rs/). I suppose that, at this point, you are aware how important it is to read the docs. What you should however be absolutely sure you don't miss is the [examples repo](https://github.com/actix/examples#readme).

## gRPC with tonic

If you never used gRPC up to this point, you should. You will very often have to interract with other languages. You will be part of teams or companies where not all developers will want to code in your language. For these cases, gRPC is plug-and-play solution. The best Rust crate for gRPC is [tonic](https://docs.rs/tonic/latest/tonic/). It allows you to generate code based on proto files.

## Tokio async

The Rust core libraries do not have the user-friendly support for green threads and concurrency that you might be used with from other language. The [Tokio crate](https://tokio.rs/) satisfies this requirement with flying colors. If you need a fast async app, Tokio is what you need.

It's important to note that Actix, Tonic and lots of other crates use Tokio in the background.

## Yew Front-End

JavaScript is dead. Or it will be... in 50 years or something...

Rust features a lot of crates that allow frontend development, but we will get into the details in another article. For now, you should check [yew](https://yew.rs/). As always, make sure you don't miss the [docs](https://yew.rs/docs/getting-started/introduction) and the [examples](https://github.com/yewstack/yew/tree/master/examples). You can just copy-paste one of the examples into your own repo use it as a template for your new rust-based website.

If you also want to create desktop apps, you should have a look at [tauri](https://tauri.app/). It's the new electron, but it's faster and eats less memory.

> At this point, Rust web apps are already a few times faster than JavaScript web apps.
{: .prompt-info }

# Become the Crab

![Desktop View](/assets/img/ferris-warrior-on-rock.jpg){: }
Are you ready to become a Rust warrior? Start diving into unsafe by reading the [rustnomicon](https://doc.rust-lang.org/nomicon/).
