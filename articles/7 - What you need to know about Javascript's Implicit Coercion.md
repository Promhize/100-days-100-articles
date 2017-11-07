---
title: What you need to know about Javascript's Implicit Coercion
published: true
description: Common Javascript implicit coercion gotchas, how it works, what should be avoided and why
tags: javascript, frontend
cover_image: https://thepracticaldev.s3.amazonaws.com/i/lq59fpy9827al29xrt4f.jpg
---

Javascript's implicit coercion simply refers to Javascript attempting to coerce an unexpected value type to the expected type. So you can pass a string where it expects a number, an object where it expects a string etc, and it will try to convert it to the right type. This is a Javascript feature that is best avoided.

```javascript
3 * "3" //9
1 + "2" + 1 //121

true + true //2
10 - true //9


const foo = {
  valueOf: () => 2
}
3 + foo // 5
4 * foo // 8

const bar = {
  toString: () => " promise is a boy :)"
}
1 + bar // "1 promise is a boy :)"


4 * [] // 0
4 * [2] // 8
4 + [2] // "42"
4 + [1, 2] // "41,2"
4 * [1, 2] // NaN

"string" ? 4 : 1 // 4
undefined ? 4 : 1 // 1
```

##Non-numeric values in numeric expressions

###Strings

Whenever you pass a string as an operand in a numeric expression involving either of these operators: `-, *, /, %`, the number's conversion process is similar to calling the in-built `Number` function on the value. This is pretty straightforward, any string containing only numeric characters will be converted to it's number equivalent, but a string containing a non-numeric character returns `NaN`. Illustrated below,

```javascript
3 * "3" // 3 * 3
3 * Number("3") // 3 * 3
Number("5") // 5

Number("1.") // 1
Number("1.34") // 1.34
Number("0") // 0
Number("012") // 12

Number("1,") // NaN
Number("1+1") // NaN
Number("1a") // NaN
Number("one") // NaN
Number("text") // NaN
```
####The case for the + operator
The + operator unlike other mathematical operators, performs two functions:
1. Mathematical addition
2. String concatenation

When a string is an operand of the + operator, Javascript instead of converting the string to a Number, converts the number to a string.

````javascript

// concatenation
1 + "2" // "12"
1 + "js" // "1js"

// addition
1 + 2 // 3
1 + 2 + 1 // 4

//addition, then concatenation
1 + 2 + "1" // "31"
(1 + 2) + "1" // "31"

//concatenation all through
1 + "2" + 1 // "121"
(1 + "2") + 1 // "121"
```

###Objects

Most Javascript Object conversions usually result in `[object Object]`, For example
```javascript
"name" + {} // "name[object Object]
```
Every javascript Object inherits a `toString` method, that is called whenever an Object is to be converted to a string. The return value of the `toString` method is used for such operations as string concatenation and mathematical expressions.
```javascript
const foo = {}
foo.toString() // [object Object]

const baz = {
  toString: () => "I'm object baz"
}

baz + "!" // "I'm object baz!"
```
When it's a mathematical expression, Javascript will attempt to convert the return value to a number, if it's not.

```javascript
const foo = {
  toString: () => 4
}

2 * foo // 8
2 / foo // 0.5
2 + foo // 6
"four" + foo // "four4"

const baz = {
  toString: () => "four"
}

2 * baz // NaN
2 + baz // 2four

const bar = {
  toString: () => "2"
}

2 + bar // "22"
2 * bar // 4
```
####Array objects

The inherited `toString` method of Arrays work abit differently, it works in a way similar to calling the `join` method of an array without any arguments.

```javascript
[1,2,3].toString() // "1,2,3"
[1,2,3].join() // "1,2,3"
[].toString() // ""
[].join() // ""

"me" + [1,2,3] // "me1,2,3"
4 + [1,2,3] // "41,2,3"
4 * [1,2,3] // NaN
```
So when you pass an array where it expects a string, Javascript concatenates the return value of the `toString` method with the second operand. If it expects a number, it attempts to convert the return value to a number.

```javascript
4 * [] // 0
4 / [2] // 2

//similar to
4 * Number([].toString())
4 * Number("")
4 * 0

//

4 / Number([2].toString())
4 / Number("2")
4 / 2
```

####True, False and ""

```javascript
Number(true) // 1
Number(false) // 0
Number("") // 0

4 + true // 5
3 * false // 0
3 * "" // 0
3 + "" // "3"
```


####The `valueOf` method
It is also possible to define a `valueOf` method that will be used by Javascript whenever you pass an Object where it expects a string or numeric value.
```javascript

const foo = {
  valueOf: () => 3
}

3 + foo // 6
3 * foo // 9
```
When both the `toString` and `valueOf` methods are defined on an Object, Javascript uses the `valueOf` method instead.

```javascript
const bar = {
  toString: () => 2,
  valueOf: () => 5
}

"sa" + bar // "sa5"
3 * bar // 15
2 + bar // 7
```

The valueOf method is intended for Objects that are supposed to represent a numeric value.

```javascript
const two = new Number(2)

two.valueOf() // 2
```

##Falsy and Truthy

~~I really wanted to make that falsy and trusy~~

Every Javascript value can be coerced into either true or false. Coercion into boolean `true` means the value is truthy. Coercion into boolean `false` means the value is falsy.

There are a handful of values in Javascript that return falsy values, they are:
1. false
2. 0
3. null
4. undefined
5. ""
6. NaN
7. -0

Everything else is truthy,

```javascript
if (-1) // truthy
if ("0") // truthy
if ({}) // truthy
```
The above snippets are okay, but it is better practice to be explicit when trying to determine truthiness of a value. Basically, don't rely on Javascript's implicit coercion, even if you feel you know them perfectly.
Instead of the code snippet below,
```javascript
const counter = 2

if (counter)
```
Any of the below is better practice depending on your requirements

```javascript
if (counter === 2)

//or

if (typeof counter === "number")
```

This is because for example, you define a function that is supposed to work with numbers

```javascript

const add = (number) => {
  if (!number) new Error("Only accepts arguments of type: number")
  //your code
}
```
So if I call the add function with 0, I will always get an unintended error

```javascript
add(0) // Error: Only accepts arguments of type: number

//better check

const add = (number) => {
  if (typeof number !== "number") new Error("Only accepts arguments of type: number")
  //your code
}

add(0) // no error

```

##NaN

`NaN` is a special numeric value that is not equal to itself.

```javascript
NaN === NaN // false

const notANumber = 3 * "a" // NaN

notANumber == notANumber // false
notANumber === notANumber // false
```
`NaN` is the only Javascript value that is not equal to itself. So you can check for `NaN` by comparing it to itself.

```javascript
if (notANumber !== notANumber) // true
```

ECMAScript 6 introduced a method for checking NaN, `Number.isNaN`

```javascript
Number.isNaN(NaN) // true
Number.isNaN("name") // false
```

Beware of the global `isNaN` function, it attempts to coerce it's argument before checking if it's `NaN`. For example,

```javascript
isNaN("name") // true
isNaN("1") // false
```

The global `isNaN` function should be avoided, the way it works is similar to the function below
```javascript
const coerceThenCheckNaN = (val) => {
  const coercedVal = Number(val)
  return coercedVal !== coercedVal ? true : false
}

coerceThenCheckNaN("1a") // true
coerceThenCheckNaN("1") // false
coerceThenCheckNaN("as") // true
coerceThenCheckNaN(NaN) // true
coerceThenCheckNaN(10) // false
```

That's most of implicit coercion. If I missed something, please chime in, in the comments below, and thanks for reading all the way.
