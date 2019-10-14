Have an expression be computed as a property name on an object

```js
function objectify(key value){
    let obj = {}
    obj[key] = value
    return obj
}
```

with computed property names we can do...

```js
function objectify(key, value){
    return {
        [key]: value
    }
} 
```