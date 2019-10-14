Internally JS sets a value to one of the six primitive data types:

1. undefined
2. Null
3. Boolean
4. Number
5. String
6. Symbol

Everything else is an object- includng arrays and functions

Each value also has an inherent boolean value, known as either truthy or falsey

The following are always falsey...

```jsx
false
0 
''
null
undefined
NaN
```

everything else is truth including...

```jsx
'0'
'false'
[]
{} // an empty object
function(){}
```

```jsx
if (value){
    // value is truthy
}
```

unexpected situations can occur when using loose equality ==

the == version is quite liberal, values may be considered equal if they are different types, sinze the == operator will force coercion into a single type before performaning a comparison

undefined and null equal each other

== converts the operands to the same type before making the comparison

```jsx
3 == '3' //true
```