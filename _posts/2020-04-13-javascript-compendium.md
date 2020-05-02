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

My notes are derived mostly from [YDKJS book series](https://github.com/getify/You-Dont-Know-JS/tree/1st-ed).

_Kyle Simpson, You Don't Know JS (book series)_

> It is simultaneously a simple, easy-to-use language that has broad appeal, and a complex and nuanced collection of language mechanics which without careful study will elude true understanding even for the most seasoned of JavaScript developers ... Because JavaScript can be used without understanding, the understanding of the language is often never attained.

## Built-in Types

string, number, boolean, null, undefined, object, symbol

### typeof null is object

This is a bug that will never be fixed, since many websites around the world depends on this behavior. Fixing this will break a lot of websites.

### typeof functions and arrays is object

These are just special variants of objects with certain built-in properties.

### == vs ===

One allows coercion when comparing value, and the other one do not allow coercion, also know as _strict-equality_.

When compared with numbers, strings are coerced to `NaN` (which is a number that don't hold value)

Even though the author of YDKJS encourage the use of equality as long as you follow certain heuristics to make sure it is safe, I beg to differ. In a team setting when collaborating on a project, it is better to be explicit than sorry. If you are implementing a functionality using equality, the next engineer that uses your function may not know about the implicit assumptions.

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

**Lexical Scopes** can be modified at runtime by `eval`, `with`, and some other built-in functions (strongly discouraged), which are restricted by strict-mode (Great!). Besides the danger of code injection through `eval`, performance of code will slow down. Scope will be dynamically generated when Engine executes the code, wasting all the optimization efforts of the Compiler from analyzing the static code before execution.

In short, we can assume JavaScript do not have Dynamic Scope (scope determined by where function was called at runtime in the call stack, instead of function declaration and respecting the lexical scope chain)

Another important concept:

> It is true that internally, scope is kind of like an object with properties for each of the available identifiers. But the scope "object" is not accessible to JavaScript code. It's an inner part of the Engine's implementation.

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

**special note on var**

Calling `var` at the top level creates a property of the Global object. It is not a copy of the variable. It is the exact same variable.

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

What is happening? In my own understanding:

- for-loop body only has one scope, regardless of the number of iterations
- variable i is bound to a new iteration value at every loop
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

## What is THIS?

### THIS is a binding in the execution context, not the SELF, not the SCOPE

`this` is a context object available to the function scope during execution.

When a function is invoked, an execution context is created. The execution context contains various information:

- where the function was called from (the call-stack) 
- how the function was invoked
- what parameters were passed
- the `this` reference, which will be determined by call-site (how the function was called)

(inspecting from the devTool will allow us to see the execution context, and also the scope chain)

### Rules that determines THIS binding

#### Default Binding

With no modifiers, the default binding of `this` is to the Global object. Strict mode do not allow default binding, so it will be `undefined`.

#### Implicit Binding

If a context object references the function as part of the object property, e.g. `obj.func()`, then this context object `obj` will be bound to `this` and be available to the function `func`.

Therefore, it is critical that a context object is used to invoke the function, if it is just a reference assignment of the function through an object, the implicit binding will be lost. This is especially common for callback functions.

```javascript
function sayHello() {
  console.log(this.hello)
}

var obj = {
  hello: "Ni Hao!",
  sayHello: sayHello
}

var justAReference = obj.sayHello
justAReference() // default binding!
```

> BEWARE! - Event handlers in popular JavaScript libraries are quite fond of forcing your callback to have a `this` which points to, for instance, the DOM element that triggered the event

#### Explicit Binding

Make use of JS Function Utilities, the `call` and `apply` methods of Function objects allow explicit binding by passing the desired context object as method parameter. If a primitive is passed as context object, they will be boxed to become the object from through object wrappers.

These utilities allow dynamic binding of `this` through explicit reference. The `bind` method provided in ES5 and ES6 an object to a function, and returns a reference to a new function that is hard bound to the object. ES6 adds a `name` property to the resultant function, that indicate the name of the source function before binding.

Many libraries provide functions with optional context parameters, giving us the convenience to explicitly bind an object to `this` as we make our function calls.

#### NEW Binding

In JavaScript, there is no Java equivalent of a constructor function. _All functions are just... functions_. A `new` keyword merely modify the invocation and return object of that function in the following sequence:

1. a new object is created
2. the newly created object gets prototype-linked to that function's prototype object
3. the newly created object gets bound to `this` of that function call
4. function gets executed
5. unless the function returns its own alternate object, the newly created object will be returned by default

I think of this sequence as a decorator around the function. And unless the new object is required by this constructor call, if not the `new` keyword can be omitted and the function should still behave as expected like a constructor call, it all depends on how you have coded the function. Omitting the `new` keyword if it is redundant will avoid additional object creation and garbage collection.

_Remember, there is no constructor in JavaScript._

### Binding Priority

- Explicit Binding takes precedence over all other bindings (if used in conjunction).
- However, `new` Binding is able to override hard bound `this` in a function, making the function apply on the new object and return the new object instead. This allows possibility of partial application (subset of currying).
- Implicit Binding takes precedence over Default Binding.
- Rules fall through to Default Binding.

`new > explicit > implicit > default`

Note: you cannot bind to an explicitly bound function again to override the context.

### ignoring THIS

Calls to explicit binding may be used for other purposes, such as spreading arguments or currying. However, the binding context is a mandatory parameter. If we pass in `null` or `undefined` for binding, we will fall back to Default Binding on Global object, which may cause unintended modifications on the Global object by other callers of the function.

An empty object `Object.create(null)` can be passed instead as a safe `this` binding. This object is more efficient than an object created by `{ }` expression, since there is no delegation from `Object.prototype`.

YDKJS suggested using a helper soft-bind function to emulate a desired soft binding behavior, which I find confusing to other users of the function. You either need to have implemented the helper function, or have a strong understanding of `this` binding, to use the function effectively

### lexical THIS in arrow functions

An arrow function do not bind to `this` according to the above binding rules, but instead inherit `this` from the lexical scope of the enclosing function. In simple terms, the lexical scope determines what `this` will be inherited by the arrow functions, instead of the call-site of the function (but of course, the binding rules still apply to the enclosing function).

Simpson encourage using either a pure lexical scoping style of coding, or a pure context binding style of coding, instead of mixing both concept, which may make our code hard to maintain (I think this is especially true in a team environment).

## Objects

_Not everything in JavaScript is an Object._

Primitives are not objects. They will be coerced or boxed into objects when you use object operations on them.

Object sub-types can be called complex primitives. These are functions and arrays.

All object access are property access. The objects do not truly "own" a method in the traditional OO language sense, just because the function is referenced by one of the object property. Even if the function access other object properties through `this` reference, this is determined by context binding during invocation.

### Shallow Copy

Since objects can contain properties that are infinitely nested objects or contain circular references, ES6 provides `Object.assign(newObject, sourceObjects... )` that creates a shallow copy only.

If an object is JSON safe, then JSON stringify followed by JSON parse on the string will create a new distinct object with no shared references.

### Data Descriptors

A property of an object that describes data. Such a property can be configured using `Object.defineProperty(obj, property, config)`. The configurations available:

- **Value** - value/data of the property
- **Writable** - determines if value of property can be changed.
- **Configurable** - determines if property descriptor can be updated. (one-way action once set to false). Also determines if a property can be `delete`.
- **Enumerable** - determines if property will show up during enumeration over all properties of object using `for..in `.

_Array should be enumerated using for-loop that act on indices only, instead of using `for..in` to prevent accessing other enumerable properties of Array object. ES5 offers useful helpers to iterate over Array values instead, but with no guarantee on ordering of Array elements_

### Enumerable and Iteration

ES6 provides `for..of` loop, which requests for the object's iterator `Symbol.iterator` property to iterate through the property values. This iterator is built-in for Array sub-types therefore we can use `for..of` for arrays immediately, but will need to be provisioned manually for other object types.

Compared to `forEach` which takes a callback to apply to each element of the array, `for..of` allows early termination but requires `Symbol.iterator` property.

### Object-level configurations

- `Object.preventExtensions(obj)` prevents new property from being added to the object.
- `Object.seal(obj)` makes object non-extensible, and make all existing properties non-configurable.
- `Object.freeze(obj)` seals the object, and make all existing properties non-writable.

### Accessor Descriptors

If an object property is defined with getter or setter functions, it is considered an accessor descriptor. JS will call the getter or setter to access the data, and the value and writable configuration of the property will be ignored. Getters and Setters can be defined at a per property level, overriding default function behavior.

So if the property remains as a data descriptor, the default `[[Get]]` and `[[Put]]` function will be used to access the value. These are the functions that gets overridden when the property upgrades to become an accessor descriptor.

### Missing Property

Accessing a missing property will return `undefined` unlike referencing a missing variable which throws ReferenceError.

If a property exists, but was set to value `undefined`, there is another way to test of existence of a property instead of testing the value of a property, by using the following:

- `obj.hasOwnProperty(property)` checks if this particular object has property.
- `property in obj` checks if this particular object, or the prototype chain, has the property.

There is no built-in way to list all properties of an object including properties up the prototype chain. `Object.keys(obj)` will list all properties of the current object that are enumerable. `Object.getOwnPropertyNames(obj)` will list all properties of current object regardless of enumerability.

## Classes

_Classes in the traditional OO sense do not exists in JavaScript._

### Inheritance via Mixin

Since classes do not exists in the language, all "class" definitions are merely objects.

Inheriting a parent "class" to a child "class" can be emulated by performing a shallow copy of all the parent object properties if they don't exist in the target child object, and methods can be extended/overridden using explicit pseudo-polymorphic reference. But even this does not provide true OOP inheritance that creates a new copy of the behavior, as the parent and child now shares reference to a common function.

This is a common and traditional approach, called **Explicit Mixin**.

```javascript
// invented function mixin
function mixin( sourceObj, targetObj ) {
	for (var key in sourceObj) {
		targetObj[key] = sourceObj[key];
	}
	return targetObj;
}

var Child = mixin( Parent, {
	myFunc: function() {
		Parent.myFunc.call( this ); // explicit reference to Parent object, due to shadowing of method name
		console.log( "Extended functionality" );
	}
} );
```

**Parasitic Inheritance** is a variant of the above explicit mixin, by creating a parent object reference within the child object and maintaining privileged references to whatever properties the child would like to inherit.

```javascript
function Child = {
  var child = new Parent() // parent object reference
  var parentMyFunc = child.myFunc // privileged reference
  function myFunc() {
    parentMyFunc.call(this);
    console.log( "Extended functionality" );
  }
  return child
}
```

**Implicit Mixin** is another approach to mixin, which make use of context binding to borrow behavior from a parent "class" and applying it to a child context object, hence inheriting the behavior.

### Prototype

JavaScript objects typically has an internal property denoted as `[[Prototype]]` which is a reference/link to another object. When trying to access a property on an object, but the property does not exists in the current object, the prototype chain will be consulted to look up the chained object for the property. Once the property matches, it will be returned. The look up stops once it reaches the root of the chain, with is the JavaScript `Object.prototype`.

`var newObj = Object.create(srcObj)` creates a new object by setting source object as the prototype reference.

#### Setting Properties in Objects

Setting properties in object using assignment (`obj.property = value`), especially if the _property do not exist in the current object_, is not straightforward.

1. if property exists in prototype chain as a data descriptor, and if it is writable, then a **new shadow property** will be created on current object.
2. if property exists in prototype chain as a data descriptor, but is not writable, then nothing will happen. In strict mode, it will **throw an error**.
3. if property exists in prototype chain as an accessor descriptor, then that accessor will be called to set the property value. The value will be set in the object **higher up the prototype chain**, instead of having a shadow property.
4. if property is set using `Object.defineProperty()` instead, a new (shadow) property will be created on the current object.

#### Prototypal Delegation

Prototypal delegation should be the defining mental model to understanding JavaScript objects.

> Objects do not inherit behaviors. Objects delegate behaviors that are missing from their own properties to a prototype chain. The prototype chain is essentially layers of behavior, and you resolve what an object can perform by flattening the layers of behavior into a projection. In short, an object never truly "owns" a majority of behavior it can perform.

#### Confusing Constructor

An unfortunate naming that cause confusion is the property `prototype.constructor` on a function.

Functions are created by default with a prototype link to an arbitrary object. That arbitrary object contains a property called `constructor` that reference back to the function. 

In other words, it is easier to think of `prototype` and `constructor` in this case as **doubly linked references** (like a doubly linked list) _at the point of function creation._ 

The word `constructor` really does not carry any additional meaning, therefore it **never indicates what is the constructor of an object. That is pure misconception.**

So when a new object is created by a function through a `new` constructor call, trying to access that `newObj.constructor` property will search up the prototype chain to resolve a value, that creates an illusion of what is the constructor. (In complex scenario with shadow properties, you may even resolve to an unexpected value).

#### Setting the Prototype

```javascript
Child.prototype = Parent.prototype // bad. shared prototype may cause corrupted modifications

Child.prototype = new Parent() // bad. constructor calls to Parent() may cause undesired side effects

Child.prototype = Object.create(Parent.prototype) // pre-ES6. Wasteful discarding of original arbitrary Child.prototype object

Object.setPrototypeOf( Child.prototype, Parent.prototype ) // ES6. Reuses Child.prototype object.
```

The conceptual model in my mind is to wrap prototype links with a Prototype Object. This object itself allows future "grandchildren" objects to delegate behaviors, yet keeping these behaviors isolated from parent prototype objects:

```javascript
Child = {
  privateChildProperties,
  prototype: {
    // child's Prototype Object
    extraChildBehaviors,
    prototype: parent.prototype // meaning parent's Prototype Object
  }
}

Parent = {
  privateParentProperties,
  prototype: {
    // parent's Prototype Object
    extraParentBehaviors,
    prototype: grandparent.prototype
  }
}
```

`Object.getPrototypeOf()` and `Object.setPrototypeOf()` should be used to interact with prototype objects, although legacy approach like `.prototype` and `.__proto__` will work as well.

#### Finding prototype chain ancestor

`child instanceof Parent`

This call checks if `child`'s prototype chain ever contained the prototype object of the `Parent` function. If Parent is a hard-bound function (it will not have a prototype property), the original function before binding will be consulted.

`parentPrototype.isPrototypeOf(child)`

This call is better, as it avoids having a function involved in this testing, and only requires two objects for comparison.

## Coding

### Constructors vs Factory

TODO: read https://tsherif.wordpress.com/2013/08/04/constructors-are-bad-for-javascript/

### strict mode

Complying to strict mode makes code more optimizable for the compiler.

### Why Named Function is preferred over Anonymous Function?

Anonymous functions makes code more readable but has the following drawbacks:

- No useful names will be displayed during stack trace or debugging
- Cannot use recursion, or allow the function to unbind itself as an event handler
- Cannot self document or carry intentions

Since there are no drawbacks of named functions, why not just use named functions all the time.

Arrow functions are anonymous functions that introduces the lexical `this` behavior.

### Delegation Design

Achieving object-oriented programming with delegation instead of class inheritance, here are some points to keep in mind:

1. **Delegate behavior to another object via prototype linkage. But keep states internal to current object**. (I find this the most profound concept to wrap by head around).
2. Use explicit method naming on delegator objects, instead of shadowing a method with the same name from the delegates. This makes code simple to maintain and reason. (Polymorphism can be achieved by having the same method name between sibling delegators that are not on the same prototype chain).
3. Delegation should be hidden as internal implementation of an API, if it provides more clarity to users.
4. Bi-directional delegation is not allowed.
5. Using composition of peer objects providing distinct behavior, instead of always designing the objects in terms of parent-child hierarchy.
6. Use `obj.isPrototypeOf()` and `obj.getPrototypeOf` to test for delegation, and forget all about classes and `instanceof`.

### CLASS approach

If we are using `class` in our development work, then I think the best practice is to never use any Delegation Design explicitly. Even though `class` from ES6 may be using prototypal delegation under the hood, we should respect the class API and stick to creating static and traditional OOP classes. I see two advantages:

- we are using the new class inheritance syntax as intended by ES6 specs.
- we do not cause unintended corruption to our program by accessing prototype delegation.
- if a problem cannot be solved using class features, then defer to a higher level workaround, such as changing design pattern, to approach the problem, instead of hacking it by using other JavaScript language features.

### ES6 Function Definition Shorthand

```javascript
var Example = {
	anon() { /*..*/ },
	named: function named() { /*..*/ }
};
```

In this example, the use of simpler syntactic shorthand to create a function to be used in constructor call is easier to risk, but do note that the concise method `anon()` will create an anonymous function, which may limit the ability to self-reference in the function code.




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
