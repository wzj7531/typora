# Methods of primitives

JavaScript allows us to work with primitives (strings, numbers, etc.) as if they were objects. They also provide methods to call as such.

A primitive

- Is a value of a primitive type.
- There are 7 primitive types: `string`, `number`, `bigint`, `boolean`, `symbol`, `null` and `undefined`.

An object

- Is capable of storing multiple values as properties.
- Can be created with `{}`, for instance: `{name: "John", age: 30}`. There are other kinds of objects in JavaScript: functions, for example, are objects.

## A primitive as an object

The “object wrappers” are different for each primitive type and are called: `String`, `Number`, `Boolean`, `Symbol` and `BigInt`. Thus, they provide different sets of methods.

For instance, there exists a string method [str.toUpperCase()](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/String/toUpperCase) that returns a capitalized `str`.

Here’s how it works:

```javascript
let str = "Hello";

alert(str.toUpperCase());//HELLO
```

Simple, right? Here’s what actually happens in `str.toUpperCase()`:

1. The string `str` is a primitive. So in the moment of accessing its property, a special object is created that knows the value of the string, and has useful methods, like `toUpperCase()`.
2. That method runs and returns a new string (shown by `alert`).
3. The special object is destroyed, leaving the primitive `str` alone.

A number has methods of its own, for instance, [toFixed(n)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toFixed) rounds the number to the given precision:

```javascript
let n = 1.23456

alert(n.toFixed(2));//1.23
```

**Constructors `String/Number/Boolean` are for internal use only**

Some languages like Java allow us to explicitly create “wrapper objects” for primitives using a syntax like `new Number(1)` or `new Boolean(false)`.

In JavaScript, that’s also possible for historical reasons, but highly **unrecommended**. Things will go crazy in several places.

For instance:

```javascript
alert( typeof 0 ); // "number"

alert( typeof new Number(0) ); // "object"!
```

Objects are always truthy in `if`, so here the alert will show up:

```javascript
let zero = new Number(0);

if (zero) { // zero is true, because it's an object
  alert( "zero is truthy!?!" );
}
```

On the other hand, using the same functions `String/Number/Boolean` without `new` is a totally sane and useful thing. They convert a value to the corresponding type: to a string, a number, or a boolean (primitive).

For example, this is entirely valid:

```javascript
let num = Number("123"); // convert a string to number
```

**null/undefined have no methods**

**The special primitives `null` and `undefined` are exceptions. They have no corresponding “wrapper objects” and provide no methods**. In a sense, they are “the most primitive”.

An attempt to access a property of such value would give the error:

```javascript
alert(null.test); // error
```