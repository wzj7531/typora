# Object methods, "this"

## Method examples

```javascript
let user = {
    name:"John",
    age:"30"
};
user.sayHi = function(){
    alert("Hello!");
};
user.sayHi();
```

Here we’ve just used a Function Expression to create a function and assign it to the property `user.sayHi` of the object.

**A function that is a property of an object is called its *method*.**

**Method shorthand**

There is a shorter syntax for methods in an object literal:

```javascript
user = {
    sayHi:function(){
        alert("Hello");
    }
};

//shorthand
user = {
    sayHi(){ //same as "sayHi:function(){...}"
        alert("Hello");
    }
};
```

## "this" in methods

It’s common that an object method needs to access the information stored in the object to do its job.

**To access the object, a method can use the `this` keyword.**

For instance:

```javascript
let user = {
    name:"John",
    age:30,
    
    sayHi(){
        //"this" is the "current object"
        alert(this.name);
    }
};
user.sayHi(); //John
```

The value of `this` is the object “before dot”, the one used to call the method. Here during the execution of `user.sayHi()`, the value of `this` will be `user`.

## "this" is not bound

In JavaScript, keyword `this` behaves unlike most other programming languages. It can be used in any function, even if it’s not a method of an object.

There's no syntax error in the following example:

```javascript
function sayHi(){
    alert(this.name);
}
```

The value of this is evaluated during the run-time, depending on the context.

For instance, here the same function is assigned to two different objects and has different "this" in the calls:

```javascript
let user = {name:"John"};
let admin = {name:"admin"};

function sayHi(){
    alert(this.name);
}

user.f = sayHi;
admin.f = sayHi;

//these calls have different this
//"this" inside the function is the object "before the dot"
user.f();// this == user
admin.f();//this == admin

admin['f']();//Admin(dot or [] access the method - doesn't matter)
```

The rule is simple: if `obj.f()` is called, then `this` is `obj` during the call of `f`. So it’s either `user` or `admin` in the example above.

**Calling without an object:** `this == undefined`

We can even call the function without an object at all:

```javascript
function sayHi() {
  alert(this);
}

sayHi(); // undefined
```

In this case `this` is `undefined` in strict mode. If we try to access `this.name`, there will be an error.

In non-strict mode the value of `this` in such case will be the *global object* (`window` in a browser, we’ll get to it later in the chapter [Global object](https://javascript.info/global-object)). This is a historical behavior that `"use strict"` fixes.

Usually such call is a programming error. If there’s `this` inside a function, it expects to be called in an object context.

**The consequences of unbound** `this`

If you come from another programming language, then you are probably used to the idea of a "bound `this`", where methods defined in an object always have `this` referencing that object.

In JavaScript `this` is “free”, its value is evaluated at call-time and does not depend on where the method was declared, but rather on what object is “**before the dot**”.

## Arrow functions have no "this"

Arrow functions are special: they don’t have their “own” `this`. If we reference `this` from such a function, it’s taken from the outer “normal” function.

For instance, here `arrow()` uses `this` from the outer `user.sayHi()` method:

```javascript
let user = {
  firstName: "Ilya",
  sayHi() {
    let arrow = () => alert(this.firstName);
    arrow();
  }
};

user.sayHi(); // Ilya
```

That’s a special feature of arrow functions, it’s useful when we actually do not want to have a separate `this`, but rather to take it from the outer context. Later in the chapter [Arrow functions revisited](https://javascript.info/arrow-functions) we’ll go more deeply into arrow functions.