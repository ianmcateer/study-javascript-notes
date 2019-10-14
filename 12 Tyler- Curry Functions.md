basic example...

```jsx
const sum = x => y => x + y

// returns the nmber three
sum(2)(1)

// returns a function
sum(2)
```

first class functions in javascript...

javascript has first class functions because it supports functions as arguments and return values

Currying is the process in functional programming in which we can transofrm a function with multiple arguments into a sequence of nested functions- it returns a new function that expects the argument inline

```jsx
function multiply(a, b, c) {
    return a * b * c;
}
multiply(3,6,9)
```

curried version of the function...

```jsx
function multiply(a){
    return (b) => {
        return (c) => {
            return a * b * c
        }
    }
}

log(multiply(1)(2)(3)) // 6
```

The idea behind currying is to take a function and derive a function that returns specialized function(s).

### Is Currying Useful?

1. useul when want to write little cod modules that can be recursed and configured with ease, much like we do with npm

eg own a store and you want to give 10% discount to your fav customers

```jsx
function discount(price, discount) {
    return price * discount
}
```

when customer buys a discount...

```jsx
const price = discount(500,0.10); // $50 
// $500  - $50 = $450
```

we can curry the discount function so that we dont always have to add the 0.10 discount

```jsx
function discount (discount){
    return function (price){
        return price * discount
    }
}
```

```jsx
const tenPercentDiscount = discount(0.1)
```

and if want to create another discount for a more special customer...

```jsx
const twentyPercentDiscount = discount(0.2);
```

2\. Avoid frequently calling a function with the same argument...

```jsx
function volume(l, w, h) {
    return l * w * h;
}
```

happens to be that all cylinders in your warehouse are of height 100m.. can cury the volume function

```jsx
function volume(h) {
    return (w) => {
        return (l) => {
            return l * w * h
        }
    }
}

const withCylinderHeight = volume(100);
withCylinderHeight(200)(30); // 600,000l
withCylinderHeight(2322)(232); // 53,870,400l
```





