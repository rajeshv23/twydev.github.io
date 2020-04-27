---
title: "My JavaScript Compendium"
toc: true
toc_label: "Chapters"
categories:
  - Collection
tags:
  - typescript
  - javascript
---

Disclaimer: This is a piece of working document.

# A Brief History of JavaScript

The best place to get started would be [wikipedia](https://en.wikipedia.org/wiki/JavaScript). Here are the important milestones extracted from this wiki page:

- **1995**: Netscape Navigator, the only browser in the market, was shipped with LiveScript and the interpreter. It was later re-branded as JavaScript to be associated with the then popular Java language.
- **1996**: Microsoft released JScript, along with Internet Explorer. Difference between the two scripting languages caused long lasting incompatibility, with certain websites only supporting one browser or the other.
- 1996: Netscape submitted JavaScript to ECMA in an attempt to standardize specification
- **2005**: Debut of Ajax, a set of technologies using JavaScript to allow web applications to load data in the background. Sparked a renaissance period of JavaScript, leading to creation of popular libraries like jQuery and Prototype.
- **2008**: Google Chrome shipped with V8 engine, the first to use just-in-time compilation, significantly improving JS execution time.
- **2009**: Multiple browser vendors came together to help build the ES5 standard.
- **2015**: Release of ES6, renamed ECMAScript 2015.
- **Beyond**: From 2016 onwards, a new version of ECMAScript standard was published with minor updates to existing standard. JS is now considered a mature language.

# How Browsers Work

Since the invention of JavaScript was solely driven by the desire to enrich web browsing experience through client side processing, we must have at least a basic understanding of how modern browsers work. Here is a link to an [article written by Tali Garsiel](http://taligarsiel.com/Projects/howbrowserswork1.htm) that explores the internals of web browsers. The article is old, but gold.

My takeaways the article:

- HTML error tolerance comments from Webkit are hilarious.
- I am thankful for the efforts by our community of great engineers to create a robust set of standards that made our modern web browsing experience possible.
- It was nice to see concrete use cases of data structures like trees and stacks, as well as algorithms like bubble sort and merge sort, in an example that is relatable to everyone, the modern web browser.
- HTML document is first parsed into a DOM tree by main thread, with parallel threads fetching network resources and scripts. Scripts will block parsing and are executed synchronously.
- Styles are parsed into a separate tree.
- A render tree is constructed to mark the correct rendering order for each DOM node, and to associate the node with the corresponding computed style (elements of the tree are called renderer).
- The render tree is traversed to layout all elements on the viewport and to paint them (methods of the renderer object itself).
- Re-layout or re-painting can be applied to only a small subset of nodes.
- Changes to any renderers that requires re-layout or re-paint will fire off the corresponding events, triggering the layout or paint execution by the main thread.

## What is the Browser API exposed to JS?

# JavaScript - The Programming Language

*Kyle Simpson, You Don't Know JS (book series)*
> It is simultaneously a simple, easy-to-use language that has broad appeal, and a complex and nuanced collection of language mechanics which without careful study will elude true understanding even for the most seasoned of JavaScript developers ... Because JavaScript can be used without understanding, the understanding of the language is often never attained.

## Built-in Types

string, number, boolean, null, undefined, object, symbol

### typeof null is object

This is a bug that will never be fixed, since many websites around the world depends on this behavior. Fixing this will break a lot of websites.

### typeof functions and arrays

These are just special variants of objects with certain built-in methods.

### == vs ===

One allows coercion when comparing value, and the other one do not allow coercion, also know as *strict-equality*.

When compared with numbers, strings are coerced to `NaN` (which is a number that don't hold value)

Even though the author of YDKJS encourage the use of equality as long as you follow certain heuristics to make sure it is safe, I beg to differ. In a team setting when collaborating on a project, it is better to be explicit than sorry. If you are implementing a functionality using equality, the next engineer that uses your function may not know about the implicit assumptions.

## Coding

### Constructors vs Factory

TODO: read https://tsherif.wordpress.com/2013/08/04/constructors-are-bad-for-javascript/

### strict mode

Complying to strict mode makes code more optimizable for the compiler.

### TODO: why named function is preferred over anonymous function?

## Scopes

### Hoisting

Allows variable to be accessed within the scope regardless of lexical position of variable declaration.

### IIFE - Immediately Invoked Function Expression

Typically used to create a scope for variables, isolated from the outer scope.

### Closure

Functions with closure over inner variables that are injected are essentially objects with internal states that can be specified by the caller on creation.

Used to implement the module pattern.

## Objects

### What is THIS?

What is `this` in a function depends on how the function was called.

### Prototype

Behavior delegation instead of inheritance.

## Projects

### Polyfill

Producing a piece of code that is able to run in older JS environments, to emulate the behavior of **functions** introduced in newer version JS that are not supported in current environment.

### Transpile

Producing code with equivalent behavior in older JS environments, for code written in newer version JS **syntax**.

## Execution

### Inside the Engine


---

To be continued
- Tail Recursion in Javascript?
- to read https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/

