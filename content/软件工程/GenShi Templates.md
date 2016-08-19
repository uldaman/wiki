---
title: GenShi Templates
layout: page
date: 2016-08-19 12:56
---

[TOC]

# Genshi XML Template Language
Genshi provides a XML-based template language that is heavily inspired by Kid, which in turn was inspired by a number of existing template languages, namely XSLT, TAL, and PHP.

This document describes the template language and will be most useful as reference to those developing Genshi XML templates. Templates are XML files of some kind (such as XHTML) that include processing directives (elements or attributes identified by a separate namespace) that affect how the template is rendered, and template expressions that are dynamically substituted by variable data.

See Genshi Templating Basics for general information on embedding Python code in templates.

# 1 Template Directives
Directives are elements and/or attributes in the template that are identified by the namespace http://genshi.edgewall.org/. They can affect how the template is rendered in a number of ways: Genshi provides directives for conditionals and looping, among others.

To use directives in a template, the namespace must be declared, which is usually done on the root element:

```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:py="http://genshi.edgewall.org/"
      lang="en">
  ...
</html>
```
<br>
In this example, the default namespace is set to the XHTML namespace, and the namespace for Genshi directives is bound to the prefix “py”.

All directives can be applied as attributes, and some can also be used as elements. The if directives for conditionals, for example, can be used in both ways:

```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:py="http://genshi.edgewall.org/"
      lang="en">
  ...
  <div py:if="foo">
    <p>Bar</p>
  </div>
  ...
</html>
```
<br>
This is basically equivalent to the following:

```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:py="http://genshi.edgewall.org/"
      lang="en">
  ...
  <py:if test="foo">
    <div>
      <p>Bar</p>
    </div>
  </py:if>
  ...
</html>
```
<br>
The rationale behind the second form is that directives do not always map naturally to elements in the template. In such cases, the py:strip directive can be used to strip off the unwanted element, or the directive can simply be used as an element.

## 1.1 Conditional Sections
### 1.1.1 py:if
The element and its content is only rendered if the expression evaluates to a truth value:

```html
<div>
  <b py:if="foo">${bar}</b>
</div>
```
<br>
Given the data foo=True and bar='Hello' in the template context, this would produce:

```html
<div>
  <b>Hello</b>
</div>
```
<br>
But setting foo=False would result in the following output:

```html
<div>
</div>
```
<br>
This directive can also be used as an element:

```html
<div>
  <py:if test="foo">
    <b>${bar}</b>
  </py:if>
</div>
```
<br>
### 1.1.2 py:choose
The py:choose directive, in combination with the directives py:when and py:otherwise provides advanced conditional processing for rendering one of several alternatives. The first matching py:when branch is rendered, or, if no py:when branch matches, the py:otherwise branch is rendered.

If the py:choose directive is empty the nested py:when directives will be tested for truth:

```html
<div py:choose="">
  <span py:when="0 == 1">0</span>
  <span py:when="1 == 1">1</span>
  <span py:otherwise="">2</span>
</div>
```
<br>
This would produce the following output:

```html
<div>
  <span>1</span>
</div>
```
<br>
If the py:choose directive contains an expression the nested py:when directives will be tested for equality to the parent py:choose value:

```html
<div py:choose="1">
  <span py:when="0">0</span>
  <span py:when="1">1</span>
  <span py:otherwise="">2</span>
</div>
```
<br>
This would produce the following output:

```html
<div>
  <span>1</span>
</div>
```
<br>
These directives can also be used as elements:

```html
<py:choose test="1">
  <py:when test="0">0</py:when>
  <py:when test="1">1</py:when>
  <py:otherwise>2</py:otherwise>
</py:choose>
```
<br>
## 1.2 Looping
### 1.2.1 py:for
The element is repeated for every item in an iterable:

```html
<ul>
  <li py:for="item in items">${item}</li>
</ul>
```
<br>
Given items=[1, 2, 3] in the context data, this would produce:

```html
<ul>
  <li>1</li><li>2</li><li>3</li>
</ul>
```
<br>
This directive can also be used as an element:

```html
<ul>
  <py:for each="item in items">
    <li>${item}</li>
  </py:for>
</ul>
```
<br>
## 1.3 Snippet Reuse
### 1.3.1 py:def
The py:def directive can be used to create macros, i.e. snippets of template code that have a name and optionally some parameters, and that can be inserted in other places:

```html
<div>
  <p py:def="greeting(name)" class="greeting">
    Hello, ${name}!
  </p>
  ${greeting('world')}
  ${greeting('everyone else')}
</div>
```
<br>
The above would be rendered to:

```html
<div>
  <p class="greeting">
    Hello, world!
  </p>
  <p class="greeting">
    Hello, everyone else!
  </p>
</div>
```
<br>
If a macro doesn't require parameters, it can be defined without the parenthesis. For example:

```html
<div>
  <p py:def="greeting" class="greeting">
    Hello, world!
  </p>
  ${greeting()}
</div>
```
<br>
The above would be rendered to:

```html
<div>
  <p class="greeting">
    Hello, world!
  </p>
</div>
```
<br>
This directive can also be used as an element:

```html
<div>
  <py:def function="greeting(name)">
    <p class="greeting">Hello, ${name}!</p>
  </py:def>
</div>
```
<br>
### 1.3.2 py:match
This directive defines a match template: given an XPath expression, it replaces any element in the template that matches the expression with its own content.

For example, the match template defined in the following template matches any element with the tag name “greeting”:

```html
<div>
  <span py:match="greeting">
    Hello ${select('@name')}
  </span>
  <greeting name="Dude" />
</div>
```
<br>
This would result in the following output:

```html
<div>
  <span>
    Hello Dude
  </span>
</div>
```
<br>
Inside the body of a py:match directive, the select(path) function is made available so that parts or all of the original element can be incorporated in the output of the match template. See Using XPath for more information about this function.

Match templates are applied both to the original markup as well to the generated markup. The order in which they are applied depends on the order they are declared in the template source: a match template defined after another match template is applied to the output generated by the first match template. The match templates basically form a pipeline.

This directive can also be used as an element:

```html
<div>
  <py:match path="greeting">
    <span>Hello ${select('@name')}</span>
  </py:match>
  <greeting name="Dude" />
</div>
```
<br>
When used this way, the py:match directive can also be annotated with a couple of optimization hints. For example, the following informs the matching engine that the match should only be applied once:

```html
<py:match path="body" once="true">
  <body py:attrs="select('@*')">
    <div id="header">...</div>
    ${select("*|text()")}
    <div id="footer">...</div>
  </body>
</py:match>
```
<br>
The following optimization hints are recognized:

| Attribute | Default |                                                                                                                                                                                                    Description                                                                                                                                                                                                    |
|-----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| buffer    | true    | Whether the matched content should be buffered in memory. Buffering can improve performance a bit at the cost of needing more memory during rendering. Buffering is ''required'' for match templates that contain more than one invocation of the select() function. If there is only one call, and the matched content can potentially be very long, consider disabling buffering to avoid excessive memory use. |
| once      | false   | Whether the engine should stop looking for more matching elements after the first match. Use this on match templates that match elements that can only occur once in the stream, such as the <head> or <body> elements in an HTML template, or elements with a specific ID.                                                                                                                                       |
| recursive | true    | Whether the match template should be applied to its own output. Note that once implies non-recursive behavior, so this attribute only needs to be set for match templates that don't also have once set.                                                                                                                                                                                                          |

Note

The py:match optimization hints were added in the 0.5 release. In earlier versions, the attributes have no effect.

## 1.4 Variable Binding
### 1.4.1 py:with
The py:with directive lets you assign expressions to variables, which can be used to make expressions inside the directive less verbose and more efficient. For example, if you need use the expression author.posts more than once, and that actually results in a database query, assigning the results to a variable using this directive would probably help.

For example:

```html
<div>
  <span py:with="y=7; z=x+10">$x $y $z</span>
</div>
```
<br>
Given x=42 in the context data, this would produce:

```html
<div>
  <span>42 7 52</span>
</div>
```
<br>
This directive can also be used as an element:

```html
<div>
  <py:with vars="y=7; z=x+10">$x $y $z</py:with>
</div>
```
<br>
Note that if a variable of the same name already existed outside of the scope of the py:with directive, it will not be overwritten. Instead, it will have the same value it had prior to the py:with assignment. Effectively, this means that variables are immutable in Genshi.

## 1.5 Structure Manipulation
### 1.5.1 py:attrs
This directive adds, modifies or removes attributes from the element:

```html
<ul>
  <li py:attrs="foo">Bar</li>
</ul>
```
<br>
Given foo={'class': 'collapse'} in the template context, this would produce:

```html
<ul>
  <li class="collapse">Bar</li>
</ul>
```
<br>
Attributes with the value None are omitted, so given foo={'class': None} in the context for the same template this would produce:

```html
<ul>
  <li>Bar</li>
</ul>
```
<br>
This directive can only be used as an attribute.

### 1.5.2   py:content
This directive replaces any nested content with the result of evaluating the expression:

```html
<ul>
  <li py:content="bar">Hello</li>
</ul>
```
<br>
Given bar='Bye' in the context data, this would produce:

```html
<ul>
  <li>Bye</li>
</ul>
```
<br>
This directive can only be used as an attribute.

### 1.5.3 py:replace
This directive replaces the element itself with the result of evaluating the expression:

```html
<div>
  <span py:replace="bar">Hello</span>
</div>
```
<br>
Given bar='Bye' in the context data, this would produce:

```html
<div>
  Bye
</div>
```
<br>
This directive can also be used as an element (since version 0.5):

```html
<div>
  <py:replace value="title">Placeholder</py:replace>
</div>
```
<br>
### 1.5.4 py:strip
This directive conditionally strips the top-level element from the output. When the value of the py:strip attribute evaluates to True, the element is stripped from the output:

```html
<div>
  <div py:strip="True"><b>foo</b></div>
</div>
```
<br>
This would be rendered as:

```html
<div>
  <b>foo</b>
</div>
```
<br>
As a shorthand, if the value of the py:strip attribute is empty, that has the same effect as using a truth value (i.e. the element is stripped).

## 1.6 Processing Order
It is possible to attach multiple directives to a single element, although not all combinations make sense. When multiple directives are encountered, they are processed in the following order:

- py:def
- py:match
- py:when
- py:otherwise
- py:for
- py:if
- py:choose
- py:with
- py:replace
- py:content
- py:attrs
- py:strip

# 2 Includes
To reuse common snippets of template code, you can include other files using XInclude.

For this, you need to declare the XInclude namespace (commonly bound to the prefix “xi”) and use the <xi:include> element where you want the external file to be pulled in:

```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:py="http://genshi.edgewall.org/"
      xmlns:xi="http://www.w3.org/2001/XInclude">
  <xi:include href="base.html" />
  ...
</html>
```
<br>
Include paths are relative to the filename of the template currently being processed. So if the example above was in the file "myapp/index.html" (relative to the template search path), the XInclude processor would look for the included file at "myapp/base.html". You can also use Unix-style relative paths, for example "../base.html" to look in the parent directory.

Any content included this way is inserted into the generated output instead of the <xi:include> element. The included template sees the same context data. Match templates and macros in the included template are also available to the including template after the point it was included.

By default, an error will be raised if an included file is not found. If that's not what you want, you can specify fallback content that should be used if the include fails. For example, to to make the include above fail silently, you'd write:

`<xi:include href="base.html"><xi:fallback /></xi:include>`

See the XInclude specification for more about fallback content. Note though that Genshi currently only supports a small subset of XInclude.

## 2.1 Dynamic Includes
Incudes in Genshi are fully dynamic: Just like normal attributes, the href attribute accepts expressions, and directives can be used on the <xi:include /> element just as on any other element, meaning you can do things like conditional includes:

```html
<xi:include href="${name}.html" py:if="not in_popup"
            py:for="name in ('foo', 'bar', 'baz')" />
```
<br>
## 2.2 Including Text Templates
The parse attribute of the <xi:include> element can be used to specify whether the included template is an XML template or a text template (using the new syntax added in Genshi 0.5):

`<xi:include href="myscript.js" parse="text" />`

This example would load the myscript.js file as a NewTextTemplate. See text templates for details on the syntax of text templates.

# 3 Comments
Normal XML/HTML comment syntax can be used in templates:

`<!-- this is a comment -->`

However, such comments get passed through the processing pipeline and are by default included in the final output. If that's not desired, prefix the comment text with an exclamation mark:

`<!-- !this is a comment too, but one that will be stripped from the output -->`

Note that it does not matter whether there's whitespace before or after the exclamation mark, so the above could also be written as follows:

`<!--! this is a comment too, but one that will be stripped from the output -->`
