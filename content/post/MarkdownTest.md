+++
title = "An exhibit of Markdown"
date = "2015-12-02T23:21:16-05:00"
tags = ["markdown"]
categories = ["Tech"]
menu = ""
+++

This note demonstrates some of what wordpress Markdown is capable of doing.

<!--more-->

## References

http://www.markitdown.net/markdown

http://en.support.wordpress.com/markdown-quick-reference/

https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

https://guides.github.com/features/mastering-markdown/


## Markdown quick reference for wordpress

http://en.support.wordpress.com/markdown-quick-reference/

Special shortcodes can be embedded in [email](http://en.support.wordpress.com/post-by-email/) to configure the published post, e.g., `[more]`, `[delay +1 hour]` etc.

*Emphasize* _emphasize_  
**Strong** __Strong__  

A [link](http://example.com "Title").

Some text with [a link][1] and another [link][2].

[1]: http://example.com/ "Title"
[2]: http://example.org/ "Title"

Images Inline-style: 
![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")


Linked Images:
[![alt text](http://s.wordpress.org/about/images/wpmini-grey.png)](http://wordpress.com/ "Title")

Footnotes: I have more[^1] to say up here.

[^1]: To say down here.

Lists

1. Item
2. Item
   * Mixed 1
   * Mixed 1
     * Mixed 2
     * Mixed 2
   - Or minuses
     + Or pluses
3. Item

Break it apart.

* Unordered list nicely aligned but not properly rendered
  - Or minuses
  - Or minuses
    + Or pluses
    + Or pluses

Now,

*  Unordered list properly rendered
   * Mixed 1
   * Mixed 1
     * Mixed 2
     * Mixed 2
   - Or minuses
     + Or pluses
*  Back to level one

Preformatted

  Begin each line with 
  two spaces or more to 
  make text look
  e x a c t l y 
  like  you  type i
  t.

Code block

```css
#button {
	border: none;
}
```

http://en.support.wordpress.com/code/posting-source-code/

The following languages are supported:

- actionscript3
- bash
- clojure
- coldfusion
- cpp
- csharp
- css
- delphi
- erlang
- fsharp
- diff
- groovy
- html
- javascript
- java
- javafx
- matlab (keywords only)
- objc
- perl
- php
- text
- powershell
- python
- r
- ruby
- scala
- sql
- vb
- xml

Definition Lists

WordPress
:  A semantic personal publishing platform 

Markdown
:  Text-to-HTML conversion tool

Abbreviations

Markdown converts text to HTML.

*[HTML]: HyperText Markup Language
Definitions can be anywhere in the document

## More Markdown Examples from [Pandoc-Markdown](http://johnmacfarlane.net/pandoc/README.html#pandocs-markdown)

http://www.unexpected-vortices.com/sw/rippledoc/quick-markdown-example.html

Use 3 dashes for --- an em-dash. Use 2 dashes for ranges (ex., "it's all
in chapters 12--14"). Three dots ... will be converted to an ellipsis.
Unicode is supported.

Inline math equations go in like so: $\omega = d\phi / dt$. Display
math should get its own line and be put in in double-dollarsigns:

$$I = \int \rho R^{2} dV$$

And note that you can backslash-escape any punctuation characters
which you wish to be displayed literally, ex.: \`foo\`, \*bar\*, etc.

> Block quotes are
> written like so.
>
> They can span multiple paragraphs,
> if you like.

Here's a "line block":

| Line one
|   Line too
| Line tree

Tables can look like this:

size  material      color
----  ------------  ------------
9     leather       brown
10    hemp canvas   natural
11    glass         transparent

Table: Shoes, their sizes, and what they're made of

(The above is the caption for the table.) Pandoc also supports
multi-line tables:

--------  -----------------------
keyword   text
--------  -----------------------
red       Sunsets, apples, and
          other red or reddish
          things.

green     Leaves, grass, frogs
          and other things it's
          not easy being.
--------  -----------------------

[nextpage]

## Emphasis

Emphasis, aka italics, with *asterisks* or _underscores_.

Strong emphasis, aka bold, with **asterisks** or __underscores__.

Combined emphasis with **asterisks and _underscores_**.

Strikethrough uses two tildes. ~~Scratch this.~~

## Lists

1. First ordered list item
2. Another item
  * Unordered sub-list. 
1. Actual numbers don't matter, just that it's a number
  1. Ordered sub-list
4. And another item.

   You can have properly indented paragraphs within list items. Notice the blank line above, and the leading spaces (at least one, but we'll use three here to also align the raw Markdown).

   To have a line break without a paragraph, you will need to use two trailing spaces.  
   Note that this line is separate, but within the same paragraph.  
   (This is contrary to the typical GFM line break behaviour, where trailing spaces are not required.)

* Unordered list can use asterisks
- Or minuses
+ Or pluses

## URLs

URLs can be made in a handful of ways:

* http://github.com - automatic!
* A named link to [MarkItDown][1]. The easiest way to do these is to select what you want to make a link and hit `Ctrl+L`.
* Another named link to [MarkItDown](http://www.markitdown.net/)

Some text to show that the reference links can follow later.

[arbitrary case-insensitive reference text]: https://www.mozilla.org
[1]: http://www.markitdown.net/
[link text itself]: http://www.reddit.com

<a name="internal"/>

### internal links / named anchors

For Markdown's support for internal links / named anchors,

http://stackoverflow.com/questions/2822089/how-to-link-to-part-of-the-same-document-in-markdown/6494918#6494918

obvious solution is to place your own anchor point in the page wherever you like, thus:

<a name="abcde"/>
before the line you want to 'link' to. Don't forget the quotation marks around it. Then a markdown link like:

[link text](#abcde)
anywhere in the document takes you there.

It might be OK to put the [anchor in the heading line](#internal) you wish to link.

## Images

Here's our logo (hover to see the title text):

Inline-style: 
![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")

Linked Images:
[![alt text](http://s.wordpress.org/about/images/wpmini-grey.png)](http://wordpress.com/ "Title")


Reference-style: 
![alt text][logo]

[logo]: https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 2"

## Code and Syntax Highlighting

Code blocks are part of the Markdown spec, but syntax highlighting isn't. However, many renderers -- like Github's and Markdown Here -- support syntax highlighting.

Inline `code` has `back-ticks around` it.

### code blocks

    Code blocks are very useful for developers and other people who look at code or other things that are written in plain text. As you can see, it uses a fixed-width font.

Blocks of code are either fenced by lines with three back-ticks ```, or are indented with four spaces. I recommend only using the fenced code blocks -- they're easier and only they support syntax highlighting.

```javascript
var s = "JavaScript syntax highlighting";
alert(s);
```
 
```python
s = "Python syntax highlighting"
print s
```
 
```
No language indicated, so no syntax highlighting. 
But let's throw in a <b>tag</b>.
```

## Blockquotes

> Blockquotes are very handy in email to emulate reply text.
> This line is part of the same quote.

Quote break.

> This is a very long line that will still be quoted properly when it wraps. Oh boy let's keep writing to make sure this is long enough to actually wrap for everyone. Oh, you can *put* **Markdown** into a blockquote.

## Inline HTML

You can also use raw HTML in your Markdown, and it'll mostly work pretty well.

<dl>
  <dt>Definition list</dt>
  <dd>Is something people use sometimes.</dd>

  <dt>Markdown in HTML</dt>
  <dd>Does *not* work **very** well. Use HTML <em>tags</em>.</dd>
</dl>


# Headings - H1,  *can* also contain **formatting**

There are six levels of headings. They correspond with the six levels of HTML headings.

## H2
### H3
#### H4
##### H5
###### H6


[end]

everything after this shortcode is ignored (i.e. signatures). Make sure it's on its own line with a blank line above it.
