---
title: "Clean Architecture"
toc: true
toc_label: "Chapters"
categories:
  - Software Design
  - Software Architecture
tags:
  - best practice
---

*Work in progress.*

# Clean Architecture

**Clean Architecture** *A Craftsman's Guide to Software Structure and Design* - Robert C. Martin (Uncle Bob), 2018
{: .notice--primary}

There are many things to look out for when reviewing a Merge Request/Pull Request, but none as important as the architecture and design of the code. By setting up proper practices and automation, we can ensure that requests are small, single purpose, comes with unit tests and passes benchmark code quality check, but only a human reviewer can verify if the architecture of the code is clean and in alignment with the initial design the team has set out to achieve. There are no tools to help the reviewer, besides having a good knowledge of Clean Architecture.

> ... the rules of software architecture are independent of every other variable. ... those timeless, changeless, rules

*Robert C. Martin*

## Introduction

The goal of software architecture is to **minimize** the **human resources** required to **build and maintain** the required system.

A software system however, brings two value to the business stakeholders.

- Functional value, the system provides features to meet business requirements.
- Architectural value, the system is able to adapt quickly and easily to new business requirements.

Very often, urgency of delivering functional features outweigh the importance of creating good software architecture. It is the responsibility of the development team to advocate and assert the importance of architecture over the urgency of features.

## The Building Blocks

A piece of software is made up of building blocks, each building block has its own good practice and principles that we should seek to learn and follow.

### Programming Paradigm

The book introduced the three paradigms in sequence, to show that each paradigm incrementally removes capabilities from the programmer. Each paradigm imposes extra discipline and advocates what negative practice the programmer should be avoiding.

The book also stated that there were no new paradigms discovered after year 1968, suggesting that this is an exhaustive list.

#### Structured Programming

> Structured programming imposes discipline on direct transfer of control

A good example to see this paradigm in action is through the game, Human Resource Machine.

{% include figure image_path="/assets/images/screenshots/human-resource-machine.png" alt="" caption="Human Resource Machine. Official Screenshot from Steam" %}

In this game, there are no `if/then/else` or `do/while` control structures, and players will need to implement such structures on their own using actions similar to `goto`. Often to produce the most optimized solution in the game, players will need to abuse the `goto` capability and create something that would be considered *dirty* code.

However, as the book has pointed out, such code cannot be decomposed recursively into smaller units, therefore prevents the use of divide-and-conquer strategy and impose a challenge for producing reasonable **proofs of correctness**. If the program is not provable, then no matter how many tests we perform, we cannot deem that it is correct.

Modern day programming language already removed unrestrained `goto` support, and control structures like `if/then/else` and `do/while` are provided by default, hence we are already influenced to do structured programming without even realising, and we all adhere to certain *discipline on direct transfer of control*.

#### Object-Oriented Programming

> Object-oriented programming imposes discipline on indirect transfer of control

The three defining traits of OOP:

- Encapsulation = grouping data and functions into a cohesive set, outside of the group only certain functions are known, while data is hidden.
- Inheritance = re-declaring of a group of variables and functions, within an enclosing scope, in another group that is a pure superset of the initial group.
- Polymorphism = the use of pointers to functions to call different functions depending on the pointers that were declared initially. 

The book pointed out that encapsulation and inheritance can already be achieved through the C language, but polymorphism support in OOP languages is the trait that truly helps us impose *discipline on indirect transfer of control* as programmers do not need to explicitly set pointers to functions and to remember and implement conventions manually which is dangerously error-prone.

Polymorphism also enabled the widespread adoption of **plugin architecture**, through dependency inversion. We know that program control flows from higher-level functions to lower-level functions, but instead of having higher-level code also directly depend on lower-level code, we can invert the dependency, by making higher-level code depend on an interface. Polymorphism then allows the lower-level code implementation to be plug-able to that interface. 

*Absolute control over the direction of all source code dependencies, not constrained to align with flow of program control.*

#### Functional Programming

> Function programming imposes discipline upon assignment

Variables in functional languages are immutable. The impact on our architecture is the avoidance of race conditions, deadlock conditions, and concurrent update problems, since these issues are all created by mutable variables. Therefore, we impose *discipline upon variable assignment* in our program.

To practically incorporate the immutability into our architecture requires segregation of mutable components from immutable components. Mutable components cannot be fully avoided due to interaction of the system with external services. However, with sufficient resources, *event sourcing* can be considered to only store immutable transactions, and compute state from those transactions when required.

## Design Principles - SOLID

SOLID principles apply to cohesive groupings of functions and data structures, and these grouping should be interconnected. In OO languages, it is usually the *class*, but it does not mean that SOLID principles only applies to classes. It is equally relevant in JavaScript.

The goal of SOLID principles is to create module level structures in a software that are

- adaptable to changes
- easy to understand
- reusable components

### Single Responsibility Principle

> Module should be responsible to one single actor

The meaning of SRP is not that **a module should only do one thing. That is a misconception.** SRP means that the functions and data structures in a module are cohesive enough, to only be changed when it's only actor requires the module to make a change. If two actors cause a single module to change, that module has violated SRP

Common pattern to tackle the problem in a class with too many responsibilities is to first reduce the scope of the class, then move the other functions that has been eliminated into separate Single Responsibility classes. Access to the original class should be swapped with a Facade class, that interacts with this group of now decoupled classes.

### Open-Closed Principle

> A software artifact should be open for extension but closed for modification

What it means is an artifact should be extensible without any modification. And this can be done by heavy use of interfaces.

{% include figure image_path="/assets/images/screenshots/clean-architecture-onion-layers.jpg" alt="" caption="The Clean Architecture diagram. From The Clean Code Blog by Uncle Bob." %}

This high-level onion abstraction of our software can be created using classes and components with uni-directional relationships.

- double-line boxes represents components
- single-line boxes represents classes
- `<I>` annotates an interface
- `<DS>` annotates data structure
- anchor at the top right corner of a box annotates a domain model
- open arrowheads represent *using* relationship
- closed arrowheads represent *implements* or *inherits* relationships

{% include figure image_path="/assets/images/screenshots/ocp-class-and-components-dependencies.png" alt="" caption="OCP Partitioning. From Clean Architecture book." %}

Notice that components that maps to the inner layer of the onion architecture are protected from changes in the outer layers of the onion.

### Liskov Substitution Principle

> essentially polymorphism. S qualifies as a subtype of T, if objects of S can seamlessly replace all objects of T, inside a program defined in terms of T.

Classes can inherit from parent classes, and seamlessly replace objects of parent class in the program. From a broader, architectural perspective, we again make heavy use of interfaces between components and classes to uphold this good principle.

**Special Note** - we can achieve the same result using composition (instead of a class inheriting or implementing an interface, we have an object that delegates to members that conform to the interface, and this object becomes an adapter/intermediary layer) when working with JavaScript. The good principle will still be conserved as this is just an implementation detail. And as the Gang of Four has said, prefer composition instead of inheritance when possible, as it often simplifies your code.

### Interface Segregation Principle

> clients of an interface should not be forced to implement more methods than necessary

Violating the principle just means that the interface itself can be further broken down into decoupled interfaces. This principle protects us from unnecessary changes to components simply because its dependency violated this principle, and has an additional baggage that needs to be changed, which is entirely unrelated to the component's main function or behavior.

### Dependency Injection Principle

> source code should depend on abstractions, and avoid volatile concretions

Some golden rules:

- Don't refer to volatile concrete classes
- Don't derive from volatile concrete classes
- Don't override concrete functions

The *Abstract Factory* pattern showcases this principle, as the client of abstract factory only works with the interface of a factory, and interfaces of the entire family of objects created by the factory. At a higher level above the client, the concrete factory is injected into the client, therefore the client's source code will only ever work with abstractions, no concretions.

**Special Note** - I was confused between *Abstract Factory* pattern and *Factory Method* patterns and did a quick search. Abstract Factory is a pattern used to produce a family of related objects from a factory, in contrast the Factory Method is only responsible for returning one object. Both patterns essentially defer the actual object creation to another layer, away from the client.

DIP violation cannot be entirely removed as we ultimately need to handle concrete classes. Most systems contain the handling of concrete components in a `main` function, which would instantiate all necessary concrete implementations depending on configuration, place these objects in a global variable, accessible to the entire application, and other parts of the source codes will only be dealing with these global variables through abstract interfaces.

## Component Principles

Applying the above SOLID principles makes good modules, and now we need good principles to compose those modules together to form components.

### What is a Component

Some concrete examples are `.jar` files in Java, `gem` files in Ruby, `DLLs` in .NET, and my guess are NPM packages in JavaScript. Regardless of language, good components are always independently deploy-able and develop-able.

### Component Cohesion Principles

These principles deal with the appropriate classes to include in a component.

#### Reuse/Release Equivalence Principle

> The granule of reuse is the granule of release

Components should have proper release versioning and documentation to allow users of the component to easily reuse them in their own applications. This can be facilitated by module management tools and package repositories.

But to have proper release, all classes and modules inside a component must be cohesive enough to be releasable together. In other words, the user of this component do not need to worry about the internals of the component being not up to date, and only need to perceive the component as one single, granular unit.

By extension, when organizing components/features within our own application, if we eventually intend to release those as individual packages, we should also take REP into consideration.

#### Common Closure Principle

> A component should be a group of classes and modules that change for the same reasons, at the same time.

This is SRP from SOLID principles, but applied to the component level. Maintainability is usually more important than reusability. If we require a change in the application, we want to ideally limit it to a component, rather than having to distribute the change across many components.

#### Common Reuse Principle

> Don't force users of a component to depend on things they don't need

This is similar to ISP in SOLID principles but applied to the component level. The consumer of a component ideally uses all classes and modules in the component (component internals are tightly cohesive), if not this dependency will carry baggages of unused classes and modules, and any changes to those unused internals will also propagate changes to the consumer for no good reasons.

#### Tension Triangle

{% include figure image_path="/assets/images/screenshots/cohesion-principles-tension.png" alt="" caption="Cohesion principles tension diagram. From Clean Architecture book." %}

When we evaluate a component, we will find that it lands somewhere within the triangular space. Edges of the triangle describes the cost of abandoning the principle on the opposite vertex. A good architect will evaluate the tradeoffs and seek to craft the component at a suitable position in the triangle, but make it appropriate and relevant to the current state of the project.

Early development of a project will likely focus more on CCP to improve develop-ability and collaboration in the team. However as project matures, focus will be shifted to the left of the triangle as there will be more external consumer of the components, and the team will need to be concern over release management.

### Component Coupling Principles

The above three principles deals with the internals of a component, the next three principles deal with the interaction between components.

#### Acyclic Dependencies Principle

> Allow no cycles in the component dependency graph

Components in an application can be mapped on to a dependency graph. Having cycles in a graph means that there will be a subset of all components that are so tightly coupled that they cannot be easily build and release without stepping all over each other's work. This subset of components behave as one large component block in the system.

Having a Directed Acyclic Graph (DAG) on the other hand means that each component only need to be concerned with maintaining their own releases, and the consumers of those components can decide which release version to consume. This allows easier and faster building of the application, and determines the order of component build.

Cycles can be broken by applying Dependency Inversion Principle, using interfaces to separate the dependency of two component, and extract out common elements of the two components into a new concrete component that implement the interfaces. This new concrete component becomes a sink that absorbs the flow of dependency direction, breaking the cycle.

It is not realistic to create the component dependency graph from top down since day one. We should gradually evolve our components as the project progresses and mature.

#### Stable Dependencies Principle

> Depend in the direction of stability

Stability measures the amount of work required to make a change. A component is highly stable if a lot of other components depend on it, but itself has no dependency on others. A component is highly unstable if it only depends on other component, but no others every depend on it. It is obvious that updating a stable component will cause a lot of repercussion than an unstable component.

**Special Note** - Stability is just a measure, and it does not indicate whether more stability is good or bad. The system needs both stable and unstable components, the key is to arrange the dependency graph of the system, such that components are less likely to change are stable, and components that are volatile are unstable.

Formula for calculating positional stability of a component:

- Fan-in: number of classes outside of this component that depends on classes inside this component (i.e. number of import statements importing classes/modules from this component)
- Fan-out: number of classes inside this component that depends on classes from other components (i.e. number of import statements this component is using)
- Instability: *I = Fan-out / (Fan-out + Fan-in)*

If a component has no Fan-in, which means nothing depends on this component, it will have *I* = 1, meaning it is maximally unstable. If a component has no Fan-out, which means it does not depend on anything, it will have *I* = 0, meaning it is maximally stable.

If a component, named A, is expected to be stable but is depending on another highly volatile component, B, we have a design problem and we will run into the unpleasant situations of having to update A unnecessarily when B changes. The solution to this is again the use of Dependency Inversion, to make both components depend on an interface. Therefore A which depends on the interface remains stable. B is unstable, but it only depends on the interface and is free to change. The interface itself do not depend on anything, so it is maximally stable.

#### Stable Abstraction Principle

> A component should be as abstract as it is stable

Abstractions allows a stable component to still be flexible to changes in future without causing pain to its dependents.

Formula for measuring Abstractness:

- Nc: number of classes in a component
- Na: number of abstract classes/interfaces in a component
- Abstractness: *A = Na / Nc*

*A* = 0 means there are no abstract classes or interfaces, *A* = 1 means that there are only abstract classes.

Looking at both Instability *I* and Abstractness *A*, we can identify the characteristics of a component and its impact to our system.

- A highly stable component with no abstractions (*I* = 0, *A* = 0) typically causes a world of hurt as it is extremely difficult to change. An example would be database schema that sits between the database and an OO application. Database schema is highly volatile in the early stage of the project and we simply don't have a good way to provide abstraction. If the component is non-volatile however, such as a library like `momentJS`, it is less of a problem as we are not expecting much changes.
- A highly abstract and unstable component (*I* = 1, *A* = 1) is typically useless, as no other components are using it. Usually these are legacy zombie code.

So it seems like the line that connects (*I* = 0, *A* = 1) and (*I* = 1, *A* = 0) is where we should strive be place our component. (The book calls this line the Main Sequence). To put it into words, an increasingly stable component should be increasingly abstract.

These metrics, *I* and *A*, and the distance between the current component's metric from the Main Sequence line can be collected over several releases to perform statistical analysis and process control, to monitor and measure design changes in our software.