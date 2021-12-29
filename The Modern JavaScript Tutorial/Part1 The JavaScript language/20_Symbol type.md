# Symbol type

By specification, **object property keys may be either of string type, or of symbol type**. Not numbers, not booleans, only strings or symbols, these two types.

## Symbols

**A "symbol" represents a unique identifier.**

A value of this symbol can be created using Symbol():

```javascript
// id is a new symbol
let id = Symbol()
```

Upon creation, we can give symbol a description (also called a symbol name), mostly useful for debugging purposes:

```javascript
// id is a symbol with the description "id"
let id = Symbol("id")
```

Symbols are guaranteed to be unique. Even if we create many symbols with the same description, they are different values. The description is just a label that doesn’t affect anything.

For instance, here are two symbols with the same description – they are not equal:

```javascript
let id1 = Symbol("id");
let id2 = Symbol("id");

alert(id1 == id2);//false
```

**Symbols don’t auto-convert to a string**

Most values in javascript support implicit conversion to a string. For instance, we can alert almost any value, and it will work. Symbols are special. They don't auto-convert.

For instance, this `alert` will show an error:

```javascript
let id = Symbol("id");
alert(id);//Type Error:Can't convert a Symbol value to a string
```

That’s a “language guard” against messing up, because strings and symbols are fundamentally different and should not accidentally convert one into another.

If we really want to show a symbol, we need to explicitly call `.toString()` on it, like here:

```javascript
let id = Symbol("id");
alert(id.toString());//Symbol(id), now it works
```

Or get `symbol.description` property to show the description only:

```javascript
let id = Symbol("id");
alert(id.description);//id
```

## "Hidden" properties

Symbols allow us to create “hidden” properties of an object, that no other part of code can accidentally access or overwrite.

For instance, if we’re working with `user` objects, that belong to a third-party code. We’d like to add identifiers to them.

Let’s use a symbol key for it:

```javascript
let user = {
    name:"John"
};

let id = Symbol("id");

user[id] = 1;

alert(user[id]);// we can access the data using the symbol as the key
```

What’s the benefit of using `Symbol("id")` over a string `"id"`?

As `user` objects belongs to another code, and that code also works with them, we shouldn’t just add any fields to it. That’s unsafe. But a symbol cannot be accessed accidentally, the third-party code probably won’t even see it, so it’s probably all right to do.

Also, imagine that another script wants to have its own identifier inside `user`, for its own purposes. That may be another JavaScript library, so that the scripts are completely unaware of each other.

Then that script can create its own `Symbol("id")`, like this:

```javascript
// ...
let id = Symbol("id");

user[id] = "Their id value";
```

There will be no conflict between our and their identifiers, because symbols are always different, even if they have the same name.

…But if we used a string `"id"` instead of a symbol for the same purpose, then there *would* be a conflict:

```javascript
let user = { name: "John" };

// Our script uses "id" property
user.id = "Our id value";

// ...Another script also wants "id" for its purposes...

user.id = "Their id value"
// Boom! overwritten by another script!
```

**Symbols in an object literal**

If we want to use a symbol in an object literal `{...}`, we need square brackets around it.

Like this:

```javascript
let id = Symbol("id");

let user = {
    name:"John",
    [id]:123,
};
```

**Symbols are skipped by for...in**

Symbolic properties do not participate in for..in loop.

For instance:

```javascript
let id = Symbol("id");
let user = {
    name:"John",
    age:30,
    [id]:123
};

for(let key in user) alert(key);//name,age (no symbols)

//the direct access by the symbol works
alert("Direct: " + user[id]);
```

Object.keys(user) also ignores them. That’s a part of the general “hiding symbolic properties” principle. If another script or a library loops over our object, it won’t unexpectedly access a symbolic property.

In contrast, Object.assign copies both string and symbol properties:

```javascript
let id = Symbol("id");
let user = {
    [id]:123
};

let clone = Object.assign({}, user);

alert(clone[id]);//123
```

## Global symbols

As we’ve seen, usually all symbols are different, even if they have the same name. But sometimes we want same-named symbols to be same entities. For instance, different parts of our application want to access symbol `"id"` meaning exactly the same property.

To achieve that, there exists a *global symbol registry*. We can create symbols in it and access them later, and it guarantees that repeated accesses by the same name return exactly the same symbol.

In order to read (create if absent) a symbol from the registry, use **Symbol.for(key)**.

That call checks the global registry, and if there’s a symbol described as `key`, then returns it, otherwise creates a new symbol `Symbol(key)` and stores it in the registry by the given `key`.

For instance:

```javascript
//read from the global registry
let id = Symbol.for("id");//if the symbol did not exist, it is created

//read it again(maybe from another part of the code)
let idAgain = Symbol.for("id");

//the same symbol
alert(id === idAgain);//true
```

Symbols inside the registry are called ***global symbols***.  If we want an application-wide symbol, accessible everywhere in the code – that’s what they are for.

**Symbol.keyFor**

For global symbols, not only `Symbol.for(key)` returns a symbol by name, but there’s a reverse call: `Symbol.keyFor(sym)`, that does the reverse: returns a name by a global symbol.

```javascript
// get symbol by name
let sym = Symbol.for("name");
let sym2 = Symbol.for("id");

// get name by symbol
alert(Symbol.keyFor(sym));// name
alert(Symbol.KeyFor(sym2));//id
```

The `Symbol.keyFor` internally uses the global symbol registry to look up the key for the symbol. So it doesn’t work for non-global symbols. If the symbol is not global, it won’t be able to find it and returns `undefined`.

```javascript
let globalSymbol = Symbol.for("name");
let localSymbol = Symbol("name");

alert(Symbol.keyFor(globalSymbol));//name, global symbol
alert(Symbol.keyFor(localSymbol));//undefined, not global.

alert(localSymbol.description);//name
alert(globalSymbol.descrption);//name
```

## System symbols

There exist many “system” symbols that JavaScript uses internally, and we can use them to fine-tune various aspects of our objects.

They are listed in the specification in the [Well-known symbols](https://tc39.github.io/ecma262/#sec-well-known-symbols) table:

- `Symbol.hasInstance`
- `Symbol.isConcatSpreadable`
- `Symbol.iterator`
- `Symbol.toPrimitive`
- …and so on.

For instance, `Symbol.toPrimitive` allows us to describe object to primitive conversion. We’ll see its use very soon.