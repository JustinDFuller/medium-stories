
# Here are three upcoming changes to JavaScript that you’ll love



Let’s take a look at some useful upcoming features in JavaScript. You’ll see their syntax, links to keep up to date with their progress, and we’ll write a small test suite to show how to begin using these proposals today!

### How JavaScript Changes

![](https://cdn-images-1.medium.com/max/2000/0*_ttLRUUBsYYoQS5u.)

*Feel free to skip this part if you are already familiar with how the [Ecma TC39](http://www.ecma-international.org/memento/TC39.htm) committee decides on and processes changes to the JavaScript language.*

For the rest of us who are curious about how the JavaScript programming language evolves, I’d like to give a quick overview of the process.

JavaScript is an implementation of the language standard called [ECMAScript](https://en.wikipedia.org/wiki/ECMAScript) which was created to standardize all the [implementations ](https://en.wikipedia.org/wiki/Category:JavaScript_dialect_engines)of the language as it evolved in the early years of web browsers.

There have been eight [editions](https://en.wikipedia.org/wiki/ECMAScript#Versions) of the ECMAScript standard, with seven releases (the fourth edition was abandoned).

Each [JavaScript engine](https://en.wikipedia.org/wiki/JavaScript_engine#JavaScript_engines) begins implementing the changes specified after each release. [This chart](https://kangax.github.io/compat-table/es6/) will show that not every engine implements every feature, and some engines take longer than others to implement the features. While this may seem sub-optimal, I believe it is better than having no standard at all!

### Proposals

Each ECMAScript edition goes through a process of vetting proposals. If a proposal is deemed to be useful and backwards compatible, it will be included in the next edition.

Proposals have five stages that are outlined in [this document](https://tc39.github.io/process-document/). Every proposal starts out as a “strawman” or [stage 0](https://github.com/tc39/proposals/blob/master/stage-0-proposals.md) where it is initially proposed. At this level, they have either not yet been presented to the technical committee, or they have not yet been rejected but still have not met the criteria to move on to the next stage.

None of the proposals that are shown below are in stage-0.

As a personal recommendation, I would like to encourage readers to avoid using stage-0 proposals in production applications until they are in a less-volatile stage. The purpose of this recommendation is simply to save you trouble in case the proposal is abandoned or drastically changed.

### Test Suite

Introductions to programming features often show code snippets out of context, or they use the features to build an application. Since I’m a huge fan of [TDD](https://en.wikipedia.org/wiki/Test-driven_development), I believe that a better way to learn what a feature does is to test it.

We will use what [Jim Newkirk coined as *learning tests](https://smile.amazon.com/Test-Driven-Development-Kent-Beck/dp/0321146530/ref=smi_www_rco2_go_smi_2609328962?_encoding=UTF8&%2AVersion%2A=1&%2Aentries%2A=0&ie=UTF8) *to accomplish this*. *The tests we write will make assertions not about our own code, but instead about the programming language itself. This same concept can be useful when learning a third party API or any other language feature.

### Transpilers

*Feel free to skip this section if you’re already familiar with transpilers.*

Some of you may be wondering how we’ll be using language features that haven’t been implemented yet!

JavaScript, being the ever evolving language that it is, comes with a handful of [transpilers](https://scotch.io/tutorials/javascript-transpilers-what-they-are-why-we-need-them) that compile JavaScript to JavaScript. On the surface that may not sound very helpful, but I assure you it is!

It allows us to write the latest version of JavaScript — including even stage-0 proposals — and still be able to execute it in today’s run time environments like web browsers and Node.js. [It does this by changing our code to be as if it were written for an older version of JavaScript](https://babeljs.io/repl/#?babili=false&browsers=&build=&builtIns=false&code_lz=GYVwdgxgLglg9mABAEziARgGwKYGUCGAnogBQDOUATgJSIDeAUIopdlCJUhZYgNSIAiADSC-ibgG4GAXwYNQkWAkQR8ABxhR8mGAC9spbrUbNW7TuKoBtAAwBdAHRQ4AVTVrslAML4y2ErT83A5kGNwwYADmJACM1FKy8uDQ8EjYAB4QmPgwALaGVMZMLGwcXFRiAOQAhJUJcr6EkIgKKcpQ2BQB9EwA9L3i2AYAFnAA7oiaiLlwyDDAMJ2IUMMGjc2tSkh9A-ggUIg4-ABuSysGnQBMNjEArCpwuWr4sFgGm6lkiNoI2AD8MiAA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&lineWrap=false&presets=es2015%2Creact%2Cstage-0&prettier=false&targets=&version=6.26.0&envVersion=).

One of the most popular JavaScript transpilers is [Babel](https://babeljs.io/). We’ll be using it in just a minute.

### Setup

If you want to follow along with the code then feel free. You’ll want to set up an npm project and install the required dependencies.

You’ll need to have [Node.js](https://nodejs.org/en/) and [NPM ](https://www.npmjs.com/)installed.

To do so, you can run the following command in a new directory:

    npm init -f && npm i ava@1.0.0-beta.3 @babel/preset-env@7.0.0-beta.42 @babel/preset-stage-0@7.0.0-beta.42 @babel/register@7.0.0-beta.42 @babel/polyfill@7.0.0-beta.42 @babel/plugin-transform-runtime@7.0.0-beta.42 @babel/runtime@7.0.0-beta.42 --save-dev

You will then want to add the following to your package.json file:

    "scripts": {
      "test": "ava"
    },
    "ava": {    
      "require": [      
        "@babel/register",
        "@babel/polyfill"   
      ]  
    }

Finally create a .babelrc file:

    {  
      "presets": [    
        ["@babel/preset-env", {      
          "targets": {        
            "node": "current"      
          }    
        }],    
        "@babel/preset-stage-0"  
      ],  
      "plugins": [    
        "@babel/plugin-transform-runtime"
      ]
    }

Now you’re ready to start writing some tests!

## 1. Optional Chaining

In JavaScript we are constantly working with Objects. Sometimes these Objects do not have the exact shape that we expect. Below you’ll find a contrived example of a data object — maybe it was retrieved from a database or API call.

    const data = {
      user: {
        address: {
          street: 'Pennsylvania Avenue',
        }, 
      },
    };

Oops, it looks like this user did not complete registration:

    const data = {
      user: {},
    };

Hypothetically, when I try to access the street on my app’s dashboard, I would get the following error:

    console.log(data.user.address.street); // Uncaught TypeError: Cannot read property 'street' of undefined

To avoid this, we currently must access the “street” property like this:

    const street = data && data.user && data.user.address && data.user.address.street;
    console.log(street); // undefined

In my opinion, this method is:

1. Ugly

1. Burdensome

1. Verbose

Here’s where optional chaining comes in. You can use it like this:

    console.log(data.user?.address?.street); // undefined

That’s much easier, right? Now that we see the usefulness of this feature, we can go ahead and take a deeper look.

So lets write a test!

<iframe src="https://medium.com/media/9b4efe65aae10c883638786b82ce65d5" frameborder=0></iframe>

Now we see that optional chaining maintains the previous functionality of dot notation. Next, let’s add a test for the unhappy path.

<iframe src="https://medium.com/media/10c48d1585de0ac8ec5b1d11767cd944" frameborder=0></iframe>

Here’s how optional chaining works for array property access:

<iframe src="https://medium.com/media/a4b4bde70cd5e0f0cf52efae40064e49" frameborder=0></iframe>

Sometimes we don’t know if a function is implemented inside an Object.

A common example of this is when you are using a web browser. Some older browsers may not have certain functions. Thankfully we can use optional chaining to detect if a function is implemented!

<iframe src="https://medium.com/media/8ebdb25091110ce9052cf14b1445df03" frameborder=0></iframe>

Expressions will not execute if the chain is not intact. Under the hood, the expressions is roughly transformed to this:

    value == null ? value[some expression here]: undefined;

So nothing after the optional chain operator ? will be executed if the value is undefined or null. We can see that rule in action in the following test:

<iframe src="https://medium.com/media/f842bba3d258202e686aaf7931740b3f" frameborder=0></iframe>

And there you have it! Optional chaining reduces the need for if statements, imported libraries like lodash, and the need for chaining with &&.

### A word of warning

You’ll hopefully notice that using this optional chain comes with some minimal level of overhead. Each level that you check with ? must be wrapped in some sort of conditional logic under the hood. This will incur a performance hit if it is over used.

I would suggest using this with some sort of Object validation when you receive or create the Object. This will limit the need for these checks and therefore limit the performance hit.

### Link

Here’s a [link](https://github.com/TC39/proposal-optional-chaining) to the proposal. I’ll also copy it at the bottom of this post so that you can see all the proposal links in one place!

## 2. Nullish coalescing
> Coalesce: to blend or come together

Here are a few common operations that we see in JavaScript:

1. Checking for undefined or null values

1. Defaulting Values

1. Ensuring the literal values 0, false, and ''are not defaulted.

You may have seen it done like this:

    value != null ? value : 'default value';

Or you may have seen it improperly done like this:

    value || 'default value'

The problem is that for the second implementation, our third operation condition is not met. The number zero, the boolean false, and an empty string are all considered false in this scenario. That’s why we must check for null and undefined explicitly.

    value != null

Which is the same as:

    value !== null && value !== undefined

This is where the new proposal, nullish coalescing, comes in. Now we can do:

    value ?? 'default value';

This protects us from accidentally defaulting those falsy values, but still catches null and undefined without a ternary and != null check.

Now that we see the syntax, we can write a simple test to validate how it works.

<iframe src="https://medium.com/media/d774d7ab83892e0b7e199ea77f1c0416" frameborder=0></iframe>

You can see in the tests that it uses default values for null, undefined, and void 0 (which evaluates to undefined). It does not default falsy values like 0, '', and false. Check it out on GitHub [here](https://github.com/tc39/proposal-nullish-coalescing).

## 3. Pipeline operator

In functional programming, we have a term “[composition](https://medium.com/javascript-scene/composing-software-an-introduction-27b72500d6ea),” which is the act of chaining together multiple function calls. Each function receives as its input the output of the previous function. Here’s an example of what we’re talking about in plain JavaScript:

    function doubleSay (str) {
      return str + ", " + str;
    }

    function capitalize (str) {
      return str[0].toUpperCase() + str.substring(1);
    }

    function exclaim (str) {
      return str + '!';
    }

    let result = exclaim(capitalize(doubleSay("hello")));
    result //=> "Hello, hello!"

This stringing together is so common that composition functions are present in most functional libraries like [lodash](https://lodash.com/docs/4.17.5#flow) and [ramda](http://ramdajs.com/docs/#compose).

With the new pipeline operator, you can skip the third party library and write the above like this:

    let result = "hello"
      |> doubleSay
      |> capitalize
      |> exclaim;
    
    result //=> "Hello, hello!"

The purpose is to make the chain more readable. It will also work nicely with partial application in the future, or for now it can be implemented like this:

    let result = 1
      |> (_ => Math.max(0, _));
    
    result //=> 1

    let result = -5
      |> (_ => Math.max(0, _));
    
    result //=> 0

Now that we see the syntax we can begin writing tests!

<iframe src="https://medium.com/media/2571d8d1cb56574470654f3f16c32fe7" frameborder=0></iframe>

One thing you may notice is that you must await the value once an async function is added to the pipeline. This is because the value has become a Promise. There are a few [proposed changes](https://github.com/tc39/proposal-pipeline-operator) to support |> await asyncFunction , but none have been implemented or decided on yet.

Alright, now that you’ve seen these proposals in action I hope you feel comfortable enough trying them out!

### The full code for the learning tests can be found [here](https://github.com/JustinDFuller/javascript-proposals-tests).

Here are all four proposal links (bonus!):
[**tc39/proposal-optional-chaining**
*Contribute to proposal-optional-chaining development by creating an account on GitHub.*github.com](https://github.com/TC39/proposal-optional-chaining)
[**tc39/proposal-nullish-coalescing**
*proposal-nullish-coalescing - Nullish coalescing proposal x ?? y*github.com](https://github.com/tc39/proposal-nullish-coalescing)
[**tc39/proposal-partial-application**
*proposal-partial-application - Proposal to add partial application to ECMAScript*github.com](https://github.com/tc39/proposal-partial-application)
[**tc39/proposal-pipeline-operator**
*proposal-pipeline-operator - A proposal for adding the simple-but-useful pipeline operator to JavaScript.*github.com](https://github.com/tc39/proposal-pipeline-operator)

Hi, I’m Justin Fuller. I’m so glad you read my post! I need to let you know that everything I’ve written here is my own opinion and is not intended to represent my employer in *any* way. All code samples are my own, and are completely unrelated to Bank Of America’s code.

I’d also love to hear from you, please feel free to connect with me on [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/), [Github](https://github.com/justindfuller), or [Medium](https://medium.com/@justindanielfuller). Thanks again for reading!