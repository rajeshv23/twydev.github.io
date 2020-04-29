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

_Kyle Simpson, You Don't Know JS (book series)_

> It is simultaneously a simple, easy-to-use language that has broad appeal, and a complex and nuanced collection of language mechanics which without careful study will elude true understanding even for the most seasoned of JavaScript developers ... Because JavaScript can be used without understanding, the understanding of the language is often never attained.

## Built-in Types

string, number, boolean, null, undefined, object, symbol

### typeof null is object

This is a bug that will never be fixed, since many websites around the world depends on this behavior. Fixing this will break a lot of websites.

### typeof functions and arrays

These are just special variants of objects with certain built-in methods.

### == vs ===

One allows coercion when comparing value, and the other one do not allow coercion, also know as _strict-equality_.

When compared with numbers, strings are coerced to `NaN` (which is a number that don't hold value)

Even though the author of YDKJS encourage the use of equality as long as you follow certain heuristics to make sure it is safe, I beg to differ. In a team setting when collaborating on a project, it is better to be explicit than sorry. If you are implementing a functionality using equality, the next engineer that uses your function may not know about the implicit assumptions.

## Coding

### Constructors vs Factory

TODO: read https://tsherif.wordpress.com/2013/08/04/constructors-are-bad-for-javascript/

### strict mode

Complying to strict mode makes code more optimizable for the compiler.

### why Named Function is preferred over Anonymous Function

Anonymous functions makes code more readable but has the following drawbacks:

- No useful names will be displayed during stack trace or debugging
- Cannot use recursion, or allow the function to unbind itself as an event handler
- Cannot self document or carry intentions

Since there are no drawbacks of named functions, why not just use named functions all the time.

### What about Arrow functions

## Scopes

### a Variable in a Function Scope

_Most of the time_, there are only 2 scopes we are interested in: the Global Scope and the Function Scope.

When a variable is declared and assigned `var test = 1`, the following sequence will happen when code is run:

1. Compiler will parse code into tokens
2. Compiler will check if variable was already declared in Scope. If not it will request for variable to be declared in Scope.
3. Execution Engine runs compiled code, retrieve variable from Scope.
4. Variable retrieved and assigned value by Engine.
5. If variable cannot be resolved in current Scope, the Engine will query from outer Scopes, searching up all the way until Global Scope if the variable cannot be found.
   a. by default, the variable will get created in Global Scope if deemed missing.
   b. in strict-mode, a ReferenceError will be thrown.

**Shadowing** refers to having the same variable identifier in the inner scope and outer scope. Since look up always start from the innermost scope, the shadow variable will be resolved instead of the outer scope variable. In most cases, this is our expectation from the Engine. Look up stops once a matching variable is found in the Scope.

In browsers, all variables declared in Global Scope are automatically properties of the Global Object "window" and are therefore accessible from any nested scope through accessing the Global object.

### How are Scopes determined

_YDKJS, Scopes & Closures, Chapter 2_

> No matter where a function is invoked from, or even how it is invoked, its lexical scope is **only** defined by **where** the function was declared.

Lexical Scopes can be modified at runtime by `eval`, `with`, and some other built-in functions (strongly discouraged), which are restricted by strict-mode (Great!). Besides the danger of code injection through `eval`, performance of code will slow down. Scope will be dynamically generated when Engine executes the code, wasting all the optimization efforts of the Compiler from analyzing the static code before execution.

### Namespace

Instead of loading variables into your Global Scope, which may result in variable name collision, the classic pattern is to use an object as a "namespace", so as to limit the scope of imported variables, and to access them via object accessors. Modern approach is to use module dependency managers to achieve the same goal.

### Block Scopes

Consists of:

- try-catch
- for loops
- { ... }

**Most of the time we may have allowed our functions to have closure over unnecessary data that are not relevant**. Allowing those variables and data to be in block scope will limit exposure, and allow garbage collection once execution has passed that block.

`let` and `const` are block scoped declarations. **Functions** are not block scoped, so declaring it in the block will be hoisted to the enclosing outer scope.

### Let vs Var vs Const

**let**

- `let` is scoped to the immediately enclosing **block**, `var` is scoped to the immediately enclosing **function**.
- `let` is not hoisted, and will only be defined when that line of code is evaluated.
- When used at top-level, `let` will not create a property on Global object.
- `let` do not allow redeclaration of the variable within the same scope.

**const**

- `const` is Block Scoped.
- No hoisting.
- Will not create Global object property.
- No redeclaration. Must assign value on declaration.

### Hoisting

Allows variable (`var`, functions) declaration any where in the lexical scope. When code is parsed by Compiler, the variables will be hoisted to the top and declared before execution starts in the scope, so that all declared variables are available.

Only declarations will be hoisted. Assignments will be left in place. Therefore this snippet will print undefined despite of hoisting.

```javascript
console.log(test); // undefined
var test = 1;

hello(); // TypeError
my(); // ReferenceError
var hello = function my() {
  console.log("my world");
};

foo(); // prints bar
function foo() {
  console.log("bar");
}
```

Functions are hoisted before variables. If there are duplicated definitions of the same function, the last declaration wins.

### IIFE - Immediately Invoked Function Expression

Typically used to create a scope for variables, isolated from the outer scope.

## Closure

_YDKJS, Scopes & Closures, Chapter 5_

> Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope.

We use "closure" as a verb, with meaning close to "reference". A function has "closure" over certain internal variables/scope. The internal variables are akin to internal state of an object. Start state can be injected on creation.

```javascript
function multiplyByX(x) {
  function multiply(input) {
    // multiply function has closure over scope of enclosing function.
    return input * x;
  }
  return multiply;
}

var multiplyByFive = multplyByX(5);

// function is executed outside of its declared lexical scope of multiply, but still retains access to the scope
console.log(multiplyByFive(5)); // 25
```

Testing our understanding of how javascript works: loops + closure, example from YDKJS

```javascript
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 1000);
}
// prints out the number "6" every second.
```

What is happening, in my own understanding:

- for-loop body only has one scope, regardless of the number of iterations
- variable i is binded to a new iteration value at every loop
- at every loop, we will set timeout, with a function called timer, with a closure over the scope of the for-loop (therefore only one single scope, ever)
- after the loop has ended, when each callback gets executed, the closure will reference variable i from for-loop scope, which already has value set to 6 (since the loop ended)
- this behavior is consistent even if timeout value is set to zero.

To make the loop work as intended, we need to freeze the value of the variable within our timeout function scope or timer function scope, and have a new scope instance on each iteration, instead of having a closure of the changing for-loop scope. The book YDKJS used an IIFE to create a scope per iteration. But I think the subsequent examples using block scope is easier for us to appreciate the concept.

```javascript
for (var i = 1; i <= 5; i++) {
  const k = i; // constant k is block scoped, and assigned on declaration at each iteration.
  setTimeout(function timer() {
    console.log(`block scoped ${k}, for-loop scoped ${i}`);
  }, k * 1000);
}

for (let i = 1; i <= 5; i++) {
  // or more simply using block scoped behavior of let.
  setTimeout(function timer() {
    console.log(i);
  }, i * 1000);
}
```

### Implementing Module Pattern

The module pattern can be implemented with closures to satisfy these conditions:

- There must be an outer enclosing function, and it must be invoked at least once to create a new module instance.
- Enclosing function must return an inner function with closure over private scope of enclosing function.

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

### ES6 Modules

ES6 has syntax support for Modules.

- Modules must be defined in separate files.
- `export member` exports a member from a file
- `import member from "file"` imports a specific member from "file.js"
- `module myMod from "file"` imports the entire module from file
- Modules API are static: Compiler will check that all references to modules and module members exists at compile time, if not it will throw an error.
- Contents in module files are treated as if they are enclosed in a scoped closure.

## Execution

### Inside the Engine

---

To be continued

- Tail Recursion in Javascript?
- to read https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/
