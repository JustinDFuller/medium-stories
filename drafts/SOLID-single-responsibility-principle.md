# SOLID — The Single Responsibility Principle

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

## Variables

The Single Responsibility principle (SRP) can be applied at any level of programming. From variables, to functions, all the way up to macro-level software components
and services, the single responsibility principle can serve to focus the role of a software system. 

Starting at the most basic level, the SRP says that a variable should only be used in one way. A variable can change in value, but it should never change in purpose.

In the following example, I will demonstrate a variable that does not follow the Single Responsibility Principle.

```typescript
function getUser(id: string | number | null): User | Users {
  if (id === 'create') {
    return new EmptyUser()
  }

  if (id === null) {
    return new Users()
  }

  return User.fromId(id)
}
```

The code above is adapted from real code that I have encountered in a production application. A single `id` property can be a string "create", so a new user should be created; 
null, implying that a list hould be shown; and a numeric id, implying that a user should be retrieved by its ID. 

This variable violates the SRP, neither because it contains different values, nor because it indicates different logical paths. This variables violates SRP because it serves
a different purpose in different scenarios. In one purpose it is an identifier, in another case it indicates state or action type. 

So, here's how we could improve this variable to only have one responsibility.

```typescript
function getData(action: string, id?: number): User | Users {
  switch(action) {
    case 'edit':
      return User.from(id);
    case 'list':
      return new Users();
    case 'create':
      return new EmptyUser();
    default:
      throw new InvalidUserActionError()
  }
}
```

Now, `id` is only ever a numeric identifier of a user and `action` is only ever a string action type. There is an extra variable, yes, but both adhere to the SRP, making
the code easier to understand.

There are still many problems with the above code, we'll get to that later, but for now we can rest easier knowing that each variable has a single responsibility.

__Metrics for detecting SRP violations in variables:__
* A variable contains different types (number, string, object) at different times.
* A variable has a different meaning depending on when it is being used.

## Functions

If variables are the most basic level that the SRP can be applied to, then the next logical level is a function or a method on an object.

For variables, responsibility equates to the symbolic meaning that a variable holds. What does responsibility mean for a function? Determining
what function responsibility means is more difficult because, by their nature, functions can do more than variables. A function can represent
a value by returning it, or it can invoke change.

One metric of a function's responsibility can found in the function name. Let's look at the User example again.

```typescript
function getData(action: string, id?: number): User | Users {
```

This function might be more accurately named `getUserOrUsers`. When a term like "and" or "or" is found in a function name, you have a clear
indicator that your function has more than one responsibility.

The `getData` function should probably be split into two functions.

```typescript
function getUserByAction(action: string, id?: number): User {
  // logic here
}

function getUsers(): Users {
  // logic here
}
```

One function is responsible for getting an individual user based on the action type. The other function is responsible for getting an array
of User objects. Each function has only a single responsibility.

At this point we should discuss the fact that this change isn't quite so easy. Splitting this function would probably only move the dual-responsibilities
to the function or functions that previously called `getData`. In my scenario (remember, this was adapted from a real scenario) I had to rethink
a large section of code before each logical path truly had a single responsibility.

__Metrics for detecting SRP violations in functions:__
* A function has multiple return types.
* A function has (or could have) "and" or "or" in the name.

## Classes, Objects, and Modules

Moving up another unit of complexity brings us to classes, objects, and modules. At this level the Single Responsibility Principle can become more
nuanced and harder to detect. Thankfully, there are still plenty of clear indicators that the SRP is being violated.

First, you may wonder why I have grouped these three concepts together. It's because they are used to group concepts together—at least, when
they are used correctly.

A class, object, or module should contain a group of methods and properties that belong to a specific set of functionality. The SRP states that
there should be a single, cohesive purpose to everything in the class, but what does that mean? Clearly, not every method and property can have the exact same purpose—that would be pointless. 

To establish what "single responsibility" means, another set of principles can be borrowed from. These principles are aimed at packages but the ideas can also be usefully applied to a single Class within a package. 

### Common-Reuse Principle

The first package principle is the __Common-Reuse Principle__. This principle states that things that are used together should be grouped together and, conversely, things that are not used together should not be grouped together. Notice that I am using the ambigious, "things". I use this vague word because I intent to apply this principle to classes, modules, and packages.

What does this look like in class form?

```typescript
class User {
  // User Creation Methods
  private validate() {},
  create() {},
  // security methods
  hasEntitlement(entitlement) {},
  // User Display Methods
  private getLastLogin() {},
  getWelcomeMessage() {},
}
```

This trivial example shows a `User` class that implements three sets of functionality. 

The first set of functionality is for the registration process. It validates and creates a new user. 

In the second scenario, the class displays a welcome message. 

These two sets of methods and related properties are never used at the same time. Therefor, the Common-Reuse Principle states that this class may benefit from becoming two classes.

So, what benefits could come from splitting up a class or module? 

At a basic level, the class should become easier to understand. By doing less, there will be less code for a developer to wade through as they seek to understand where to make a change.

Next, the class or module becomes easier to use. 

For example, suppose the `User` class needs to have a data store injected for the `create()` method. On the other hand, the display methods do not need a store injected. The display methods only need a simple `User` data object. When the above code is only one class you must inject an unecessary dependency. Once it's split up there is less overhead.

By splitting up the class, giving it less responsibility, it is now open for extension. The following examples shows how an extendable `EntitlementChecker` can now be implemented.

```typescript
interface EntitlementChecker {
  hasEntitlement(entitlement: string): boolean
}

class UserEntitlementChecker implements EntitlementChecker {
  hasEntitlements(entitlement: string): boolean {
    return this.entitlements.includes(entitlement)
  }
}

class SuperuserEntitlementChecker implements EntitlementChecker {
  hasEntitlements(): boolean {
    return true
  }
}

class BanneduserEntitlementChecker implements EntitlementChecker {
  hasEntitlements(): boolean {
    return false
  }
}
```

_Note:_ Examples like this always fall short, since the example has to be small to create a reasonably sized post, the example is almost always to small to really be worth the effort. You'll just have to imagine the example as part of a larger application.

Here's what the class diagram looked like before it was split up.

![User UML](https://raw.githubusercontent.com/JustinDFuller/blog-posts/master/media/SOLID-single-responsibility-principle/User.png)

This polymorphism would have been cumbursome before the split. Each implementation would be overriding one method while being forced to inherit from the `User` base class. It may have been forced to unecessarily inject a dependency for the creation methods. 

![User with EntitlementChecker](https://raw.githubusercontent.com/JustinDFuller/blog-posts/master/media/SOLID-single-responsibility-principle/EntitlementCheckers.png)

The diagram now shows that each type of entitlement checker gets a separate implementation. This polymorphism is easy to accomplish because the class is focused. Adding a new way of checking entitlements will be easy and changes to each entitlement implementation will be isolated from the other checkers. This makes future changes easier and safer.

How could this structure have been implemented before the class was split up? What if A `SuperUser` could have multiple display implementations? Polymorphism would be very difficult to accomplish. Instead the class would likely be forced to use if-else statements to determine which logic to use.

### Common-Closure Principle

The next package principle is the __Common-Closure Principle__ (CCP). This principle states that a package should only have one reason to change. Code that changes together should stay together.

Applying this principle to classes, objects, and modules suggests that properties and methods should be grouped based on how and why they change.

Assume the `User` class has been split up into multiple, focused classes. Now there is a `UserCreator` class that only implements methods related to the creation of a new user.

```typescript
class UserCreator {
  private validateEmail(email: string): boolean {}
  
  private encryptPassword(password: string): Promise<string> {}
  
  private insertIntoDatabase(options): Promise<void> {}
  
  private getWelcomeMessage(error: Error): string {}
  
  private getRegistrationFailureMessage(): string {}
  
  create(options): string {
    try {
      this.validateEmail(options.email)
      const encryptedPassword = await this.encryptPassword(options.password)
      await this.insertIntoDatabase({
        name: options.name,
        email: options.email,
        password: encryptedPassword
      })
      return this.getWelcomeMessage()
    } catch (e) {
      return this.getRegistrationFailureMessage(e)
    }
  }
}
```

Let's examine each method to see what could cause it to change.

* First, the `getWelcomeMessage` and `getRegistrationFailureMessage` methods are likely the responsibility of a design team. 
* Next, the `insertIntoDatabase` method could change if the database model is changed or if we switch to a different database. 
* `encryptPassword` might change if a better encryption algorithm becomes available. 
* Finally, `validateEmail` could change due to a bug in email validation, but more interestingly it could change because of a business decision.

What kind of business decision would affect email validation? Imagine that at first you have loose validation on emails. If it follows the proper shape then it will be valid. The database will enforce unique emails, so `validateEmail` does not need to worry about that. Now, say the business is having problems with spam accounts. They discover that many accounts can be created with "email@gmail.com" by doing things like "email+1@gmail.com". Only one email is actually used, because gmail ignores the "+1" bit. Now, the business asks for stricter email validation. Emails will be stripped down so that any ignored characters can't be used to make a new account.

Not only are we changing how emails are validated, the business has also decided that users should be able to log in with their phone number or with a third party service. With our current implementation we are going to have a huge if/else chain or case block to determine what validation to use.

Applying the Common-Closure Principle (CCP) to a class states that it should only have one reason to change. I have just listed four reasons, and just as many responsible teams, for this class to change. Could it be possible that it violates the CCP? If so, how can we fix it?

![User Creator Diagram](https://raw.githubusercontent.com/JustinDFuller/blog-posts/master/media/SOLID-single-responsibility-principle/User%20Creator.png)

This diagram shows a potential way to isolate the different reasons for change. Now the `UserCreator` will use a `UserValidator`, `UserStore`, and `StringEncryptor` implementation. This means that `UserCreator` is now focused on the domain logic, which is "First we validate the user, then we encrypt the password, then we save it in the database, finally we return the results, which include if an error occured." The _how_ of the validation, encryption, and saving are left as mere implementation details. The high level flow is the only thing that `UserCreator` cares about.

What have we done here? We've made a trade-off. The trade is simplicity (all the code lived in a single class) for safety and malleability.
