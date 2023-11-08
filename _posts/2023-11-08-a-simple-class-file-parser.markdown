---
title:  "A simple class file parser"
date:   2023-11-08 15:30:00 +0100
categories: programming
tags:
- update
- jvm
- internals
- low-level
- C++
- Java
- class file
---
A little while ago, I started working on a Java class file parser, that functionally does a job similar to the [javap](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javap.html#BEHHDJGA) tool. (With some small output formatting differences, and with all output always enabled, instead of fine-grained control.) I called my project ClassDump. The aim of this project is to experiment with creating a class file parser that can later be modified slightly to be used in an implementation of a fully working JVM. That project will also be published on my Github and will be announced on this blog as well. The target version of all these projects is Java 8, or class file version 52.0.

But before starting on the JVM implementation, I will continue to implement features, and stabilize the java parser code. Some things that still need to be done are:
1. Complete the parsing of all types of attributes
1. Complete the parsing of all types of opcodes
1. Do some more testing of all features and maybe even add some more automated tests
1. Improve the memory code. (At the moment a fixed amount of memory is allocated no matter how big the class file. This should be able to increase the size if needed.)
1. Make the code work on other platforms than Windows/MSVC.

I might only implement tasks 4. and 5. in the JVM project since this is only a temporary project to get the class file parser part working.

The purpose of all this work, is to for me to learn more about how the JVM works internally, since I learn the best by doing something, with the knowledge. When actually trying to make it work by writing the code yourself, your understanding really gets tested whether it is correct or not. I also really like to work on code that is challenging and requires me to think in new ways or learn new concepts along the way. Spending all this time researching and programming, will help me improve my skills. I already have learned a lot more about the internals of  Java, but also about C++ (and related technologies, like CMake), and I hope to continue to learn even more about this other programming language.

In the following posts, I will try to add some more details and/or updates about ClassDump.

The source code for this project, can be found on my personal Github [here](https://github.com/fernandezseb/Experimental-ClassDump).


Thanks for reading, and see you in the next post!