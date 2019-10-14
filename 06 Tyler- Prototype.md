You cant very far in javascript without dealing with objects- learning how to create objects is one of the irst thigns you studied when starting out

Objects are key/value pairs- most ocmon way is to create an object with curly braces

```js
let animal = {}
animal.name = 'Leo'
animal.energy = 10

animal.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

animal.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

animal.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}
```

Now odds are in our ap we need to create more than one animal - next step would be to encapsulate that logic inside of a function that we can invoke whenever we needed to create a new animal- call this pattern funcitonal instantiation and we will call that function a constructor function since its responsible for “constructing” a new object

### Funcitonal Instantiation

```js
function Animal (name, energy) {
  let animal = {}
  animal.name = name
  animal.energy = energy

  animal.eat = function (amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  }

  animal.sleep = function (length) {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }

  animal.play = function (length) {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }

  return animal
}

const leo = Animal('Leo', 7)
const snoop = Animal('Snoop', 10)
```



now whenever we want to create a new animal (new “instance”) all we have to do is invoke our Animal function passing it the animals name and energy level. this works great and is simple. The problem is is that there’s no reason t re-create those methods when creating a new animal- just wasting memory and making each animal object bigger than it needs to be- instead of re-creating those methods we create move them to their own object and we can have the animal instance reference that object- we call this funcitonal instantiation with shared methods

```js
const animalMethods = {
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  },
  sleep(length) {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  },
  play(length) {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }
}

function Animal (name, energy) {
  let animal = {}
  animal.name = name
  animal.energy = energy
  animal.eat = animalMethods.eat
  animal.sleep = animalMethods.sleep
  animal.play = animalMethods.play

  return animal
}

const leo = Animal('Leo', 7)
const snoop = Animal('Snoop', 10)
```

### Object.create()

Can improve our example. Object.create allows you to create an object which will delegate to another object on failed lookups. Put differently, Object.create allows you to create an object and whenever there’s a failed property lookup on that object, it can consult another object to see if that other object has the property. That was a lot of words. Let’s see some code

```js
const parent = {
  name: 'Stacey',
  age: 35,
  heritage: 'Irish'
}

const child = Object.create(parent)
child.name = 'Ryan'
child.age = 7

console.log(child.name) // Ryan
console.log(child.age) // 7
console.log(child.heritage) // Irish
```

because child was created with Object.create(parent) whenever there is a failed property lookup on child Javascript will delegate that lookup to the parent object. even though child doesnt have a heritage proeprty parent does

```js
const animalMethods = {
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  },
  sleep(length) {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  },
  play(length) {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }
}

function Animal (name, energy) {
  let animal = Object.create(animalMethods)
  animal.name = name
  animal.energy = energy

  return animal
}

const leo = Animal('Leo', 7)
const snoop = Animal('Snoop', 10)

leo.eat(10)
snoop.play(5)
```

So far, so good. There are still some improvements we can make though. It seems just a tad “hacky” to have to manage a separate object (`animalMethods`) in order to share methods across instances. That seems like a common feature that you’d want to be implemented into the language itself. Turns out it is and it’s the whole reason you’re here - `prototype`.

What exactly is prototype?

Well, simply put, every function in JavaScript has a `prototype` property that references an object. Anticlimactic, right? Test it out for yourself.

```js
function doThing () {}
console.log(doThing.prototype) // {}
```

What if instead of creating a separate object to manage our methods (like we’re doing with `animalMethods`), we just put each of those methods on the `Animal` function’s prototype? Then all we would have to do is instead of using Object.create to delegate to `animalMethods`, we could use it to delegate to `Animal.prototype`. We’ll call this pattern `Prototypal Instantiation`

```js
function Animal (name, energy) {
  let animal = Object.create(Animal.prototype)
  animal.name = name
  animal.energy = energy

  return animal
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Animal.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

const leo = Animal('Leo', 7)
const snoop = Animal('Snoop', 10)

leo.eat(10)
snoop.play(5)
```

All our functionality is still the same but now instead of having to manage a separate object for all the methods, we can just use another object that comes built into the `Animal` function itself, `Animal.prototype`

At this point we know three things:

1. How to create a constructor function.
2. How to add methods to the constructor function’s prototype.
3. How to use Object.create to delegate failed lookups to the function’s prototype.

Those three tasks seem pretty foundational to any programming language. Is JavaScript really that bad that there’s no easier, "built in" way to accomplish the same thing? As you can probably guess at this point there is, and it’s by using the new keyword

Looking back at our `Animal` constructor, the two most important parts were creating the object and returning it. Without creating the object with `Object.create`, we wouldn’t be able to delegate to the function’s prototype on failed lookups. Without the `return` statement, we wouldn’t ever get back the created object

Here’s the cool thing about `new` - when you invoke a function using the `new` keyword, those two lines are done for you implicitly (“under the hood”) and the object that is created is called `this`

```js
function Animal (name, energy) {
  // const this = Object.create(Animal.prototype)

  this.name = name
  this.energy = energy

  // return this
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Animal.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

const leo = new Animal('Leo', 7)
const snoop = new Animal('Snoop', 10)
```

Again the reason this works and that the `this` object is created for us is because we called the constructor function with the `new` keyword. If you leave off `new` when you invoke the function, that `this`object never gets created nor does it get implicitly returned. We can see the issue with this in the example below.

For those unfamiliar, a Class allows you to create a blueprint for an object. Then whenever you create an instance of that Class, you get an object with the properties and methods defined in the blueprint.

n 2015, EcmaScript (the official JavaScript specification) 6 was released with support for Classes and the `class` keyword. Let’s see how our `Animal` constructor function above would look like with the new class syntax.

```js
class Animal {
  constructor(name, energy) {
    this.name = name
    this.energy = energy
  }
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  }
  sleep(length) {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }
  play(length) {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }
}

const leo = new Animal('Leo', 7)
const snoop = new Animal('Snoop', 10)
```

So if this is the new way to create classes, why did we spend so much time going over the old way? The reason for that is because the new way (with the `class` keyword) is primarily just “syntactical sugar” over the existing way we’ve called the pseudoclassical pattern. In order to *fully* understand the convenience syntax of ES6 classes, you first must understand the pseudoclassical pattern.

### Array Methods

We talked in depth above about how if you want to share methods across instances of a class, you should stick those methods on the class’ (or function’s) prototype. We can see this same pattern demonstrated if we look at the `Array` class. Historically you’ve probably created your arrays like this

```js
const friends = []
```

```js
const friendsWithSugar = []

const friendsWithoutSugar = new Array()
```

One thing you might have never thought about is how does every instance of an array have all of those built in methods (`splice`, `slice`, `pop`, etc)?

Well as you now know, it’s because those methods live on `Array.prototype` and when you create a new instance of `Array`, you use the `new` keyword which sets up that delegation to `Array.prototype` on failed lookups.

We can see all the array’s methods by simply logging `Array.prototype`.

Up until this point we’ve covered the why and how of sharing methods between instances of a Class. However, what if we had a method that was important to the Class, but didn’t need to be shared across instances? For example, what if we had a function that took in an array of `Animal` instances and determined which one needed to be fed next? We’ll call it `nextToEat`.

```js
function nextToEat (animals) {
  const sortedByLeastEnergy = animals.sort((a,b) => {
    return a.energy - b.energy
  })

  return sortedByLeastEnergy[0].name
}
```

Whenever you have a method that is specific to a class itself, but doesn’t need to be shared across instances of that class, you can add it as a `static` property of the class.

```js
class Animal {
  constructor(name, energy) {
    this.name = name
    this.energy = energy
  }
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  }
  sleep(length) {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }
  play(length) {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }
  static nextToEat(animals) {
    const sortedByLeastEnergy = animals.sort((a,b) => {
      return a.energy - b.energy
    })

    return sortedByLeastEnergy[0].name
  }
}
```

Now, because we added `nextToEat` as a `static` property on the class, it lives on the `Animal` class itself (not its prototype) and can be accessed using `Animal.nextToEat`

### Getting the Prototype of An Object

Regardless of whichever pattern you used to create an object, getting that object’s prototype can be accomplished using the `Object.getPrototypeOf` method.

```js
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Animal.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

const leo = new Animal('Leo', 7)
const prototype = Object.getPrototypeOf(leo)

console.log(prototype)
// {constructor: ƒ, eat: ƒ, sleep: ƒ, play: ƒ}

prototype === Animal.prototype // true
```

First, you’ll notice that `proto` is an object with 4 methods, `constructor`, `eat`, `sleep`, and `play`. That makes sense. We used `getPrototypeOf` passing in the instance, `leo` getting back that instances’ prototype, which is where all of our methods are living. This tells us one more thing about `prototype` as well that we haven’t talked about yet. By default, the `prototype` object will have a `constructor`property which points to the original function or the class that the instance was created from. What this also means is that because JavaScript puts a `constructor` property on the prototype by default, any instances will be able to access their constructor via `instance.constructor`.

The second important takeaway from above is that `Object.getPrototypeOf(leo) === Animal.prototype`. That makes sense as well. The `Animal` constructor function has a prototype property where we can share methods across all instances and `getPrototypeOf` allows us to see the prototype of the instance itself.

You may have seen \_\_proto\_\_ used before to get an instances’ prototype. That’s a relic of the past. Instead, use Object.getPrototypeOf(instance) as we saw above.

### Determining if a property lives on the prototype

There are certain cases where you need to know if a property lives on the instance itself or if it lives on the prototype the object delegates to. We can see this in action by looping over our `leo` object we’ve been creating. Let’s say the goal was the loop over `leo` and log all of its keys and values. Using a `for in` loop, that would probably look like this.

```js
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Animal.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

const leo = new Animal('Leo', 7)

for(let key in leo) {
  console.log(`Key: ${key}. Value: ${leo[key]}`)
}
```

What would you expect to see? Most likely, it was something like this -

Key: name. Value: Leo Key: energy. Value: 7

However, what you saw if you ran the code was this

```js
Key: name. Value: Leo
Key: energy. Value: 7
Key: eat. Value: function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}
Key: sleep. Value: function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}
Key: play. Value: function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}
```

Why is that? Well a `for in` loop is going to loop over all of the enumerable properties on both the object itself as well as the prototype it delegates to. Because by default any property you add to the function’s prototype is enumerable, we see not only `name` and `energy`, but we also see all the methods on the prototype - `eat`, `sleep`, and `play`. To fix this, we either need to specify that all of the prototype methods are non-enumerable or we need a way to only console.log if the property is on the `leo` object itself and not the prototype that `leo` delegates to on failed lookups. This is where `hasOwnProperty` can help us out.

`hasOwnProperty` is a property on every object that returns a boolean indicating whether the object has the specified property as its own property rather than on the prototype the object delegates to. That’s exactly what we need. Now with this new knowledge we can modify our code to take advantage of `hasOwnProperty` inside of our `for in` loop.

```js
const leo = new Animal('Leo', 7)

for(let key in leo) {
  if (leo.hasOwnProperty(key)) {
    console.log(`Key: ${key}. Value: ${leo[key]}`)
  }
}
```

And now what we see are only the properties that are on the `leo` object itself rather than on the prototype `leo` delegates to as well.

### Check if an object is an instance of a Class

Sometimes you want to know whether an object is an instance of a specific class. To do this, you can use the `instanceof` operator. The use case is pretty straight forward but the actual syntax is a bit weird if you’ve never seen it before. It works like this

```js
object instanceof Class
```

```js
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

function User () {}

const leo = new Animal('Leo', 7)

leo instanceof Animal // true
leo instanceof User // false
```

The way that `instanceof` works is it checks for the presence of `constructor.prototype` in the object’s prototype chain. In the example above, `leo instanceof Animal` is `true` because `Object.getPrototypeOf(leo) === Animal.prototype`. In addition, `leo instanceof User` is `false` because `Object.getPrototypeOf(leo) !== User.prototype`

If the constructor was called with the `new` keyword, then `this` inside of the body of the constructor will be an `instanceof` the constructor function itself. That was a lot of big words. Here’s some code.

```js
function Animal (name, energy) {
  if (this instanceof Animal === false) {
    console.warn('Forgot to call Animal with the new keyword')
  }

  this.name = name
  this.energy = energy
}
```

```js
function Animal (name, energy) {
  if (this instanceof Animal === false) {
    return new Animal(name, energy)
  }

  this.name = name
  this.energy = energy
}
```

### Re-creating Object.create

Throughout this post we’ve relied heavily upon `Object.create` in order to create objects which delegate to the constructor function’s prototype. At this point, you should know how to use `Object.create` inside of your code but one thing that you might not have thought of is how `Object.create` actually works under the hood. In order for you to really understand how `Object.create` works, we’re going to re-create it ourselves. First, what do we know about how `Object.create` works?

1. It takes in an argument that is an object.
2. It creates an object that delegates to the argument object on failed lookups.
3. It returns the new created object.

Let’s start off with \#1.

```js
Object.create = function (objToDelegateTo) {

}
```

Now \#2 - we need to create an object that will delegate to the argument object on failed lookups. This one is a little more tricky. To do this, we’ll use our knowledge of how the `new` keyword and prototypes work in JavaScript. First, inside the body of our `Object.create` implementation, we’ll create an empty function. Then, we’ll set the prototype of that empty function equal to the argument object. Then, in order to create a new object, we’ll invoke our empty function using the `new` keyword. If we return that newly created object, that’ll finish \#3 as well.

```js
Object.create = function (objToDelegateTo) {
  function Fn(){}
  Fn.prototype = objToDelegateTo
  return new Fn()
}
```

Arrow functions don’t have their own `this` keyword. As a result, arrow functions can’t be constructor functions and if you try to invoke an arrow function with the `new` keyword, it’ll throw an error

