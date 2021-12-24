# JavaScript Fundamentals

## Hello, world!

### [The “script” tag](https://javascript.info/hello-world#the-script-tag)

The `<script>` tag has a few attributes that are rarely used nowadays but can still be found in old code:

**The** `type` **attribute:** `<script type=…>`

***The old HTML standard, HTML4, required a script to have a `type`. Usually it was `type="text/javascript"`. It’s not required anymore.*** Also, the modern HTML standard totally changed the meaning of this attribute. Now, it can be used for JavaScript modules. But that’s an advanced topic, we’ll talk about modules in another part of the tutorial.

**The** `language` **attribute:** `<script language=…>`

This attribute was meant to show the language of the script. ***This attribute no longer makes sense because JavaScript is the default language. There is no need to use it.***

### [External scripts](https://javascript.info/hello-world#external-scripts)

If we have a lot of JavaScript code, we can put it into a separate file.

Script files are attached to HTML with the `src` attribute:

```javascript
<script src="/path/to/script.js"></script>
```

Here, `/path/to/script.js` is an absolute path to the script from the site root. One can also provide a relative path from the current page. For instance, `src="script.js"`, just like `src="./script.js"`, would mean a file `"script.js"` in the current folder.

We can give a full URL as well. For instance:

```javascript
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js"></script>
```

**Please note:**

As a rule, only the simplest scripts are put into HTML. More complex ones reside in separate files.

***The benefit of a separate file is that the browser will download it and store it in its [cache](https://en.wikipedia.org/wiki/Web_cache).***

***Other pages that reference the same script will take it from the cache instead of downloading it, so the file is actually downloaded only once.***

That reduces traffic and makes pages faster.

**If** `src` **is set, the script content is ignored.**

A single `<script>` tag can’t have both the `src` attribute and code inside.

This won’t work:

```javascript
<script src="file.js">
  alert(1); // the content is ignored, because src is set
</script>
```

## Code structure

### Statements

Statements are syntax constructs and commands that perform actions.

### Semicolons

A semicolon may be omitted in most cases when a line break exists.

This would also work:

```javascript
alert('Hello')
alert('World')
```

Here, JavaScript interprets the line break as an “implicit” semicolon. This is called an [automatic semicolon insertion](https://tc39.github.io/ecma262/#sec-automatic-semicolon-insertion).

**In most cases, a newline implies a semicolon. But “in most cases” does not mean “always”!**

There are cases when a newline does not mean a semicolon. For example:

```javascript
alert(3 +
1
+ 2);
```

The code outputs `6` because JavaScript does not insert semicolons here. It is intuitively obvious that if the line ends with a plus `"+"`, then it is an “incomplete expression”, so a semicolon there would be incorrect. And in this case, that works as intended.

**But there are situations where JavaScript “fails” to assume a semicolon where it is really needed.**

```javascript
alert("Hello")

[1, 2].forEach(alert);

//等同于

alert("Hello")[1, 2].forEach(alert);
```

***We recommend putting semicolons between statements even if they are separated by newlines.*** 

## The modern mode, "use strict"

ECMAScript 5 (ES5) appeared. It added new features to the language and modified some of the existing ones. ***To keep the old code working, most such modifications are off by default. You need to explicitly enable them with a special directive: `"use strict"`.***

### "use strict"

 When it is located at the top of a script, the whole script works the “modern” way.

```javascript
"use strict";

// this code works the modern way
...
```

`"use strict"` can be put at the beginning of a function. ***Doing that enables strict mode in that function only. But usually people use it for the whole script.***

**Ensure that “use strict” is at the top**

Please make sure that `"use strict"` is at the top of your scripts, otherwise strict mode may not be enabled.

Strict mode isn’t enabled here:

```javascript
alert("some code");
// "use strict" below is ignored--it must be at the top

"use strict";

// strict mode is not activated
```

Only comments may appear above `"use strict"`.

**There’s no way to cancel** `use strict`

There is no directive like `"no use strict"` that reverts the engine to old behavior.

Once we enter strict mode, there’s no going back.

### Browser console

When you use a [developer console](https://javascript.info/devtools) to run code, please note that it doesn’t `use strict` by default.

So, how to actually `use strict` in the console? 

First, you can try to press Shift+Enter to input multiple lines, and put `use strict` on top, like this:

```javascript
'use strict'; <Shift+Enter for a newline>
//  ...your code
<Enter to run>
```

It works in most browsers, namely Firefox and Chrome.

If it doesn’t, e.g. in an old browser, there’s an ugly, but reliable way to ensure `use strict`. Put it inside this kind of wrapper:

```javascript
(function() {
  'use strict';

  // ...your code here...
})()
```

### Should we "use strict"?

Modern JavaScript supports “classes” and “modules” – advanced language structures, that enable `use strict` automatically. So we don’t need to add the `"use strict"` directive, if we use them.

**So, for now `"use strict";` is a welcome guest at the top of your scripts. Later, when your code is all in classes and modules, you may omit it.**

## Variables

To create a variable in JavaScript, use the `let` keyword.

**Declaring twice triggers an error**

A variable should be declared only once.

A repeated declaration of the same variable is an error:

```javascript
let message = "This";

// repeated 'let' leads to an error
let message = "That"; // SyntaxError: 'message' has already been declared
```

**Functional languages**

It’s interesting to note that there exist [functional](https://en.wikipedia.org/wiki/Functional_programming) programming languages, like [Scala](http://www.scala-lang.org/) or [Erlang](http://www.erlang.org/) that forbid changing variable values.

In such languages, once the value is stored “in the box”, it’s there forever. If we need to store something else, the language forces us to create a new box (declare a new variable). We can’t reuse the old one.

Though it may seem a little odd at first sight, these languages are quite capable of serious development. More than that, there are areas like parallel computations where this limitation confers certain benefits. Studying such a language (even if you’re not planning to use it soon) is recommended to broaden the mind.

### Variable naming

There are two limitations on variable names in JavaScript:

1. The name must contain only letters, digits, or the symbols `$` and `_`.
2. The first character must not be a digit.

When the name contains multiple words, [camelCase](https://en.wikipedia.org/wiki/CamelCase) is commonly used.  That is: words go one after another, each word except first starting with a capital letter: `myVeryLongName`.

What’s interesting – the dollar sign `'$'` and the underscore `'_'` can also be used in names. They are regular symbols, just like letters, without any special meaning.

***Case matters***

Variables named `apple` and `AppLE` are two different variables.

**An assignment without** `use strict`

Normally, we need to define a variable before using it. But in the old times, it was technically possible to create a variable by a mere assignment of the value without using `let`. This still works now if we don’t put `use strict` in our scripts to maintain compatibility with old scripts.

```javascript
// note: no "use strict" in this example

num = 5; // the variable "num" is created if it didn't exist

alert(num); // 5
```

This is a bad practice and would cause an error in strict mode:

```javascript
"use strict";

num = 5; // error: num is not defined
```

### Constants

To declare a constant (unchanging) variable, use `const` instead of `let`:

```javascript
const myBirthday = '18.04.1982'
```

Variables declared using `const` are called “constants”. They cannot be reassigned. An attempt to do so would cause an error:

```javascript
const myBirthday = '18.04.1982';

myBirthday = '01.01.2001'; // error, can't reassign the constant!
```

## DataTypes

There are eight basic data types in JavaScript. 

We can put any type in a variable. For example, a variable can at one moment be a string and then store a number:

```javascript
// no error
let message = "hello";
message = 123456;
```

Programming languages that allow such things, such as JavaScript, are called “***dynamically typed***”, meaning that there exist data types, but variables are not bound to any of them.

### Number

```javascript
let n = 123;
n = 12.345
```

The *number* type represents ***both integer and floating point*** numbers.

Besides regular numbers, there are so-called “special numeric values” which also belong to this data type: `Infinity`, `-Infinity` and `NaN`.

- `Infinity` represents the mathematical [Infinity](https://en.wikipedia.org/wiki/Infinity) ∞. It is a special value that’s greater than any number.

  We can get it as a result of division by zero:

  ```javascript
  alert( 1 / 0 ); // Infinity
  ```

  Or just reference it directly:

  ```javascript
  alert( "not a number" / 2 ); // NaN, such division is erroneous
  ```

- `NaN` represents a computational error. It is a result of an incorrect or an undefined mathematical operation, for instance:

  ```javascript
  alert( "not a number" / 2 ); // NaN, such division is erroneous
  ```

  `NaN` is sticky. Any further operation on `NaN` returns `NaN`:

  ```javascript
  alert( "not a number" / 2 + 5 ); // NaN
  ```

  So, if there’s a `NaN` somewhere in a mathematical expression, it propagates to the whole result.

**Mathematical operations are safe**

Doing maths is “safe” in JavaScript. We can do anything: divide by zero, treat non-numeric strings as numbers, etc.

The script will never stop with a fatal error (“die”). At worst, we’ll get `NaN` as the result.

### BigInt

In JavaScript, the “number” type cannot represent integer values larger than $2^{53} - 1$ (that’s `9007199254740991`), or less than $-(2^{53}-1)$ for negatives.

`BigInt` type was recently added to the language to represent integers of arbitrary length.

A `BigInt` value is created by appending `n` to the end of an integer:

```javascript
// the "n" at the end means it's a BigInt
const bigInt = 1234567890123456789012345678901234567890n;
```

### String

In JavaScript, there are 3 types of quotes.

1. Double quotes: `"Hello"`.
2. Single quotes: `'Hello'`.
3. Backticks: ``Hello``.

Double and single quotes are “simple” quotes. There’s practically no difference between them in JavaScript.

Backticks are “extended functionality” quotes. They allow us to embed variables and expressions into a string by wrapping them in `${…}`, for example:

```javascript
let name = "John";

// embed a variable
alert( `Hello, ${name}!` ); // Hello, John!

// embed an expression
alert( `the result is ${1 + 2}` ); // the result is 3
```

**There is no** *character* **type.**

### Boolean (logical type)

The boolean type has only two values: `true` and `false`.

### The "null" value

The special `null` value does not belong to any of the types described above.

It forms a separate type of its own which contains only the `null` value:

```javascript
let age = null;
```

In JavaScript, `null` is not a “reference to a non-existing object” or a “null pointer” like in some other languages.

It’s just a special value which represents “nothing”, “empty” or “value unknown”.

The code above states that `age` is unknown.

### The "undefined" value

The special value `undefined` also stands apart. It makes a type of its own, just like `null`.

The meaning of `undefined` is “value is not assigned”.

If a variable is declared, but not assigned, then its value is `undefined`:

```javascript
let age;
alert(age); // shows "undefined"
```

Technically, it is possible to explicitly assign `undefined` to a variable:

```javascript
let age = 100;

// change the value to undefined
age = undefined;

alert(age); // "undefined"
```

…But we don’t recommend doing that. Normally, one uses `null` to assign an “empty” or “unknown” value to a variable, while `undefined` is reserved as a default initial value for unassigned things.

### Object and Symbols

The `object` type is special.

All other types are called “primitive” because their values can contain only a single thing (be it a string or a number or whatever). In contrast, objects are used to store collections of data and more complex entities.

The `symbol` type is used to create unique identifiers for objects.

### The typeof operator

The `typeof` operator returns the type of the argument. It’s useful when we want to process values of different types differently or just want to do a quick check.

A call to `typeof x` returns a string with the type name:

```javascript
typeof undefined // "undefined"

typeof 0 // "number"

typeof 10n // "bigint"

typeof true // "boolean"

typeof "foo" // "string"

typeof Symbol("id") // "symbol"

typeof Math // "object"  (1)

typeof null // "object"  (2)

typeof alert // "function"  (3)
```

The last three lines may need additional explanation:

1. `Math` is a built-in object that provides mathematical operations. We will learn it in the chapter [Numbers](https://javascript.info/number). **Here, it serves just as an example of an object.**
2. The result of `typeof null` is `"object"`. That’s an officially recognized error in `typeof`, coming from very early days of JavaScript and kept for compatibility. **Definitely, `null` is not an object. It is a special value with a separate type of its own. The behavior of `typeof` is wrong here.**
3. The result of `typeof alert` is `"function"`, because `alert` is a function. We’ll study functions in the next chapters where we’ll also see that there’s no special “function” type in JavaScript. **Functions belong to the object type. But `typeof` treats them differently, returning `"function"`.** That also comes from the early days of JavaScript. Technically, such behavior isn’t correct, but can be convenient in practice.

**The** `typeof(x)` **syntax**

You may also come across another syntax: `typeof(x)`. It’s the same as `typeof x`.

To put it clear: `typeof` is an operator, not a function. The parentheses here aren’t a part of `typeof`. It’s the kind of parentheses used for mathematical grouping.

Usually, such parentheses contain a mathematical expression, such as `(2 + 2)`, but here they contain only one argument `(x)`. Syntactically, they allow to avoid a space between the `typeof` operator and its argument, and some people like it.

Some people prefer `typeof(x)`, although the `typeof x` syntax is much more common.























