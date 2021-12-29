# Object references and copying

**One of the fundamental differences of objects versus primitives is that objects are stored and copied “by reference”, whereas primitive values: strings, numbers, booleans, etc – are always copied “as a whole value”.**

Let’s start with a primitive, such as a string.

Here we put a copy of `message` into `phrase`:

```javascript
let message = "Hello!";
let phrase = message;
```

**As a result we have two independent variables, each one storing the string `"Hello!"`.**

![image-20211228091719492](C:\Users\03010673\Documents\typora\The Modern JavaScript Tutorial\Part1 The JavaScript language\images\Object references and copying)

**A variable assigned to an object stores not the object itself, but its “address in memory” – in other words “a reference” to it.**

**When an object variable is copied, the reference is copied, but the object itself is not duplicated.**

## Comparison by reference

Two objects are equal only if they are the same object.

For instance, here `a` and `b` reference the same object, thus they are equal:

```javascript
let a = {};
let b = a; // copy the reference

alert( a == b ); // true, both variables reference the same object
alert( a === b ); // true
```

**For comparisons like `obj1 > obj2` or for a comparison against a primitive `obj == 5`, objects are converted to primitives**. We’ll study how object conversions work very soon, but to tell the truth, such comparisons are needed very rarely – usually they appear as a result of a programming mistake.

## Cloning and merging, Object.assign

But what if we need to duplicate an object? Create an independent copy, a clone?

 If we really want that, then we need to create a new object and replicate the structure of the existing one by iterating over its properties and copying them on the primitive level.

Like this:

```javascript
let user = {
  name: "John",
  age: 30
};

let clone = {}; // the new empty object

// let's copy all user properties into it
for (let key in user) {
  clone[key] = user[key];
}

// now clone is a fully independent object with the same content
clone.name = "Pete"; // changed the data in it

alert( user.name ); // still John in the original object
```

Also we can use the method [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) for that.

The syntax is:

```javascript
Object.assign(dest, [src1, src2, src3...])
```

- The first argument `dest` is a target object.
- Further arguments `src1, ..., srcN` (can be as many as needed) are source objects.
- It copies the properties of all source objects `src1, ..., srcN` into the target `dest`. In other words, properties of all arguments starting from the second are copied into the first object.
- The call returns `dest`.

For instance, we can use it to merge several objects into one:

```javascript
let user = { name: "John" };

let permissions1 = { canView: true };
let permissions2 = { canEdit: true };

// copies all properties from permissions1 and permissions2 into user
Object.assign(user, permissions1, permissions2);

// now user = { name: "John", canView: true, canEdit: true }
```

If the copied property name already exists, it gets overwritten:

```javascript
let user = { name: "John" };

Object.assign(user, { name: "Pete" });

alert(user.name); // now user = { name: "Pete" }
```

We also can use `Object.assign` to replace `for..in` loop for simple cloning:

```javascript
let user = {
  name: "John",
  age: 30
};

let clone = Object.assign({}, user);
```

It copies all properties of `user` into the empty object and returns it.

## Netsted cloning

We should use a cloning loop that examines each value of `user[key]` and, if it’s an object, then replicate its structure as well. That is called a “deep cloning”.

**We can use recursion to implement it. Or, to not reinvent the wheel, take an existing implementation, for instance [_.cloneDeep(obj)](https://lodash.com/docs#cloneDeep) from the JavaScript library [lodash](https://lodash.com/).**

**Const objects can be modified**

An important side effect of storing objects as references is that an object declared as `const` *can* be modified.

For instance:

```javascript
const user = {
  name: "John"
};

user.name = "Pete"; // (*)

alert(user.name); // Pete
```

It might seem that the line `(*)` would cause an error, but it does not. The value of `user` is constant, it must always reference the same object, but properties of that object are free to change.

In other words, the `const user` gives an error only if we try to set `user=...` as a whole.

That said, if we really need to make constant object properties, it’s also possible, but using totally different methods. We’ll mention that in the chapter [Property flags and descriptors](https://javascript.info/property-descriptors).