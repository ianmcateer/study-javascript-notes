shorthand method names allow you to drop the ‘function’ part of a emthod

```js
const actions = {
  sayName: function () {
    alert(this.name)
  },
  takeStep: function () {
    this.step++
  }
}
```

```js
const actions = {
  sayName () {
    alert(this.name)
  },
  takeStep () {
    this.step++
  }
}
```