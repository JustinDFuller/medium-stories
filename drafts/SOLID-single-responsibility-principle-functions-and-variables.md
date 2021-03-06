# SOLID JavaScript - The Single-Responsibility Principle (Variables & Functions)

In this series we are exploring what the SOLID principles look like when they are applied to JavaScript. In the [previous post](INSERT URL HERE) I outlined what the Single-Responsibility Principle is and why one would care about it. In this post we will look at its practicle applications for JavaScript variables and functions. In a later post I will explore what this principle can do for objects, classes, and modules.

![SOLID JavaScript](https://raw.githubusercontent.com/JustinDFuller/blog-posts/master/media/SOLID-single-responsibility-principle/Logo.jpg)

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
