## JASM - A JVM Assembler for the modern age

### What?

JASM is an assembler for JVM bytecode. Because how many times have you needed
_that_ already today?

Let's just get this out of the way, shall we?

```java
public class com/example/HelloWorld {
    public static main([java/lang/String)V {
        getstatic java/lang/System.out
        ldc "Hello, World"
        invokevirtual java/io/PrintStream.println(java/lang/String)V
        return
    }
}
```

All your favourite instructions (in fact, nearly _all_ the JVM instructions) are supported:

```java
public class com/example/MyClass
extends com/example/Superclass
implements com/example/SomeInterface {
    public <init>()V {
        aload 0
        invokespecial com/example/Superclass.<init>()V
        return
    }

    public dontAddCanary(java/util/List)V {
        goto skipAdd

        aload 1
        ldc "CANARY"
        invokeinterface java/util/List.add(Ljava/lang/Object)Z 
        
    skipAdd:
        return
    }
}
```

And you can use advanced JVM features like `invokedynamic` and dynamic constants that aren't 
directly available in the Java language:

```java
public doBasicInvokeDynamicTest()Ljava/lang/String {
    invokedynamic get()Ljava/util/function/Supplier {
        invokestatic java/lang/invoke/LambdaMetafactory.metafactory(
            Ljava/lang/invoke/MethodHandles$Lookup,
            Ljava/lang/String,
            Ljava/lang/invoke/MethodType,
            Ljava/lang/invoke/MethodType,
            Ljava/lang/invoke/MethodHandle,
            Ljava/lang/invoke/MethodType
        )Ljava/lang/invoke/CallSite
        [
            ()Ljava/lang/Object,
            invokestatic com/roscopeco/jasm/model/TestBootstrap.lambdaGetImpl()Ljava/lang/String,
            ()Ljava/lang/String
        ]
    }

    invokeinterface java/util/function/Supplier.get()Ljava/lang/Object
    checkcast java/lang/String
    areturn
}
```

There's a smidge of syntactic sugar around types in case you just can't 
get used to the JVM internal names (for primitives), so you can do e.g. :

```java
public myGreatMethod(int, long, java/util/List) java/util/List {
    
    // cool assembly stuff...
        
}
```

There are lots of examples in [the tests](src/test/resources/jasm) showing the syntax for
the different instructions and with examples of how they're used.

### How?

#### Requirements

* Java 17 or higher
* `JAVA_HOME` set up correctly
* Patience and curiousity :D

#### Using the command-line tool

If you [downloaded a binary distribution](https://github.com/roscopeco/jasm/releases) then
you should be all set. Inside the archive you'll find a `bin/jasm' script that will take
care of running the command-line tool for you.

To see usage details:

`bin/jasm --help`

To simply assemble a file `src/com/example/MyClass.jasm` to the `classes` directory:

`bin/jasm -i src -o classes com/example/MyClass.jasm`

Notice that you set the source and destination directories, and just pass the relative
path to the files within them - this is how the assembler creates the class files in the
appropriate place the JVM expects to find them.

#### Building with Gradle

If you grabbed the source from [Github](https://github.com/roscopeco/jasm) you can 
easily build a binary distribution with `./gradlew clean assemble` (pun not intended).

#### Using as a library 

If you want to use this as a library (with Maven or Gradle), you'll currently need to
publish it to your local Maven repository manually. 

`./gradlew clean build publishToMavenLocal`

As it matures a bit and things stabilise, I'll look at putting it in Maven Central.

### Why??

Well, **why not**?

I wrote this for fun, which I had both in writing it and playing with it. 

I release it without any expectation that it will be at all _useful_, but with 
the sincere hope that you too might find it fun.

If you really need some use-cases to justify the electrons squandered in
pursuit of this project, how about these (some lifted from Jasmin's README):

* Curious People - Want to understand more about the way JVM bytecode works
  or the things that are possible at the bytecode level? Always wondered what 
  `invokedynamic` is for? Curious as to how `@SneakyThrows` can possibly work?
  This might help!

* System Implementors - If you're writing a JVM or runtime this could be useful
  for generating test classes...

* Advanced Programmers - You could hand-generate critical classes or methods
  if you think Javac isn't doing the right thing (but spoiler alert: by this
  point, it almost certainly is).

* Language Implementors - You could use this as an IL if you liked, rather
  than getting involved in the nuts and bolts of the binary `.class` format.

* Security Researchers - Create hostile classes and see if you can sneak them
  past the class verifier.

* Teachers - Perhaps you're teaching a compiler course, maybe you could use this
  to introduce students to JVM bytecode, or even as an IL for the compilers.

### Who?

JASM is copyright 2022 Ross Bamford (roscopeco AT gmail DOT com). 

See LICENSE.md for the gory legal stuff (spoiler: MIT).