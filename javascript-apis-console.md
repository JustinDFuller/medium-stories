
# JavaScript APIs: Console



The console API could be the most well known JavaScript API. Pretty much every developer who was written any substantial amount of code has had to console.log *something. *If you’ve written any kind of libraries or services, you’ve probably used console.warn or console.error to notify the user that they’ve done something incorrectly. But there’s far more that the console can do for us, so today we’re going to see how to leverage that!

## **Assert**

First up we have: console.assert(*assertion*) which allows us to run an *assertion *in the console.
> An assertion is a boolean expression at a specific point in a program which will be true unless there is a bug in the program. —[ http://wiki.c2.com/?WhatAreAssertions](http://wiki.c2.com/?WhatAreAssertions)

If the assertion is true, the console does not output anything, if the assertion is false it will output that an assertion has failed, along with any message or data you give it.

<iframe src="https://medium.com/media/6e51e02cafebe7cbd5f2ecdff6bfff6b" frameborder=0></iframe>

*Note: *In Node.js falsy assertions will throw a AssertionError and stop the execution of code. So it is not recommended for use in production environments.

## **Count**

Sometimes it could be important to know how many times a given line was called. Maybe you’re not ready to write your unit test yet that asserts it was only called once or twice; or maybe you’re just debugging and trying to track down something that is happening more times than expected. This is where we can count on console.count.

This one is simple, add console.count(*message*) and run your code. You’ll see a message and a number pop up every time that line runs.

<iframe src="https://medium.com/media/bdb768683402830f8ea9ad9232627cbf" frameborder=0></iframe>

An important thing to note in this example is that the count is set to 1 for each output. That’s because the message changed. No user was called more than once, so count never goes up! When you call user 4 again at the end, you see count finally go to 2.

## **Trace**

Finally, we’ve gotten to my favorite console method! Here we have: console.trace which will spit out a [stack trace](https://developer.mozilla.org/en-US/docs/Web/API/console#Stack_traces). It starts at the method that invoked console.trace and moves all the way back to the initial call.

This method is extremely useful when you know a certain method is being called, but you don’t know how.

But I can hear you saying right now, “Just put a debugger in the code and you get this, plus more!” To that, all I can say is, “yep”. Usually you can just use a debugger, but sometimes you don’t want the code to stop (like if timing is involved, or repetition + timing), or if for some reason you are needing to debug minified code.

Here’s a small example:

<iframe src="https://medium.com/media/680c17b1ee8981f81fc1d78caaff3a97" frameborder=0></iframe>

## **Formatting**

You don’t need ES6 to format a console string. They come with built in formatters.

%o and %O will format an Object.

%d and %i will format numbers.

%s will format a string.

%f will format a floating point number.

You can use them like this:

<iframe src="https://medium.com/media/504d4a6e7f5981bbc0573b2d30c5cd32" frameborder=0></iframe>

A nice part of this method is the object is still expandable in browsers that support pretty-printing/expanding/collapsing of objects in the console.

## A Final Word — Some “Gotcha’s!”

If you use Node.js, be very wary of the console. It prints to stdout synchronously, which could harm performance.

However, in some browser environments, some console statements are asynchronous, so using console.log will take a snapshot of any variables at the time it outputs, rather than when it is actually first invoked. This means you may not be seeing what you expect. In this case you can use console.dir to make sure you are seeing what you expect.

For that reason and others I’d also caution against using console statements too often in a web browser environment. Typically you can use a debugger statement or breakpoints in the browser for anything you want to do with a console statement. And with a debugger or breakpoint you are able to investigate your variables without having to worry about async problems.

If you missed the first installment you can find it [here](https://medium.com/@justindanielfuller/javascript-apis-video-api-db803f9fd1b7#.u5nd8sjwq). Please stay tuned as we dive deeper into some less known JavaScript APIs. We’re starting off easy but we’ll be diving deep soon!

Hi, I’m Justin Fuller. I’m so glad you read my post! I need to let you know that everything I’ve written here is my own opinion and is not intended to represent my employer in *any* way. All code samples are my own, and are completely unrelated to Bank Of America’s code.

I’d also love to hear from you, please feel free to connect with me on [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/), [Github](https://github.com/justindfuller), or [Medium](https://medium.com/@justindanielfuller). Thanks again for reading!
> [Hacker Noon](http://bit.ly/Hackernoon) is how hackers start their afternoons. We’re a part of the [@AMI](http://bit.ly/atAMIatAMI)family. We are now [accepting submissions](http://bit.ly/hackernoonsubmission) and happy to [discuss advertising &sponsorship](mailto:partners@amipublications.com) opportunities.
> To learn more, [read our about page](https://goo.gl/4ofytp), [like/message us on Facebook](http://bit.ly/HackernoonFB), or simply, [tweet/DM @HackerNoon.](https://goo.gl/k7XYbx)
> If you enjoyed this story, we recommend reading our [latest tech stories](http://bit.ly/hackernoonlatestt) and [trending tech stories](https://hackernoon.com/trending). Until next time, don’t take the realities of the world for granted!
