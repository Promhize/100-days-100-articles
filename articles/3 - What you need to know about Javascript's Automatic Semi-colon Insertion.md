---
title: What you need to know about Javascript's Automatic Semi-colon Insertion
published: true
description: Explaining how the Javascript's Automatic Semi-colon insertion works.
tags: javascript, frontend
cover_image: https://thepracticaldev.s3.amazonaws.com/i/1y3rgwpqs4zqpoh1hgc6.jpg
---

Omitting semi-colons is a convenience that Javascript affords you. Javascript allows you to omit semi-colons in places where they’d normally appear, that is, at the end of statements or by convention before a new line character.


```javascript
const foo = "food"; const randomNumber = Math.random()
```

Which can be rewritten as

```javascript
const foo = "food"
const randomNumber = Math.random()
```

This is made possible thanks to the Automatic Semi-colon Insertion rules included in the official ECMAScript specification.

It’s a convenience though that if not properly understood can introduce a lot of Javascript gotcha scenarios. So, in this article I’ll talk about how the automatic semi-colon insertion works.

It might not be apparent that the two code snippets below produce totally different results,

```javascript
/**/
const val = "foo";
['semi']
console.log(val) //foo
```
```javascript
const foo = {
  semi: 'not semi'
}
const val = foo
['semi']
console.log(val) //not semi
```

If you are wondering why that is so, here’s what the ECMAScript 6 specification says about how automatic semi-colon insertion should work:

>When, as a Script or Module is parsed from left to right, a token (called the offending token) is encountered that is not allowed by any production of the grammar, then a semicolon is automatically inserted before the offending token if one or more of the following conditions is true:

1. The offending token is separated from the previous token by at least one LineTerminator
2. The offending token is }
3. The previous token is ) and the inserted semicolon would then be parsed as the terminating semicolon of a do-while statement

I have tried to explain the rules above with the code sample below

```javascript
/* Rule 1.1 */
const foo = 'food'
const bar = 'bar' /* Yo, I found token const on a new line, parsing it with the previous line as a single statement doesn't make sense, insert semi-colon before line 3 */

/* Rule 1.2 */
const baz = () => {
	const boo = true
  return boo } /* Found token }, insert semi-colon before it, parse previous statement */

/* Rule 1.3 */
do {
	const GT = foo + 'bar'
} while (baz()) /* Insert semi-colon after closing parentheses, gold could be on the next line for all I care */
```

Another way to sum up the first rule is,

“Hey, JS engine, if there’s no error parsing the code, continue and ignore the new line character. If there’s a parse error, do all these checks and insert semi-colon where necessary.”

The important thing to note here is, browsers will not insert semi-colons at the end of a line, if parsing a new line together with the previous line as one single statement, still results in valid Javascript. Back to the example at the beginning of this article:

```javascript
const foo = {
  semi: 'not semi'
}
const bar = foo
['semi']
```

Even though `[‘semi’]` is on a new line, parsing it together with the previous line still results in valid Javascript.
So it is parsed as `const bar = foo[‘semi’]`, which results in ‘not semi’
While omitting semi-colons, developers must take special care when starting new lines with these characters:


1. [
2. (
3. +
4. /
5. -
6. {


```javascript
const one = '1'
const two = 1
+one // this is a common technique for converting type 'string' to 'number'
console.log(two) // 11
```

It is common to see code similar to the example below in JS projects, and it might not become immediately apparent why it parses as correct Javascript grammar, but it is thanks to the Automatic Semi-colon Insertion rules we just discussed.

```javascript
object
.childObject
.method()
```

The specification also state that,

>When, as the Script or Module is parsed from left to right, a token is encountered that is allowed by some production of the grammar, but the production is a restricted production and the token would be the first token for a terminal or nonterminal immediately following the annotation “[no LineTerminator here]” within the restricted production (and therefore such a token is called a restricted token), and the restricted token is separated from the previous token by at least one LineTerminator, then a semicolon is automatically inserted before the restricted token.

This simply says no line breaks after certain tokens (keywords) like return, break, postfix expressions (- -, ++), throw, etc.

```javascript
return //semi-colon is automatically inserted here
4 // no

break //semi-colon is automatically inserted here
a //no

continue //semi-colon is automatically inserted here
a = 4

const arr = arr () //semi-colon is automatically inserted here
=> {} // no

...
```

I hope you can now write Javascript without semi-colons with more confidence :)
