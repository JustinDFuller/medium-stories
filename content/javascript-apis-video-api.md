
# JavaScript APIs: Video Api



In this series I will be exploring the different JavaScript APIs that are available for use in a browser environment. Some will be well known, others may be completely new to you. Hopefully each section will present you with some new information and useful real-world tips! So, let’s get started.

**Not Just For YouTube**

If you haven’t used it before, you may not even know the Video API exists, or you may be under the impression it can only be used for creating a custom video player. However, if you just need to embed a video into your site, and don’t want to create your own fancy video player, the Video API still has something for you!

**Remembering User Settings**

All options available to the video HTML tag, such as autoplay, controls, loop, and src can be set with JavaScript instead of HTML. So, imagine that you have an automatically playing video, but you want to let your users opt out of that probably-annoying feature, you could do the following:

<iframe src="https://medium.com/media/669514a9fa7bb63cf499701cd2b89932" frameborder=0></iframe>

Now you’re only showing autoplay for those who are OK with that feature!

**Playing the correct size video based on connection**

This next option is a bit more specific, and a bit more complicated, but it has huge benefits when it comes to user experience. You’ll have to play around with it to find the right balance for you and your users, but this should provide you with a good starting point.

The Video API comes with a set of events that happen over the course of a video’s lifecycle. oncanplay, oncanplaythrough, onplay, onpause are all examples of good lifecycle events that can be used, but we’re not concerned about when things are going well, we want to know when the video isn’t loading properly.

So we are going to watch for onstalled, onwaiting, onerror so that we can make changes when these events happen.

When we get the onerror event, we will give the user an option to reload the video. When we have onstalled or onwaiting we are going to check to see if this has happened before, how many times it has happened, and if it’s been too many times we’re going to change the src to a smaller bitrate video.

Here is an example of reloading the video (or giving the option to do so) on an error:

<iframe src="https://medium.com/media/ecadf5d7a213d2e78971e9783dd87ede" frameborder=0></iframe>

Here is an example of changing the src when the video is loading too often:

<iframe src="https://medium.com/media/811885e251d00340e58d2299c2c1e76b" frameborder=0></iframe>

Finally, a good resource for the Video API is [http://www.w3schools.com/tags/ref_av_dom.asp](http://www.w3schools.com/tags/ref_av_dom.asp).

Stay tuned for more posts about JavaScript APIs!

Hi, I’m Justin Fuller. I’m so glad you read my post! I need to let you know that everything I’ve written here is my own opinion and is not intended to represent my employer in *any* way. All code samples are my own, and are completely unrelated to Bank Of America’s code.

I’d also love to hear from you, please feel free to connect with me on [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/), [Github](https://github.com/justindfuller), or [Medium](https://medium.com/@justindanielfuller). Thanks again for reading!
> [Hacker Noon](http://bit.ly/Hackernoon) is how hackers start their afternoons. We’re a part of the [@AMI](http://bit.ly/atAMIatAMI)family. We are now [accepting submissions](http://bit.ly/hackernoonsubmission) and happy to [discuss advertising &sponsorship](mailto:partners@amipublications.com) opportunities.
> To learn more, [read our about page](https://goo.gl/4ofytp), [like/message us on Facebook](http://bit.ly/HackernoonFB), or simply, [tweet/DM @HackerNoon.](https://goo.gl/k7XYbx)
> If you enjoyed this story, we recommend reading our [latest tech stories](http://bit.ly/hackernoonlatestt) and [trending tech stories](https://hackernoon.com/trending). Until next time, don’t take the realities of the world for granted!
