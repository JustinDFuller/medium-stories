# A Pattern For Testing Strings

When writing tests, one often finds that a test is needed to validate that a result is some expected string value. 

String tests can be important because they ensure a program will exit with the correct message. Unfortunately, string tests are often brittle. 

A developer can easily become frustrated when a grammatical fix causes one or more tests to break. It is likely that a developer will
quickly discontinue any frustrating process. So, if one desires to encourage testing against the string outputs of a program, it should be 
extremely simple to do so.

## Wrong Way—Duplicate Strings

The wrong way to test a string output is to copy that string directly into the test. Maintaining both strings is frustrating because of 
the unecessary redundancy. When a change is made to the string in the source file, a change is also required in the test file.

Here's an example of the Wrong Way™.

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

```js
// User.test.js
import { Suite } from 'javascript-testing-library'
import { User } from './User'

export const suite = new Suite({
  name: 'User'
})

suite.addTest({
  name: 'An appropriate message is given when email address is empty',
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

The string is duplicated; the test forces a change with the implementation.

## Wrong Way—Strings File

Here's another solution that I have, unfortunately, encountered. 

A developer has realized that duplicating strings is a bad idea; a developer places all string values in a single, shared file. Each file that wants to use a string, in any way, is required to depend on and know about this file. A string is modified, it was depended on by many other files, now there are a number of broken tests.

The first problem with this approach is one of encapsulation. A component that cannot own its own strings is a component that cannot safely change any string. 

Another Wrong Way™ example. First, the string is added to the strings file.
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
  name: 'An appropriate message is given when email address is empty',
  test(t) {
    let error = new Error('Expected an error to be thrown')
    
    try {
      new User().validate()
    } catch (e) {
      error = e
    }
    
    t.equal({
      expected: USER_INVALID_EMAIL, // Look, no more duplication!
      actual: error.message
    })
  }
})
```

Now, in defense of this pattern, it should be observed that there is no more duplication. One will only have to change the string in a single file. 

Unfortunately, that place is not with the `User` class. This is may only amount to a trivial annoyance—due to modern text editors—but it still adds mental effort. The team must remind new developers that all strings are to be placed in the `strings.js` file, for example.

Interestingly, it could be considered that the above test does not actually ensure correct functionality. If so, this would be far more worrying than an issue of code organization could ever be.

In the following example, a slightly more generic error message is used in two places. This behavior is encouraged by the shared `strings.js` file.

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

Image a scenario where a new User Invitation feature is being added. Now a user can invite a friend by simply entering their email. An account will be opened and an invitation email is sent. 

A test may be written—hopefully—seeking to guarantee that an error is thrown when a user attempts to invite a friend without an email address. 

Can you see how this might go badly? Perhaps there is a bug where the new user is not created correctly. It throws the `EMAIL_REQUIRED` error message, and the unit test believes that all is well.

## Error Tests

To avoid error message confusion, there should ideally not be any assertions against the message, or, at least, there should not be only assertions against the message.

In this example the issue of shared strings is still not solved, but message confusion will be.
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
    let error = new Error()
    
    try {
      new UserInvitation().validate()
    } catch (e) {
      error = e
    }
    
    t.equal({
      expected: UserInvitationValidationError,
      actual: error.constructor // The constructor (type) of the error is being tested
    })
  }
})
```

This test will fail when a `UserValidationError` is thrown instead of a `UserInvitationValidationError`. The error messages can be the same, but the test will be more trustworthy.
