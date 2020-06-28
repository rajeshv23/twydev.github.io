---
title: "Clean Architecture"
toc: true
toc_label: "Chapters"
categories:
  - Book Review
tags:
  - software development
  - best practice
---

**Clean Architecture** *A Craftsman's Guide to Software Structure and Design* - Robert C. Martin (Uncle Bob), 2018
{: .notice--primary}

There are many things to look out for when reviewing a Merge Request/Pull Request, but none as important as the architecture and design of the code. By setting up proper practices and automation, we can ensure that requests are small, single purpose, comes with unit tests and passes benchmark code quality check, but only a human reviewer can verify if the architecture of the code is clean and in alignment with the initial design the team has set out to achieve. There are no tools to help the reviewer, besides having a good knowledge of Clean Architecture.

> ... the rules of software architecture are independent of every other variable. ... those timeless, changeless, rules

*Robert C. Martin*

# Introduction

The goal of software architecture is to **minimize** the **human resources** required to **build and maintain** the required system.

A software system however, brings two value to the business stakeholders.

- Functional value, the system provides features to meet business requirements.
- Architectural value, the system is able to adapt quickly and easily to new business requirements.

Very often, urgency of delivering functional features outweigh the importance of creating good software architecture. It is the responsibility of the development team to advocate and assert the importance of architecture over the urgency of features.

# The Building Blocks

A piece of software is made up of building blocks, each building block has its own good practice and principles that we should seek to learn and follow.

## Programming Paradigm

The book introduced the three paradigms in sequence, to show that each paradigm incrementally removes capabilities from the programmer. Each paradigm imposes extra discipline and advocates what negative practice the programmer should be avoiding.

The book also stated that there were no new paradigms discovered after year 1968, suggesting that this is an exhaustive list.

### Structured Programming

> Structured programming imposes discipline on direct transfer of control

A good example to see this paradigm in action is through the game, Human Resource Machine.

{% include figure image_path="/assets/images/screenshots/human-resource-machine.png" alt="" caption="Human Resource Machine. Official Screenshot from Steam" %}

In this game, there are no `if/then/else` or `do/while` control structures, and players will need to implement such structures on their own using actions similar to `goto`. Often to produce the most optimized solution in the game, players will need to abuse the `goto` capability and create something that would be considered *dirty* code.

However, as the book has pointed out, such code cannot be decomposed recursively into smaller units, therefore prevents the use of divide-and-conquer strategy and impose a challenge for producing reasonable **proofs of correctness**. If the program is not provable, then no matter how many tests we perform, we cannot deem that it is correct.

Modern day programming language already removed unrestrained `goto` support, and control structures like `if/then/else` and `do/while` are provided by default, hence we are already influenced to do structured programming without even realising, and we all adhere to certain *discipline on direct transfer of control*.

### Object-Oriented Programming

> Object-oriented programming imposes discipline on indirect transfer of control

The three defining traits of OOP:

- Encapsulation = grouping data and functions into a cohesive set, outside of the group only certain functions are known, while data is hidden.
- Inheritance = re-declaring of a group of variables and functions, within an enclosing scope, in another group that is a pure superset of the initial group.
- Polymorphism = the use of pointers to functions to call different functions depending on the pointers that were declared initially. 

The book pointed out that encapsulation and inheritance can already be achieved through the C language, but polymorphism support in OOP languages is the trait that truly helps us impose *discipline on indirect transfer of control* as programmers do not need to explicitly set pointers to functions and to remember and implement conventions manually which is dangerously error-prone.

Polymorphism also enabled the widespread adoption of **plugin architecture**, through dependency inversion. We know that program control flows from higher-level functions to lower-level functions, but instead of having higher-level code also directly depend on lower-level code, we can invert the dependency, by making higher-level code depend on an interface. Polymorphism then allows the lower-level code implementation to be plug-able to that interface. 

*Absolute control over the direction of all source code dependencies, not constrained to align with flow of program control.*

### Functional Programming

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