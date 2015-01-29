---
layout: post
title:  "Designing Fluent API for Self-Documented Code"
date:   2015-01-28 22:00:00
categories: blog
---

I want to write self-documented code, and I have encountered a mentionable pattern that could help achieving this goal - Fluent API

"Good code documents itself" is becoming the norm, and I appreciate the motivation behind it. Yes, we should put comments in our code, but even better, we should write clean code that is easy to understand, so that commenting is not always required. However, there are so many occurrences where people (including myself) just use "good code documents itself" as an excuse to not write any comments. As a result, I had to trace through tangled logic with poorly named functions and variables to figure out what is happening. It became a mystery puzzle game. Surely, it is really satisfying to have the "A-ha!" moment when we finally understand what is going on with the undocumented code, but let's be honest - we could have saved time and energy if we had written sufficient documentation or clean code at the first place.

### Fluent API, a Monad Pattern

Fluent API is a way to write easy-to-understand code. It's closely related to the [Monad Pattern](http://en.wikipedia.org/wiki/Monad_%28functional_programming%29), where the term "monad" originated from [category theory](http://en.wikipedia.org/wiki/Category_theory). In the modern computer science world, there are many complex operations: I/O, exception handling, concurrency...etc. We want to divide and conquer, breaking down such complicated operations into smaller, manageable components. Thus, the Monad Pattern comes to rescue!

Unfortunately, it's awfully hard to explain the Monad Pattern in plain English. Let's just look at what a Fluent API appears like in code - it's a chain of operations: 

#### Example 1: Object construction
Overloaded constructors normally read like:

{% highlight csharp %}
var player = new Player("Jerry", 0, 100, Color.Red);
{% endhighlight %}

Without intelligence, it is quite difficult to understand what each parameter is representing.

In Fluent API, it is much easier to comprehend:

{% highlight csharp %}
var player = new Player().setName("Jerry")
                   .setPosition(0, 100)
                   .setTeamColor(Color.Red);
{% endhighlight %}

#### Example 2: Function chaining
This is usually how we would call multiple member functions in a series:

{% highlight csharp %}
var enemy = new EnemyShip();
enemy.Move()
enemy.ShootMissle();
enemy.DropItem();
{% endhighlight %}

Each line begins with the same caller, which is arguably too much redundant information to be presented to the reader.

In Fluent API, we can chain them together for readability:

{% highlight csharp %}
var enemy = new EnemyShip()
enemy.Move()
  .ShootMissle()
  .DropItem();
{% endhighlight %}

#### Wait. How is that useful?

I have to admit that the above two examples are merely quick demonstration of what Fluent API would read like. However, they are not all that useful in their context. 

To the point, Example #1 could have been solved with appropriate object initializer:

{% highlight csharp %}
var player = new Player
  {
    Name = "Jerry",
    XPos = 0,
    YPos = 100,
    TeamColor = Color.Red  
  };
{% endhighlight %}

Example #2 suffers from poor debuggability, as many debuggers do not currently support break-pointing in function chains. However, I want to argue that all modern debuggers should support Fluent API, so that we do not have to sacrifice debuggability over readability. In order to make a convincing argument, I'd like to show a few real-world examples to demonstrate the usefulness of the Monad Pattern.

### Real-world Monad Pattern

Fluent API is not a brand new idea. In fact, I have already encountered it numerous times without realizing it:

#### jQuery object
As a web developer, we have all used jQuery at some point. jQuery is famous for its intuitiveness when it comes to traversing DOM elements:

{% highlight javascript %}
$(".body").children().addClass("hidden").fadeOut();
{% endhighlight %}

The monad pattern allows jQuery object to encapsulate single or multiple DOM elements. In the above example, addClass() and fadeOut() function are both applied to ALL children of the body element.

Without the fluent design, the same code would have looked like this:
{% highlight javascript %}
var $children = $(".body").children();
$children.forEach(function($child) {
  $child.addClass("hidden");
  $child.fadeOut();
}
{% endhighlight %}

The code not only is harder to read, but it also involves more keystrokes at development time.

#### LINQ expressions
IEnumerable&lt;T&gt; and LINQ expressions are also a Monad Pattern, which encapsulates the complexity of data structures and search algorithms. For example, to search and sort items in a data structure, we can simply write:

{% highlight csharp %}
var weakEnemies = enemies.Where(e => e.Power < 10)
                    .OrderBy(e => e.Age)
                    .Select(e => e.Name);
{% endhighlight %}

We do not have worry about whether the data structure is a linked-list or a hashset. We do not need to repeat the effort of writing our own log(N) search or quick sort algorithm against it. All of the complexity related to searching and sorting have been hidden behind-the-scene with a readable, SQL-like syntax. The beauty of LINQ expressions is that we let the language library to do all the laboring work for us. 

#### Jasmine's assertion library
Jasmine is the unit test framework for Javascript. It has a fluent assertion library that reads:

{% highlight javascript %}
expect(playerShip.Life).toBe(100);
expect(playerShip.IsAlive).toBeTruthy();
{% endhighlight %}

We can also write inverted assertion easily:

{% highlight javascript %}
expect(playerShip.Life).not.toBe(0);
expect(playerShip.IsAlive).not.toBeFalsy();
{% endhighlight %}

It reads like plain English! In my opinion, that's brilliant! In contrast, the default xUnit assertion library lacks the differentiation between expected value and actual value without intellisense:

{% highlight csharp %}
Assert.Equal(playerShip.Life, 100); // wrong order
Assert.Equal(100, playerShip.Life); // good
{% endhighlight %}

The incorrect ordering of the expected and actual values would result in misleading assertion message, which could have been avoided if the API had a fluent design.

#### Continuation
Continuation is the monad pattern for asynchronous programming. In jQuery, we have the promise object when making AJAX request:

{% highlight javascript %}
// Make asynchronous AJAX request
var promise = $.ajax({
  url: "http://jwhee.github.io/",
});

// When we get a response
promise.done(function(data) {
  // process data
});
{% endhighlight %}

In .NET framework, we have Task&lt;T&gt;.ContinueWith()
{% highlight csharp %}
// Run first task
Task firstTask = httpClient.RequestAsync();

// When the first task is completed, run the second task
Task secondTask = firstTask.ContinueWith(() => Process());
{% endhighlight %}

Both examples are encapsulations of concurrency, making it easier to read and write asynchronous operations.

### Benefits of Monad Pattern
In summary, the Monad Pattern has the following benefits:

1. **Improved readability.** Each step in a series of operation is granular and easy to follow. Each function call reads like plain English, so we no longer need to rely on intellisense or write extensive comments for the code to be understandable.
2. **Shortens design time for API consumers.** The complexity is encapsulated within the APIs, so the API consumers do not have to worry about the complicated operations that are taking place behind the curtain. Using the Fluent API, we can simply write clean code in just a few keystrokes.

### Drawbacks of the Monad Pattern
It is important that we are aware of the shortcoming with the Monad Pattern:

1. **Poor debuggability.** As mentioned previously, not many debuggers currently supoort break-pointing in the method chains. Also, if an exception is thrown, the debugger may also failed to recognize which function call in the chain is the root cause.
2. **It turns into a domain-specific language (DSL).** Fluent APIs are soft syntax for a given language. If not widely accepted, the APIs would just become a tribal knowledge that is maintained by subset of developers. 

Despite those two problems, I remain as a huge proponent for fluent designs. I think debuggers should eventually be improved to effectively support debugging method chains. I think it's OK that Fluent APIs turns into a DSL, because a well-designed Fluent API will eventually become part of the programming language.

For example, LINQ can also be written with the following C# syntax:

{% highlight csharp %}
var weakEnemies = from e in enemies
                   where e.Power < 10
                   orderby e.Age
                   select e.Name;
{% endhighlight %}

Moreover, a well-accepted Monad Pattern is observable among multiple languages. As seen in the previous example, the Continuation Monad is observable in JavaScript, C#, and many other languages. At that point, it's not just a piece of domain-specific knowledge, but rather it's a new language syntax in its incubation stage, slowly being accepted by different communities. Therefore, I believe  that a good Fluent API design will help the evolution and advancement of modern programming languages.

### Self-Documented Code
When designing APIs and object classes, developers should consider the Fluent API pattern. It enforces the idea that the code should read like English, hence making it easier to achieve the goal of self-documented code.

For example, let's assume we have the following container for a data entry:

{% highlight csharp %}
class Entry
{
  IEnumerable<string> Columns { get; set; }
  IEnumerable<string> Values { get; set; }
}
{% endhighlight %}

Each entry is a collection of key-value-pairs, so we have to ensure that Columns and Values are in their respective orders when constructed:

{% highlight csharp %}
var data = new Entry()
{
  Columns = new List<string> { "key1", "key2" ),
  Values = new List<string> { "value1", "value2" }
};
{% endhighlight %}

Preserving the order requires a lot of mental work, and the above object initializer does not necessary communicate the importance of the key-value-pair mapping. It also required the developer to repeatedly choose an implementation of IEnumerable&lt;T&gt; at design time. Instead of using the object initializer, we can design a Fluent API that does not take as much toll to read and write:

{% highlight csharp %}
var data = new EntryBuilder()
                 .Set("key1", "value1")
                 .Set("key2", "value2")
                 .Build();
{% endhighlight %}

With the Fluent API syntax, the reader can easily visualize the key-value-pair mapping. Also, we abstract the data structure inside of EntryBuilder, so that we do not have to repetitively choose data structures at design time. By designing our APIs with the Fluent API pattern, we can easily write readable code.

### Conclusion
The Fluent API design is proven to be an attractive option for writing clean, readable code. Despite its drawbacks in debuggability and accessibility, I believe those problems can be overcomed, and eventually the design will yield a net-positive gain in the programming community.