### variable declarations

in Js variables are initalised with undefined when they are created

```js
var declaration
console.log(declaration) 
//undefined)
```

initialisation

when you first assign a value to a variable

```js
declaration = ‘this is a declaration'
```

### Scope

if the variable statement occurs inside a FunctionDeclaration, the variables are defined with function-local scope in that function. Otherwise, they are defined with global scope that is, they are created as memebrs of the global object

```js
function getDate(){
    var date = new Date()
    
    function formatDate(){
        return date.toString().slice(4)
    }
    
    return formatDate();
}
getDate()
console.log(date)
```

more complicated example...

```js
function discountPrices(prices, discount){
    var discounted = [];
    for (var i = 0; i < prices.length; i++){
        var discountedPrice = prices[i] * (1 - discount)
        var finalPrice = Math.round()
        discounted.push(finalPrice)
    }
    console.log(i)
    console.log(discountedPrice)
    return discounted
}

```

odd because still have access to i and discountedPrice even outside for loop

var is function scoped

###  Hoisting

When JS interpreter evalueates your code it will move all of your funciton and variable declarations to the top of the current scope

```js
console.log(hoisted)
var hoisted
```

the interpreter will do this...

```js
var hoisted;
console.log(hoisted)
```

```js
function discountPrices(prices, discount){
    var discounted
    var i 
    var discountedPrice
    var finalPrice
    
    discounted = []
    
    for (var i = 0; i < prices.length; i++){
         discountedPrice = prices[i] * (1 - discount)
         finalPrice = Math.round()
        discounted.push(finalPrice)
    }
    console.log(i)
    console.log(discountedPrice)
    return discounted
}
```

if cant find the variable it will look in its parents scope and look until the global scope- 

discounted = [] 

this will become a property on the global scope

How does var compare with let or const?

let is block scoped {}

var is function scoped

if change i to let i inside for loop if console log it get reference error bc i is now only scoped to the block

```js
console.log(hoisted)
let hoisted
```

Interpreter will see it as this...

```js
let hoisted
console.log(hoisted)// reference error
```

block scope is better than function scope

everything let has const also has- only difference is const cant be re-assigned

### Further Notes

Javascript is funciton scoped - only functions introduce new scopes

```js
// Global Scope
var firstFunction = function () {
  // firstFunction's Scope
  var secondFunction = function () {
    // secondFunction's Scope
  };
};
```

we have two scopes- any child scopes have access to every one of its parents scopes

```js
function doThing() {
  var num = 1;
  if (num >= 0) {
    var secondNum = 2;
    console.log(num); // 1
    console.log(secondNum); // 2
  }
  console.log(num); // 1
  console.log(secondNum); // 2
}
doThing();
```

the biggest difference between var and let is let allows you to have code that is block scoped, meaning anywhere we have an opening and closing curly brace we are creating a new scope

generally agreed that block scope is better than function scope

everything let has const has also - only difference is when you create a variable with const that variable cant be re-assigned a new reference- notice i didnt say that variable is immutable

```js
const user = {
    name: 'tyler',
    age: 25
}
user.name = 'ian'
```

the code above is valid - we arent re-assigning the reference to user- we are just re assinging a specific value





