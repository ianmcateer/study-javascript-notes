Previously we learned how to create an `Animal` class both in ES5 as well as in ES6\. We also learned how to share methods across those classes using JavaScript’s prototype. To review, here’s the code we saw in an earlier post.

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
```

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
  sleep() {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }
  play() {
    console.log(`${this.name} is playing.`)
    this.energy -= length
  }
}

const leo = new Animal('Leo', 7)
```

Now let’s say we wanted to start making individual classes for specific animals. For example, what if we wanted to start making a bunch of dog instances. What properties and methods will these dogs have? Well, similar to our `Animal` class, we could give each dog a `name`, an `energy` level, and the ability to `eat`, `sleep`, and `play`. Unique to our `Dog` class, we could also give them a `breed` property as well as the ability to `bark`. In ES5, our `Dog` class could look something like this

```js
function Dog (name, energy, breed) {
  this.name = name
  this.energy = energy
  this.breed = breed
}

Dog.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Dog.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

Dog.prototype.play = function (length) {
  console.log(`${this.name} is playing.`)
  this.energy -= length
}

Dog.prototype.bark = function () {
  console.log('Woof-Woof!')
  this.energy -= .1
}

const charlie = new Dog('Charlie', 10, 'Goldendoodle')
```

Alright, well… we just recreated the `Animal` class and added a few new properties to it. If we wanted to create another animal, say a `Cat`, at this point we’d again have to create a `Cat` class, duplicate all the common logic located in the `Animal` class to it, then add on `Cat` specific properties just like we did with the `Dog` class. In fact, we’d have to do this for each different type of animal we created.

```js
function Dog (name, energy, breed) {}

function Cat (name, energy, declawed) {}

function Giraffe (name, energy, height) {}

function Monkey (name, energy, domesticated) {}
```

This work, but it seems wasteful. The `Animal` class is the perfect base class. What that means is that it has all the properties that each one of our animals has in common. Whether we’re creating a dog, cat, giraffe, or monkey, all of them will have a `name`, `energy` level, and the ability to `eat`, `sleep`, and `play`. With that said, is there a way we can utilize the `Animal` class whenever we create the individual classes for each different animal? Let’s try it out. I’ll paste the `Animal` class again below for easy reference.

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

function Dog (name, energy, breed) {
  Animal.call(this, name, energy)

  this.breed = breed
}

const charlie = new Dog('Charlie', 10, 'Goldendoodle')

charlie.name // Charlie
charlie.energy // 10
charlie.breed // Goldendoodle
```

Not finished yet...