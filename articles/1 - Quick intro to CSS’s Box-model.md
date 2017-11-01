---
title: Quick intro to CSS’s Box-model
published: true
description: A quick intro to what you need to know about CSS's box-model, what to avoid and what’s best to use.
tags: css, frontend, html
---

This is a quick intro to what you need to know about the box-model, what to avoid and what’s best to use.

The CSS box model is quite simple. Every HTML element is represented as a rectangular box taking up different portions of the users screen. As in the space taken up by the `div` element below for example

```
<div>A div that will take up some rectangular space on the screen</div>
```

![A screenshot of the div](https://thepracticaldev.s3.amazonaws.com/i/0kwmqp0hwgsx8ibcqc5p.png)


The `div` tag above is a BLOCK element taking up the height of its content (text, in this case) and the full width of its parent element. In this case, its parent element is the `body` which takes up the full width of its parent element, the `html` element. The `html` element is the root element in any HTML document, so it automatically takes up 100% of the screen width, so every BLOCK element that is its descendant takes up 100% of the screen width as well, unless it’s width is explicitly set with CSS.

Back to the box model, four things make up the box of any HTML element,

1. Width and height
2. Padding
3. Border
4. Margin

![Box representing the css box-model](https://thepracticaldev.s3.amazonaws.com/i/mt8rd6vs99689tfifnvn.png)

In CSS, there are two ways to calculate the box-model for an element, they are,

1. content-box
2. border-box

The content-box is the model that browsers use by default, and it is the less desirable one.
 
The way the content-box model works, is, you specify the width and height for an element, any further additions to the box of any element will be added to its width and height. For example,

```
div {
  box-sizing: content-box;
  width: 200px;
  padding: 50px;
}
```

We have specified the width of the `div` element to take up `200px` but with the content box model, `div` will take up a width of `300px` instead. That is, any further additions via padding or border will be added to the element’s width to make up the actual space it would take up on the users screen.

The border-box model on the other hand, takes the specified width and height as the total space an element will take up on the users screen, regardless of how much padding or border is added to the element.

```
div {
  box-sizing: border-box;
  width: 200px;
  padding: 50px;
}
```

In the code example above, div will take up a width of 200px on the users screen and it’s content or children will have only 100px to work with. So if I wrote

```
<div>
<span></span>
<div>

div {
  box-sizing: border-box;
  width: 200px;
  padding: 50px;
}

div span {
  width: 100%;
}
```
The width of span would be 100px


![Representation of border-box example](https://thepracticaldev.s3.amazonaws.com/i/3phn1iexj4r6hc1ikl4r.png)

The margin in the box in both models doesn’t get subtracted from the specified width. So far example, if you wrote,

```
div {
  box-sizing: border-box;
  width: 200px;
  margin: 50px;
}
div {
  box-sizing: content-box;
  width: 200px;
  margin: 50px;
}
```
Both do the same thing, there is  no difference in what is translated to the screen.

The content-box model can get confusing and requires you to always keep paddings and border values applied to elements in my mind. With the border-box model you are guaranteed that whatever width you specify would remain the same regardless of the padding and border, which is easier to remember.

Rather than specifying the `box-sizing` property for every element, you would usually find this snippet in most CSS projects

```
*, *::before, *::after {
  box-sizing: border-box;
}
```

This effectively causes every element on the page to use the `border-box` box model and also applies it to the `before` and `after` pseudo-elements.

That’s a wrap on box-model. For more info, checkout MDN’s awesome documentation https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing
