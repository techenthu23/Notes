# Markdown

- [Markdown](#markdown)
- [HTML Elements](#html-elements)
- [Headings](#headings)
- [Simple text styles](#simple-text-styles)
- [Paragraphs](#paragraphs)
- [Lists](#lists)
- [Code blocks](#code-blocks)
- [Horizontal rule](#horizontal-rule)
- [Links](#links)
- [Table of contents](#table-of-contents)
- [Images](#images)
- [Auto-links](#auto-links)
- [Auto-links for emails](#auto-links-for-emails)
- [Escaping characters](#escaping-characters)
- [Keyboard keys](#keyboard-keys)
- [Tables](#tables)
  

Markdown was created by John Gruber in 2004. It’s meant to be an easy to read and write syntax which converts easily to HTML (and now many other formats as well).

Markdown also varies in implementation from one parser to a next. This guide will attempt to clarify when features are universal or when they are specific to a certain parser.

# HTML Elements

Markdown is a superset of HTML, so any HTML file is valid Markdown.

<!--This means we can use HTML elements in Markdown, such as the comment element, and they won't be affected by a markdown parser. However, if you create an HTML element in your markdown file, you cannot use markdown syntax within that element's contents.-->

# Headings

You can create HTML elements `<h1>` through `<h6>` easily by prepending the text you want to be in that element by a number of hashes (#).

```markdown

| # Heading level 1      | <h1>Heading level 1</h1> | Heading level 1 |
| :--------------------- | :----------------------- | :-------------- |
| ## Heading level 2     | <h2>Heading level 2</h2> | Heading level 2 |
| ### Heading level 3    | <h3>Heading level 3</h3> | Heading level 3 |
| #### Heading level 4   | <h4>Heading level 4</h4> | Heading level 4 |
| ##### Heading level 5  | <h5>Heading level 5</h5> | Heading level 5 |
| ###### Heading level 6 | <h6>Heading level 6</h6> | Heading level 6 |
```

Alternatively, on the line below the text, add any number of == characters for heading level 1 or -- characters for heading level 2.

```markdown
This is an h1
=============

This is an h2
-------------
```

# Simple text styles

Text can be easily styled as italic or bold using markdown.
- - -

```markdown
*This text is in italics.*
*And so is this text.*

**This text is in bold.**
**And so is this text.**

***This text is in both.***
***As is this!***
***And this!***
```


*This text is in italics.*
*And so is this text.*

**This text is in bold.**
**And so is this text.**

***This text is in both.***
***As is this!***
***And this!***
- - -

In GitHub Flavored Markdown, which is used to render markdown files on GitHub, we also have strikethrough:
- - -
```markdown
~~This text is rendered with strikethrough.~~
```
~~This text is rendered with strikethrough.~~
- - -

# Paragraphs

Paragraphs are a one or multiple adjacent lines of text separated by one or multiple blank lines.
- - -
```markdown
This is a paragraph. I'm typing in a paragraph isn't this fun?

Now I'm in paragraph 2.
I'm still in paragraph 2 too!

I'm in paragraph three!
```

This is a paragraph. I'm typing in a paragraph isn't this fun?

Now I'm in paragraph 2.
I'm still in paragraph 2 too!

I'm in paragraph three!
- - -

```markdown
I end with two spaces (highlight me to see them).  
There's a <br /> above me!
```

I end with two spaces (highlight me to see them).  
There's a <br /> above me!
- - -

Block quotes are easy and done with the > character.

```markdown
> This is a block quote. You can either
> manually wrap your lines and put a `>` before every line or you can let your lines get really long and wrap on their own.
> It doesn't make a difference so long as they start with a `>`.

> You can also use more than one level
>> of indentation?
> How neat is that?

```

> This is a block quote. You can either
> manually wrap your lines and put a `>` before every line or you can let your lines get really long and wrap on their own.
> It doesn't make a difference so long as they start with a `>`.

> You can also use more than one level
>> of indentation?
> How neat is that?
- - -

# Lists

Unordered lists can be made using asterisks, pluses, or hyphens.
- - -

```markdown
* Item
* Item
* Another item

or

+ Item
+ Item
+ One more item

or

- Item
- Item
- One last item
```

* Item
* Item
* Another item

or

+ Item
+ Item
+ One more item

or

- Item
- Item
- One last item
- - -

Ordered lists are done with a number followed by a period.
- - -

```markdown
1. Item one
2. Item two
3. Item three
```

1. Item one
2. Item two
3. Item three
- - -

You don’t even have to label the items correctly and Markdown will still render the numbers in order, but this may not be a good idea.
- - -

```markdown
1. Item one
1. Item two
1. Item three
```

1. Item one
1. Item two
1. Item three
- - -
(This renders the same as the example above.)

You can also use sublists.
- - - 

```markdown
1. Item one
2. Item two
3. Item three
    * Sub-item
    * Sub-item
4. Item four
```

1. Item one
2. Item two
3. Item three
    * Sub-item
    * Sub-item
4. Item four
- - -

There are even task lists. This creates HTML checkboxes.
- - -

```markdown
Boxes below without the 'x' are unchecked HTML checkboxes.
- [ ] First task to complete.
- [ ] Second task that needs done
This checkbox below will be a checked HTML checkbox.
- [x] This task has been completed
```

Boxes below without the 'x' are unchecked HTML checkboxes.
- [ ] First task to complete.
- [ ] Second task that needs done
This checkbox below will be a checked HTML checkbox.
- [x] This task has been completed
- - -

# Code blocks

You can indicate a code block (which uses the `<code> `element) by indenting a line with four spaces or a tab.
- - -

```
    ```markdown
        This is code
        So is this
    ```
```
- - -

You can also re-tab (or add an additional four spaces) for indentation inside your code.
- - -

```
    ```ruby
        my_array.each do |item|
            puts item
        end
    ```
```

```ruby
my_array.each do |item|
    puts item
end
```
- - -
Inline code can be created using the backtick character `

```
John didn't even know what the `go_to()` function did!
```
John didn't even know what the `go_to()` function did!
- - -
In GitHub Flavored Markdown, you can use a special syntax for code.
- - -
```
    ```ruby
    def foobar
        puts "Hello world!"
    end
    ```
```

```ruby
def foobar
    puts "Hello world!"
end
```
- - -

The above text doesn’t require indenting, plus GitHub will use syntax highlighting of the language you specify after the opening ```.

# Horizontal rule


Horizontal rules (`<hr/>`) are easily added with three or more asterisks or hyphens, with or without spaces.
- - -
```
***
---
- - -
****************
___
_ _ _
```

***
---
- - -
****************
___
_ _ _

# Links
One of the best things about markdown is how easy it is to make links. Put the text to display in hard brackets [] followed by the url in parentheses ()



`[Click me!](http://test.com/)`

[Click me!](http://test.com/)


You can also add a link title using quotes inside the parentheses.

`[Click me!](http://test.com/ "Link to Test.com")`

[Click me!](http://test.com/ "Link to Test.com")

Relative paths work too.

`[Go to music](/music/).`

[Go to music](/music/).

Markdown also supports reference style links.

```
[Click this link][link1] for more info about it!
[Also check out this link][foobar] if you want to.

[link1]: http://test.com/ "Cool!"
[foobar]: http://foobar.biz/ "Alright!"
```

[Click this link][link1] for more info about it!
[Also check out this link][foobar] if you want to.

[link1]: http://test.com/ "Cool!"
[foobar]: http://foobar.biz/ "Alright!"
- - -

The title can also be in single quotes or in parentheses, or omitted entirely. The references can be anywhere in your document and the reference IDs can be anything so long as they are unique.

There is also “implicit naming” which lets you use the link text as the id.
- - -
```
[This][] is a link.

[This]: http://thisisalink.com/
```
[This][] is a link.

[This]: http://thisisalink.com/
- - -

Markdown applications don’t agree on how to handle spaces in the middle of a URL. For compatibility, try to URL encode any spaces with `%20`. Alternatively, if your Markdown application supports HTML, you could use the a HTML tag.
Parentheses in the middle of a URL can also be problematic. For compatibility, try to URL encode the opening parenthesis (() with %28 and the closing parenthesis ()) with %29. 

```
[link](https://www.example.com/my great page)
[link](https://www.example.com/my%20great%20page)
<a href="https://www.example.com/my great page">link</a>

[a novel](https://en.wikipedia.org/wiki/The_Milagro_Beanfield_War_%28novel%29)
```
[link](https://www.example.com/my great page)
[link](https://www.example.com/my%20great%20page)
<a href="https://www.example.com/my great page">link</a>

[a novel](https://en.wikipedia.org/wiki/The_Milagro_Beanfield_War_%28novel%29)
- - -

# Table of contents
Some Markdown flavors even make use of the combination of lists, links and headings in order to create tables of contents. In this case, heading titles in lowercase are prepended with hash (#) and are used as link ids. Should the heading have multiple words, they will be connected with a hyphen (-), that also replaces some special characters. (Some other special characters are omitted though.)

```
- [Heading](#heading)
- [Another heading](#another-heading)
- [Chapter](#chapter)
  - [Subchapter <h3 />](#subchapter-h3-)
```

Nonetheless, this is a feature that might not be working in all Markdown implementations the same way.
- - -
# Images

Images are done the same way as links but with an exclamation point in front!
```
![This is the alt-attribute for my image](http://imgur.com/myimage.jpg "An optional title")
```
![This is the alt-attribute for my image](http://imgur.com/myimage.jpg "An optional title")

And reference style works as expected.
```
![This is the alt-attribute.][myimage]

[myimage]: relative/urls/cool/image.jpg "if you need a title, it's here"
```
![This is the alt-attribute.][myimage]

[myimage]: relative/urls/cool/image.jpg "if you need a title, it's here"
- - -

# Auto-links
```
<http://testwebsite.com/> is equivalent to
[http://testwebsite.com/](http://testwebsite.com/)
```
<http://testwebsite.com/> is equivalent to
[http://testwebsite.com/](http://testwebsite.com/)
- - -

# Auto-links for emails
```
<foo@bar.com>
```
<foo@bar.com>
- - -

# Escaping characters
```
I want to type *this text surrounded by asterisks* but I don't want it to be
in italics, so I do this: \*this text surrounded by asterisks\*.
```
I want to type *this text surrounded by asterisks* but I don't want it to be
in italics, so I do this: \*this text surrounded by asterisks\*.

You can use a backslash to escape the following characters.

```
Character 	Name
\ 	        backslash
` 	        backtick (see also escaping backticks in code)
* 	        asterisk
_ 	        underscore
{ } 	    curly braces
[ ] 	    brackets
< > 	    angle brackets
( ) 	    parentheses
# 	        pound sign
+ 	        plus sign
- 	        minus sign (hyphen)
. 	        dot
! 	        exclamation mark
| 	        pipe (see also escaping pipe in tables)
```
- - -

# Keyboard keys

In GitHub Flavored Markdown, you can use a <kbd> tag to represent keyboard keys.
```
Your computer crashed? Try sending a
<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd>
```
Your computer crashed? Try sending a
<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd>
- - -

# Tables

Tables are only available in GitHub Flavored Markdown and are slightly cumbersome, but if you really want it:
```
| Col1         | Col2     | Col3          |
| :----------- | :------: | ------------: |
| Left-aligned | Centered | Right-aligned |
| blah         | blah     | blah          |
```
| Col1         | Col2     | Col3          |
| :----------- | :------: | ------------: |
| Left-aligned | Centered | Right-aligned |
| blah         | blah     | blah          |
- - -

or, for the same results

```
Col 1 | Col2 | Col3
:-- | :-: | --:
Ugh this is so ugly | make it | stop
```
Col 1 | Col2 | Col3
:-- | :-: | --:
Ugh this is so ugly | make it | stop
- - -
