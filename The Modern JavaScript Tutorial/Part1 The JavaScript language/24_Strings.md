# Strings

The internal format for strings is always [UTF-16](https://en.wikipedia.org/wiki/UTF-16), it is not tied to the page encoding.

## Quotes

Single and double quotes are essentially the same. Backticks, however, allow us to embed any expression into the string, by wrapping it in `${…}`:

```javascript
function sum(a, b){
    return a + b;
}

alert(`1 + 2 = ${sum(1,2)}`);
```

Another advantage of using backticks is that they allow a string to span multiple lines:

```javascript
let guestList = `Gusets:
* John
* Pete
* Mary
`;

alert(guestList);
```

## Special characters

It is still possible to create multiline strings with single and double quotes by using a so-called “newline character”, written as `\n`, which denotes a line break:

```javascript
let guestList = "Guests:\n * John\n * Pete\n * Mary";

alert(guestList); // a multiline list of guests
```

## String length

The `length` property has the string length:

```javascript
alert(`My\n`.length);
```

Note that `\n` is a single “special” character, so the length is indeed `3`.

**`length` is a property**

People with a background in some other languages sometimes mistype by calling `str.length()` instead of just `str.length`. That doesn’t work.

Please note that `str.length` is a numeric property, not a function. There is no need to add parenthesis after it.

## Accessing characters

To get a character at position `pos`, use square brackets `[pos]` or call the method [str.charAt(pos)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/charAt). The first character starts from the zero position:

```javascript
let str = `Hello`;

alert(str[0]); //H
alert(str.charAt(0)); //H

alert(str[str.length - 1]);//o
```

The square brackets are a modern way of getting a character, while `charAt` exists mostly for historical reasons.

**The only difference between them is that if no character is found, `[]` returns `undefined`, and `charAt` returns an empty string**:

```javascript
let str = `Hello`;

alert(str[1000]);//undefined
alert(str.charAt(1000));//''(an empty string)
```

We can also iterate over characters using `for..of`:

```javascript
for(let char of 'Hello'){
    alert(char);
}
```

## Strings are immutable

Strings can’t be changed in JavaScript. **It is impossible to change a character**.

Let’s try it to show that it doesn’t work:

```javascript
let str = 'Hi';

str[0] = 'h';//error
alert(str[0]);//doesn't work
```

The usual workaround is to create a whole new string and assign it to `str` instead of the old one.

For instance:

```javascript
let str = 'Hi';

str = 'h' + str[1];//replace the string

alert(str);//hi
```

## Changing the case

Methods [toLowerCase()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase) and [toUpperCase()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toUpperCase) change the case:

```javascript
alert('Interface'.toUpperCase());//INTERFACE
alert('Interface'.toLowerCase());//interface
```

Or, if we want a single character lowercased:

```javascript
alert('Interface'[0].toLowerCase());//'i'
```

## Searching for a substring

There are multiple ways to look for a substring within a string.

### str.indexOf

The first method is [str.indexOf(substr, pos)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/indexOf).

It looks for the `substr` in `str`, starting from the given position `pos`, and returns the position where the match was found or `-1` if nothing can be found.

```javascript
let str = 'Widget with id';

alert(str.indexOf('Widget'));//0
alert(str.indexOf('widget'));//-1, not found, the serach is case-sensitive
alert(str.indexOf("id"));//1, "id" is found at the position 1

alert(str.indexOf('id', 2))//12


```

**`str.lastIndexOf(substr, position)`**

There is also a similar method [str.lastIndexOf(substr, position)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/lastIndexOf) that searches from the end of a string to its beginning.

It would list the occurrences in the reverse order.

There is a slight inconvenience with `indexOf` in the `if` test. We can’t put it in the `if` like this:

```javascript
let str = "Widget with id";

if (str.indexOf("Widget")) {
    alert("We found it"); // doesn't work!
}
```

The `alert` in the example above doesn’t show because `str.indexOf("Widget")` returns `0` (meaning that it found the match at the starting position). Right, but `if` considers `0` to be `false`.

So, we should actually check for `-1`, like this:

```javascript
let str = "Widget with id";

if (str.indexOf("Widget") != -1) {
    alert("We found it"); // works now!
}
```

#### The bitwise NOT trick

One of the old tricks used here is the [bitwise NOT](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_NOT) `~` operator. It converts the number to a 32-bit integer (removes the decimal part if exists) and then reverses all bits in its binary representation.

In practice, **that means a simple thing: for 32-bit integers `~n` equals `-(n+1)`**.

```javascript
alert(~2);//-3
alert(~1);//-2
alert(~0);//-2
alert(~-1);//0
```

So, the test `if ( ~str.indexOf("...") )` is truthy only if the result of `indexOf` is not `-1`. In other words, when there is a match.

People use it to shorten `indexOf` checks:

```javascript
let str = "Widget";

if(~str.indexOf("Widget")){
    alert('Found it!');//works
}
```

It is usually not recommended to use language features in a non-obvious way, modern JavaScript provides `.includes` method .

### includes, startsWith, endsWith

The more modern method [str.includes(substr, pos)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/includes) returns `true/false` depending on whether `str` contains `substr` within.

It’s the right choice if we need to test for the match, but don’t need its position:

```javascript
alert( "Widget with id".includes("Widget") ); // true

alert( "Hello".includes("Bye") ); // false
```

The optional second argument of `str.includes` is the position to start searching from:

```javascript
alert( "Widget".includes("id") ); // true
alert( "Widget".includes("id", 3) ); // false, from position 3 there is no "id"
```

The methods [str.startsWith](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/startsWith) and [str.endsWith](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/endsWith) do exactly what they say:

```javascript
alert( "Widget".startsWith("Wid") ); // true, "Widget" starts with "Wid"
alert( "Widget".endsWith("get") ); // true, "Widget" ends with "get"
```

## Getting a substring

**str.slice(start [, end])**

Returns the part of the string from `start` to (but not including) `end`.

```javascript
let str = "stringify";
alert(str.slice(0, 5));//'strin'
alert(str.slice(0, 1));//'s'
```

If there is no second argument, then `slice` goes till the end of the string:

```javascript
let str = "stringify";
alert(str.slice(2))//"ringify"
```

Negative values for `start/end` are also possible. They mean the position is counted from the string end:

```javascript
let str = "stringify";

//start at the 4th position from the right, end at the 1st from the right
alert(str.slice(-4, -1));//'gif'
```

**str.substring(start [, end])**

Returns the part of the string *between* `start` and `end`.

This is almost the same as `slice`, **but it allows `start` to be greater than `end`.**

```javascript
let str = "stringify";

alert(str.substring(2, 6));//"ring"
alert(str.substring(6, 2));//"ring"
```

Negative arguments are (unlike slice) not supported, they are treated as `0`.

**str.substr(start [, length])**

Returns the part of the string from `start`, with the given `length`.

```javascript
let str = "stringify";
alert(str.substr(2, 4));//'ring'
```

The first argument may be negative, to count from the end:

```javascript
let str = "stringify";
alert(str.substr(-4, 2));//'gi'
```

**Which one to choose?**

All of them can do the job. Formally, `substr` has a minor drawback: it is described not in the core JavaScript specification, but in Annex B, which covers browser-only features that exist mainly for historical reasons. So, non-browser environments may fail to support it. But in practice it works everywhere.

Of the other two variants, `slice` is a little bit more flexible, it allows negative arguments and shorter to write. **So, it’s enough to remember solely `slice` of these three methods.**

## Comparing strings

As we know from the chapter [Comparisons](https://javascript.info/comparison), strings are compared character-by-character in alphabetical order.

Although, there are some oddities.

1. A lowercase letter is always greater than the uppercase:

   ```javascript
   alert( 'a' > 'Z' ); // true
   ```

   

2. Letters with diacritical(变调的) marks are “out of order”:

   ```javascript
   alert( 'Österreich' > 'Zealand' ); // true
   ```

All strings are encoded using [UTF-16](https://en.wikipedia.org/wiki/UTF-16). That is: each character has a corresponding numeric code. There are special methods that allow to get the character for the code and back.

**str.codePointAt(pos)**

```javascript
alert("z".codePointAt(0));//122
alert("Z".codePointAt(0));//90
```

**String.fromCodePoint(code)**

```javascript
alert(String.fromCodePoint(90));//Z
```

We can also add Unicode characters by their codes using `\u` followed by the hex code:

```javascript
alert('\u005a');//Z
```

### Correct comparisons

The “right” algorithm to do string comparisons is more complex than it may seem, because alphabets are different for different languages.

Luckily, all modern browsers (IE10- requires the additional library [Intl.js](https://github.com/andyearnshaw/Intl.js/)) support the internationalization standard [ECMA-402](http://www.ecma-international.org/ecma-402/1.0/ECMA-402.pdf).

It provides a special method to compare strings in different languages, following their rules.

The call [str.localeCompare(str2)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare) returns an integer indicating whether `str` is less, equal or greater than `str2` according to the language rules:

- Returns a negative number if str is less than str2.
- Returns a positive number if str is greater than str2.
- Retuns 0 if they are equivalent.

```javascript
alert( 'Österreich'.localeCompare('Zealand') ); // -1
```

This method actually has two additional arguments specified in [the documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare), which allows it to specify the language (by default taken from the environment, letter order depends on the language) and setup additional rules like case sensitivity or should `"a"` and `"á"` be treated as the same etc.

### Internals, Unicode

### Surrogate pairs

All frequently used characters have **2-byte codes**. Letters in most european languages, numbers, and even most hieroglyphs(象形文字), have a 2-byte representation.

But 2 bytes only allow 65536 combinations and that’s not enough for every possible symbol. So rare symbols are encoded with a pair of 2-byte characters called “a surrogate pair”.

The length of such symbols is `2`:

```javascript
alert( '𝒳'.length ); // 2, MATHEMATICAL SCRIPT CAPITAL X
alert( '😂'.length ); // 2, FACE WITH TEARS OF JOY
alert( '𩷶'.length ); // 2, a rare Chinese hieroglyph
```

Note that surrogate pairs did not exist at the time when JavaScript was created, and thus are not correctly processed by the language!

We actually have a single symbol in each of the strings above, but the `length` shows a length of `2`.

`String.fromCodePoint` and `str.codePointAt` are few rare methods that deal with surrogate pairs right. They recently appeared in the language. Before them, there were only [String.fromCharCode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/fromCharCode) and [str.charCodeAt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt). These methods are actually the same as `fromCodePoint/codePointAt`, but don’t work with surrogate pairs.

Getting a symbol can be tricky, because surrogate pairs are treated as two characters:

```javascript
alert( '𝒳'[0] ); // strange symbols...
alert( '𝒳'[1] ); // ...pieces of the surrogate pair
```

Technically, surrogate pairs are also detectable by their codes: if a character has the code in the interval of `0xd800..0xdbff`, then it is the first part of the surrogate pair. The next character (second part) must have the code in interval `0xdc00..0xdfff`. These intervals are reserved exclusively for surrogate pairs by the standard.

```javascript
// charCodeAt is not surrogate-pair aware, so it gives codes for parts

alert( '𝒳'.charCodeAt(0).toString(16) ); // d835, between 0xd800 and 0xdbff
alert( '𝒳'.charCodeAt(1).toString(16) ); // dcb3, between 0xdc00 and 0xdfff
```

### Diacritical marks and normalization

In many languages there are symbols that are composed of the base character with a mark above/under it.

For instance, the letter `a` can be the base character for: `àáâäãåā`. Most common “composite” character have their own code in the UTF-16 table. But not all of them, because there are too many possible combinations.

To support arbitrary compositions, UTF-16 allows us to use several Unicode characters: the base character followed by one or many “mark” characters that “decorate” it.

For instance, if we have `S` followed by the special “dot above” character (code `\u0307`), it is shown as Ṡ.

```javascript
alert( 'S\u0307' ); // Ṡ
```

If we need an additional mark above the letter (or below it) – no problem, just add the necessary mark character.

For instance, if we append a character “dot below” (code `\u0323`), then we’ll have “S with dots above and below”: `Ṩ`.

```javascript
alert( 'S\u0307\u0323' ); // Ṩ
```

This provides great flexibility, but also an interesting problem: two characters may visually look the same, but be represented with different Unicode compositions.

```javascript
let s1 = 'S\u0307\u0323'; // Ṩ, S + dot above + dot below
let s2 = 'S\u0323\u0307'; // Ṩ, S + dot below + dot above

alert( `s1: ${s1}, s2: ${s2}` );

alert( s1 == s2 ); // false though the characters look identical (?!)
```

To solve this, there exists a “Unicode normalization” algorithm that brings each string to the single “normal” form.

It is implemented by [str.normalize()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/normalize).

```javascript
alert( "S\u0307\u0323".normalize() == "S\u0323\u0307".normalize() ); // true
```

It’s funny that in our situation `normalize()` actually brings together a sequence of 3 characters to one: `\u1e68` (S with two dots).

```javascript
alert( "S\u0307\u0323".normalize().length ); // 1

alert( "S\u0307\u0323".normalize() == "\u1e68" ); // true
```

In reality, this is not always the case. The reason being that the symbol `Ṩ` is “common enough”, so UTF-16 creators included it in the main table and gave it the code.

