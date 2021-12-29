# Objects

An object can be created with figure brackets `{…}` with an optional list of *properties*. A property is a “key: value” pair, **where `key` is a string** (also called a “property name”), and `value` can be anything.

An empty object (“empty cabinet”) can be created using one of two syntaxes:

```javascript
let user = new Object();//"object constructor" syntax 
let user = {}; //"object literal" syntax
```

Usually, the figure brackets `{...}` are used. That declaration is called an *object literal*.

## Literals and properties

We can immediately put some properties into `{...}` as “key: value” pairs:

```javascript
let user = {     // an object
  name: "John",  // by key "name" store value "John"
  age: 30        // by key "age" store value 30
};
```

Property values are accessible using the dot notation:

```javascript
alert(user.name);
alert(user.age);
```

The value can be of any type. Let's add a boolean one:

```javascript
user.isAdmin = true;
```

To remove a property, we can use `delete` operator:

```javascript
delete user.age;
```

We can also use multiword property names, but then **they must be quoted**:

```javascript
let user = {
	name:"John",
	age:30,
	"like birds":true
};
```

The last property in the list may end with a comma:

```javascript
let user = {
  name: "John",
  age: 30,
}
```

That is called a “**trailing**” or “**hanging**” comma. Makes it easier to add/remove/move around properties, because all lines become alike.

## Square brackets

For multiword properties, the dot access doesn’t work:

```javascript
// this would give a syntax error
user.likes birds = true
```

There’s an alternative “square bracket notation” that works with any string:

```javascript
let user = {};

// set
user["likes birds"] = true;

// get
alert(user["likes birds"]); // true

// delete
delete user["likes birds"];
```

Square brackets also **provide a way to obtain the property name as the result of any expression** – as opposed to a literal string – like from a variable as follows:

```javascript
let key = "likes birds";

// same as user["likes birds"] = true;
user[key] = true;
```

**Computed properties**

**We can use square brackets in an object literal, when creating an object. That’s called *computed properties*.**

For instance:

```javascript
let fruit = prompt("Which fruit to buy?", "apple");

let bag = {
  [fruit]: 5, // the name of the property is taken from the variable fruit
};

alert( bag.apple ); // 5 if fruit="apple"
```

The meaning of a computed property is simple: `[fruit]` means that the property name should be taken from `fruit`.

## Property value shorthand

In real code we often use existing variables as values for property names.

For instance:

```javascript
function makeUser(name, age) {
  return {
    name: name,
    age: age,
    // ...other properties
  };
}

let user = makeUser("John", 30);
alert(user.name); // John
```

In the example above, properties have the same names as variables. The use-case of making a property from a variable is so common, that there’s a special *property value shorthand* to make it shorter.

Instead of `name:name` we can just write `name`, like this:

```javascript
function makeUser(name, age) {
  return {
    name, // same as name: name
    age,  // same as age: age
    // ...
  };
}
```

We can use both normal properties and shorthands in the same object:

```javascript
let user = {
  name,  // same as name:name
  age: 30
};
```

## Property names limitations

As we already know, a variable cannot have a name equal to one of language-reserved words like “for”, “let”, “return” etc.

**But for an object property, there’s no such restriction**:

```javascript
// these properties are all right
let obj = {
  for: 1,
  let: 2,
  return: 3
};

alert( obj.for + obj.let + obj.return );  // 6
```

In short, there are no limitations on property names. They can be any strings or symbols.

**Other types are automatically converted to strings**.

For instance, a number `0` becomes a string `"0"` when used as a property key:

```javascript
let obj = {
  0: "test" // same as "0": "test"
};

// both alerts access the same property (the number 0 is converted to string "0")
alert( obj["0"] ); // test
alert( obj[0] ); // test (same property)
```

There’s a minor gotcha with a special property named `__proto__`. We can’t set it to a non-object value:

```javascript
let obj = {};
obj.__proto__ = 5; // assign a number
alert(obj.__proto__); // [object Object] - the value is an object, didn't work as intended
```

## Property existence test, "in" operator

A notable feature of objects in JavaScript, compared to many other languages, is that it’s possible to access any property. There will be no error if the property doesn’t exist!

Reading a non-existing property just returns `undefined`. So we can easily test whether the property exists:

```javascript
let user = {};

alert( user.noSuchProperty === undefined ); // true means "no such property"
```

There’s also a special operator `"in"` for that.

The syntax is:

```javascript
"key" in object
```

For instance:

```javascript
let user = { name: "John", age: 30 };

alert( "age" in user ); // true, user.age exists
alert( "blabla" in user ); // false, user.blabla doesn't exist
```

Please note that on the left side of `in` there must be a *property name*. That’s usually a quoted string.

If we omit quotes, that means a variable, it should contain the actual name to be tested. For instance:

```javascript
let user = { age: 30 };

let key = "age";
alert( key in user ); // true, property "age" exists
```

Why does the `in` operator exist? Isn’t it enough to compare against `undefined`?

Well, most of the time the comparison with `undefined` works fine. But there’s a special case when it fails, but `"in"` works correctly.

It’s when an object property exists, but stores `undefined`:

```javascript
let obj = {
  test: undefined
};

alert( obj.test ); // it's undefined, so - no such property?

alert( "test" in obj ); // true, the property does exist!
```

Situations like this happen very rarely, because `undefined` should not be explicitly assigned. We mostly use `null` for “unknown” or “empty” values. So the `in` operator is an exotic guest in the code.

## The "for...in" loop

To walk over all keys of an object, there exists a special form of the loop: `for..in`. This is a completely different thing from the `for(;;)` construct that we studied before.

The syntax:

```javascript
for (key in object) {
  // executes the body for each key among object properties
}
```

For instance, let’s output all properties of `user`:

```javascript
let user = {
  name: "John",
  age: 30,
  isAdmin: true
};

for (let key in user) {
  // keys
  alert( key );  // name, age, isAdmin
  // values for the keys
  alert( user[key] ); // John, 30, true
}
```

**Ordered like an object**

The short answer is: “ordered in a special fashion”: integer properties are sorted, others appear in creation order.

As an example, let’s consider an object with the phone codes:

```javascript
let codes = {
  "49": "Germany",
  "41": "Switzerland",
  "44": "Great Britain",
  // ..,
  "1": "USA"
};

for (let code in codes) {
  alert(code); // 1, 41, 44, 49
}
```

…On the other hand, if the keys are non-integer, then they are listed in the creation order, for instance:

```javascript
let user = {
  name: "John",
  surname: "Smith"
};
user.age = 25; // add one more

// non-integer properties are listed in the creation order
for (let prop in user) {
  alert( prop ); // name, surname, age
}
```

So, to fix the issue with the phone codes, we can “cheat” by making the codes non-integer. Adding a plus `"+"` sign before each code is enough.

Like this:

```javascript
let codes = {
  "+49": "Germany",
  "+41": "Switzerland",
  "+44": "Great Britain",
  // ..,
  "+1": "USA"
};

for (let code in codes) {
  alert( +code ); // 49, 41, 44, 1
}
```

