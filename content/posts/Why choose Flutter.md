---
author: Clement
title: "Why choose Flutter?"
description: ""
date: 2024-09-12T20:47:12-03:00
---

You might ask, "Why use Flutter to write a web browser?" and it would be a legitimate question. There are a lot of discussions about Flutter on the internet. Some people say it is the best solution to quickly build mobile apps, while others believe that Google will kill it soon.

It would be interesting to talk about my choice and the pros and cons of Flutter and Dart, the language used by Flutter.

For those who don't know Flutter, it's a multiplatform UI framework. It can build apps for different platforms: Android, iOS, macOS, Windows, Linux, web, TVs, cars, etc. It's mainly used to build mobile apps, and among the biggest apps, we find Alibaba, eBay Motors, Rive, SNCF Connect, and BMW. It uses **Dart**, a programming language with many modern features like null-safety.

So let me give you the good and bad aspects of using Flutter for this kind of project.

## Pros

### It's Flutter

<span style="color: green">Importance: high</span>

I'm a Flutter enthusiast, and I believe that it's a great choice to build desktop apps. I wanted to show a project to the people proving they could build anything with it. A browser is one of the most complex pieces of software. **Chrome** has more than 45 million lines of code, and **Firefox** has 31 million. A browser will definitely show that Flutter is a viable solution for desktop applications.

### It's multiplatform

<span style="color: green">Importance: high</span>

I write my code on Linux, and I know that when I build the project, I will build it for Android, iOS, macOS, Windows, Linux, cars, TVs, and even for other web browsers. One codebase, multiple platforms.

Of course, I have to consider that all those platforms have different ways to handle sounds, storage, and permissions. It's a bit of work but much less than having three different codebases (Java, Swift, C++).

### Easy UI framework

<span style="color: green">Importance: high</span>

It's very easy to build a UI. The Flutter framework has a toolbox full of useful widgets. They are modern, and it's quite easy to modify them or draw new ones from scratch. If I compare it to other GUI frameworks, **Qt** is an old and very common solution in the industry. I have used it to build a complex program, and it's not so easy... It lacks many modern components, the documentation is not clear, the license is not free, and the developer experience is subpar.

### Dart, a good programming language

<span style="color: green">Importance: high</span>

**Dart** is a programming language that is not so famous compared to Rust, JavaScript, Python, or C++. It's mainly used to build Flutter apps. That's a shame because it's a very powerful language with modern features. It's a pleasure to write code in Dart. Here are my main reasons for using Dart:

- Developer tools: Dart comes with a bunch of useful tools for developers: formatter, linter, documentation generator, package manager, test runner, and dev tools (a debug tool). We don't have to waste time thinking about which linter or formatter to choose (hi Python).
- Typed language: It's strongly typed, allowing us to write robust code, and the compiler can make optimizations.
- Null-safety: It helps prevent null pointer exceptions and makes the program safer.
- Garbage collector: It speeds up project development and considerably reduces memory leaks.
- Fast: It compiles to native code and has decent performance. According to [this benchmark](https://benchmarksgame-team.pages.debian.net/benchmarksgame/performance/mandelbrot.html), it has similar performance to Go and Node.js.

Even though there are many good reasons to use Dart, I will explain the downsides of this choice later.

### Fast layout and rendering

<span style="color: green">Importance: high</span>

A web engine has two steps that are very important: **layout** and **painting**, also known as **reflows** and **rendering**.

To keep it simple, **layout/reflow** involves taking a tree of HTML nodes and computing the position and geometry for all of them. For instance, it computes the position of a row's children in the viewport.  
**Painting/rendering** consists of drawing the HTML nodes at the positions computed by the layout. It draws lines, text, colors — everything we will see on the screen.

You can imagine that this is not an easy task. The algorithms are not trivial; there are many optimizations to do and much testing... I'm shaking just thinking about doing it from scratch... These two steps are also the most expensive in the HTML rendering process.

Flutter provides these two steps for us. It's very fast because it's made in C++. It guarantees rendering at 60 FPS (if we don't do bad things, of course). It's already well-tested, and a bunch of very smart people are continually working on it to make it faster.

You can find a post talking more about the browser [here]({{< ref "Main concept of a web engine" >}}).

### Hot reload/restart

<span style="color: orange">Importance: medium</span>

During the layout and rendering steps, the browser's developer would probably have to change the way they render the HTML page quite a lot. Thanks to hot reload, they don't have to close the app, recompile, restart the app, and navigate to the HTML page. Instead, pressing r will perform all those steps in a second. The developer experience is better, as we spend more time writing code than visually testing the UI. It's a big plus to me.

## Cons

### Performance

<span style="color: red">Importance: high</span>

Disclaimer: I haven't benchmarked any of the following assumptions. I could be wrong. Feel free to bring corrections.

As you know, if you want speed and low memory usage, the kings are C/C++/Rust. For two equivalent codes in C and Dart with no optimization, C will always be faster.

Dart doesn't have primitives to better control memory usage. You have an integer? It will always be 64 bits.  
It has multithreading but no concept of threads, only processes. Some algorithms could be harder to implement with processes and use more memory.

Even though performance is important, Flutter is already handling the heavy load. The DOM, CSSOM, and RenderTree building are in Dart, but we don't need to rebuild all of them every frame. Also, performance is not crucial at the beginning of development. It could become a major issue if one day this web engine becomes production-ready.

I'm only considering an HTML/CSS browser. I believe it would be worse with a JS interpreter.

### Community packages

<span style="color: green">Importance: low</span>

I have issues with community packages. First, we don't have many low-level packages in Dart. For instance, I had to write a package to read audio metadata in pure Dart. The other solutions weren't maintained anymore and were using C/Rust bridges. In my case, there's only one CSS parser in Dart. It's maintained by the Dart team but hasn't received any updates in 15 months. You could say I could contribute to this project, of course... In my opinion, the reason is that people are mainly using Dart with Flutter, so they don't use Dart for other projects in the same way we use Python or Rust.

Unfortunately, the Dart/Flutter community doesn't maintain many of its packages. Most of them are maintained by one person. It's very common to find packages that haven't been updated for six months.

The few packages needed for a web engine are already well maintained. I'm talking about vibrations, notifications, and battery, which a website can use via some JS APIs.

### Google will kill Flutter

<span style="color: green">Importance: low</span>

You will read A LOT that Google is going to kill Flutter one day. The company has a bad reputation for shutting down most of its products. I personally don't care. I don't think they will ever stop the Flutter development. We cannot predict the future.
