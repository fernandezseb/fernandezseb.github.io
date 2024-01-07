---
title:  "A simple JVM"
date:   2024-01-07 08:28::23 +0100
categories: programming
tags:
- update
- jvm
- internals
- low-level
- C++
- Java
- class file
- HotSpot
- Java 8
---

The last [post]({% post_url 2023-11-08-a-simple-class-file-parser %}) was about a class file parser that I worked on for some time. In the meantime, I integrated this parser in a new project that aims to implement a simple JVM. The JVM has the same target, class files compiled for the Java 8 standard (class file version 52).

The main goal at the moment of the project is to be able to print at least Hello World to standard out. For this to work, the JVM has to set up a PrinterWriter at System.out. This object and all of its dependencies need to be created by the JVM. At the moment it is not yet possible to do this with the current instructions and standard library native method implementations.

The idea behind getting the this to work was initially to be able to more easily debug the JVM while developing new instructions and builtin features. It is also turning out to be a good way to find a lot of commonly used instructions and native implementations. It is also a kind of test to see if the current implementation can execute a relatively complex program that loads and uses quite a lot of core classes of the JVM.

After this seems to work okay, we can switch to focusing on more specific features of the JVM and testing these specifically.

## Instructions
With **instructions** is meant the instruction that make up the code that has to run in the JVM when a non-native method is executed. On the Oracle JVM specification document [here](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html), we can see a list of the supported instructions.

## Native Implementations
In Java it is possible to execute external code that is implemented in C/C++ or that can be called from C/C++ using native methods. In the sourcode, the user marks the method as **native** using the **native** keyword. A native method can't have a body inside the Java sourcecode, because the code to be executed by the JVM resides somewhere else. The JVM at runtime must look in a predefined way for the correct C compatible implementation. This can be used by anyone, but is also a very important specifically inside the JVM itself. The JVM implementation uses a lot of native methods to handle interactions with the code of the JVM itself. Because this happens quite frequently, we handle this in a more optimized way than when we want to call external code that resides outside of the JVM implementaion. This way of handling native code is implemented already currently in VigurVM. Calling external native code isn't implemented yet, at the moment of writing and will be lower on the priority list of features.

## Project repository
The source code for this project, can be found on my personal Github [here](https://github.com/fernandezseb/Experimental-VigurVM).


Thanks for reading, and see you in the next post!