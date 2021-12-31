# Numbers

In modern JavaScript, there are two types of numbers:

1. Regular numbers in JavaScript are stored in 64-bit format [IEEE-754](https://en.wikipedia.org/wiki/IEEE_754-2008_revision), also known as “double precision floating point numbers”. These are numbers that we’re using most of the time, and we’ll talk about them in this chapter.
2. BigInt numbers, to represent integers of arbitrary length. They are sometimes needed, because a regular number can’t safely exceed `253` or be less than `-253`. As bigints are used in few special areas, we devote them a special chapter [BigInt](https://javascript.info/bigint).

So here we’ll talk about regular numbers. Let’s expand our knowledge of them.

## More ways to write a number

```javascript
let billion = 10000000000;

let billion = 1_000_000_000;

let billion = 1e9;

let ms = 1e-6;
```

## Hex, binary and octal numbers

```javascript
//Hex
alert(0xff);
alert(oxFF);

//Binary
let a = 0b11111111;

//octal
let b = 0o377;
```

## toString(base)

The method `num.toString(base)` returns a string representation of `num` in the numeral system with the given `base`.

```javascript
let num = 255;

alert(num.toString(16));//ff
alert(num.toString(2));//111111111;
```

The `base` can vary from `2` to `36`. By default it’s `10`.

```javascript
alert(123456..toString(36));
```

**Two dots to call a method**

Please note that two dots in `123456..toString(36)` is not a typo. If we want to call a method directly on a number, like `toString` in the example above, then we need to place two dots `..` after it.

If we placed a single dot: `123456.toString(36)`, then there would be an error, because JavaScript syntax implies the decimal part after the first dot. And if we place one more dot, then JavaScript knows that the decimal part is empty and now goes the method.

Also could write `(123456).toString(36)`.

## Rounding

**Math.floor**

Rounds down: `3.1` becomes `3`, and `-1.1` becomes `-2`.

**Math.ceil**

Rounds up: `3.1` becomes `4`, and `-1.1` becomes `-1`.

**Math.round**

Rounds to the nearest integer: `3.1` becomes `3`, `3.6` becomes `4`, the middle case: `3.5` rounds up to `4` too.

**Math.trunc**

Removes anything after the decimal point without rounding: `3.1` becomes `3`, `-1.1` becomes `-1`.

These functions cover all of the possible ways to deal with the decimal part of a number. But what if we’d like to round the number to `n-th` digit after the decimal?

For instance, we have `1.2345` and want to round it to 2 digits, getting only `1.23`.

There are two ways to do so:

1. Multiply-and-divide.

   ```javascript
   let num = 1.23456;
   alert(Math.round(num * 100) / 100);
   ```

2. The method [toFixed(n)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toFixed) rounds the number to `n` digits after the point and returns a string representation of the result.

   ```javascript
   let num = 12.34
   alert(num.toFixed(1));//"12.3"
   
   let num = 12.36;
   alert(num.toFixed(1));//"12.4"
   ```

   Please note that result of `toFixed` is a string. If the decimal part is shorter than required, zeroes are appended to the end:

   ```javascript
   let num = 12.34;
   alert(num.toFixed(5));//"12.34000"
   ```

## Imprecise calculations

Internally, a number is represented in 64-bit format [IEEE-754](https://en.wikipedia.org/wiki/IEEE_754-2008_revision), so there are exactly 64 bits to store a number: **52 of them are used to store the digits**, **11 of them store the position of the decimal point** (they are zero for integer numbers), **and 1 bit is for the sign**.

If a number is too big, it would overflow the 64-bit storage, potentially giving an infinity:

```javascript
alert(1e500);//Infinity
```

What may be a little less obvious, but happens quite often, is the loss of precision.

```javascript
alert(0.1 + 0.2 == 0.3);//false
alert(0.1 + 0.2);//0.30000000000000004
```

There’s just no way to store *exactly 0.1* or *exactly 0.2* using the binary system, just like there is no way to store one-third as a decimal fraction.

The numeric format IEEE-754 solves this by rounding to the nearest possible number. These rounding rules normally don’t allow us to see that “tiny precision loss”, but it exists.

We can see this in action:

```javascript
alert(0.1.toFixed(20));//0.10000000000000000555
```

And when we sum two numbers, their “precision losses” add up.

That’s why `0.1 + 0.2` is not exactly `0.3`.

Can we work around the problem? Sure, the most reliable method is to round the result with the help of a method [toFixed(n)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toFixed):

```javascript
let sum = 0.1 + 0.2;
alert(+sum.toFixed(2));//0.30
```

Please note that `toFixed` always returns a string. It ensures that it has 2 digits after the decimal point. That’s actually convenient if we have an e-shopping and need to show `$0.30`. For other cases, we can use the unary plus to coerce it into a number:

```javascript
let sum = 0.1 + 0.2;
alert(+sum.toFixed(2));//0.3
```

## Tests: isFinite and is NaN

- `Infinity` (and `-Infinity`) is a special numeric value that is greater (less) than anything.
- `NaN` represents an error.

They belong to the type `number`, but are not “normal” numbers, so there are special functions to check for them:

**isNaN(value)**: converts its argument to a number and then tests it for being NaN:

```javascript
alert(iSNaN(NaN));//true;
alert(isNaN("str"));//true;
```

But do we need this function? Can’t we just use the comparison `=== NaN`? **Sorry, but the answer is no**. **The value** `NaN` **is unique in that it does not equal anything, including itself**:

```javascript
alert(NaN === NaN);//false
```

**isFinite(value)**: converts its argument to a number and returns `true` if it’s a regular number, not `NaN/Infinity/-Infinity`:

```javascript
alert(isFinite("15"));//true
alert(isFinite("str"));//false,because a special value:NaN
alert(isFinite(Infinity));//false,because a speical value:Infinity
```

Sometimes `isFinite` is used to validate whether a string value is a regular number:

```javascript
let num = +prompt("Enter a number",'');

alert(isFinite(num));
```

Please note that **an empty or a space-only string is treated as `0` in all numeric functions including `isFinite`**.

**Compare with** `Object.is`

There is a special built-in method [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) that compares values like `===`, but is more reliable for two edge cases:

1. It works with `NaN`: `Object.is(NaN, NaN) === true`, that’s a good thing.
2. Values `0` and `-0` are different: `Object.is(0, -0) === false`, technically that’s true, because internally the number has a sign bit that may be different even if all other bits are zeroes.

In all other cases, `Object.is(a, b)` is the same as `a === b`.

This way of comparison is often used in JavaScript specification. When an internal algorithm needs to compare two values for being exactly the same, it uses `Object.is` (internally called [SameValue](https://tc39.github.io/ecma262/#sec-samevalue)).

## ParseInt and parseFloat

Numeric conversion using a plus `+` or `Number()` is strict. If a value is not exactly a number, it fails:

```javascript
alert(+"100px");//NaN
```

The sole exception is spaces at the beginning or at the end of the string, as they are ignored.

But in real life we often have values in units, like `"100px"` or `"12pt"` in CSS. Also in many countries the currency symbol goes after the amount, so we have `"19€"` and would like to extract a numeric value out of that.

That’s what `parseInt` and `parseFloat` are for.

They “read” a number from a string until they can’t. In case of an error, the gathered number is returned. The function `parseInt` returns an integer, whilst `parseFloat` will return a floating-point number:

```javascript
alert(parseInt("100px"));//100
alert(parseFloat('12.5em'));//12.5

alert(parseInt('12.3'));//12,only the integer part is returned
alert(parseFloat('12.3.4'))//12.3
```

There are situations when `parseInt/parseFloat` will return `NaN`. It happens when no digits could be read:

```javascript
alert(parseInt('a123'));
```

**The second argument of `parseInt(str, radix)`**

The `parseInt()` function has an optional second parameter. It specifies the base of the numeral system, so `parseInt` can also parse strings of hex numbers, binary numbers and so on:

```javascript
alert( parseInt('0xff', 16) ); // 255
alert( parseInt('ff', 16) ); // 255, without 0x also works

alert( parseInt('2n9c', 36) ); // 123456
```

## Other math functions

JavaScript has a built-in [Math](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Math) object which contains a small library of mathematical functions and constants.

A few examples:

- `Math.random()`

  Returns a random number from 0 to 1 (not including 1).`alert( Math.random() ); // 0.1234567894322 alert( Math.random() ); // 0.5435252343232 alert( Math.random() ); // ... (any random numbers)`

- `Math.max(a, b, c...)` / `Math.min(a, b, c...)`

  Returns the greatest/smallest from the arbitrary number of arguments.`alert( Math.max(3, 5, -10, 0, 1) ); // 5 alert( Math.min(1, 2) ); // 1`

- `Math.pow(n, power)`

  Returns `n` raised to the given power.`alert( Math.pow(2, 10) ); // 2 in power 10 = 1024`

There are more functions and constants in `Math` object, including trigonometry, which you can find in the [docs for the Math object](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Math).
