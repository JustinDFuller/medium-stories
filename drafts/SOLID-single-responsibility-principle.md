# SOLID — The Single Responsibility Principle

The SOLID series will be setting out to explore a set of principles, often attributed to statically typed–object oriented programming languages, and their
application to the JavaScript programming language. This series will use Typescript in order to visualize the types involved, although Typescript is not
necessary to apply these principles. 

First up in this series in the Single Responsibility Principle. This, being the first SOLID "S" principle, as well as the most well-known principle, is probably
the most misunderstood principle. At least, it was for me.

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

Having less overhead frees the class up for reuse. 

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

Before the class was split up, this polymorphism would have been cumbursome. We would be overriding one method while being forced to inherit from some other base class and we would have been forced to inject a dependency for the creation methods. Now, the polymorphism is easy to accomplish because the class is focused.


