# Logical operators

There are four logical operators in JavaScript: `||` (OR), `&&` (AND), `!` (NOT), `??` (Nullish Coalescing). 

Here we cover the first three, the `??` operator is in the next article.

**Although they are called “logical”, they can be applied to values of any type, not only boolean. Their result can also be of any type.**

## || (OR)

The “OR” operator is represented with two vertical line symbols:

```javascript
result = a || b;
```

In classical programming, the logical OR is meant to manipulate boolean values only. If any of its arguments are `true`, it returns `true`, otherwise it returns `false`.

In JavaScript, the operator is a little bit trickier and more powerful. But first, let’s see what happens with boolean values.

**If an operand is not a boolean, it’s converted to a boolean for the evaluation.**

For instance, the number `1` is treated as `true`, the number `0` as `false`:

```javascript
if (1 || 0) { // works just like if( true || false )
  alert( 'truthy!' );
}
```

## OR "||" finds the first truthy value

The OR `||` operator does the following:

- Evaluates operands from left to right.
- For each operand, converts it to boolean. If the result is `true`, stops and **returns the original value of that operand**.
- If all operands have been evaluated (i.e. all were `false`), returns the last operand.

A value is returned in its original form, without the conversion.

In other words, a chain of OR `||` returns the first truthy value or the last one if no truthy value is found.

For instance:

```javascript
alert( 1 || 0 ); // 1 (1 is truthy)

alert( null || 1 ); // 1 (1 is the first truthy value)
alert( null || 0 || 1 ); // 1 (the first truthy value)

alert( undefined || null || 0 ); // 0 (all falsy, returns the last value)
```

1. Getting the first truthy value from a list of variables or expressions.

   For instance, we have `firstName`, `lastName` and `nickName` variables, all optional (i.e. can be undefined or have falsy values).

   Let’s use OR `||` to choose the one that has the data and show it (or `"Anonymous"` if nothing set):

   ```javascript
   let firstName = "";
   let lastName = "";
   let nickName = "SuperCoder";
   
   alert( firstName || lastName || nickName || "Anonymous"); // SuperCoder
   ```

   If all variables were falsy, `"Anonymous"` would show up.

2. Short-circuit evaluation.

   Another feature of OR `||` operator is the so-called “short-circuit” evaluation.

   It means that `||` processes its arguments until the first truthy value is reached, and then the value is returned immediately, without even touching the other argument.

   The importance of this feature becomes obvious if an operand isn’t just a value, but an expression with a side effect, such as a variable assignment or a function call.

   In the example below, only the second message is printed:

   ```javascript
   true || alert("not printed");
   false || alert("printed");
   ```

   In the first line, the OR `||` operator stops the evaluation immediately upon seeing `true`, so the `alert` isn’t run.

   Sometimes, people use this feature to execute commands only if the condition on the left part is falsy.

## && (And)

The AND operator is represented with two ampersands `&&`.

In classical programming, AND returns `true` if both operands are truthy and `false` otherwise.

Just as with OR, any value is allowed as an operand of AND:

```javascript
if (1 && 0) { // evaluated as true && false
  alert( "won't work, because the result is falsy" );
}
```

## AND "&&" finds the first falsy value

Given multiple AND’ed values:

```javascript
result = value1 && value2 && value3;
```

The AND `&&` operator does the following:

- Evaluates operands from left to right.
- For each operand, converts it to a boolean. If the result is `false`, stops and returns the **original value** of that operand.
- If all operands have been evaluated (i.e. all were truthy), returns the last operand.

In other words, **AND returns the first falsy value or the last value if none were found.**

The rules above are similar to OR. The difference is that AND returns the first *falsy* value while OR returns the first *truthy* one.

Examples:

```javascript
// if the first operand is truthy,
// AND returns the second operand:
alert( 1 && 0 ); // 0
alert( 1 && 5 ); // 5

// if the first operand is falsy,
// AND returns it. The second operand is ignored
alert( null && 5 ); // null
alert( 0 && "no matter what" ); // 0
```

We can also pass several values in a row. See how the first falsy one is returned:

```javascript
alert( 1 && 2 && null && 3 ); // null
```

When all values are truthy, the last value is returned:

```javascript
alert( 1 && 2 && 3 ); // 3, the last one
```

**Precedence of AND** `&&` **is higher than OR** `||`

The precedence of AND `&&` operator is higher than OR `||`.

So the code `a && b || c && d` is essentially the same as if the `&&` expressions were in parentheses: `(a && b) || (c && d)`.

## ! (NOT)

The boolean NOT operator is represented with an exclamation sign `!`.

The operator accepts a single argument and does the following:

1. Converts the operand to boolean type: `true/false`.
2. Returns the inverse value.

For instance:

```javascript
alert( !true ); // false
alert( !0 ); // true
```

A double NOT `!!` is sometimes used for converting a value to boolean type:

```javascript
alert( !!"non-empty string" ); // true
alert( !!null ); // false
```

That is, the first NOT converts the value to boolean and returns the inverse, and the second NOT inverses it again. In the end, we have a plain value-to-boolean conversion.

There’s a little more verbose way to do the same thing – a built-in `Boolean` function:

```javascript
alert( Boolean("non-empty string") ); // true
alert( Boolean(null) ); // false
```

**The precedence of NOT `!` is the highest of all logical operators, so it always executes first, before `&&` or `||`.**



## Tasks

### What's the result of OR'ed alerts?

What will the code below output?

```javascript
alert( alert(1) || 2 || alert(3) );
```

The answer: first `1`, then `2`.

```javascript
alert( alert(1) || 2 || alert(3) );
```

The call to `alert` does not return a value. Or, in other words, it returns `undefined`.

1. The first OR `||` evaluates its left operand `alert(1)`. That shows the first message with `1`.
2. The `alert` returns `undefined`, so OR goes on to the second operand searching for a truthy value.
3. The second operand `2` is truthy, so the execution is halted, `2` is returned and then shown by the outer alert.

There will be no `3`, because the evaluation does not reach `alert(3)`.

### What is the result of AND'ed alerts?

What will this code show?

```javascript
alert( alert(1) && alert(2) );
```

solution

The answer: `1`, and then `undefined`.

```javascript
alert( alert(1) && alert(2) );
```

The call to `alert` returns `undefined` (it just shows a message, so there’s no meaningful return).

Because of that, `&&` evaluates the left operand (outputs `1`), and immediately stops, because `undefined` is a falsy value. And `&&` looks for a falsy value and returns it, so it’s done.

### The result of OR AND OR

What will the result be?

```javascript
alert( null || 2 && 3 || 4 );
```

solution

The answer: `3`.

```javascript
alert( null || 2 && 3 || 4 );
```

The precedence of AND `&&` is higher than `||`, so it executes first.

The result of `2 && 3 = 3`, so the expression becomes:

```none
null || 3 || 4
```

Now the result is the first truthy value: `3`.





