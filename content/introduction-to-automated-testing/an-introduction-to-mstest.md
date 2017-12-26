---
title: "An Introduction to MSTest"
date: 2017-12-24T16:13:29-05:00
draft: false
---

So far in this series, we've explored the concept of automated testing. We've seen that it's nothing more than writing code to test other code. To mobilize this, we experimented with a custom console app that calls test functions that exercise the class we really wanted to test. We saw that this would show us when all the tests work--but also when they don't, to help us catch breaking changes. We don't yet have a sustainable way of doing this; maintaining a test-runner by hand isn't what we want, especially if we hope to accrue lots of tests.

We need a test framework.

# What a testing framework does

Testing frameworks are super-common tools that we use to help organize and run our tests: They discover our test cases, they're able to run them, and they integrate really nicely with our development tools.

They take the place of our toy example of a console app. They provide the infrastructure for testing, so we can concentrate on higher-level concepts: the testing itself. We're freed up to put all our effort into writing clean code, and not the manual labor of bookkeeping.

# Enter MSTest

Testing frameworks exist for tons of languages, but we're interested in C#. The first test framework you'll encounter in this arena is _MSTest_ from Microsoft. It comes with Visual Studio and is easy to use.

At its core, MSTest is made of _a DLL_ and _a Visual Studio extension_. We'll see how each of these help us out. Over your time with automated testing in C#, you'll find other frameworks that may suit your needs better than MSTest. They all have their own features, but they're fundamentally made of these same two components.

# What exactly are tests?

Tests are methods, just like in our toy example. They live in _test classes_, and these are written specifically to jive with the test framework. For MSTest, it requires a few things. Let's take our `StringPluralizer` class from last time and see how we'd write it for MSTest:

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace Tests
{
    [TestClass]
    public class StringPluralizerTests
    {
        [TestMethod]
        public void PluralizesAStringWhenThereAreMultiple()
        {
            string result = StringPluralizer.Pluralize( "thing", 2 );

            Assert.AreEqual( "things", result );
        }
    }
}
```

Let's go over the differences that make this work with MSTest.

### Importing the MSTest namespace

To write tests, we need to use a few attributes. We'll also use the `Assert` class to ensure our test results match what we expect. These guys live in the MSTest DLL that was mentioned earlier and they come from the `Microsoft.VisualStudio.TestTools.UnitTesting` namespace. We'll use this stuff a lot, so we have a `using` directive to shorthand it.

### Our class's namespace

Here, we wrote `namespace Tests`, but you can call it whatever you like. It should be meaningful for your project, typically named after the project you're testing, along with "Tests" at the end.

### The class

The test class houses the test methods. It must be `public` and an _instance_ class (as opposed to a static class). It also must have the `[TestClass]` attribute. The test runner looks for this combination of stuff, so if it isn't seeing your tests, double-check these things.

### The test method

This is the test itself. It must be `public`, return `void`, and have the `[TestMethod]` attribute. The test runner looks for methods that match this criteria to be considered tests.

One test method should be one test case. That is, a single test should exercise only one scenario. When a test fails, it makes it really clear what the problem is. There's a lot of organization and strategy we can use to make robust tests that we'll explore in the future.

### The method body

The first line was copied from last time. We use the object we want to test (sometimes called the "system under test", or SUT), and capture its return value. Next, we use MSTest's `Assert` class to measure the result against the value we expect. For this test case, if everything works, then `"things"` is the right answer. The `Assert` class will fail the test if they don't match.

Under the hood, the `Assert.AreEqual` method will throw an exception if the values don't match. The test runner will report test failures if it sees unhandled exceptions. If no exceptions are thrown during the test, then it gives it a passing grade. **This can be a "gotcha."** Make sure _all tests_ assert something. Asserts are what make tests correctly pass and fail.

### What else?

That's the makeup of a test class. We spend the majority of our testing time writing test methods and enumerating cases. But we haven't yet seen where test classes live, or even how to run them. We'll wrap up by going through just that.

# Where tests live

With all this, we have a solid blueprint of what a test looks like. But where do we put these classes? The answer is a _test project_.

We keep tests in a different project than the application code. They're closely related, but their responsibilities are different. The test project needs to reference the MSTest DLL, but also the application project, since it has to see the classes it's testing. The test project acts on the application project, but not the other way around, so the app project shouldn't reference the test project. Objects shouldn't have to know how they're being used, so they remain generic and reusable. It doesn't help your app to know about tests, but instead just muddies the responsibilities.

Let's create a home for our tests.

# Creating a test project

Test projects are nothing more than class libraries with a reference to MSTest. You can create it manually this way, but it's much easier to use the project template. There are flavors of templates for different kinds of Windows development--regular .NET, .NET Core, UWP, etc. If you're doing a traditional console app, WinForms or WPF, you'll want the standard one:

1. In the Solution Explorer, right-click the solution itself and hit _Add_ -> _New Project_
2. Find _Unit Test Project (.NET Framework)_ (there's a search box if you need it)
3. Give it a name
4. Hit OK

When naming your project, it's helpful to incorporate the name of the corresponding application project, so it's clear what it contains and what it's allowed to reference. For example, for the project `MyApplication`, the test project should be named something to the tune of `MyApplicationTests`. There are lots of conventions here, but it doesn't really matter which you choose--go with one and be consistent. A few other examples are `MyApplication.Test`, `MyApplication.UnitTests`, and so on.

After creating the project, we're almost done. We need to reference the project we're going to test:

1. In the Solution Explorer, make sure the new test project is expanded
2. Right-click the _References_ node
3. Hit _Add Reference..._
4. Make sure _Projects_ is selected, since we're referencing a project in the same solution
5. Check the box for the application project
6. Hit OK

Your test project can now use all the `public` members from the application project. This means we can now start creating test classes, and writing tests, using the main project's objects.

# Running the tests

This varies depending on the test framework, but we're looking at MSTest today. Visual Studio has a few ways to do this:

* From the _Test_ menu, hit _Run_ -> _All Tests_
* From the Solution Explorer, right-click your project and hit _Run Unit Tests_
* Press the shortcut, which should default to `Ctrl+R`,`A`

It'll pop up the list of tests that it finds and give you a nice red/green status indicator for each one.

# What now?

This is just the beginning. These basic steps are fundamental, and you'll need them again and again. Apps are often made of many projects, and that means we'll have lots of corresponding test projects, so you'll likely be making one before you know it.

# Next time

With a baseline knowledge of what tests are and the basic structure of how they work, we're set to dig into the details a little more. Let's look past the foundational mechanics and into some philosophy. As we progress in anything, we're liable to find subtlety and nuance once we surpass the very basics--and there's no shortage of that in automated testing.

See you next time.
