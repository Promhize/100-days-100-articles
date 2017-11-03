---
title: "All you need to know about Javascript's Expressions, Statements and Expression Statements"
published: true
description: The syntactic distinction between expressions and statements. Not knowing these distinctions form the basis for many of Javascript gotchas.
tags: javascript, webdev, frontend
cover_image: https://thepracticaldev.s3.amazonaws.com/i/m1aku5myf19g62srhh0y.jpg
---

By the end of this article, you should be able to describe in detail how the code in the image below works and why it works.

![Example code Object statement expression screentshot](https://thepracticaldev.s3.amazonaws.com/i/tkjfva9zp7kt9tajmxy8.PNG)

There are two major syntactic categories in Javascript:
1. Statements
2. Expressions

It is important to make this distinction because expressions can act like statements, which is why we also have Expression statements. Though, on other the hand, statements cannot act like expressions.

##EXPRESSIONS
###**Expressions produce value**

Expressions are Javascript code snippets that result in a single value. Expressions can be as long as you want them to be, but they would always result in a single value.

```javascript
2 + 2 * 3 / 2

(Math.random() * (100 - 20)) + 20

functionCall()

window.history ? useHistory() : noHistoryFallback()

1+1, 2+2, 3+3

declaredVariable

true && functionCall()

true && declaredVariable
```

All of the above are expressions, and can appear anywhere Javascript expects a value. So that the argument to `console.log` below, resolves to a single value, that is logged to the console.


```javascript
console.log(true && 2 * 9) // 18
```

###**Expressions don’t necessarily change state**

For example,

```javascript
const assignedVariable = 2; //this is a statement, assignedVariable is state

assignedVariable + 4 // expression

assignedVariable * 10 // expression

assignedVariable - 10 // expression

console.log(assignedVariable) // 2
```

Despite all the expressions in the snippet above,  assignedVariable’s value is still 2. So why the `necessarily` in the heading for this section, it’s because function calls are expressions but a function can contain statements that change state. So `foo()` in itself is an expression, that either returns undefined or some other value, but if `foo` was written as


```javascript
const foo = foo () => {
  assignedVariable = 14
}
```

then, even though its call is an expression, its call has also resulted in a state change. So a better way to rewrite the foo function and statement would be:

```javascript
const foo = foo () => {
  return 14 //explicit return for readability
}
assignedVariable = foo()
```

or even better

```javascript
const foo = foo (n) => {
  return n//explicit return for readability
}
assignedVariable = foo(14)
```


This way your code is more readable, composable, and there is a clear distinction and separation between expression and statements. This a fundamental of functional and declarative Javascript.


##STATEMENTS

Statements are the headache of functional programming 😄. Basically, statements perform actions, they do things.

In javascript, statements can never be used where a value is expected. So they cannot be used as function arguments, right-hand side of assignments, operators operand, return values…

```javascript
foo(if () {return 2}) //js engine mind = blown
```

These are all javascript statements:

1. if
2. if-else
3. while
4. do-while
5. for
6. switch
7. for-in
8. with (deprecated)
9. debugger
10. variable declaration

If you type the snippet below in your browser’s console and hit enter

```javascript
if (true) {9+9}
```

you will see that it returns `18` but despite that you cannot use it as an expression or where Javascript expects a value. It is weird because you’d expect statements not to return anything, since the return value is pretty much useless if you cannot use it. That’s Javascript for you, weird.

###**Function declarations, Function expressions and Named Function expressions**

A function declaration is a statement

```javascript
function foo (func) {
  return func.name
}
```

A function expression is an expression, what you call an anonymous function

```javascript
console.log(foo(function () {} )) // ""
```

A named function expression is an expression, like an anonymous function, but it has a name

```javascript
console.log(foo(function myName () {} )) // "myName"
```

The distinction between function as an expression and function as a declaration boils down to understanding this:
**whenever you declare a function where Javascript is expecting a value, it will attempt to treat it as a value, if it can’t use it as a value, an error will be thrown.
Whereas declaring a function at the global level of a script, module, or top level of a block statement (that is, where it is not expecting a value), will result in a function declaration.**

Examples:

```javascript
if () {
  function foo () {} // top level of block, declaration
}

function foo () {} //global level, declaration

function foo () {
  function bar() {} //top level of block, declaration
}

function foo () {
  return function bar () {} // named function expression
}

foo(function () {}) // anonymous function expression

function foo () {
  return function bar () {
    function baz () {} // top level of block, declaration
  }
}

function () {} // SyntaxError: function statement requires a name

if (true){
  function () {} //SyntaxError: function statement requires a name
}
```

###**Converting Expressions to Statements: Expression Statements**

Is anything ever simple and straightforward with Javascript 😃

```javascript
2+2; //expression statement
foo(); //expression statement
```
You can convert expressions to expression statement, just by adding a semi-colon to the end of the line or allowing [automatic semi-colon insertion](https://dev.to/promhize/what-you-need-to-know-about-javascripts-automatic-semi-colon-insertion-78a) to do the work. `2+2` itself is an expression but the complete line is a statement.

```javascript
2+2 // on its own is an opposition

foo(2+2) //so you can use it anywhere a value is expected

true ? 2+2 : 1 + 1

function foo () {return 2+2}


2+2; //expression statement
foo(2+2;) //syntaxError
```

###**Semi-colon vs Comma operator**

With semi-colon, you can keep multiple statements on the same line

```javascript
const a; function foo () {}; const b = 2
```

The comma operator allows you to chain multiple expression, returning only the last expression

```javascript
console.log( (1+2,3,4) ) //4

console.log( (2, 9/3, function () {}) ) // function (){}

console.log( (3, true ? 2+2 : 1+1) ) // 4
```

>Sidenote: one way to tell the Javascript engine to expect a value is via parentheses, (), without the parentheses, each expression will be treated as an argument to console.log.


```javascript
function foo () {return 1, 2, 3, 4}
foo() //4
```
All the expressions will be evaluated from left to right, and the last one will be returned.

###**IIFEs (Immediately Invoked Function Expressions)**

An anonymous function can be an expression, if we use it where Javascript is expecting a value, that means we if we can tell Javascript to expect a value with parentheses, we can pass an anonymous function as that value.

```javascript
function () {}
```
So while the snippet above is invalid, the snippet below is valid

```javascript
(function () {}) // this returns function () {}
```

If putting a anonymous function inside a parentheses immediately returns the same anonymous function, that means we can call it straight away, like this:

```javascript
(function () {
  //do something
})()
```

So, these are possible

```javascript
(function () {
  console.log("immediately invoke anonymous function call")
})() // "immediately invoke anonymous function call"

(function () {
  return 3
})() // 3

console.log((function () {
  return 3
})()) // 3

//you can also pass an argument to it
(function (a) {
  return a
})("I'm an argument") // I'm an argument
```

###**Object literals vs Block Statements**

>Sidenote: this is valid Javascript

```javascript
r: 2+2 // valid

foo()

const foo = () => {}
```

The above are sequence of statements in the global scope that will be parsed as valid Javascript and executed. The `r` is what you’ll call a label, and they are mostly useful in breaking loops. Example:

```javascript
loop: {
  for (const i = 0; i < 2; i++) {
    for (const n = 0; n <2; n++) {
      break loop //breaks outer loop and stops entire loop
    }
  }
}
```

You can prepend a label to any expression or expression statement, note that you not are creating a variable lab  by doing this:

```javascript
lab: function a () {}
console.log(lab) //ReferenceError: lab is not defined
```

Curly braces, {}, allow you to group expression statements and statements. So you can write,

```javascript
{var a = "b"; func(); 2+2} // 4
```

If you paste the above in your browsers console, it will return 4 and when you do `console.log(a)`, you will get string `b`. You can call that a block statement, which is different from the object literal you might be used to.


```javascript
console.log({a: 'b'}) // {a: 'b'}

console.log({var a = "b", func(), 2+2}) // SyntaxError

const obj = {var a = "b", func(), 2+2} // SyntaxError
```

You cannot use a block statement as a value or expression, because console.log is a function, it cannot accept a statement as an argument. It can accept an object literal though. 
I hope you understood all I explained above, cause the next snippet below might throw you off.


```javascript
{} + 1 //1

{2} + 2 // 2

{2+2} + 3 // 3

{2+2} -3 // -3

```

You might expect it to throw either a syntax error or to return 1, 4, 7 respectively. Remember statements aren’t supposed to return anything because they can’t be used as values. So Javascript rather throwing an error, attempts to convert the operands of the `+` operator to a number or string, if it can’t then it throws. So whatever is returned by the block statement, is implicitly coerced to `0` used as the operand.

Whew, if you read all the way, you are the real MVP. That probably is all you need to know about Expressions, Statements, and Expression Statements.
