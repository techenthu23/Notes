---
title:  "Cascading Style Sheets"
draft: false
---

- [CSS Introduction](#css-introduction)
  - [What is CSS?](#what-is-css)
- [CSS Syntax](#css-syntax)
- [CSS Selectors](#css-selectors)
  - [CSS id Selector](#css-id-selector)

# CSS Introduction

## What is CSS?

- CSS stands for Cascading Style Sheets
- CSS describes how HTML elements are to be displayed on screen, paper, or in other media
- CSS saves a lot of work. It can control the layout of multiple web pages all at once
- External stylesheets are stored in CSS files

HTML was NEVER intended to contain tags for formatting a web page and was created to describe the content of a web page.

```html
<h1>This is a heading</h1>

<p>This is a paragraph.</p>
```

When tags like `<font>`, and color attributes were added to the HTML 3.2 specification, it started a nightmare for web developers. Development of large websites, where fonts and color information were added to every single page, became a long and expensive process.

To solve this problem, the World Wide Web Consortium (W3C) created CSS.

CSS removed the style formatting from the HTML page! The style definitions are normally saved in external .css files. With an external stylesheet file, you can change the look of an entire website by changing just one file!

# CSS Syntax

A CSS rule consists of a selector and a declaration block.
The selector points to the HTML element you want to style.
The declaration block contains one or more declarations separated by semicolons.
Each declaration includes a CSS property name and a value, separated by a colon.
Multiple CSS declarations are separated with semicolons, and declaration blocks are surrounded by curly braces.

Example :
In this example all `<p>` elements will be center-aligned, with a red text color:

```css
p {
  color: red;
  text-align: center;
}
```

In this example

- `p` is a selector in CSS (it points to the HTML element you want to style: `<p>`).
- `color: red` color is a property, and red is the property value

# CSS Selectors

A CSS selector selects the HTML element(s) you want to style based on the element name.
CSS selectors are used to "find" (or select) the HTML elements you want to style.

We can divide CSS selectors into five categories:

- Simple selectors (select elements based on name, id, class)
- Combinator selectors (select elements based on a specific relationship between them)
- Pseudo-class selectors (select elements based on a certain state)
- Pseudo-elements selectors (select and style a part of an element)
- Attribute selectors (select elements based on an attribute or attribute value)

## CSS id Selector

The id selector uses the id attribute of an HTML element to select a specific element. The id of an element is unique within a page, so the id selector is used to select one unique element!

To select an element with a specific id, write a hash (#) character, followed by the id of the element.

Example
The CSS rule below will be applied to the HTML element with id="para1":

```css
#para1 {
  text-align: center;
  color: red;
}
```

__Note: An id name cannot start with a number!__
