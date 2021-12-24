# The "switch" statement

A `switch` statement can replace multiple `if` checks.

## The syntax

It looks like this:

```javascript
switch(x) {
  case 'value1':  // if (x === 'value1')
    ...
    [break]

  case 'value2':  // if (x === 'value2')
    ...
    [break]

  default:
    ...
    [break]
}
```

- The value of `x` is checked for a strict equality to the value from the first `case` (that is, `value1`) then to the second (`value2`) and so on.
- If the equality is found, `switch` starts to execute the code starting from the corresponding `case`, until the nearest `break` (or until the end of `switch`).
- If no case is matched then the `default` code is executed (if it exists).

**If there is no `break` then the execution continues with the next `case` without any checks.**

**Any expression can be a** `switch/case` **argument**

For example:

```javascript
let a = "1";
let b = 0;

switch (+a) {
  case b + 1:
    alert("this runs, because +a is 1, exactly equals b+1");
    break;

  default:
    alert("this doesn't run");
}
```

Here `+a` gives `1`, that’s compared with `b + 1` in `case`, and the corresponding code is executed.

## Grouping of "case"

Several variants of `case` which share the same code can be grouped.

## Type matters

Let’s emphasize that the equality check is always strict. The values must be of the same type to match.

