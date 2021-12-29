# Functions

## Function Declaration

To create a function we can use a *function declaration*.

```javascript
function name(parameter1, parameter2, ... parameterN) {
  ...body...
}
```

## Local variables

A variable declared inside a function is only visible inside that function.

For example:

```javascript
function showMessage() {
  let message = "Hello, I'm JavaScript!"; // local variable

  alert( message );
}

showMessage(); // Hello, I'm JavaScript!

alert( message ); // <-- Error! The variable is local to the function
```

## Outer variables

A function can access an outer variable as well, for example:

```javascript
let userName = 'John';

function showMessage() {
  let message = 'Hello, ' + userName;
  alert(message);
}

showMessage(); // Hello, John
```

**The outer variable is only used if there’s no local one.**

If a same-named variable is declared inside the function then it *shadows* the outer one. For instance, in the code below the function uses the local `userName`. The outer one is ignored:

```javascript
let userName = 'John';

function showMessage() {
  let userName = "Bob"; // declare a local variable

  let message = 'Hello, ' + userName; // Bob
  alert(message);
}

// the function will create and use its own userName
showMessage();

alert( userName ); // John, unchanged, the function did not access the outer variable
```

**Global variables**

Variables declared outside of any function, such as the outer `userName` in the code above, are called *global*.

Global variables are visible from any function (unless shadowed by locals).

It’s a good practice to minimize the use of global variables. Modern code has few or no globals. Most variables reside in their functions. Sometimes though, they can be useful to store project-level data.

## Parameters

We can pass arbitrary data to functions using parameters.

We have a variable `from` and pass it to the function. Please note: the function changes `from`, but the change is not seen outside, **because a function always gets a copy of the value**:

```javascript
function showMessage(from, text) {

  from = '*' + from + '*'; // make "from" look nicer

  alert( from + ': ' + text );
}

let from = "Ann";

showMessage(from, "Hello"); // *Ann*: Hello

// the value of "from" is the same, the function modified a local copy
alert( from ); // Ann
```

## Default values

We can specify the so-called “default” (to use if omitted) value for a parameter in the function declaration, using `=`:

```javascript
function showMessage(from, text = "no text given") {
  alert( from + ": " + text );
}

showMessage("Ann"); // Ann: no text given
```

Now if the `text` parameter is not passed, it will get the value `"no text given"`

Here `"no text given"` is a string, but it can be a more complex expression, which is only evaluated and assigned if the parameter is missing. So, this is also possible:

```javascript
function showMessage(from, text = anotherFunction()) {
  // anotherFunction() only executed if no text given
  // its result becomes the value of text
}
```

**Evaluation of default parameters**

In JavaScript, a default parameter is evaluated every time the function is called without the respective parameter.

In the example above, `anotherFunction()` isn’t called at all, if the `text` parameter is provided.

On the other hand, it’s independently called every time when `text` is missing.

**Alternative default parameters**

We can check if the parameter is passed during the function execution, by comparing it with `undefined`:

```javascript
function showMessage(text) {
  // ...

  if (text === undefined) { // if the parameter is missing
    text = 'empty message';
  }

  alert(text);
}

showMessage(); // empty message
```

…Or we could use the `||` operator:

```javascript
function showMessage(text) {
  // if text is undefined or otherwise falsy, set it to 'empty'
  text = text || 'empty';
  ...
}
```

Modern JavaScript engines support the [nullish coalescing operator](https://javascript.info/nullish-coalescing-operator) `??`, it’s better when most falsy values, such as `0`, should be considered “normal”:

```javascript
function showCount(count) {
  // if count is undefined or null, show "unknown"
  alert(count ?? "unknown");
}

showCount(0); // 0
showCount(null); // unknown
showCount(); // unknown
```

**A function with an empty** `return` **or without it returns** `undefined`

If a function does not return a value, it is the same as if it returns `undefined`:

```javascript
function doNothing() { /* empty */ }

alert( doNothing() === undefined ); // true
```

An empty `return` is also the same as `return undefined`:

```javascript
function doNothing() {
  return;
}

alert( doNothing() === undefined ); // true
```

**Never add a newline between `return` and the value**

For a long expression in `return`, it might be tempting to put it on a separate line, like this:

```javascript
return
 (some + long + expression + or + whatever * f(a) + f(b))
```

That doesn’t work, because JavaScript assumes a semicolon after `return`. That’ll work the same as:

```javascript
return;
 (some + long + expression + or + whatever * f(a) + f(b))
```

So, it effectively becomes an empty return.

If we want the returned expression to wrap across multiple lines, we should start it at the same line as `return`. Or at least put the opening parentheses there as follows:

```javascript
return (
  some + long + expression
  + or +
  whatever * f(a) + f(b)
  )
```

And it will work just as we expect it to.

