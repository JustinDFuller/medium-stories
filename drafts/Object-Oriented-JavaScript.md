# Object Oriented JavaScript

## Outline

* [Why am I writing about Object Oriented JavaScript?](#why-am-i-writing-about-object-oriented-javaScript)
* [Why have I switched from functional to Object Oriented programming?](#why-have-i-switched-from-functional-to-object-oriented-programming)
  * [Programming Paradigms](#programming-paradigms)
* [What is Object Oriented Programming?](#what-is-object-oriented-programming)
  * [What is the goal of Object Oriented Programming](#what-is-the-goal-of-object-oriented-programming)
  * Encapsulation
  * Inheritance, composition, delegation
  * Polymorphism
* Is Object Oriented programming in JavaScript different than other programming languages?
  * Classical vs Prototypal inheritance
  * What does it mean in practice?
* Guiding Principles of Object Oriented Programming
  * SOLID
    * Single-Responsibility Principle
    * Open-Closed Principle
    * Liskov-Substitution Principle
    * Interface-Segregation Principle
    * Dependency-Inversion Principle
  * Package Principles
    * Reuse-release Equivalence Principle
    * Common-Reuse Principle
    * Common-Closure Principle
    * Acyclic Dependencies Principle
    * Stable-Dependencies Principle
    * Stable-Abstractions Principle
  * Refactoring
    * Strategies
    * Test-Driven Development
* Case Studies
  * Why case studies?
  * How to read the case studies
  * The actual case studies
    * Case Study 1: Todo App
      * Defining the idea
      * Initial strategy for implementation
      * Write the initial code with TDD
      * New features!
      * Refactor
      * Implement the new features
      * Review
    * Case Study 2: Bed and Breakfast App
      * Defining the idea
      * Defining the actors
      * Splitting it all up (agile-sprint style)
      * Stategy for first round of features
      * Implement with TDD
      * Strategy for second round of features
      * Refactor
      * Implement with TDD
      * Review
    * Case Study 3: MMORPG Game
      * Defining the game & rules
      * Choosing a scenario to implement first
      * Designing the game engine
      * Implementing the game engine with TDD while implementing the first scenario
      * Review
* Design Patterns
  * Will likely put these in the case studies if they are used
  * When to use a design pattern
  * When _not_ to use a design pattern

## Why am I writing about Object Oriented JavaScript?

My wife and I recently took a personality test that was meant to help us understand our deepest motivations. Unsurprisingly, we learned that her deepest motivators are to feel supported and secure. I, on the other hand, found that I am motivated simply by the desire to understand things. Ever since then she has poked fun at me, saying that most people are motivated by a desire for success or friendship, a need for survival or a higher calling — everyone except me, I "just want to learn stuff".

Learning is really fun and if you are reading this you likely feel the same way. I doubt anyone will ever force another human to read this work, so you must really enjoy learning to wind up reading this. Just like I found myself, over the course of the past two years, reading as many programming books as I can. 

The books that I feel have provided the most benefit have been older and focused heavily on Object Oriented Programming. I'm sure that it needs no introduction from me; you can hardly learn about programming without hearing about "OOP".

So I found myself enjoying the work of [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_C._Martin) through books like [Clean  Code](https://amzn.to/31GmPO3) and [Clean Architecture](https://amzn.to/2Nur1g3). However, Clean Architecture really didn't make much sense to me. It was obvious that I had some fundamental knowledge gap that didn't allow me to fully understand the importance of the architectural decisions he was suggesting. He recommended his earlier book—which, at the time of this writing, was published 17 years ago—that claimed to focus on the fundamentals of Object Oriented Programming. [Agile Software Development, Principles, Patterns, and Practices](https://amzn.to/31H3Lz6) certainly lived up to its name, providing a strong foundation of what it is to program an Object Oriented application. 

Further reading led me to [Refactoring](https://amzn.to/2AJ0VO8) by Martin Fowler. This book helped me understand how to turn a poorly written OOP program into a "good" (much, much more on this later) OOP program. 

There's only one problem. These books are all written in Java and C++.

That means that, in order to apply these concepts to the Web Development work that I do every day, the examples and concepts must be translated—even if just in my head—to JavaScript. 

I don't want this valuable content to only be available to backend developers who already know these languages. The principles and strategies are applicable to web development too. So that leads to my __first goal__, which is to provide Object Oriented Programming principles and strategies in the JavaScript language. 

I plan to cover much of the same content that is provided by Martin Fowler and Robert Martin's books, the difference being that it will be in JavaScript. 

The other difference is that these books focus heavily on the logic without connecting them to a fully functioning application. I imagine this was an intentional decision, and we'll talk more about this later, but my __second goal__ is to show these concepts in context with fully functioning applications.

Now, before I conclude this section, I believe that it would be dishonest of me to pretend, or even to omit, the fact that I have not been able to fully use these concepts and strategies in a production application. This is not to say that I don't practice them whenever I write code, but—unfortunately—I have only ever worked on applications that are, perhaps accidentally, not Object Oriented. This leads me to my __third and final goal__, which is to formulate the words needed to explain and advocate for these strategies in the context of real-world day-to-day development. I have found that, after learning about these principles and strategies, I can find uses for them regularly; still, I struggle to convince others to use them. This may be my most important goal, because it has the most direct impact for my growth. Hopefully it can provide others with the same benefits as well.

This is why I find myself writing about Object Oriented Programming principles and strategies. To translate it to JavaScript, to give them context in fully-functioning applications, and to provide the words necessary to explain and advocate them in the real world.

## Why have I switched from functional to Object Oriented programming? 

Here's a question that absolutely no one is asking. Why am I writing about OOP instead of functional programming? If you look through the history of my posts you'll see that I have mostly written about functional programming. I have been heavily influenced by Eric Elliot's [Composing Software book](https://medium.com/javascript-scene/composing-software-the-book-f31c77fc3ddc), which breaks down functional JavaScript in an incredibly easy-to-digest way.

I don't deny that functional programming is an excellent paradigm, but I have found that, under certain circumstances, adding into an existing application can lead to a big mess. I would like to explain what those circumstances are, but first we must briefly look at the history of several programming paradigms.

I discovered why I was having so much trouble when I read Robert Martin's explanation of programming paradigms in his book, [_Clean Architecture_](https://amzn.to/2Nur1g3). Martin outlines programming paradigms as a set of stair steps.

### Programming Paradigms

All programs, as far as the computer is aware, are unstructured. This means that the computer will allow a program to access any variable and jump to any line in the program. These are behaviors provided by the operating system. This is clearly not ideal. First, if a variable can be accessed or modified from anywhere in the program, then it can't really be trusted. Second, imagine that any line of code can jump to any other line of code; this would make changes incredibly difficult, because you have no idea who is attempting to jump to the line you are changing. 

So this led to the next programming paradigm step, [Structured programming](https://en.wikipedia.org/wiki/Structured_programming), which imposed discipline on programs by eleminating the use of `goto`—largely due to Edsger Dijkstra, who is credited with coining the term "Structured Programming", in his open letter, [_Go To Statement Considered Harmful_](https://homepages.cwi.nl/~storm/teaching/reader/Dijkstra68.pdf). Programs were improved by introducing control flow concepts such as `if/then/else` blocks and `for` or `while` loops; structures that replace unpredictable `goto` jumps with predictable, logical movements. Programs were also improved by making variables only accessible to certain well-defined scopes.

> The unbridled use of the go to statement has as an immediate consequence that it becomes terribly hard to find a meaningful set of coordinates in which to describe the process progress. ... The go to statement as it stands is just too primitive, it is too much an invitation to make a mess of one's program. 
>
> — Edsger Dijkstra, _Go To Statement Considered Harmful_

This first step led to safer programs, because the movement through the program's instructions, as well as changes to the data, became far more predictable.

Building off of structured programming, [Procedural programming](https://en.wikipedia.org/wiki/Procedural_programming) imposed discipline on programs by grouping sections of logic into groups known as procedures, routines, or—most commonly today—functions. This paradigm was used by programming languages such as Fortran, Pascal, and C. This isn't to say that structured programming languages don't have functions or subroutines, they certainly can, but procedural programming makes them a focal point, whereas structured programming does not. Procedural programming's goal is to continue the progress made by structured programming—that is, to make the program more predictable. To do this, variables and instructions are split into self-contained procedures. This made programs even more predictable, because logic and variables were grouped into functions.

Still, sometimes logic and data needed to be shared, but it should not be shared with everyone, because that would still be fundamentally unsafe and unpredictable. So, the question was, how could programming languages solve the problem of needing to share logic and data, but without sharing it too broadly? Enter, Object Oriented Programming. This paradigm bundles logic and data into a single `object`. An object might be a `struct` or a `class`; it doesn't matter. The point is that, ideally, only a single object knows about and manipulates its data, and only that single object can use its shared logic. This is intended to make it easy and safe to share logic and data.

Now, where does functional programming come in, and why have I shared all of this to explain why I am having problems introducing it to my own programs?

Well, as it turns out, Functional programming was first used in the 1950s and 1960s, around the same time as Procedural programming and Object Oriented programming. While Structured, Procedural, and Object Oriented programming were all building directly off of each other, Functional programming was moving down an entirely new branch in the programming paradigm tree (I know. Earlier, I described them as a set of steps, now they're a tree.) that solves the same problems in a fundamentally different way.

I don't want to go too deeply into functional programming, but at its core is the fact that data structures are immutable. When you have immutable data—data that cannot be changed—it no longer matters who can see it or who can operate on it because it cannot be modified in an unpredictable way; it cannot be modified at all. So, instead of hiding data and shared logic (think, private methods and properties in Object Oriented programming), Functional programming can leave it all (generally) public without experiencing the same issues.

Here lies the issue with adding functional programming in the middle of a procedural or Object Oriented program. You have paradigms that attempt to solve the same issues in very different ways. It can be very jarring to a developer when they are forced to jump back-and-forth between paradigms. Worse, assuming the safety of functional programming without immutable data can lead to unpredictable results.

In a perfect world these paradigms would not clash, but in the real world they often do. Instead of climbing up the tree, now you're swinging from branch to branch. (First steps, then branches, now a whole tree. I know.)

So, what's my take? If you can be completely functional, be functional. Otherwise, use Object Oriented programming. In most pre-existing applications you will experience more trouble than it's worth by mixing Functional programming in with Object Oriented and Procedural programming.

## What is Object Oriented Programming?

What is an object, and how can one be oriented to it? What can a program be oriented to, other than an object?

It turns out that the term "object" began showing up, in relation to programming, at MIT in the early 1960s. The term was associated with Lisp, you can find mentions of it in the [Lisp 1](http://history.siam.org/sup/Fox_1960_LISP.pdf) and [Lisp 1.5](https://archive.org/details/lisp15programmer00john/page/n3) programming manuals. It started out as a synonym for a specific type of data but over time came to represent any item that has attributes. 

This has a clear correlation to real life, that's what makes it so useful as a programming concept. A human has hair color, height, a birth date. A car has a make, model, color, and mileage. So also, an object in a program has attributes. The examples I gave could be for a program or for real life. 

In life, an object can have abilities, each of which knows how to use all it's individual attributes to perform some actions. 

For example, when you a turn a lamp on, do you take all the individual pieces of the lamp and adjust them in the appropriate way that a current of electricity will make it illuminate? No, you pull a cable or press a button and it does everything itself.

When you start your car, or turn on your tv, do you manipulate all the inner-circutry, or do you press the "on" button? 

In this way too, program objects can represent real life. They can hide all the attributes required for it to function and instead only expose to you the functionality that they provide.

This is how a program can be "oriented" to objects. The focus is on objects. Objects are used to represent your program, and your program of objects attempts to represent some real-world behavior. 

Think about what it means to be oriented to something. At work, I find that I am naturally "process oriented", meaning that I naturally tend to think about, and spend time on, improving the processes that my team uses to create software. Others are "detail oriented", meaning that they naturally focus intensely on every little detail and they can't stop until it's perfect. Your living room may be oriented toward a television, bookshelf, or window. Your house may be oriented north, east, south, or west.

Your program may be oriented toward individual instructions, primitive data types, immutable data types, lists, functions, or objects. Maybe it's partially oriented towards a few of them. After all, my living room is oriented toward my projector and my fire place. My house is oriented north and east. It doesn't have to be only one orientation.

### What is the goal of Object Oriented Programming?

We are now at a crucial point in exploring Object Oriented JavaScript. Hopefully it is understood what Object Oriented programming is, where it came from, and why I am discussing it, but there's still no clear goal. 

Why does Object Oriented programming exist? Why do programming paradigms exist?

__The goal of Object Oriented programming is to make a program easy to change.__

There are many benefits provided by Object Oriented programming, there are many principles of Object Oriented programming, there are many strategies for Object Oriented programming, but the goal, the goal of any programming paradigm, is to make programs that are easy to change.

Let's explore why this is the case, and why other goals didn't make the cut.

First, one must understand that software has two values.

1. The value of what the software can do today.
2. The value of what the software can do tomorrow.

Your manager, project manager, and product owner are very likely to be focused on what the software can do today. You, on the other hand, need to be focused on what the software can do tomorrow. Why? Because, as should be expected by any growing company, the business needs will constantly change, and so should your software.

Think about what happens when "requirements changes" or "feature requests" come to your team. What is the general reaction? 

Despair and frustration? 

> "Why can't they just make up their minds?" 
> 
> "We've already made changes three times this year!" 

Or are you able to say,

> "no problem, we've designed an application that is easy to change, this should only take us a couple of days."

If you are a software developer it is very likely that you are using an agile development methodology. If you are, it's right here in the [Agile Manifesto](https://agilemanifesto.org/).

> Responding to change over following a plan

If your business can't change, it can't adapt. If your business can't adapt, it will die. Therefor, your software must be able to change and adapt to the needs of the business, so your software must be easy to change. 

Now, let's address some worthy goals that didn't quite make the cut.

### But, what about being easy to understand? 

Why do you need to understand software? Almost all software is easy to understand while you are writing it for the first time. Usually all you need is clean formatting with expressive and accurate variable naming. This will get you through the first iteration of many programs. 

Many of this have experienced this first-hand. You implement a program, it works perfectly and you feel great about it—only to come back months later and think "what a mess, I don't understand any of this".

You need to understand software when you want to change it. Being easy to understand is a sub-goal of being easy to change, because there's no other reason to understand the software. In fact, if you were to create software that never needs to be changed then you might as well compile the application and delete the source code; it serves no further purpose.

### But, what about performance?

There are a number of reasons that performance cannot be the main goal of Object Oriented programming.

First, it can't be because performance is a moving goal-post and it's always relative. A program considered fast in the year 1999 would likely be considerd unusably slow today. Even the fastest internal corporate applications would be unlikely to meet the speed standards of a consumer-facing shopping application like Amazon.

Second, performance gains have largely been due to hardware, rather than software. Hardware has improved drastically since the 1980s, but, fundamentally, our programming languages are still the same. This presents a problem because your software implementation may have to change to keep up with new hardware. If your software is unable to change to keep up with modern hardware or software improvements, even the fastest application today will be a slow application tomorrow.

It should also be considered that most applications do not start off with great performance. Performance typically comes from tuning and adjusting as scale increases. If your software is difficult to change, you may not be able to make the necessary adjustments as your product usage increases, destroying the usability of your software. If you can't change to keep up with usage, this means you have to get it right the first time, which will probably end up looking similar to [one of the major causes of startup failures, premature scaling](https://s3.amazonaws.com/startupcompass-public/StartupGenomeReport2_Why_Startups_Fail_v2.pdf), but your software will be prematurely scaled, rather than your business.

Therefor performance is a benefit that comes from being easy to change, so it cannot be the main goal of OOP.

### But, what about reliability?
