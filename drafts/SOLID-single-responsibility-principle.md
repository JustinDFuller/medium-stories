# SOLID JavaScript - The Single-Responsibility Principle

As I continue on the journey of learning programming, I recently spent a lot of time learning about Object Oriented Programming principles. Probably the most well known set of principles are the SOLID principles. These principles were popularized by [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_C._Martin) through books like [Clean  Code](https://amzn.to/31GmPO3) and [Clean Architecture](https://amzn.to/2Nur1g3). 

Most recently I have been reading his book, [Agile Software Development, Principles, Patterns, and Practices](https://amzn.to/31H3Lz6) which dives deeply into SOLID and Object Oriented Programming in C++ and Java. I highly recommend all three books and I'll be sharing what I learned in this series.

The [SOLID](https://en.wikipedia.org/wiki/SOLID) principles are intended to assist a developer in writing code that is easy to change. The idea is that software provides two values:

1. What the software can do today.
2. What the software can do tomorrow.

This can also be thought of as short-term and long-term value. The short-term value is the features the software can do today. The long term value is what the software can be made to do when the changes roll in.

Too often, we as developers, managers, product owners, etc., focus on the short-term value at the expense of the long-term. We incur technical debt and make our programs difficult to change. This is why you often see programs being re-written every few years. The software isn't soft, it's hard. It can't be changed easily. So we have to create a completely new version to be able to continue making the changes necessitated by the business.

## Why "easy to change"?

The distinction of this goal is important. Some people have different goals for software. They want it to be beautiful. They want it to be concise. They want it to be blazing fast. 

These are worthy goals, but who cares if your software is fast if it can't be made to do what the business needs tomorrow? No one will want to use your blazingly-fast-but-outdated-or-useless product.

Change is the goal. Your software should be soft. That's where SOLID comes in.

## Single Responsibility

The "S" in SOLID stands for the __Single-Responsibility Principle__ (SRP). This principle states that a given piece of code should have one and only one responsibility.

What is responsibility?

Responsibility can be many things, as will be shown throughout this post. It can be the data it operates on, the type of operation or operations, or even the number of operations.

Responsibility can also flow the other way. Not what is the code responsible for, but instead, who is responsible for the code? Does the code serve the User Interface and the team that designs it? Does the code support business rules and the business partners who supply them? Does the code support the database or devops team who provide the databases or other storage solutions?

To see in detail what this concept of responibility looks like, it would be best to look at the Single-Responsibility Principle in action. The next sections will demonstrate the SRP, starting at the lowest level, a single variable, then working up to more complex topics.



