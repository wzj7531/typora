# Conditional branching: if, '?'

"if" statement and conditional operator ?

## The "if" statement

We recommend wrapping your code block with curly braces `{}` every time you use an `if` statement, even if there is only one statement to execute. Doing so improves readability.

## Boolean conversion

**The `if (…)` statement evaluates the expression in its parentheses and converts the result to a boolean.**

Let’s recall the conversion rules from the chapter [Type Conversions](https://javascript.info/type-conversions):

- A number `0`, an empty string `""`, `null`, `undefined`, and `NaN` all become `false`. Because of that they are called “falsy” values.
- Other values become `true`, so they are called “truthy”.

## The "else" clause

The `if` statement may contain an optional “else” block. It executes when the condition is falsy.

## Several conditions: "else if"

Sometimes, we’d like to test several variants of a condition. The `else if` clause lets us do that.

## Conditional operator '?'

The syntax is:

```javascript
let result = condition ? value1 : value2;
```

The `condition` is evaluated: if it’s truthy then `value1` is returned, otherwise – `value2`.

For example:

```javascript
let accessAllowed = (age > 18) ? true : false;
```

Technically, we can omit the parentheses around `age > 18`.  The question mark operator has a low precedence, so it executes after the comparison `>`.

But parentheses make the code more readable, so we recommend using them.

## Multiple '?'

A sequence of question mark operators `?` can return a value that depends on more than one condition.

For instance:

```javascript
let age = prompt('age?', 18);

let message = (age < 3) ? 'Hi, baby!' :
  (age < 18) ? 'Hello!' :
  (age < 100) ? 'Greetings!' :
  'What an unusual age!';

alert( message );
```

## Non-traditional use of '?'

Sometimes the question mark `?` is used as a replacement for `if`:

```javascript
let company = prompt('Which company created JavaScript?', '');

(company == 'Netscape') ?
   alert('Right!') : alert('Wrong.');
```

We don’t assign a result to a variable here. Instead, we execute different code depending on the condition.

**It’s not recommended to use the question mark operator in this way.**

