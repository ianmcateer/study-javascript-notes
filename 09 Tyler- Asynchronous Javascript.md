### Async JavaScript: From Callbacks, to Promises, to Async/Await

One of my favorite sites is [BerkshireHathaway.com](http://www.berkshirehathaway.com/) - it’s simple, effective, and has been doing its job well since it launched in 1997\. Even more remarkable, over the last 20 years, there’s a good chance this site has never had a bug. Why? Because it’s all static. It’s been pretty much the same since it launched over 20 years ago. Turns out sites are pretty simple to build if you have all of your data up front. Unfortunately, most sites now days don’t. To compensate for this, we’ve invented “patterns” for handling fetching external data for our apps. Like most things, these patterns each have tradeoffs that have changed over time. In this post we’ll break down the pros and cons of three of the most common patterns, `Callbacks`, `Promises`, and `Async/Await` and talk about their significance and progression from a historical context.

Let’s start with the OG of these data fetching patterns, Callbacks

### Callbacks

When I was first learning to program, it helped me to think about functions as machines. These machines can do anything you want them to. They can even accept input and return a value. Each machine has a button on it that you can press when you want the machine to run, ().

```jsx
function add (x, y) {
  return x + y
}

add(2,3) // 5 - Press the button, run the machine.
```

Whether I press the button, you press the button, or someone else presses the button doesn’t matter. Whenever the button is pressed, like it or not, the machine is going to run.

```jsx
function add (x, y) {
  return x + y
}

const me = add
const you = add
const someoneElse = add

me(2,3) // 5 - Press the button, run the machine.
you(2,3) // 5 - Press the button, run the machine.
someoneElse(2,3) // 5 - Press the button, run the machine.
```

In the code above we assign the `add` function to three different variables, `me`, `you`, and `someoneElse`. It’s important to note that the original `add` and each of the variables we created are pointing to the same spot in memory. They’re literally the exact same thing under different names. So when we invoke `me`, `you`, or `someoneElse`, it’s as if we’re invoking `add`.

Now what if we take our `add` machine and pass it to another machine? Remember, it doesn’t matter who presses the () button, if it’s pressed, it’s going to run.

```jsx
function add (x, y) {
  return x + y
}

function addFive (x, addReference) {
  return addReference(x, 5) // 15 - Press the button, run the machine.
}

addFive(10, add) // 15
```

Your brain might have got a little weird on this one, nothing new is going on here though. Instead of “pressing the button” on `add`, we pass `add` as an argument to `addFive`, rename it `addReference`, and then we “press the button” or invoke it.

This highlights some important concepts of the JavaScript language. First, just as you can pass a string or a number as an argument to a function, so too can you pass a reference to a function as an argument. When you do this the function you’re passing as an argument is called a callback function and the function you’re passing the callback function to is called a higher order function.

Because vocabulary is important, here’s the same code with the variables re-named to match the concepts they’re demonstrating.

```jsx
function add (x,y) {
  return x + y
}

function higherOrderFunction (x, callback) {
  return callback(x, 5)
}

higherOrderFunction(10, add)
```

This pattern should look familiar, it’s everywhere. If you’ve ever used any of the JavaScript Array methods, you’ve used a callback. If you’ve ever used lodash, you’ve used a callback. If you’ve ever used jQuery, you’ve used a callback.

```jsx
[1,2,3].map((i) => i + 5)

_.filter([1,2,3,4], (n) => n % 2 === 0 );

$('#btn').on('click', () =>
  console.log('Callbacks are everywhere')
)
```

In general, there are two popular use cases for callbacks. The first, and what we see in the `.map` and `_.filter` examples, is a nice abstraction over turning one value into another. We say “Hey, here’s an array and a function. Go ahead and get me a new value based on the function I gave you”. The second, and what we see in the jQuery example, is delaying execution of a function until a particular time. “Hey, here’s this function. Go ahead and invoke it whenever the element with an id of `btn` is clicked.” It’s this second use case that we’re going to focus on, “delaying execution of a function until a particular time”.

Right now we’ve only looked at examples that are synchronous. As we talked about at the beginning of this post, most of the apps we build don’t have all the data they need up front. Instead, they need to fetch external data as the user interacts with the app. We’ve just seen how callbacks can be a great use case for this because, again, they allow you to “delay execution of a function until a particular time”. It doesn’t take much imagination to see how we can adapt that sentence to work with data fetching. Instead of delaying execution of a function until *a particular time*, we can delay execution of a function *until we have the data we need*. Here’s probably the most popular example of this, jQuery’s `getJSON` method.

```jsx
// updateUI and showError are irrelevant.
// Pretend they do what they sound like.

const id = 'tylermcginnis'

$.getJSON({
  url: `https://api.github.com/users/${id}`,
  success: updateUI,
  error: showError,
})
```

We can’t update the UI of our app until we have the user’s data. So what do we do? We say, “Hey, here’s an object. If the request succeeds, go ahead and call `success` passing it the user’s data. If it doesn’t, go ahead and call `error` passing it the error object. You don’t need to worry about what each method does, just be sure to call them when you’re supposed to”. This is a perfect demonstration of using a callback for async requests.

At this point we’ve learned about what callbacks are and how they can be beneficial both in synchronous and asynchronous code. What we haven’t talked yet is the dark side of callbacks. Take a look at this code below. Can you tell what’s happening?

```jsx
// updateUI, showError, and getLocationURL are irrelevant.
// Pretend they do what they sound like.

const id = 'tylermcginnis'

$("#btn").on("click", () => {
  $.getJSON({
    url: `https://api.github.com/users/${id}`,
    success: (user) => {
      $.getJSON({
        url: getLocationURL(user.location.split(',')),
        success (weather) {
          updateUI({
            user,
            weather: weather.query.results
          })
        },
        error: showError,
      })
    },
    error: showError
  })
})
```

Notice we’ve added a few more layers of callbacks. First we’re saying don’t run the initial AJAX request until the element with an id of `btn` is clicked. Once the button is clicked, we make the first request. If that request succeeds, we make a second request. If that request succeeds, we invoke the `updateUI` method passing it the data we got from both requests. Regardless of if you understood the code at first glance or not, objectively it’s much harder to read than the code before. This brings us to the topic of “Callback Hell”.

As humans, we naturally think sequentially. When you have nested callbacks inside of nested callbacks, it forces you out of your natural way of thinking. Bugs happen when there’s a disconnect between how your software is read and how you naturally think.

Like most solutions to software problems, a commonly prescribed approach for making “Callback Hell” easier to consume is to modularize your code.

```jsx
function getUser(id, onSuccess, onFailure) {
  $.getJSON({
    url: `https://api.github.com/users/${id}`,
    success: onSuccess,
    error: onFailure
  })
}

function getWeather(user, onSuccess, onFailure) {
  $.getJSON({
    url: getLocationURL(user.location.split(',')),
    success: onSuccess,
    error: onFailure,
  })
}

$("#btn").on("click", () => {
  getUser("tylermcginnis", (user) => {
    getWeather(user, (weather) => {
      updateUI({
        user,
        weather: weather.query.results
      })
    }, showError)
  }, showError)
})
```

OK, the function names help us understand what’s going on, but is it objectively “better”? Not by much. We’ve put a band-aid over the readability issue of Callback Hell. The problem still exists that we naturally think sequentially and, even with the extra functions, nested callbacks break us out of that sequential way of thinking.

The next issue of callbacks has to do with [inversion of control](https://en.wikipedia.org/wiki/Inversion_of_control). When you write a callback, you’re assuming that the program you’re giving the callback to is responsible and will call it when (and only when) it’s supposed to. You’re essentially inverting the control of your program over to another program. When you’re dealing with libraries like jQuery, lodash, or even vanilla JavaScript, it’s safe to assume that the callback function will be invoked at the correct time with the correct arguments. However, for many third party libraries, callback functions are the interface for how you interact with them. It’s entirely plausible that a third party library could, whether on purpose or accidentally, break how they interact with your callback.

```jsx
function criticalFunction () {
  // It's critical that this function
  // gets called and with the correct
  // arguments.
}

thirdPartyLib(criticalFunction)
```

Since you’re not the one calling `criticalFunction`, you have 0 control over when and with what argument it’s invoked. *Most* of the time this isn’t an issue, but when it is, it’s a big one.

### Promises

Have you ever been to a busy restaurant without a reservation? When this happens, the restaurant needs a way to get back in contact with you when a table opens up. Historically, they’d just take your name and yell it when your table was ready. Then, as naturally occurs, they decided to start getting fancy. One solution was, instead of taking your name, they’d take your number and text you once a table opened up. This allowed you to be out of yelling range but more importantly, it allowed them to target your phone with ads whenever they wanted. Sound familiar? It should! OK, maybe it shouldn’t. It’s a metaphor for callbacks! Giving your number to a restaurant is just like giving a callback function to a third party service. You *expect* the restaurant to text you when a table opens up, just like you *expect* the third party service to invoke your function when and how they said they would. Once your number or callback function is in their hands though, you’ve lost all control.

Thankfully, there is another solution that exists. One that, by design, allows you to keep all the control. You’ve probably even experienced it before - it’s that little buzzer thing they give you. You know, this one.

If you’ve never used one before, the idea is simple. Instead of taking your name or number, they give you this device. When the device starts buzzing and glowing, your table is ready. You can still do whatever you’d like as you’re waiting for your table to open up, but now you don’t have to give up anything. In fact, it’s the exact opposite. They have to give you something. There is no inversion of control.

The buzzer will always be in one of three different states - `pending`, `fulfilled`, or `rejected`.

`pending` is the default, initial state. When they give you the buzzer, it’s in this state.

`fulfilled` is the state the buzzer is in when it’s flashing and your table is ready.

`rejected` is the state the buzzer is in when something goes wrong. Maybe the restaurant is about to close or they forgot someone rented out the restaurant for the night.

Again, the important thing to remember is that you, the receiver of the buzzer, have all the control. If the buzzer gets put into `fulfilled`, you can go to your table. If it gets put into `fulfilled` and you want to ignore it, cool, you can do that too. If it gets put into `rejected`, that sucks but you can go somewhere else to eat. If nothing ever happens and it stays in `pending`, you never get to eat but you’re not actually out anything.

Now that you’re a master of the restaurant buzzer thingy, let’s apply that knowledge to something that matters.