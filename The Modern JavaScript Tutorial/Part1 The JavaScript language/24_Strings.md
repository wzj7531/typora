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

