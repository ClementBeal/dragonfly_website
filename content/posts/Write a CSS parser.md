---
author: Clement
title: "Write a CSS Parser"
date: 2024-09-24T13:28:43-03:00
---

As you know, most of the websites are using CSS to customize the appearance of their pages so **Dragonfly** needs a way to parse the CSS in order to apply the styles to the DOM. 

As usual, I want a parser written in Dart. It's something essential to me because I also want to show that *Dart* is an efficient programming language and we can write efficient programs. 

First, I checked the existing solutions. It's not very complicated, there's only one package to parse CSS and it's made by the Dart team. I tried it and it worked! But the package doesn't have documentation and the last update was 15 months ago. It returned me something that should be the CSS's AST but I didn't know what to do with it and, to be honest, I haven't tried to find a solution too.

So as every software engineer, I said I can rewrite it from scratch. It shouldn't be very hard and it probably would be better. How could I be so wrong!

My first very basic parser used `petitparser`. It's a package that allows us to write parsers very quickly. For example,  [xml](https://pub.dev/packages/xml) and [puppeteer](https://pub.dev/packages/puppeteer) implement a parser made with petit parser. After a few hours of work, I had a parser that could return me all the CSS rules! It was working fast or at least, it wasn't very slow.

Unfortunately, I wasn't happy with this implementation because writing a parser is something new to me and the last time I had this exercise was during university where we had to write some compiler with `lex` and `yacc` and I also skipped most of the courses... So I lacked the experience and I had the feeling that what I did with `petitparser` was bad and not maintainable. I remember that if the CSS was wrong, the parser would throw an exception. It's very bad! If we take the following code :

```css
body {
	color: red
}
```

The color declaration misses the final semicolon but it's still usable! The browser cannot refuse to parse the full style sheet because of one error. The UX would be horrible and the web wouldn't be a very pleasant experience. Last thing, I wanted to know the exact position of a declaration in the style sheet. I saw that *Firefox* was doing it and I want that too. 

Now the plan is simple. Write my own CSS parser from scratch! My parser is very inspired by the one made by the Dart team. You can find it [here](https://pub.dev/packages/csslib). It's more complete and error-prone than my current solution. I also want to say that I'm nothing of a parser/compiler expert. It's a new experience and that's also why I'm writing it from scratch. My parser could contain errors.

## Regular expressions?

Let's take a basic style sheet :

```css
body {
	color: red;
}

p {
	margin: 0;
}
```

At first sight, it's very hard to analyze this code. How do we know that `body` is a selector, `color` a property and `red` its value? We could use RegEx! 


```dart
RegExp(r"(\w)+\s*{([^}]*)}")
```

This regular expression could be used to detect a rule and capture its content. As you can see, it's not readable. It's 16 characters but I need several seconds to understand what it's doing. The expression is incomplete. It wouldn't recognize `h1`. The updated regex would be 

```dart
RegExp(r"(\w|\d)+\s*{([^}]*)}")
```

Worse... now with the wild star selector, the `.` class selector, the `#` id selector..

```dart
RegExp(r"(\.|\#)?(\w|\d|\*)+\s*{([^}]*)}")
```

You understand the problem. We could split the expression into multiple smaller strings that we merge in the final expression to improve the readability. Maybe it just needs better organization but it's not the only bad thing about regex in this case. They can't handle nor detect errors. 

```css
bod%y { ... }
```

The previous regex is not able to detect this wrong rule and to report that it's wrong. It wouldn't recognize a media query :

```css
@media screen and (min-width: 900px) {
  /* For the regex, the following rule is a normal rule. */
  article {
    padding: 1rem 3rem;
  }
}
```

Other reasons would be that they can have bad performance like this infamous incident from [StackOverflow](https://stackstatus.tumblr.com/post/147710624694/outage-postmortem-july-20-2016). 

In short, regular expressions are not very maintainable, can have performance issues, can't report parsing errors and may lack context. 


## The lexer/tokenizer

The tokenizer is a component that takes a text input and returns a list of tokens. A token is a piece of information that describes a part of the input. It can be a number, punctuation, commas, words, special keywords (like `if` or `else` in Dart) etc... It simplifies the parsing and the parsing can be done with linear time complexity.

For the following CSS:

```css
body {
  margin: 0;
}
```

The tokens will be :

`IDENTIFIER ; WHITESPACE ; LEFTBRACE ; WHITESPACE ; IDENTIFIER ; COLON ; WHITESPACE ; NUMBER ; SEMICOLON ; RIGHTBRACE`

Or with the CSS structure :

```
IDENTIFIER WHITESPACE LEFTBRACE
   IDENTIFIER COLON WHITESPACE NUMBER SEMICOLON
RIGHTBRACE
```

It looks easier now to parse, right? So how to reach this result?

First, let's define an enum `TokenKind` that defines all the possible kinds of tokens.

```dart
enum TokenKind {
	identifier,
	endOfFile,
	whitespace,
	comment,
	number,
	leftBrace,
	...
}
```

Then we need a `Token` class.

```dart
class Token {
	final TokenKind tokenKind;
	final int startPosition;
	final int endPosition; 
	
	Token(
		this.tokenKind,
		this.startPosition,
		this.endPosition,
	);
}
```

Each token must have a type of token. I also define `startPosition` and `endPosition`. They are cursors to store the token position in the style sheet. It makes the tokens lighter. An int in Dart is almost always 8 bytes and a string is between 2 and 4 bytes per character. So we have 16 bytes of int. I think it's worth it because a lot of token content are very long like comments or some special properties. 

It would probably be worth it to have `int32` in Dart. We will never have `9 223 372 036 854 775 807` characters in a CSS file.

Whatever! Now we can create our `Tokenizer` class.

```dart
class Tokenizer {
	int _index = 0;
	int _startIndex = 0;	  
	
	final String css;
	
	Tokenizer({required this.css});
}
```

The class needs a `css` String. That's the CSS we will tokenize. We also need `_startIndex` to know the offset of a new token and `_index` is a simple cursor.

Our public interface is :

```dart
List<Token> tokenize() {
	final tokens = <Token>[];

	Token currentToken = next();
	tokens.add(currentToken);

	while (currentToken.tokenKind != TokenKind.endOfFile) {
		currentToken = next();
		tokens.add(currentToken);
	} 

	return tokens;
}
```
It has a list of `Token` that will be returned at the end of the method execution. Then it calls the `next()` method. We will see it later in detail. We add this token to the list and we start a loop. This loop creates tokens until it produces an `END OF FILE` token. It's the last token to be created. If the CSS is empty, we only have it.

```dart
Token next() {
	_startIndex = _index;
	var char = _getNextChar();
	
	switch (char) {
	  case TokenChar.endOfFile:
		return _closeToken(TokenKind.endOfFile);
	  default:
		return _closeToken(TokenKind.endOfFile);
	}
}

Token _closeToken(TokenKind kind) {
	return Token(kind, _startIndex, _index);
}

int _getNextChar() {
	if (_index < css.length) {
		return css.codeUnitAt(_index++);
	} else {
		return 0;
	}
}
```

This method is the one producing the tokens. Each call we call it, it returns a token. First, it saves the offset of the current cursor and then it requests the next character with `_getNextChar()`. It's a very simple method that returns 0 if we have already read all the string otherwise, it returns the character at the index position and increments it! After writing this, maybe I should rename it `_getCurrentChar()`...

We have the character so we pass it into a switch. `TokenChar` is a class with constants like this `static const endOfFile = 0;`. It contains all the special characters we expect. We could use `狗` if our grammar needs it. 

If the character matches one of our expected characters, we call a special function to handle this token if it's a multicharacter token or otherwise, we close the token. It creates the appropriate token with the start and end positions.

We could extend our switch like with more cases :

```dart
  case TokenChar.leftParenthesis:
	return _closeToken(TokenKind.leftParenthesis);
  case TokenChar.rightParenthesis:
	return _closeToken(TokenKind.rightParenthesis);
  case TokenChar.leftBracket:
	return _closeToken(TokenKind.leftBracket);
  case TokenChar.rightBracket:
	return _closeToken(TokenKind.rightBracket);
  case TokenChar.leftBrace:
	return _closeToken(TokenKind.leftBrace);
  case TokenChar.rightBrace:
	return _closeToken(TokenKind.rightBrace);
  case TokenChar.semicolon:
	return _closeToken(TokenKind.semicolon);
  case TokenChar.colon:
	return _closeToken(TokenKind.colon);
  case TokenChar.comma:
	return _closeToken(TokenKind.comma);
```

We can tokenize every single character but what about something like a comment? A comment is CSS has this format `/* ... */`. We need a slash then an asterisk to know that we are reading a comment.

```dart
switch(...) {
  case TokenChar.slash:
    if (_maybeEatChar(TokenChar.asterisk)) {
      return _consumeComment();
    }
    return _closeToken(TokenKind.slash);
}

bool _maybeEatChar(int char) {
  if (_index < css.length) {
    if (css.codeUnitAt(_index) == char) {
      _index++;
      return true;
    } else {
      return false;
    }
  } else {
    return false;
  }
}
```

I changed a bit the case for the slash. I want to know if the next character is an asterisk. The method `_mayEatChar()` checks if the next character is the one I expect and returns true. It only increments the index cursor when it matches. It's very useful when we are expecting a bunch of special characters like `--var`  or `<!--`.

That's more or less enough for now. Later, we can add actual error tokens, handle special cases etc. This implementation is quite basic in comparison with the one from the `csslib` package.

## The parser

Now that we have all the tokens, we need to analyze them. We need to build an AST (Abstract Syntax Tree). It's a representation of our style sheet. We could regenerate our style sheet using the AST. It would allow us to minify or prettify it. 

Some possible optimizations :

```css
.card {
	color: red;
}

.title {
	color: red;
}

/* would be  */

.card, .title {
	color: red;
}

/* ------------- */

.card {
	color: #ffffff;
}

/* would be */

.card {
	color: #fff;
}

/* ------------- */

body {
	margin-top: 10px;
	margin-right: 10px;
	margin-bottom: 10px;
	margin-left: 10px;
}

/* would be */

body {
	margin: 10px;
}
```

This AST will be useful because later, I'll be able to use it to generate my CSSOM.

Let's start by writing a tree class.

```dart
abstract class TreeNode {
	final int start;
	final int end;

	TreeNode(this.start, this.end);
	
	dynamic getValue(String text) {}
}
```

Like for the token, the `start` and `end` are here to get the value from the CSS file. The `getValue(...)` will be useful later when we want to minify or create the CSSOM. The return type is `dynamic` because it could return a string, a number, a color, a URI etc...

Now, we can extend this class for each kind of node of our AST. We could have `Stylesheet` to represent all the CSS, `Comment` to represent a comment, `Block` to represent something like this `{ ... }`. 

Some of them will have children like `StyleSheet` or `Block`. Some will not need anything else like `Comment` and some like `Rule` could have more control over the accepted children.

Let's start with `StyleSheet` and `Comments`. A style sheet can only be composed of comments.

```dart
class StyleSheet extends TreeNode {
  final List<TreeNode> children;

  StyleSheet(super.start, super.end, this.children);
}

class Comment extends TreeNode {
  Comment(super.start, super.end);
}
```

Nothing special to say. It's straightforward. Now we can write our parser:

```dart
class CssParser {
  late Token _firstToken;
  late final Tokenizer _tokenizer;

  CssParser(String css) : _tokenizer = Tokenizer(css: css);

  StyleSheet parse() {
    final rules = <TreeNode>[];

    return StyleSheet(0, 0, rules);
  }
}
```

The `CSSParser` accepts a CSS string that it will directly pass to the tokenizer. It also has a `_firstToken` that has a similar role than the `_index` of the tokenizer. It's a "pointer" to the current token to analyze. 

You can also notice the public method `parse()`. It's the main method of our class. It will parse each token and produce a bunch of `TreeNode`. I expect a `StyleSheet` as the return type. 

Now let's create a loop to consume the tokens.

```dart
StyleSheet parse() {
  final rules = <TreeNode>[];

  _firstToken = _tokenizer.next();

  while (_firstToken.tokenKind != TokenKind.endOfFile) {
    if (_firstToken.tokenKind == TokenKind.comment) {
      rules.add(_consumeComment());
    }

    _firstToken = _tokenizer.next();
  }

  return StyleSheet(0, 0, rules);
}

Comment _consumeComment() {
	return Comment(_firstToken.startPosition, _firstToken.endPosition);
}
```

We ask the tokenizer to return a token and we start a loop that only stops when we encounter an `End Of File` token (so at the very end). If the token is a comment token, we consume it. It returns a simple `Comment` node will the start and end of the string. 

The parser has a solid base. It needs to handle more tokens but that's more work.

To give you an example of what I expect at the end :

```css
body {
	color: red;
}
```

Would generate the following AST :

```
StyleSheet
|
|-Rule
|--SelectorList
|----Selector
|------TypeSelector -> value = body
|--Block
|---Declaration -> property = color
|-----|-value = Identifier -> name = red
```

## Conclusion

Writing a parser is hard. I'm lucky because CSS is a simple language and there's no ambiguity so I didn't suffer a lot writing this one. It's a very funny experience and I learned a lot about parsing. 

I was thinking about how to make this implementation faster. I think the tokenizer could be run inside another isolate. It would produce all the tokens and send them to the main isolate to be consumed by the parser. I don't want to do early optimization and I would need benchmarks. Perhaps the time to set up the isolate is way too important. Maybe it could be done only with very big CSS like Bootstrap or Tailwind.

I was talking a lot about why this kind of parser is so great for errors and I didn't show any examples. Very quickly, let's take `body $ { color: red;}`. We would expect to have an `IDENTIFIER`, a `WHITESPACE`, a `LEFT BRACE` etc... In my example, `$` is not expected. The parser would add an error in the parser `errors` attribute with the position of the token, what was expected etc... Then it will try to continue to parse again the following tokens to still load some CSS.

If you want to explore more code, I really recommend reading the repo of [csslib](https://github.com/dart-lang/csslib/tree/main). For now, my implementation is a bad copy of `csslib`. I think that with my explanation, you would navigate through the repo quickly.

