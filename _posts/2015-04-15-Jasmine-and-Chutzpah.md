---
layout: post
title:  "Jasmine and Chutzpah"
date:   2015-04-15 17:30:00
categories: blog
---
Not only your server code needs unit tests, your front-end code should also be unit tested! For javascript code, I highly recommend picking up Jasmine as your unit test framework, and use Chutzpah as your runner.

### Jasmine Sweetness
Of course, there are many other javascript unit test framework out there. Jasmine shines for the following reasons:

#### 1. Fluent API

Jasmine's behavior-driven syntax is a huge selling point for me. It makes the tests read very nicely:

{% highlight javascript %}
describe("My button", function() {
  it("turns red when clicked", function() {
    // test code here
  });
});
{% endhighlight %}

Notice how the second line reads just like plain English with the it() function - "It turns red when clicked". It basically turns your software specification into a unit test file. Each of your requirement will be a unit test! In my opinion, that's just a brilliant design.

#### 2. Built-in assertion library that is extensible
As opposed to other javascript unit testing framework, Jasmine comes with a default assertion library that is extensible. Again, the assertion library is written in Fluent API style:

{% highlight javascript %}
expect(isClicked).toBe(true);
{% endhighlight %}

It reads so nicely, compared to other non-fluent assertion libraries:

{% highlight javascript %}
// What does the following mean?
// Are we asserting true to be isClicked?
// Or are we asserting isClicked to be true?
assert(true, isClicked);
{% endhighlight %}

#### 3. Stubbing with a spy
Jasmine comes with "Spies" that helps with easy stub testing. If your javascript code makes AJAX calls to a real server, you might want to swap it out in your unit tests to avoid external dependency, yet still testing that your methods is getting called:

{% highlight javascript %}
descript("An AJAX spy", function() {
  it("intercepts $.ajax() call", function({
    // Time to spy!
    spyOn($, 'ajax');
    
    // Simulate button clicking that calls $.ajax()
    // Since we stubbed out with a spy, it won't talk to a real server
    $("#button").trigger("click");
    
    // Assert that $.ajax() is called
    expect($.ajax).toHaveBeenCalled();
  });
});
{% endhighlight %}

### Resharper, what's wrong with you?
For people like me who does .NET development, Resharper is a wonderful tool. It also comes with built-in support for running Jasmine tests. However, I found out that Resharper runs Jasmine tests in a somewhat unexpectedly awkward way - It seems like Resharper runner would force-load its baked-in version of Jasmine.js AFTER my other referenced dependency are loaded. Remember that in the javascript world, the last loading file wins. As a result, I cannot load up my custom Jasmine matchers, and I cannot really load up other versions of Jasmine either. Moreover, with Resharper, you can really only run the tests at development time. Ideally, you would actually also want to run tests at build time in your continuous integration systems. Therefore, it's a good idea to find a stand-alone Jasmine test runner... 

### Chutzpah!
I found [Chutzpah](http://mmanela.github.io/chutzpah/)to be a sufficient solution as my Jasmine test runner. The best thing about Chutzpah is that it does not have other external dependency. There are many other Jasmine runners out in the wild, but many requires complicated setups with dependency like Node.js. Luckily, Chutzpah is here to rescue! With Chutzpah, I can simply commit the package into my repository, and then one simple command line to run my tests:

{% highlight sh %}
> ./chutzpah.console.exe ./Project/myJasmineTests.js
{% endhighlight %}

There are also several Visual Studio extensions that one can install (For free!) to easily run Jasmine tests with Chutzpah:

* [Chutzpah Test Adapter for the Test Explorer](https://visualstudiogallery.msdn.microsoft.com/f8741f04-bae4-4900-81c7-7c9bfb9ed1fe) - This allows Jasmine tests to show up in Test Explorer which you can run with Chutzpah
* [Chutzpah Test Runner Context Menu Extension](https://visualstudiogallery.msdn.microsoft.com/71a4e9bd-f660-448f-bd92-f5a65d39b7f0) - This allows you to right click on a Jasmine test file to run with Chutzpah