
# JavaScript APIs: Battery



Yes, you heard me right, the Battery API! As the web expands, so does the number of devices we expect to use our programs. This API can be particularly useful for protecting our mobile users and their precious battery life! So before we look at the API lets think of some reasons why we might need such a thing:

* Decreasing processing and requests when the battery is low.

* Provide users with warnings. Especially useful if your users are working with time-sensitive data or actions, or if you know your app is a power-drainer.

* Serve a simpler, low power version of the site.

So let’s take a look at how you might use it.

<iframe src="https://medium.com/media/4063266cce3ebe91b936b9af6b2dc5d5" frameborder=0></iframe>

A few things to note in the above example:

* getBattery is not available in all browsers. Make sure you perform a check before attempting to use it!

* The return value of getBattery is a Promise which resolves with the BatteryManager object.

* When the onchange function is called, you must re-inspect the original BatteryManager object that was provided by the promise. It will be modified and contain the new battery information.

* When the onchange function is called it receives one argument, event, which helpfully contains event.type that tells us which battery event happened. This allows you to reuse a single event handler!

## Using The Data

Now that we see how to *get *the data, let’s take a look at how we can *use* it!

In this example we will watch for the battery to get low, and show an alert when it’s below a certain level.

<iframe src="https://medium.com/media/e3dbbaa69abefcf62588ac354286845b" frameborder=0></iframe>

The battery properties we are inspecting:

* dischargeTime is the amount of time until the battery is empty, in seconds. Its value will be infinity if the device is plugged in to a power source.

* level is a percentage. So 0.2 is 20%.

You can customize these to fit your needs. In a highly time-sensitive environment you may want to let them know earlier than 20% or 20 minutes.

A final note: Most devices will warn users when the battery is low, so you likely won’t need to add the example warning message to your application. Instead, you could limit your processes and display a message that processing is limited until the battery has been charged. Remember, this API is just another resource that allows you to solve problems for your users.

Feel free to check out some of my past posts: [Console API](https://hackernoon.com/javascript-apis-console-23ebce270419#.w6dps5pi1), [Video API](https://medium.com/@justindanielfuller/javascript-apis-video-api-db803f9fd1b7#.7vgvrfhrj).

Hi, I’m Justin Fuller. I’m so glad you read my post! I need to let you know that everything I’ve written here is my own opinion and is not intended to represent my employer in *any* way. All code samples are my own, and are completely unrelated to Bank Of America’s code.

I’d also love to hear from you, please feel free to connect with me on [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/), [Github](https://github.com/justindfuller), or [Medium](https://medium.com/@justindanielfuller). Thanks again for reading!
> [Hacker Noon](http://bit.ly/Hackernoon) is how hackers start their afternoons. We’re a part of the [@AMI](http://bit.ly/atAMIatAMI)family. We are now [accepting submissions](http://bit.ly/hackernoonsubmission) and happy to [discuss advertising &sponsorship](mailto:partners@amipublications.com) opportunities.
> To learn more, [read our about page](https://goo.gl/4ofytp), [like/message us on Facebook](http://bit.ly/HackernoonFB), or simply, [tweet/DM @HackerNoon.](https://goo.gl/k7XYbx)
> If you enjoyed this story, we recommend reading our [latest tech stories](http://bit.ly/hackernoonlatestt) and [trending tech stories](https://hackernoon.com/trending). Until next time, don’t take the realities of the world for granted!
