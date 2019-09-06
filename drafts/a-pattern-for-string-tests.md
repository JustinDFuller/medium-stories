# A Pattern For Testing Strings

In my most recent project, I set out to create a [Javascript testing library](https://github.com/JustinDFuller/javascript-testing-library) that solves the common issues that I face while writing unit tests.

I quickly realized that the unit tests for this library were presented with a challenge that I've seen in many of the production applications that I've built. That is, testing string outputs. 

The testing library exits with an error message in many scenarios, like when a suite is defined without any tests, or what is—in my opinion—the best part of the library, when a test attempts to access an expensive IO mechanism, like the file system. 

The problem is, what is the best way to test these strings? I need to be able to change the strings and I don't want to have to update my tests every time. 

If you'll allow me, I'll walk you through a few of the string-testing methods that I've encountered and I will discuss why some are better than others.

## Duplicate Strings

The wrongest way I've found to test a string output is to copy that string directly into the test. Maintaining both strings is frustrating because of the redundancy. When a change is made to the string in the source file, a change is also required in the test file.

Here's an example of this Wrong Way™.

First, the implementation.

```js
// User.js
class User {
  validate(options) {
    if (!options.email) {
      throw new Error('An email address is required when creating a new user.')
    }
  }
}
```

Then, the tests.

```js
// User.test.js
import { Suite } from 'javascript-testing-library' // Here's that testing library I mentioned earlie
import { User } from './User'

export const suite = new Suite({
  name: 'User'
})

suite.addTest({
  name: 'An appropriate error message is thrown when the email address is empty',
  test(t) {
    let error = new Error('Expected an error to be thrown')
    
    try {
      new User().validate()
    } catch (e) {
      error = e
    }
    
    t.equal({
      expected: 'An email address is required when creating a new user.', // Duplicate string!
      actual: error.message
    })
  }
})
```

The string is duplicated; the test is forced to change along with the implementation.

## Partial Duplication

Sometimes I see string duplication addressed by matching only part of the string. I mostly see this when there is a dynamic section of the string.

Partial duplication looks like this.


```js
t.equal({
  expected: error.message.includes('email address is required'), // Partial duplication
  actual: true
})
```

This is better as I can now change a large portion of the string without breaking the test. Plus, the core of the message is still verified. Still, I would like it better if I was free to change the entire string whenever I need to. I want the test to verify that the correct error was thrown, not the content of the error message, so I don't think this fully satisfies my needs. More on this later.

## Strings File

Here's another solution I run into pretty frequently. To me, the frustrations of this solution are more insidious than the others I've considered so far.

This is how it works:

A developer has realized that duplicating strings is a bad idea; a developer places all string values in a single, shared file (look—no more duplication). Each file that wants to use a string, in any way, is required to depend on and know about this file. Later, a string is modified, it was depended on by many other files, now the application is sending out messages that are not correct.

The first problem with this approach is one of encapsulation. A component that cannot own its strings is a component that cannot safely change any string. 

An example. First, the string is added to the strings file.

```js
// strings.js

// many strings above this line

// somewhere in the middle
export const USER_INVALID_EMAIL = 'An email address is required when creating a new user.'

// countless other strings as well
```

Next, the string is imported from the strings file.
```js
// User.js
import { USER_INVALID_EMAIL } from './strings'

class User {
  validate(options) {
    if (!options.email) {
      throw new Error(USER_INVALID_EMAIL)
    }
  }
}
```

Finally, the string is tested against the strings file.

```js
// User.test.js
import { Suite } from 'javascript-testing-library'

import { USER_INVALID_EMAIL } from './strings'
import { User } from './User'

export const suite = new Suite({
  name: 'User'
})

suite.addTest({
  name: 'An appropriate message is given when the email address is empty',
  test(t) {
    let error = new Error('Expected an error to be thrown')
    
    try {
      new User().validate()
    } catch (e) {
      error = e
    }
    
    t.equal({
      expected: USER_INVALID_EMAIL, // Look, no more duplication
      actual: error.message
    })
  }
})
```

Now, in defense of this pattern, it should be observed that there is no more duplication. One will only have to change the string in a single file. 

Unfortunately, that place is not with the `User` class. This may only amount to a trivial annoyance—due to modern text editors—but it still adds mental effort. The team must remind new developers that all strings are to be placed in the `strings.js` file, for example.

Those aren't great arguments against the string file, but do you remember how I mentioned encapsulation? That string isn't owned by `User`, so any module can use this string. A change to this string could affect many parts of the application and, worse, it may be hard to tell what will be affected.

Interestingly, it could be considered that the above test does not ensure correct functionality. If so, this would be far more worrying than an issue of code organization could ever be. Code organization serves functionality, so, if the functionality is broken, the organization has not served its purpose.

In the following example, a slightly more generic error message is used in two places. This behavior is encouraged by the shared `strings.js` file because, naturally, rather than creating a new string, a developer will look for an existing string. Sharing strings could lead to something like the next example.

```js
// User.js
import { EMAIL_REQUIRED } from './strings'

class User {
  validate(options) {
    if (!options.email) {
      throw new Error(EMAIL_REQUIRED)
    }
  }
}

// UserInvitation.js
import { EMAIL_REQUIRED } from './strings'

class UserInvitation {
  send(options) {
    if (!options.email) {
      throw new Error(EMAIL_REQUIRED)
    }
  }
}
```

Imagine a scenario where a new User Invitation feature is being added. Now, a user can invite a friend by simply entering their email. An account will be opened and an invitation email is sent. 

Ideally, a test will be written seeking to verify that an error is thrown when a user attempts to invite a friend without an email address. 

Can you see how this might go badly? Perhaps there is a bug where the new user is not created correctly. It throws the `EMAIL_REQUIRED` error message, and the unit test believes that all is well. The actual failure was in user creation, yet the unit test believes the error occurred in the invitation.

## Error Tests

To avoid error message confusion, there should ideally not be any assertions against the message, or, at least, there should not be only assertions against the message.

In this example, the issue of shared strings is still not solved, but message confusion will be.

```js
// UserInvitation.js
import { EMAIL_REQUIRED } from './strings'

export class UserInvitationValidationError extends Error {
  message = EMAIL_REQUIRED
  name = 'UserInvitationValidationError'
}

export class UserInvitation {
  send(options) {
    if (!options.email) {
      throw new Error(EMAIL_REQUIRED)
    }
  }
}
```

Now a test can be written that checks for the type of error, rather than the message.

```js
// User.test.js
import { Suite } from 'javascript-testing-library'

import { UserInvitation, UserInvitationValidationError } from './UserInvitation'

export const suite = new Suite({
  name: 'UserInvitation'
})

suite.addTest({
  name: 'An error is thrown when email address is empty',
  test(t) {
    t.throws({
        expected: UserInvitationValidationError,
        actual() {
          new UserInvitation().validate()
        }
    })
  }
})
```

[javascript-testing-library](https://github.com/JustinDFuller/javascript-testing-library) provides a `t.throws` assertion that performs a `actual instanceof expected` check.

This test will fail when a `UserValidationError` is thrown instead of a `UserInvitationValidationError`. The error messages can still be the same and the test will be more trustworthy.

## Back To Strings

Now that we've established that error tests shouldn't be string tests, let's return to valid string-test scenarios.

To be a valid string-test scenario we'll need to do an assertion against something that isn't an error. For this scenario, I'll use another `User` example. This time, we'll look at the user type.

In this example, there are three types of users. A guest, normal user, and premium user. Each of these users should receive a welcome message on the screen. For example, a guest will see `Welcome, guest.`, and a premium user will see `Welcome, premium user.`. 

At this point, we don't want to have to care if this welcome message is hardcoded or manage by a CMS. What we care about is that each type of user is assigned the appropriate greeting.

In the code, that might look something like this:

```js
class User {
  greeting = 'Welcome back.'
  
  getGreeting() {
    return this.greeting
  }
}

class Guest extends User {
  greeting = 'Welcome, guest.'
}

class PremiumUser extends User {
  greeting = 'Welcome back, premium user.'
}
```

Now, the question is, what is the best way to test these strings? We want to leave our options open for CMS management, and we know the language of the greetings might change pretty often, so the tests will need to validate that the correct output is given, without worrying too much about what's in the strings.

Here's where I've landed.

```js
import { shallow } from 'enzyme'
import { Suite } from 'javascript-testing-library'

import { Welcome } from './Dashboard'
import { User, Guest, PremiumUser } from './User'

export const suite = new Suite({
  name: '<WelcomeComponent />'
})

suite.addTest({
  name: 'shows a greeting for a normal user',
  test(t) {
    const welcome = shallow(<Welcome user={new User()} />)
    
    t.equal({
      expected: welcome.text().includes(User.greeting),
      actual: true
    })
  }
})

suite.addTest({
  name: 'shows a greeting for a guest user',
  test(t) {
    const welcome = shallow(<Welcome user={new Guest()} />)
    
    t.equal({
      expected: welcome.text().includes(Guest.greeting),
      actual: true
    })
  }
})

suite.addTest({
  name: 'shows a greeting for a premium user',
  test(t) {
    const welcome = shallow(<Welcome user={new PremiumUser()} />)
    
    t.equal({
      expected: welcome.text().includes(PremiumUser.greeting),
      actual: true
    })
  }
})
```

These tests render a `<Welcome />` component for the different user types. They expect that the text comes from the greeting property. The benefit of testing this way is that the greeting can be changed without affecting the tests. If the wrong greeting is given, that will be caught. If the greeting is missing, that will be caught. So, the test still verifies functionality, with the added benefit of not caring about the actual text value.
