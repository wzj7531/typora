# Nullish coalescing operator '??'

The nullish coalescing operator is written as two question marks `??`.

As it treats `null` and `undefined` similarly, we’ll use a special term here, in this article. We’ll say that an expression is “defined” when it’s neither `null` nor `undefined`.

The result of `a ?? b` is:

- if `a` is defined, then `a`,
- if `a` isn’t defined, then `b`.

**In other words, `??` returns the first argument if it’s not `null/undefined`. Otherwise, the second one.**

The nullish coalescing operator isn’t anything completely new. It’s just a nice syntax to get the first “defined” value of the two.

We can rewrite `result = a ?? b` using the operators that we already know, like this:

```javascript
result = (a !== null && a !== undefined) ? a : b;
```

The common use case for `??` is to provide a default value for a potentially undefined variable.

For example, here we show `user` if defined, otherwise `Anonymous`:

```javascript
let user;

alert(user ?? "Anonymous"); // Anonymous (user not defined)
```

We can also use a sequence of `??` to select the first value from a list that isn’t `null/undefined`.

Let’s say we have a user’s data in variables `firstName`, `lastName` or `nickName`. All of them may be not defined, if the user decided not to enter a value.

We’d like to display the user name using one of these variables, or show “Anonymous” if all of them aren’t defined.

Let’s use the `??` operator for that:

```javascript
let firstName = null;
let lastName = null;
let nickName = "Supercoder";

// shows the first defined value:
alert(firstName ?? lastName ?? nickName ?? "Anonymous"); // Supercoder
```

## Comparison with ||

The OR `||` operator can be used in the same way as `??`, as it was described in the [previous chapter](https://javascript.info/logical-operators#or-finds-the-first-truthy-value).

For example, in the code above we could replace `??` with `||` and still get the same result:

```javascript
let firstName = null;
let lastName = null;
let nickName = "Supercoder";

// shows the first truthy value:
alert(firstName || lastName || nickName || "Anonymous"); // Supercoder
```

The important difference between them is that:

- `||` returns the first *truthy* value.
- `??` returns the first *defined* value.

In other words, `||` doesn’t distinguish between `false`, `0`, an empty string `""` and `null/undefined`. They are all the same – falsy values. If any of these is the first argument of `||`, then we’ll get the second argument as the result.

In practice though, we may want to use default value only when the variable is `null/undefined`. That is, when the value is really unknown/not set.

For example, consider this:

```javascript
let height = 0;

alert(height || 100); // 100
alert(height ?? 100); // 0
```

## Precedence

The precedence of the `??` operator is about the same as `||`, just a bit lower. 

That means that, just like `||`, the nullish coalescing operator `??` is evaluated before `=` and `?`, but after most other operations, such as `+`, `*`.

## Using ?? with && or ||

Due to safety reasons, JavaScript forbids using `??` together with `&&` and `||` operators, unless the precedence is explicitly specified with parentheses.

The code below triggers a syntax error:

```javascript
let x = 1 && 2 ?? 3; // Syntax error
```

Use explicit parentheses to work around it:

```javascript
let x = (1 && 2) ?? 3; // Works

alert(x); // 2
```

