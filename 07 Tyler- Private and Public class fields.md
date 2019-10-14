In the [previous post](https://tylermcginnis.com/beginners-guide-to-javascript-prototype/) we learned how to create JavaScript classes in both ES5 as well as ES6\. We also discussed how to add state to the instances of those classes via the constructor as well as how to share methods across instances via the classes’ prototype. Here’s a simple `Player` class which encompasses everything we discussed in regards to ES6 classes.

```js
class Player {
  constructor() {
    this.points = 0
    this.assists = 0
    this.rebounds = 0
    this.steals = 0
  }
  addPoints(amount) {
    this.points += amount
  }
  addAssist() {
    this.assists++
  }
  addRebound() {
    this.rebounds++
  }
  addSteal() {
    this.steals++
  }
}
```

now with Class Fields can add instance properties directly as a property on the class without having to use the `constructor` method. Pretty slick, but where this proposal really shines is if we look at some React code. Here’s a typical React component. It has local state, some methods, and a few static properties being added to the class.

```js
class PlayerInput extends Component {
  constructor(props) {
    super(props)
    this.state = {
      username: ''
    }

    this.handleChange = this.handleChange.bind(this)
  }
  handleChange(event) {
    this.setState({
      username: event.target.value
    })
  }
  render() {
    ...
  }
}

PlayerInput.propTypes = {
  id: PropTypes.string.isRequired,
  label: PropTypes.string.isRequired,
  onSubmit: PropTypes.func.isRequired,
}

PlayerInput.defaultProps = {
  label: 'Username',
}
```

Let’s see how the new `Class Fields` proposal improves the code above First, we can take our `state`variable out of the constructor and define it directly as a property (or “field”) on the clas

```js
class PlayerInput extends Component {
  state = {
    username: ''
  }
  constructor(props) {
    super(props)

    this.handleChange = this.handleChange.bind(this)
  }
  handleChange(event) {
    this.setState({
      username: event.target.value
    })
  }
  render() {
    ...
  }
}

PlayerInput.propTypes = {
  id: PropTypes.string.isRequired,
  label: PropTypes.string.isRequired,
  onSubmit: PropTypes.func.isRequired,
}

PlayerInput.defaultProps = {
  label: 'Username',
}
```

Cool, but nothing to get too excited over. Let’s keep going. In the previous post we talked about how you can add static methods to the class itself by using the `static` keyword. However, according to the ES6 class specification, this only works with methods, not values. That’s why in the code above we have to add `propTypes` and `defaultProps` to `PlayerInput` after we define it and not in the class body. Again, why can’t those go directly on the class body just as a static method would? Well the good news is this is encompassed in the `Class Fields` proposal as well. So now instead of just defining static methods in the class body, you can also define static values. What that means for our code is we can move `propTypes` and `defaultProps` up into the class definition.

```js
class PlayerInput extends Component {
  static propTypes = {
    id: PropTypes.string.isRequired,
    label: PropTypes.string.isRequired,
    onSubmit: PropTypes.func.isRequired,
  }
  static defaultProps = {
    label: 'Username'
  }
  state = {
    username: ''
  }
  constructor(props) {
    super(props)

    this.handleChange = this.handleChange.bind(this)
  }
  handleChange(event) {
    this.setState({
      username: event.target.value
    })
  }
  render() {
    ...
  }
}
```

Much better, but we still have that ugly `constructor` method and `super` invocation. Again, the reason we need the constructor right now is in order to bind the `handleChange` method to the correct context. If we could figure out another way to make sure `handleChange` was always invoked in the correct context, we could get rid of the `constructor` altogether.

If you’ve used arrow functions before, you know that they don’t have their own `this` keyword. Instead, the `this` keyword is bound `lexically`. That’s a fancy way of saying when you use the `this` keyword inside of an arrow function, things behave how you’d expect them to. Taking that knowledge and combining it with the “Class Fields” proposal, what if we swapped out the `handleChange` method for an arrow function? Seems a little weird but by doing this we’d get rid of the bind issue altogether since, again, arrow functions bind `this` lexically

```js
class PlayerInput extends Component {
  static propTypes = {
    id: PropTypes.string.isRequired,
    label: PropTypes.string.isRequired,
    onSubmit: PropTypes.func.isRequired,
  }
  static defaultProps = {
    label: 'Username'
  }
  state = {
    username: ''
  }
  handleChange = (event) => {
    this.setState({
      username: event.target.value
    })
  }
  render() {
    ...
  }
}

```

Well would you look at that. That’s much better than the original class we started with and it’s all thanks to the Class Fields proposal which will be part of the official EcmaScript specification soon.

From a developer experience standpoint, Class Fields are a clear win. However, there are some downsides to them that are rarely talked about. In the last post we talked about how ES6 classes are really just sugar over what we called the “pseudoclassical” pattern. Meaning, when you add a method to a class, that’s really like adding a method to the function’s prototype.

```js
class Animal {
  eat() {}
}

// Is equivalent to

function Animal () {}
Animal.prototype.eat = function () {}
```

This is performant because `eat` is defined once and shared across all instances of the class. What does this have to do with Class Fields? Well, as we saw above, Class Fields are added to the instance. This means that for each instance we create, we’ll be creating a new `eat` method.

```js
class Animal {
  eat() {}
  sleep = () => {}
}

// Is equivalent to

function Animal () {
  this.sleep = function () {}
}

Animal.prototype.eat = function () {}
```

Notice how `sleep` gets put on the instance and not on `Animal.prototype`. Is this a bad thing? Well, it can be. Making broad statements about performance without measuring is generally a bad idea. The question you need to answer in your application is if the developer experience you gain from Class Fields outweighs the potential performance hit.

If you want to use any of what we’ve talked about so far in your app, you’ll need to use the [babel-plugin-transform-class-properties](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties) plugin.