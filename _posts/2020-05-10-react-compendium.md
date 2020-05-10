---
title: "My React Compendium"
toc: true
toc_label: "Chapters"
categories:
  - Collection
tags:
  - javascript
  - react
---

Disclaimer: This is a piece of working document.

# Overview

My notes are derived mostly from [reactjs.org](https://reactjs.org/) and [edX course, HarvardX: CS50M](https://courses.edx.org/courses/course-v1:HarvardX+CS50M+Mobile/).

- React is declarative (interested in _what_ to execute, not _how_ to execute).
- Paradigm encourages decomposing problem to be handled by smaller reusable components.
- React has performant Reconciliation process to sync app state to DOM.
  - reconstruct Virtual DOM.
  - find difference between Virtual DOM and actual DOM.
  - only apply necessary changes to actual DOM.

# JSX

JSX = JavaScript XML, is a language used by React that will be transpiled to JavaScript. React acknowledges that logic and UI are coupled, and it may be more beneficial to be able to declare both using a single langauge. Separation of concern can be acheived by organizing our code into reusable components.

- lowercase tags are HTML tags.
- uppercase tags are React component tags.
- ReactDOM escapes any values from JSX and convert them to string before rendering, protecting us against injection attacks.

# React Element

JSX objects are transpiled into React elements. Elements are the building blocks used to render to the DOM. These are pure objects, therefore they are cheaper to create and maintain, as compared to DOM elements.

- ReactDOM is responsible of rendering React elements. (Need to specify the target DOM node).
- React Elements are immutable. Re-rendering require us to call ReactDOM render method again with new Element.
- Even though a new Element instance is passed to ReactDOM, only the necesary changes will be reflected to DOM, making it more efficient to update.
- Developers can simply focus on what an entire element look like at every point in time, instead of worrying about how an update can be applied.

# React Components

React Components are reusable JavaScript functions that form the building blocks of React application logic.

- accepts an input object (commonly known as "Props", which stands for properties).
- returns a React element to be rendered.

If a component tag is used to to define a React Element, any JSX attributes and children to this tag will be passed as a single Props object to the component function.

_Creating a Component as a JavaScript class provides access to additional functionalities such as state and lifecycle_

## State and Lifecycle

As long as we render a component (class) into the same DOM node, only a single instance of the class will be used. This allows us to use `state` to manage our Component internals over time, instead of strictly depending on Props. The class also inherit a set of lifecycle methods that runs at certain critical juncture of React Component's lifecycle.

```JSX
class MyComponent extends React.Component { // need to extend React Component class
  constructor(props) {
    super(props); // must call parent constructor to initialize context object/THIS. ES6 class behavior
    this.state = { date: new Date() };
  }
  // this.props is accessible as it will be initialized by parent constructor.

  componentDidMount() { // Run once, after initial render of component
    this.timerID = setInterval( // add a timer as member of the class to retain reference
      () => this.tick(), // callback function triggers a class method, using lexical THIS through arrow function.
      1000
    );
  }

  componentWillUnmount() { // Run once, when component is getting removed from the DOM
    clearInterval(this.timerID);
  }

  tick() {
    this.setState( // calls setState to update component state. This triggers React re-rendering
      { date: new Date() }
    );
  }

  render() {
    return (
      <div>
        <h2>Time Now: {this.state.date.toLocaleTimeString()}</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <MyComponent />,  document.getElementById('root')
);
```

_Example from [reactjs.org](https://reactjs.org/docs/state-and-lifecycle.html)_

State must be updated with `setState()` method.

- State updates may happen in batch, and is asynchronous.
- When using object as input to `setState()`, the object will be merged with current state. (Since updates may be batched, all the inputs will be merged, therefore we may not observe all updates rendered to DOM).
- Callback function can be used as input to `setState()`. Callback should expect two inputs `(state, prop)`, the first being the existing state before update, and second is the props at the time of update. Callback should return the updated state object.

## Props

Props are immutable, with respect to their React components.

`this.props` accesses `props` property, which is specially reserved (like `state`) and will be initialized by `React.Component` class, which all components will inherit from.

`props.children` will be populated if the current component has been called with child element included within the component tag in JSX.

## Data Flow

- Component state is encapsulated without component. Parent or child components should not know whether a component is stateful or stateless.
- We can allow state updates to flow down from parent to child, by passing it as props to the child.
- Therefore, data can only flow down, to the next component below in the tree.

## Losing Callback Context

As with all callback functions in JavaScript, if we pass a class method as a callback event handler, when it is eventually invoked, reference to context object `this` will be lost.

We can do a few things to get the desired behavior we want:

1. explicitly bind `this` from within the class to the method when registering the callback.
2. explicitly bind `this` to those methods within constructor.
3. use lexical `this` instead. Define class methods using arrow function, which statically inherit `this` context from the class (since the class is the enclosing scope of the arrow function).
4. use lexical `this`. Only pass arrow functions as callbacks. When registering the callbacks, define arrow functions that internally calls class methods, which inherits lexical `this` context.

This is an expected JavaScript behavior and is not a limitation of React or JSX.

[reactjs.org](https://reactjs.org/docs/handling-events.html) recommends the 2nd or 3rd approach to avoid extra re-rendering in child components. (If we define an arrow function in our props passed to a child component, the child will re-render whenever the parent element re-render and re-create that arrow function);

## Handling Events

- There is no need to explicitly call `addEventListener`, since we can simply register a handler when an element is rendered.
- Event default behavior can be suppressed using `event.preventDefault`
- If we want to pass extra parameters to our callback handler, we have two approach depending on how we are binding our context.

```JSX
<button onClick={(event) => this.deleteRow(extraParams, event)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, extraParams)}>Delete Row</button>
```

An event handler function can be set as callback at multiple locations in the React element, and it can vary its behavior based on e.g. `name` of the element that triggered the event, or other input properties.

## Render Null

Returning `null` in a component render method will prevent it from rendering, however the component will still go through the component lifecycle as usual (related lifecycle methods will still be called).

## Render Lists

JavaScript arrays can be first mapped to a list of `<li>...</li>` elements. An array of such element can be directly injected into JSX and will be rendered as a HTML list (provided we insert the appropriate `<ul>` tags).

React expect such list items to contain a key. `<li key={number/string}>`

This helps React to figure out which list item was updated and needs to be rendered. Therefore key should be derived from the item values and can uniquely identify the item. We can also resort to simply using index of item in the array as key (will incur huge performance penalty if ordering of items in the array is prone to changes).

The key can be specified when declaring a list item component as an element in JSX.

```JSX
function ListItem(props) {
  return <li>{props.value}</li>;
}

const listItems = numbers.map((number) => {
  <ListItem key={number.toString()} // this key do not get passed into the component, but will render the list properly in React. Therefore component code has no access to this key.
    value={number} />
);
```

## Controlled Components

Certain HTML elements has their own internal states and will conflict with React state if not managed properly.

The recommended approach is to let React state be the "single source of truth" and update those HTML element states using React states, creating _controlled components_.

The idea is straightforward:

- value of HTML element is solely based on React component state.
- events from HTML element will be processed by handlers within React component. (sometimes we may need to suppress event default behavior, to allow React component full control over that HTML element).
- these handlers extra the required update, and update React component state.
- this triggers re-rendering, and updates value of HTML element.

## Lifting State

{% include figure image_path="/assets/images/screenshots/react-lifting-state-up.png" alt="" caption="Lifting State Up" %}

_Visualisation of components used in this [example](https://reactjs.org/docs/lifting-state-up.html) on reactjs.org_

- Essentially, if a few children/sibling components need to share states, then their parent (common ancestor) will need to be the one maintaining that state.
- There can be no two way binding or sharing of state between components, since **data flow downwards**.
- Using IOC, we can allow child components to trigger state changes in the parent component, and these changes will propagate to all other child components via props.

# Design Pattern

React documentation guide recommends using Composition instead of Inheritance to achieve any software designs we want.

If we want to focus on business logic, then we should implement it in a separate JavaScript module, and import it into React component only as APIs.

React application should focus on what it does best, building good UI. Therefore, Composition should be sufficient.

## Thinking in React

Summary of an example thinking process provided by React documentation guide:

1. Create a mock UI
2. Break UI into Component Hierarchy
3. Build a static version of UI in React (sample data)
4. Identify the minimal state representation necessary to make UI interactive. (e.g. if we need both data and statistics of those data to provide interactivity, we should only represent the data in our state, and derive statistics only when we require them).
5. Identify where states should live (consider lifting state to common ancestor if necessary).
6. Add inverse data flow (egistering callbacks that introduce IOC for child components to trigger update on parent component states).
