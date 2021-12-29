# Optional chaining '?.'

The optional chaining `?.` is a safe way to access nested object properties, even if an intermediate property doesn’t exist.

## The "non-existing property" problem

As an example, let’s say we have `user` objects that hold the information about our users.

Most of our users have addresses in `user.address` property, with the street `user.address.street`, but some did not provide them.

In such case, when we attempt to get `user.address.street`, and the user happens to be without an address, we get an error:

```javascript
let user = {};// a user without “address” property
alert(user.address.street)//Error
```

That’s the expected result. JavaScript works like this. As `user.address` is `undefined`, an attempt to get `user.address.street` fails with an error.

In many practical cases we’d prefer to get `undefined` instead of an error here (meaning “no street”).

…And another example. In the web development, we can get an object that corresponds to a web page element using a special method call, such as `document.querySelector('.elem')`, and it returns `null` when there’s no such element.

```javascript
//document.querySelector('.elem') is null if there's no element
let html = document.querySelector('.elem').innerHTML;//Error if it's null
```

Once again, if the element doesn’t exist, we’ll get an error accessing `.innerHTML` of `null`.

And in some cases, when the absence of the element is normal, we’d like to avoid the error and just accept `html = null` as the result.

The obvious solution would be to check the value using `if` or the conditional operator `?`, before accessing its property, like this:

```javascript
let user = {};
alert(user.address ? user.address.street : undefined);
```

It works, there’s no error… But it’s quite inelegant. As you can see, the `"user.address"` appears twice in the code. For more deeply nested properties, that becomes a problem as more repetitions are required.

Don’t even care to, as there’s a better way to write it, using the `&&` operator:

```javascript
let user = {};
alert(user.address && user.address.street && user.address.street.name);//undefined (no error)
```

AND’ing the whole path to the property ensures that all components exist (if not, the evaluation stops), but also isn’t ideal.

As you can see, property names are still duplicated in the code. E.g. in the code above, `user.address` appears three times.

That’s why the optional chaining `?.` was added to the language. To solve this problem once and for all!

## Optional chaining

**The optional chaining `?.` stops the evaluation if the value before `?.` is `undefined` or `null` and returns `undefined`.**

In other words, `value?.prop`:

- works as `value.prop`, if `value` exists,
- otherwise (when `value` is `undefined/null`) it returns `undefined`.

Here’s the safe way to access `user.address.street` using `?.`:

```javascript
let user = {};//user has no address
alert(user?.address?.street); //undefined(no error)
```

**The variable before** `?.` **must be declared**

If there’s no variable `user` at all, then `user?.anything` triggers an error:

```javascript
//ReferenceError:user is not defined
user?.address;
```

The variable must be declared (e.g. `let/const/var user` or as a function parameter). The optional chaining works only for declared variables.

## Short-circuiting

As it was said before, the `?.` immediately stops (“short-circuits”) the evaluation if the left part doesn’t exist.

So, if there are any further function calls or side effects, they don’t occur.

For instance:

```javascript
let user = null;
let x = 0;

user?.sayHi(x++); // no "sayHi", so the execution doesn't reach x++

alert(x); // 0, value not incremented
```

## Other variants:"?.()","?.[]"

The optional chaining `?.` is not an operator, but a **special syntax construct**, that also works with functions and square brackets.

For example, `?.()` is used to call a function that may not exist.

In the code below, some of our users have `admin` method, and some don’t:

```javascript
let userAdmin = {
    admin(){
        alert("I am admin");
    }
};

let userGuest = {};

userAdmin.admin?.();//I am admin
userGuset.admin?.();//nothing (no such method)
```

The `?.[]` syntax also works, if we’d like to use brackets `[]` to access properties instead of dot `.`. Similar to previous cases, it allows to safely read a property from an object that may not exist.

```javascript
let key = "firstName";

let user1 = {
    firstName:"John"
};

let user2 = null;

alert(user1?.[key]);//John
alert(user2?.[key]);//undefined
```

Also we can use ?. with delete:

```javascript
delete user?.name;//delete user.name if user exists
```

**We can use** `?.` **for safe reading and deleting, but not writing**

The optional chaining `?.` has no use at the left side of an assignment.

For example:

```javascript
let user = null;

user?.name = "John"; // Error, doesn't work
// because it evaluates to undefined = "John"
```

It’s just not that smart.

