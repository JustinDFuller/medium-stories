# Object Oriented JavaScript

## Outline

* Why am I writing about Object Oriented JavaScript?
* Why have I switched from functional programming to Object Oriented?
  * Does it have to be one or the other?
* What is Object Oriented Programming?
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

## Why have I switched from functional programming to Object Oriented? 

Here's a question that absolutely no one is asking. Why am I writing about OOP instead of functional programming? If you look through the history of my posts you'll see that I have mostly written about functional programming. I have been heavily influenced by Eric Elliot's [Composing Software book](https://medium.com/javascript-scene/composing-software-the-book-f31c77fc3ddc), which breaks down functional JavaScript in an incredibly easy-to-digest way.

I don't deny that functional programming is an excellent paradigm, but I have found that, under certain circumstances, adding into an existing application can lead to a big mess. I would like to explain what those circumstances are, but first we must briefly look at the history of several programming paradigms.

I discovered why I was having so much trouble when I read Robert Martin's explanation of programming paradigms in his book, [_Clean Architecture_](https://amzn.to/2Nur1g3). Martin outlines programming paradigms as a set of stair steps.

### Programming Paradigms

All programs, as far as the computer is aware, are unstructured. This means that the computer will allow a program to access any variable and jump to any line in the program. These are behaviors provided by the operating system. This is clearly not ideal. First, if a variable can be accessed or modified from anywhere in the program, then it can't really be trusted. Second, imagine that any line of code can jump to any other line of code; this would make changes incredibly difficult, because you have no idea who is attempting to jump to the line you are changing. 

So this led to the next programming paradigm step, [Structured programming](https://en.wikipedia.org/wiki/Structured_programming), which imposed discipline on programs by eleminating the use of `goto`—largely due to Edsger Dijkstra's open letter [_Go To Statement Considered Harmful_](https://homepages.cwi.nl/~storm/teaching/reader/Dijkstra68.pdf). Programs were improved by introducing control flow concepts such as `if/then/else` blocks and `for` or `while` loops; structures that replace unpredictable `goto` jumps with predictable, logical movements. Programs were also improved by making variables only accessible to certain well-defined scopes.

This first step led to safer programs, because the movement through the program's instructions, as well as changes to the data, became far more predictable.

Building off of structured programming, [Procedural programming](https://en.wikipedia.org/wiki/Procedural_programming) imposed discipline on programs by grouping sections of logic into groups known as procedures, routines, or—most commonly today—functions. This paradigm was used by programming languages such as Fortran, Pascal, and C. This isn't to say that structured programming languages don't have functions or subroutines, they certainly can, but procedural programming makes them a focal point, whereas structured programming does not. Procedural programming's goal is to continue the progress made by structured programming—that is, to make the program more predictable. To do this, variables and instructions are split into self-contained procedures. This made programs even more predictable, because logic and variables were grouped into functions.

Still, sometimes logic and data needed to be shared, but it should not be shared with everyone, because that would still be fundamentally unsafe and unpredictable. So, the question was, how could programming languages solve the problem of needing to share logic and data, but without sharing it too broadly? Enter, Object Oriented Programming. This paradigm bundles logic and data into a single `object`. An object might be a `struct` or a `class`; it doesn't matter. The point is that, ideally, only a single object knows about and manipulates its data, and only that single object can use its shared logic. This is intended to make it easy and safe to share logic and data.

Now, where does functional programming come in, and why have I shared all of this to explain why I am having problems introducing it to my own programs?

Well, as it turns out, Functional programming was first used in the 1950s and 1960s, around the same time as Procedural programming and Object Oriented programming. While Structured, Procedural, and Object Oriented programming were all building directly off of each other, Functional programming was moving down an entirely new branch in the programming paradigm tree (I know. Earlier, I described them as a set of steps, now they're a tree.) that solves the same problems in a fundamentally different way.

I don't want to go too deeply into functional programming, but at its core is the fact that data structures are immutable. When you have immutable data—data that cannot be changed—it no longer matters who can see it or who can operate on it because it cannot be modified in an unpredictable way; it cannot be modified at all. So, instead of hiding data and shared logic (think, private methods and properties in Object Oriented programming), Functional programming can leave it all (generally) public without experiencing the same issues.

Here lies the issue with adding functional programming in the middle of a procedural or Object Oriented program. You have paradigms that attempt to solve the same issues in very different ways. It can be very jarring to a developer when they are forced to jump back-and-forth between paradigms. Worse, assuming the safety of functional programming without immutable data can lead to unpredictable results.

In a perfect world these paradigms would not clash, but in the real world they often do. Instead of climbing up the tree, now you're swinging from branch to branch. (First steps, then branches, now a whole tree. I know.)

So, what's my take? If you can be completely functional, be functional. Otherwise, use Object Oriented programming. In most pre-existing applications you will experience more trouble than it's worth by mixing Functional programming in with Object Oriented and Procedural programming.
