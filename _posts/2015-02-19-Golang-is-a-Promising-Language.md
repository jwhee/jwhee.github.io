---
layout: post
title:  "Golang is a Promising Language"
date:   2015-02-19 17:30:00
categories: blog
---
I have written some C/C++ code, but mostly enjoying writing C# code everyday. I have pulled my facial hair out while doing some Javascript stuff, and developed Python tools here and there. There was also a time where I was in the mood of Java and wrote a few Groovy scripts. As a matter of fact, I am pretty comfortable writing code in almost any language. Every language has its strengths and weaknesses. At the end of the day, they are just tools so that we don't have to speak machine. Thus, I hardly have a favorite language... until now.

Go is a promising language. After watching Rob Pike's conference talks, I have decided that Go could potentially be my favorite language. No, I cannot say it's my favorite just yet, because I have not written sufficient Go code yet. But just by learning about its design philosophy and the problems it's addressing, I am becoming a believer in Go.

### A Static Language That Feels Dynamic
One of Go's design philosophy is to feel "modern". In traditional compiled language like C++, we have to explicitly declare a variable type. We have to also indicate if it's a value or a pointer. As a side effect of being explicit, there are a lot of redundant information.

{% highlight cpp %}
int count = 0; // This is a value variable!
GameObject* gameObject = new GameObject(); // This is a pointer variable!
{% endhighlight %}

On the other hand, dynamic language like Javascript allows us to declare a variable without types. Even crazier, you may assign a value of a different type to the same variable.
{% highlight javascript %}
var foo = 1; // foo is an integer
foo = "Foo"; // foo is now a string!
{% endhighlight %}

Sure, the dynamic example above is probably a bad practice, and could cause a lot runtime errors and debugging headaches, but that's the point! We want type-safety provided by compilers, but compiler should be able to perform type checking without us developers being explicit. That's the beauty of type inference. We developers can be lazy, and let compilers to do all the hard work. In fact, C# already supports it!

{% highlight csharp %}
var gameObject = new GameObject();
{% endhighlight %}

As a code reader and writer, I don't really care what type the variable is. That's the compiler's job.

Go also supports this philosophy. Here's how you declare and initialize a variable in Go:

{% highlight csharp %} 
gameObject := GameObject{}
{% endhighlight %}

Kinda derpy, isn't it? Notably the Newsqueak flavored := notation looks so odd at first glance. To be honest, I don't fully buy in this notation, and I rather prefer the C# way of expressing type inference. However, I do appreciate how Go is trying to minimize the number of key presses I need to do for declaring a variable. Bottom line, Go has support for type inference when declaring a variable, and this is the first step to modern languages.

### Dependency Management
To really make compiled language feel dynamic, it has to build fast. Yes, It has to build REALLY fast. Traditional large C/C++ projects have been infamous for their extremely slow build time. It mainly has to do with the poor I/O time when reading headers files and resolving dependency. Go provides dependency management at language level. It's a build error if there's a chained dependency. It's a build error if an imported package is unused. All exported code are read first so I/O overhead is minimized. With enforcement of better dependency structure, the fast compile time of Go projects is the big selling point of the language, and is what really helps the language to feel like a dynamic language.

Shorter build time helps development process to have shorter iteration cycles, and that's definitely a good thing.

### Forget Inheritance
Inheritance is a very strong methodology in object oriented programming world. Students are taught the magic of polymorphism at schools, and not to mention those trick question on final exams:

{% highlight cpp %}
Animal* fox = new Fox();
fox.Speak(); // what does the fox say?
{% endhighlight %}

For a long time, it feels great to master inheritance and polymorphism, but soon I learned that inheritance can be a problem if abused. In theory, inheritance is a great way to abstract and reuse code, if and only if the inheritance hierarchy is designed correctly. In practice, it is almost impossible to design a good inheritance structure upfront in the modern agile development process. As a result, iterative development process often yields a poorly designed inheritance structure, which introduces massive maintenance headache. I have maintained a code base where objects have inheritance that is more than five levels deep; it was so easy to break things but so difficult to fix them.

Outside of the academic world, developers have generally recognized the problem with inheritance. It's advised to best stay away from inheritance. If required, the inheritance structure should stay as flat as possible, which more than often leads into a conversation about the other methodology - interfaces.

Go developers are also aware of the problems with inheritance, and they took an extreme measure to solve the issue - They chopped it. Inheritance is simply not supported in Go, and instead Go emphasizes on interfaces and compositional code. Of course, there are still ways to emulate inheritance in Go, but Go enforces developers to think in a compositional way, which is proven to be a better methodology in the modern agile development world.

Go encourages compositional pattern at the language level, and I like this design philosophy!

### Designed for concurrency
The REAL big selling point for Go is that concurrency is its first class citizen.

At the time when C/C++ was first designed, the mainstream is about single core, single process programs. Slowly into the years of Java boom, people are talking about multi-threading, but such model is still too resource heavy. In recent year, we are finally moving into concurrency models with green threads, coroutines, and cooperative yielding. However, very few languages have native support for concurrency. For example, it is until .NET 4.5 for C# to support async-await syntax for cooperative yielding. Python still needs a external library like gevent to support coroutine-based operations.

Go support coroutines called "Goroutines" at version 1.0 release, and this is very impressive. Moreover, instead of using mutex, locks, and semaphores to share memory, Go emphasizes on using the concept of Channels for its concurrency model. As Rob Pike said, we should be "sharing memory by communicating, not communicating by sharing memory". This is bringing the network programming concept into programming language design. Once again, this is enforcing an arguably better pattern at the language level. I would really want to see this channel pattern being taught at school instead of mutexes and semaphores.

This native support for coroutines is one huge win with Go.

### Oh, and Gopher
I thought this is worth mention, but which other programming language has a cute mascot like the Go Gopher?
![](/images/gopher.jpg)

### Final Words
Again, I have not written much Go code yet, so I cannot say Go is the one. However, I very appreciate Go's design philosophy. I don't agree with a lot of Go's syntax, but it's designed to solve practical problems so I believe in its potential.

For my next personal project, I'm definitely going Go.
