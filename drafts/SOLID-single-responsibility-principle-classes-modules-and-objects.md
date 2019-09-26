# SOLID JavaScript - The Single-Responsibility Principle (Objects, Classes, & Modules)

In this series we are exploring what the SOLID principles look like when they are applied to JavaScript. In a [previous post](INSERT URL HERE) I outlined what the Single-Responsibility Principle (SRP) is and why one would care about it. The last post began to explore its practicle applications for JavaScript variables and functions. Here we will apply the principle to Objects, Classes, and Modules. Three ways to group code by their logical purpose. 

![SOLID JavaScript](https://raw.githubusercontent.com/JustinDFuller/blog-posts/master/media/SOLID-single-responsibility-principle/Logo.jpg)

As we move up in orders of complexity, first we have variables, then we have functions, and higher up we have groupings such as a class. As the complexity of the grouping increases, so does the complexity of the Single-Responsibility Principle. When you group functionality together, it becomes harder to ensure that it all serves the same purpose. It also becomes more nuanced and difficult to detect. Thankfully, there are still plenty of clear indicators that the SRP is being violated.

## Classes, Objects, and Modules

First, you may wonder why I have grouped these three concepts—classes, objects, and modules—together. It's because they are used to group concepts together—at least, when they are used correctly.

A class, object, or module should contain a group of methods and properties that belong to a specific set of functionality. The SRP states that there should be a single, cohesive purpose to everything in the class, but what does that mean? Clearly, not every method and property can have the exact same purpose. That would be pointless. 

To establish what "single responsibility" means, another set of principles can be borrowed from. These principles are aimed at packages but the ideas can also be usefully applied to a single Class, object, or module within a package. 

### Common-Reuse Principle

The first package principle is the __Common-Reuse Principle__. This principle states that things that are used together should be grouped together and, conversely, things that are not used together should not be grouped together.

Here's a class.

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

What does it do?

The first set of functionality is for user registration. It validates and creates a new user. The second set displays a welcome message. 

These functions are never used at the same time. Therefor, the Common-Reuse Principle states that this class may benefit from being split.

What benefits come from dividing the class? 

At the most basic level, the class should become easier to understand. By doing less, the class will have less code to understand as I seek to find where to make a change.

Next, the class or module becomes easier to extend. This will be very important when we begin to apply the next principle, the [Open-Closed Principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle), which states that (paraphrasing) software should be changed through addition, rather than modification.

To understand this, suppose this `User` has multiple types of entitlements, multiple types of welcome messages, and multiple ways to create the user. Worse, the multiple types are not compatible. Imagine that there are 3 types of users for entitlements: a regular user, a super (admin) user, and a banned user. There are also multiple welcome message types: a normal user, a user who has been gone a long time, and a premium user.

This would be very difficult to implement with polymorphism. You could create an additional `SuperUser` and `BannedUser` class, but what if the SuperUser is logged out a long time? What if a `BannedUser` is a `PremiumUser`? You will probably be forced to resort to an `if/else` chain to handle this logic. Polymorphism and extension won't be options.

How can this be fixed? Here's one idea.

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

Here's what the class diagram looked like before it was split up.

![User UML](https://raw.githubusercontent.com/JustinDFuller/blog-posts/master/media/SOLID-single-responsibility-principle/User.png)

Since all the logic lived in the single `User` class it would have been close to impossible to close this class to validation changes. We would have to modify and re-test the whole class whenever a change occurred.

![User with EntitlementChecker](https://raw.githubusercontent.com/JustinDFuller/blog-posts/master/media/SOLID-single-responsibility-principle/EntitlementCheckers.png)

The diagram now shows that each type of entitlement checker gets a separate implementation. This polymorphism is easy to accomplish because the `EntitlementChecker` interface, and related implementations, is focused. Adding a new way of checking entitlements will be easy and changes to each entitlement implementation will be isolated. Change is now easy and safe.

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

You may have noticed that all the dependencies are pointing towards abstract interfaces. Later in this series we will discuss the [Dependency Inversion Principle (DIP)](https://en.wikipedia.org/wiki/Dependency_inversion_principle) which tells us to depend on abstract interfaces, rather than concrete implementations. The diagram shows that we used the DIP to allow us to adhere to the Single-Responsibility Principle in the `UserCreator` class.

Here's how the `UserCreator` class looks in code.

```typescript
import { UserCreationResult, UserValidator, UserStore } from './'
import { StringEncryptor } from '../encryption'

class UserCreator {
  constructor(store: UserStore, validator: UserValidator, encryptor: StringEncryptor) {
    this.store = store
    this.validator = validator
    this.encryptor = encryptor
  }
  
  create(options): UserCreationResult {
    try {
      this.validator.validate(options)
      const encryptedPassword = await this.encryptor.encrypt(options.password)
      await this.store.write({
        name: options.name,
        email: options.email,
        password: encryptedPassword
      })
      return { success: true }
    } catch (e) {
      return { success: false, errorCode: /* logic to create error code */ }
    }
  }
}
```

What has been accomplished by all this work? `UserCreator` is no longer susceptible to changes of validation, encryption, data storage, or UI welcome messages. In fact, `UserCreator` will only change if the act of how users are created, the order and processes involved, change fundamentally. In that case, the `UserCreator` class is exactly what we would expect to change.

Conversely, user validation, user storage, password encryption, and UI welcome messages are less likely to be affected by each other or by a change in the User Creation process.

## Is it worth it?

This is the question I always ask when I am considering any abstraction. I know that I must implement all the same features, but I don't have to accomplish it in this way.
