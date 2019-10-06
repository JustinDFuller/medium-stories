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

The other difference is that these books focus heavily on the logic without connecting them to a fully functioning application. I imagine this was an intentional decision, and we'll talk more about this later, but my __second goal__ is to show these concepts in context with fully funtioning applications.

Now, before I conclude this section, I believe that it would be dishonest of me to pretend, or even to omit, the fact that I have not been able to fully use these concepts and strategies in a production application. This is not to say that I don't practice them whenever I write code, but—unfortunately—I have only ever worked on applications that are, perhaps accidentally, not Object Oriented. This leads me to my __third and final goal__, which is to formulate the words needed to explain and advocate for these strategies in the context of real-world day-to-day development. I have found that, after learning about these principles and strategies, I can use them but I struggle to convince others to use them. This may be my most important goal, because it has the most direct impact for my growth. Hopefully it can provide others with the same benefits as well.

This is why I find myself writing about Object Oriented Programming principles and strategies. To translate it to JavaScript, to give them context in fully-functioning applications, and to provide the words necessary to explain and advocate them in the real world.
