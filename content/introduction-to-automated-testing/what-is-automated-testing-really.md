---
title: "What is Automated Testing Really?"
date: 2017-12-02T13:09:39-05:00
---

Last time, we saw an overview of automated testing and why it matters. We covered the high-level concepts of what it is and what it can mean for the quality and stability of our code. But we didn't _see_ any of it. Let's look deeper here and get a peek of test code.

# Well, what is it _really_?

In the previous post we saw the definition: Automated tests are when you write code that tests other code. That is, you typically have your application, and then a second codebase that exercises it. The latter are the tests.

Before we see a test, I want to cement in a basic idea: Tests aren't magic. It's just code that runs other code.

It's easy to get overwhelmed with the tools—testing frameworks, mocking libraries, assertion libraries, etc. It's easy to get overwhelmed by the types of testing—unit, integration, UI, acceptance, smoke, end-to-end (the list feels endless). There's a lot to it, but always remember:

Tests aren't magic. It's just code that runs other code.

# A scenario

Let's invent a trivial example. Don't look too deeply into it—the idea is the important part here. Take the following class:

```csharp
public static class StringPluralizer
{
    public static string Pluralize( string s, int count )
    {
        if ( count > 1 )
            return s + "s";
      
        return s;
    }
}
```

Imagine how we could use it to pluralize things:

```csharp
// Returns "thing" since there is only 1
string thing = StringPluralizer.Pluralize( "thing", 1 );

// Returns "things" since there are more than 1
string things = StringPluralizer.Pluralize( "thing" 2 );
```

We want to test this class to establish a set of cases that will _stay true_. The tests act as a contract and ensures the code is doing what it's supposed to. If the code changes and cases are broken, our tests will notice (because as programmers, we probably won't).

What does this look like?

# A test

We'll later delve into all the tools I mentioned above. But before we get anywhere, let's really understand that tests are just code that runs other code. Specifically, one test is one case, and we'll accumulate lots of them. Let's imagine a test:

```csharp
public static class StringPluralizerTests
{
    public static void PluralizesAStringWhenThereAreMultiple()
    {
        string s = StringPluralizer.Pluralize( "thing", 2 );
      
        if ( s == "things" )
            Console.WriteLine( "Passed" );
        else
            Console.WriteLine( "Failed" )
    }
}
```

No magic here. It invokes our `Pluralize` method with a set of inputs and captures the result. It then compares it to the correct, expected result. If it matches, we get a good output, and a failure output if not. If our `Pluralize` method stops working for this case from refactoring or maintenance, we'll suddenly start seeing error output.

But how do we use this test class?

We could start a new solution and add a console app—our test runner. It could look something like this:

```csharp
public static void Main()
{
    StringPluralizerTests.PluralizesAStringWhenThereAreMultiple()
}
```

Not bad. Launching the test runner would exercise our one case. From there, we could add more tests—methods on `StringPluralizerTests`—and call them from `Main`. This is the foundation of a test suite.

But manually calling these methods sounds like a lot of work.

# A rich toolset

In practice, we don't maintain our own test runners. Instead, we use _testing frameworks_. These are libraries and tools that provide the test runners. They do the heavy lifting of calling our test methods and reporting success or failures. This frees us to focus on the tests themselves, not the infrastructure supporting them. The point of all this is to remember that they're not magical, but instead (basically) do what we've done here. They can be sophisticated and have nice features, but at their core, they run tests and report results.

There are many more tools that we can use to facilitate testing and we'll see plenty of them. The core idea is that they reduce the amount of work that we—the test authors—have to do. We use these additional tools to bear that burden so we don't have to, and to keep our attention on the tests.

# A promise

Tests are a contract, a set of promises. They verify that the code is upholding its end of the bargain: that it works for all the cases we've established. This is the big value we want, since code changes all the time. It can be difficult or impossible to understand the implications of a code change, and having an automatic watchdog is an attractive safety net.

# The future

Next time we'll dive into a test framework and apply our trivial example. We'll see that it saves us a ton of maintenance by providing the facilities. We can then turn our attention to the tests and look deeper into them.
