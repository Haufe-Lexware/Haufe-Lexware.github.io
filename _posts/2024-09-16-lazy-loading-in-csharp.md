---
layout: post
title: Lazy Loading in C# (and not only)
subtitle:
category: dev
tags: [dotnet, development]
author: Stoian Dan
author_email: dan.stoian@haufe.com
header-img: "images/lazy-loading-in-csharp/British_club_scene.jpg"
---

## Introduction
_Lazy loading_ is a technique used to delay the execution of code for later on. There are a couple of reasons as to why you'd want to do that, and we'll enumerate some:

- Speed – the best code, is also the fastest code, the most secure and most maintainable… namely _no code at all_. However, for obvious reasons, we do need at times to write code, but while we can't avoid writing it, we could delay or even, _possibly_, completely avoid _executing_ it.
- Memory footprint – RAM is still important, avoiding loading a heavy object in memory does not just result in increased speed, but also in a more efficient system, memory-wise.

## Hands-on

While the theory sounds great, there still remains the question of how do you actually _avoid_ calling code? While there are a couple of ways to do that, I'd like to focus on two of them:


###  Singletons 
First, when it comes the the [_singleton pattern_](https://en.wikipedia.org/wiki/Singleton_pattern), objects (if we're talking in an _OOP_ context, but this goes for other paradigms as well) are often _statically_ allocated. This affects us even more, as static code, often get initialized early on. For example in `.NET Framework`  _static_ fields get initialized before the constructor of the class is called. This is also the case for other platforms, like `Java`. Here an `if` check is usually employed for _lazy loading_:

```csharp
   class Singleton 
   {
      private static Singleton _instance = null; // redundant, for explicity
      
      private Singleton { ... }
      
      public Singleton Instance()
      {
          if (Singleton._instance == null)
          {
             _instance = new Singleton();
          }
          return _instance;
      }
   }
```

In the above example ☝️ we can see how an `if` statement is used to check if our singleton has ever been instantiated before, if so, we just return that instance. However, if this is the first time, we create a first instance. This saves us some time and memory, because we might not get to use the class at all (depending on the use case) or we might just delay the execution.

Notice, the more moder `.NET` platform actually uses lazy loading by default. Static fields only get initialized before being used, and so, `.NET` does this for us, as opposed to the older `.NET Framework`.


## Instance fields

The more interesting and common use case is when we'd like to delay the initialization of an _instance_ field. For this, both frameworks (`.NET` and `.NET Framework`) come with build-in support, namely: `Lazy<T>`, a generic class which can wrap objects of any type and delay their initialization. We'll explore a simple implementation of such an idea, and see how we can achieve this in pretty much any programming language.

Frist imagine the scenario of two classes:
```csharp
public class Foo { ... }
``` 
and 
```csharp
public class Bar { ... }
```
with a _composition_ relationship of type:
```csharp
public class Foo 
{
   Bar bar = new();
   ...
}
```
Our goal is to delay the initialization of the field `bar` in any `Foo` instance.

From the get go, our solution needs to address any possible type, not just `Bar`. This is why _generics_ are needed. So our solution begins to look as such:

```csharp
   public class Lazy<T> { … }
```

Second, we'd need to know all about how _exactly_ to create this `bar` object, and yet, _delay_ the process… This sounds like a _producer_ – a method that produces such and object, encorporates the _how_ – and a _callback_ (_lambda_ or _higher-order function_), a method passed as argument, to be called when needed. This is exactly what we need! In `.NET` the `Func<T>` type holds a _method_ (or _function_ for you functional programmers) that takes no parameters and returns a `T` – `Func` comes in a buch of variations that also take arguments, the last one always representing the return value, such as `Func<T,TResult>`, `Func<T,T,TResult>` and so on.

We can use is as such:

```csharp
   public class Lazy<T> 
    {
        private Func<T> _generator;
        public Lazy(Func<T> generator) => _generator = generator;

        public T Load() => this._generator();
        
    }
```
All that we're providing to the constructor of the `Lazy` class is a function, that describes how to return a `T`, and whenever we want to actually return that `T`, we just call `Load`.

Let's see this in action:
```csharp
public class Foo 
{
   // old code: Bar bar = new();
   Lazy<Bar> bar_lazy = new Lazy<Bar>(() => new());
   ...
}
```

This looks great! Now, whenever we want our instance, we can just call `Load` on `bar_lazy`, but the beauty of warping things in a function is that, even if our `Bar` constructor required parameters, this would be fixed with just a small adjustment:

```csharp
public class Foo 
{
    //ctor injection, provide `lazy_bar` as a dependency
    Foo(Lazy<Bar> lazy_bar)
    ...
}
```
```csharp
// calling code
lazy_bar = new Lazy<Bar> (() => new Bar(name, age, other_param))
new Foo(lazy_bar);
...
```
It's true that now we can't really do an in-line initialization, but that would have been true even without our static wrapper, unless, of-course, we use static variables.
