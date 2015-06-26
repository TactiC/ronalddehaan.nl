---
layout: post
title: Cool singleton solution
date: 2015-06-26 21:10
---

Singletons are considered a [bad design pattern][bad design pattern],
but sometimes they can be very handy.

Usually I implement them like this:

```java
public final class Singleton {

    private static final Singleton instance;

    static {
        instance = new Singleton();
    }

    public static Singleton getInstance() {
        return instance;
    }

    public void doSingleStuf() {

    }
}
```

But flipping through 'Effective Java' the other day I saw
some other approach, using a enum type.

```java
public enum Singleton {
    INSTANCE;

    public void doSingleStuf() {

    }
}
```

Haven't seen that approach before, but I like it!

[bad design pattern]:https://code.google.com/p/google-singleton-detector/wiki/WhySingletonsAreControversial
