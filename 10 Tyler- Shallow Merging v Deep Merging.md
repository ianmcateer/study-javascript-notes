We want to create an empty Object, and push the keys and properties of each of the objects we want to merge into it

```jsx
var obj = {
	sandwich: 'chicken',
	condiment: 'mayo',
	desert: true
};

// Create a new object
var extended = {};

// Loop through our object
for (var prop in obj) {
	if (obj.hasOwnProperty(prop)) {
		// Push each value from `obj` into `extended`
		extended[prop] = obj[prop];
	}
}
```

Here we are lopping through obj and adding each key/value pair into extended- the extended object is now identical to obj1

can create a funciton to do this for us

```jsx
var obj1 = {
	sandwich: 'chicken',
	condiment: 'mayo',
	desert: true
};

var obj2 = {
	sandwich: 'tuna',
	chips: 'Cape Cod',
	desert: false
}

// Create a new object
var extended = {};

var merge = function (obj) {
	for (var prop in obj) {
		if (obj.hasOwnProperty(prop)) {
			// Push each value from `obj` into `extended`
			extended[prop] = obj[prop];
		}
	}
};

merge(obj1);
merge(obj2);
```

we dont want to call our merge() method on each object we want to merge- lets create an extend function to automate the whole thing for us

```jsx
var extend = function () {

	// Create a new object
	var extended = {};

	// Merge the object into the extended object
	var merge = function (obj) {
		for (var prop in obj) {
			if (obj.hasOwnProperty(prop)) {
				// Push each value from `obj` into `extended`
				extended[prop] = obj[prop];
			}
		}
	};

	// Loop through each object and conduct a merge
    // can use arguments variable to access any arguments passed into method
	for (var i = 0; i < arguments.length; i++) {
		merge(arguments[i]);
	}

	return extended;

};

var newObj = extend(obj1, obj2);
```

### Deep Merging

Our helper method currently does a shallow merge. Imagine you had two objects with a nested structure.

```jsx
var obj1 = {
	sandwich: 'chicken',
	condiment: 'mayo',
	desert: true,
	days: {
		monday: true,
		wednesday: true,
		friday: true
	}
};

var obj2 = {
	sandwich: 'tuna',
	chips: 'Cape Cod',
	desert: false,
	days: {
		monday: false,
		tuesday: true,
		thursday: true
	}
}
```

In a shallow merge, the days key in obj2 would completely overwrite the value from obj1\. In a deep merge, the days objects from obj1 and obj2 would get merged together.

```jsx
// Shallow merge
{
	monday: false,
	tuesday: true,
	thursday: true
}

// Deep merge
{
	monday: false,
	tuesday: true,
	wednesday: true,
	thursday: true,
	friday: true
}
```

Depending on what you’re trying to do, a shallow merge might be the desired result. Other times, you might want a deep merge.

### Adding a Deep Merge

In jQuery’s extend() method, you can pass in the first argument as a boolean. If it’s true, it will do a deep merge instead of a shallow one. Let’s use that same approach.

First, we’re going to set up a new variable, deep, to store whether or not a merge should be deep. We’ll set it to false by default. We’re also going to predefine var i = 0 for our for loop.

```jsx
var extend = function () {

	// Variables
	var extended = {};
	var deep = false;
	var i = 0;

	// Check if a deep merge
    // Also advance i++ so loop will start with the first object
    // and not the boolean
	if (typeof (arguments[0]) === 'boolean') {
		deep = arguments[0];
		i++;
	}

	// Merge the object into the extended object
	var merge = function (obj) {
		for (var prop in obj) {
			if (obj.hasOwnProperty(prop)) {
				if (deep && Object.prototype.toString.call(obj[prop]) === '[object Object]') {
					// If we're doing a deep merge and the property is an object
					extended[prop] = extend(true, extended[prop], obj[prop]);
				} else {
					// Otherwise, do a regular merge
					extended[prop] = obj[prop];
				}
			}
		}
	};

// Loop through each object and conduct a merge
// 	dont have to set i bc aready declared it
	for (; i < arguments.length; i++) {
		merge(arguments[i]);
	}

	return extended;

};

var shallowMerge = extend(obj1, obj2);
var deepMerge = extend(true, obj1, obj2);
```

