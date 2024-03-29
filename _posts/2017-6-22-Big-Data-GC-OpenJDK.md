---
layout: post
author: Xuanrui
title: Big Data, Garbage Collectors, and OpenJDK 8
location: Somerville, MA
---

Recently, I have been working on garbage collection in big data applications. Many big data applications 
run on Java, and garbage collection pauses in Java applications are known to be an annoyance - especially 
with big heaps. There have been some attempts at this, such as 
the runtime [Facade](https://www.ics.uci.edu/~guoqingx/papers/nguyen-asplos15.pdf), which segregates the 
manually-managed "data" heap - intended for large objects which can induce performance issues in 
the GC - with the plain old garbage-collected JVM heap.

What I have been looking at recently was a system called [NG2C](http://www.gsd.inesc-id.pt/~rbruno/publications/rbruno-ismm17.pdf), 
which is a new take at this issue. NG2C divides the whole heaps into a number of generations, the default being the *young* generation. 
In minor GC runs, only the young generation is collected; in major GC runs, the other generations are touched only if they are nearly 
full. There are also full GC runs, but they are triggered if there's really (almost) no space in the heap. When allocating objects, one 
may allocate objects into a generation other than *young* by explicitly annotating the allocation.

The more interesting part for me, though, was building the software on one of [our department](http://www.cs.tufts.edu)'s Arch Linux 
servers. Arch Linux is not one of the natively supported platforms for OpenJDK builds, and our system had newer software than what 
OpenJDK 8 mandated; I decided to give it a try anyway.

Interestingly, the trial took a lot of Makefile debugging. There was one build script in the set of makefile (adjust-mflags.sh) that 
called `sed` in a way which broke command line arguments; I had to remove the `sed` argument. The code was nonconformant for newer 
`gcc` compilers, so there were warnings everywhere. Interestingly, the optimization on newer `gcc` compilers broke (sort of) the build 
by causing seg faults (which technically didn't interfere with the functionality). I decided to give up on the server and use instead one 
of the normal RHEL 6.9 servers, but there was a simple but important lesson from all this battling with the Makefiles: outdated software 
break things, but updates are often incompatible as well. Software that's too new could also break things.

Tomorrow, I'll be flying into Seattle, and I will be at the [Oregon Programming Languages Summer School](https://www.cs.uoregon.edu/research/summerschool/summer17/) starting next Monday. If I run into something interesting there, I'll write about it.
