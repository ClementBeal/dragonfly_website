---
author: Clement
title: "Main Concept of a Web Engine"
description: ""
date: 2024-09-12T13:59:58-03:00
---

When I started this project, I searched for information on Google explaining how a browser works. Quickly, I noticed that there are almost no sources with correct explanations. They all provide very basic information. That's why I want to share my current knowledge to help you if you want to build a web engine from scratch as well.

As you know, the first thing that a browser does is download an HTML file. It contains two important tags: `<head>` and `<body>`. The `<head>` element contains the metadata of the page, like the title, description, language, and so on. It's this element that contains the URLs to the CSS and JS files. The `<body>` provides the content of the page. The browser will only render the body, although it's not completely true because the `<head>` has the `display: none` style, so you could render the metadata if you wanted to.

That's nice. The browser has the HTML, but how does it know how to render it in the viewport? That's the complex part of the process.

## The DOM

First, it parses the HTML and builds the DOM. The DOM (Document Object Model) is a data structure that enables the browser to easily manipulate the HTML. It's very useful because it makes an HTML page "dynamic." There's a JavaScript API that manipulates the DOM; it can remove or add elements to the DOM. I forgot to mention that the DOM is a tree because, by nature, HTML has a hierarchy between elements.

The HTML parser is not a simple `.xml` parser; it's much more complex. Let's take this case: The browser downloads an HTML file that is not correctly formatted, and some tags are not closed. In this case, if the parser raised an error, the user would see a blank or error page. As you can see, that would be a very poor user experience. That's why HTML parsers are smart and can decide to apply some changes if the HTML is bad. For example, you could open an HTML file with the following code `<p>I don't close it` and it would still work.

Now the parser can build the DOM. For each HTML element, a tree node will be created. Of course, the children of an element will be the children of its node too. The nodes will contain all the information provided by the HTML: the tag, classes, id, attributes, etc.

## The CSSOM

The CSSOM (CSS Object Model) is the sibling of the DOM. It's exactly the same thing but for the CSS. The stylesheet is made up of a bunch of rules. Each rule is a child of the root. We also use a tree because there's a hierarchy within the rules, which we notice when using selectors (`div > p`). It's also useful when the CSS changes dynamically, something that websites do frequently.

## The Render Tree

We can't render a page with only the DOM and the CSSOM. The browser has to apply the correct styles to each DOM node, which is not an easy task due to all the CSS rules and selectors that can be defined. That's why a browser must run this very important step.

The render tree is an abstraction of the DOM and the CSSOM. A `<p>` or an `input` are rendered differently, and the same goes for `<h1>`. The browser defines special render nodes that contain information about how to render the DOM/CSSOM. For instance, we could have:

- `RenderText`: Renders text. It could be used to render `<h1>`, `<p>`, `<span>`, etc. The node will also contain the font size, color, letter spacing, and other properties.
- `RenderImage`: Renders an image. To render an image, it will need to decode formats like `png`, `jpeg`, `webp`, etc. The node will have an image URL, the image's dimensions, and details on whether it should fit the height or width.
- `RenderTextInput`: Renders a text input. The user can enter data, and the field can have validation.

The nodes depend on the implementer, but they mainly depend on the `display` property and if the node has a special rendering. We will have custom render nodes for pictures, videos, audios, forms, tables, and similar cases if the display is `grid`, `flex`, or `none`.

This is a very important step because we compute the units. Most font sizes use the `em` unit, which is based on the parent's font size and applies a factor. It's also important to handle style cascading (using the parent's values), media queries, variables, and more.

## The Layout

During this step, the engine computes the position and dimensions of every render node. It will use the margin, padding, width, and height. If the node is a flexbox (`display: flex`), it will position the children correctly.

This step also determines the different layers. As you know, we have the `z-index` property that allows elements to be layered over one another.

The following video is a visualization of this step. It was made 15 years ago with the Gecko engine, the former web engine of Mozilla Firefox.

{{< youtube ZTnIxIA5KGw >}}

## The Rendering

This step simply renders the layout onto the screen. During this step, the engine will draw the render tree to the position and dimensions computed by the layout. It will draw lines, circles, and more.
