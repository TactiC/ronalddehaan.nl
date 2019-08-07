---
layout: post
title: Creating singletons with parameters
date: 2019-06-17 13:37
---
Add some point I needed a singleton object, but I also needed to initialze it with specific code. So a standard Scala object wan’t going to help me there since passing parameters to object like this does not compile.
```java
object Singleton(param: String) {

  def foo() = println(param)
}
```
I decided to go with implicit parameters. First I created the Singleton object and state that the actual thing to be put into foo will be provided implicit (in a rather explicit way, but you can’t have it all).
```java
object Singleton {

  private val foo = implicitly[String](Main.foo)

  def useFoo() = println(foo)
}
```
And then in Main I declare the implicit and after that the object can be used anywhere.
```java
object Main extends App {

  implicit val foo = "Ain't this cool or what..."

  Singleton.useFoo()
}
```
And we’re done, another problem solved. Let’s grab a coffee.