---
title:  "A simple JVM"
date:   2024-01-09 20:40::23 +0100
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

The last [post]({% post_url 2023-11-08-a-simple-class-file-parser %}) was about a class file parser that I worked on for some time. In the meantime, I integrated this parser in a new project that aims to implement a simple JVM. The project is called **VigurVM**. Named after an island from Iceland.
The source code, can be found on my personal Github at [https://github.com/fernandezseb/Experimental-VigurVM](https://github.com/fernandezseb/Experimental-VigurVM).

The main goal at the moment of the project is to be able to print at least Hello World to standard out. For this to work, the JVM has to set up a PrintStream at System.out.[^1] This object and all of its dependencies need to be created by the JVM. At the moment it is not yet possible to do this with the current instructions and standard library native method implementations.

The idea behind getting the this to work, was initially to be able to more easily debug the JVM while developing new instructions and builtin features. It is also turning out to be a good way to find a lot of commonly used instructions and native implementations. It is also a kind of test to see if the current implementation can execute a relatively complex program that loads and uses quite a lot of core classes of the JVM.

After this seems to work okay, we can switch to focusing on more specific features of the JVM and testing these using a targetted approach.

## Instructions
With **instructions** is meant the instruction that make up the code that has to run in the JVM when a non-native method is executed. In the Oracle JVM specification document at [https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html), we can see a list of the supported instructions.

For example if you write the following code in Java:
{% highlight java %}
public static void main(String[] args) {
    int doodle = 25;
    int babble = 67;
    int sum = doodle + babble;
}
{% endhighlight %}
The following bytecode may get generated for this method:
{% highlight text %}
BIPUSH 25
ISTORE 1
BIPUSH 67
ISTORE 2
ILOAD 1
ILOAD 2
IADD
ISTORE 3
RETURN
{% endhighlight %}

This is the code that we can find inside the compiled class file. The code is not exactly stored as text like in the above example. But in the form as specified in the specification document at: [https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html).

For example, the `BIPUSH` instruction is encoded in the class file as follows:

The byte with hex value `0x10` (16 as unsigned int) denoting the `BIPUSH` instruction (which we can also call the opcode, for operation code), with after that, the byte value that should be pushed on the operand stack of the VM. 

This exact definition of how the instruction should appear as well as its operands, can be found in the specification. So if the program should have to push a value that can't be contained inside a signed byte[^2], another instruction should be used instead of the `BIPUSH` instruction, for example.

## Native Implementations
In Java it is possible to execute external code that is implemented in other programming languages using native methods. In the sourcode, the user marks the method as *native* using the `native` keyword.[^4] A native method can't have a body inside the Java sourcecode, because the code to be executed by the JVM resides somewhere else. The JVM at runtime must look in a predefined way for the correct implementation.[^3] This can be used by anyone, but is also a very important specifically inside the JVM itself. The JVM implementation uses a lot of native methods to handle interactions with the code of the JVM itself (Which is written in C++ in the case of VigurVM. The Hotspot virtual machine also is written in C++.). Because this happens quite frequently, we handle this in a more optimized way than when we want to call external code that resides outside of the JVM implementaion. This optimized, but less flexible way of handling native code, is implemented already currently in VigurVM. Calling external native code, isn't implemented yet at the moment of writing, and will be lower on the priority list of features.


Thanks for reading, and see you in the next post!


[^1]: See javadoc at [https://docs.oracle.com/javase/8/docs/api/java/lang/System.html](https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#out)
[^2]: The byte is converted to an integer value, so it can be only be in the closed interval `[-128,127]`.
[^4]: [*8.4.3.4. native Methods* - The Java Language Specification, Java SE 8 Edition](https://docs.oracle.com/javase/specs/jls/se8/html/jls-8.html#jls-8.4.3.4)
[^3]: [*5.6. Binding Native Method Implementations* - The Java Virtual Machine Specification, Java SE 8 Edition ](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-5.html#jvms-5.6)