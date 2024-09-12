---
author: Clement
title: "Main concept of a web engine"
description: '"'
date: 2024-09-12T13:59:58-03:00
---

# Main concept of a web engine

When I started this project, I have been searching for information on Google explaining how works a browser. Quickly, I have notices that there is almost no source with correct explainations.They all provide very basic information. It's why I want to give you my current knowledges to help you if you want to build a web engine from scratch as well.

As you know, the first thing that a browser does is downloading an HTML file. It contains two importants tags : `<head>` and `body`. The `<head>` element contains the metadata of the page like the title, the description, the language and so on. It's this element that contains the URLs to the CSS and JS files. The `<body>` provides the content of the page. The browser will only render the body althought it's not completely true because `<head>` has the `display: none` style so you could render the metadata if you want to.

That's nice. The browser has an HTML but how does it know how to render it in the viewport? That's the complex parts of the process.

## The DOM

First, it parses the HTML and builds the DOM. The DOM (Document Object Model) is a data structure that ables the browser to easily manipulates the HTML. It's very useful because it makes an HTML page "dynamic". There's Javascript API that manipulates the DOM. It can remove or add elements to the DOM. I forget to say that the DOM is a tree because by nature, the HTML has a hierarchy between the elements.

The HTML parser is not a simple `.xml` parser. It's way more complex. Let's take this case. The browser download an HTML that is not correctly formatted. Some tags are not closed. In this case, the parser will raise an error and the user will have a blank or an erro page. As you can see, it's a very poor user experience... That's why the HTML parser are smart and can decide to apply some changes if the HTML is bad. You could try to open an HTML file with the following code `<p>I don't close it` and it will stil work.

Now the parser can build the DOM. For each html element, a tree node will be created. Of course, the children of an element will be children of its node too. The nodes will contains all the information that provides the html : the tag, the classes, the id, the attributes etc...


## The CSSOM

The CSSOM (CSS Object Document) is the brother of the DOM. It's exactly the same thing but with the CSS. The stylesheet is made of a bunch of rules. Each rule is the child of the root. We also use a tree because there's a hierarchy inside the rules. We notice it when we use selectors (`div > p`). It's also useful when the CSS changes dynamically, something that a website does a lot.

## The render tree

We can't render a page with only the DOM and the CSSOM. The browser has to apply the correct styles to each DOM nodes and that's not an easy thing because of all the CSS rules that can be defined and the selectors. That's why a browser must run this very important step.

The render tree is an abstraction of the DOM and the CSSOM. A `<p>` or an `input` are rendered differently, the same with `<h1`>.  The browser define some special render nodes that contains information about how to render the DOM/CSSOM. For instance, we could have :

- `RenderText` : renders a text. It could be used to render `<h1>`, `<p>`, `<span>` etc... The node will also contains the font size, the color, the letter spacing and other properties.
- `RenderImage` : renders an image. To render an image, it will need to decode `png`, `jpeg`, `webp` etc... The will will have an image URL, the image's dimensions, if it should fit the height or the width...
- `RenderTextInput` : renders a text input. The user can enter data and the field can have validation.

The nodes depends of the implementer but it mainly depends of the `display` property and if the node has very special rendering. We will have custom render nodes for pictures, videos, audios, forms, tables and the same if the display is `grid`, `flex` or `none`.

It's a very important step because we will compute the units. Most of the font sizes use the `em` unit. It's a unit using the parent's font size and applies a factor. It's important to do the style cascading (use the parent's values) and some other stuff like media queries, variables and more,

## The layout

During this step, the engine computes the position and dimension of every render nodes. It will use the margin and the padding, the width and height. Also if the node is a flex box (`display: flex`), it will put the children to the good position.

It's also here that we will have differents layers. As you know, we have the `z-index` property that we can use to have elements all over each other.

The following video is a visualization of this step. It was made 15 years ago with the Geko engine, the former web engine of Mozilla Firefox.

{{< youtube ZTnIxIA5KGw >}}

## The rendering

It's simply render the layout into the screen. During this step, the engine will draw the render tree to the position and dimension computed by the layout. It will draw lines, circles and more.


