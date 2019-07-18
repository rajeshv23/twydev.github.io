---
title: "Modern Software Architecture: Domain Models, CQRS, and Event Sourcing"
toc: true
toc_label: "Chapters"
categories:
  - Online Course
tags:
  - domain driven design
  - cqrs
  - event sourcing
---

A course by Dino Esposito on PluralSight.
Dino recommends following this project https://github.com/mastreeno/Merp which is an open source ERP system built using DDD and CQRS architecture.

1. Repositioning DDD as the ideal methodology to conduct domain analysis.
2. Explore architectures supporting DDD that are implemented in software systems (domain models, CQRS, event sourcing)
3. UX-first approach to designing software system.


### DDD Overview

when building software, there are always 2 choices - just building something vs building something right. Although the end result may appear the same to the end users, the working software may have been built using a wrong approach, which may turn into a technical debt that you are going to pay at some point.

DDD is a software design approach introduced by Eric Evans over a decade ago.
Primary intent was to tackle complexity in the heart of software.

You can think about the complexity of software as the same complexity in the real world. Developers are merely describing what they can see in the real world problem with programming languages.

Without DDD, a typical software development project may look like this:
1. gather requirements
2. build a (relational) data model
3. identify tasks the system has to do with the data. (sequence of tasks forms the so-called business logic)
4. build UI for those tasks.
5. produced artifacts close to meeting user needs to fails acceptance tests.
6. repeats process to refine artifact.

Developers lack domain knowledge and this makes the software development an unmanageable process if the complexity passes certain threshold.

Big Ball of Mud (BBM):
A system that's largely unstructured, padded with hidden dependencies between parts, with a lot of data and code duplication and an unclear identification of layers and concerns - a spaghetti code jungle.

DDD has been the first attempt in the industry to organize and systematize a set of principles and practices to build sophisticated software systems reliably and more easily.

DDD was like the traditional approach, which is still aimed at organizing business logic. However, it focuses more on a domain, which is both data and behavior, instead of just data. 

Here is what a DDD flow may look like:
1. crunch as much knowledge about the domain as possible, to capture all internal dynamics and how things work in that domain in the real world. May seek the help of domain experts, but that is not sufficient to form specifications and requirements.
2. split the big domain into multiple subdomains
3. design a rich object model for each recognized subdomains. Design regardless of concerns like persistence and databases first, focus on describing how entities behave and what are the user actions.
4. code by telling objects in the domain model what to do.

Dream of every developer is to build an all-encompassing object model describing the entire domain.

MARTIN FOWLER GRAPHS = data-centric vs domain driven, looking at time and complexity

In 2009, DDD shifted focus. It became a tool used for discovering domain architecture more than organizing business logic.
Domain Model remains a valid pattern to organize business logic bit other patterns can be used as well:
- Object-oriented models
- Functional models
- CQRS
- Classic 3-tier
- client/server 2-tier
More info in youtube video: Eric Evans talk at QCON 2009

DDD Misconceptions
Common misconception of DDD is:
- building object model for the business domain and call it "domain model"
- consume the model in a multi-layer architecture (with app layer, domain layer, etc.)

But in actual fact the most valuable part of DDD is the tools it provides to help us make sense of a domain. This enables you to **Design** the system, **Driven** by your knowledge of the **Domain**.

DDD actually has 2 distinct parts.
1. the Analytical component:
    - sets the top level architecture for the business domain
    - this top level architecture is expressed as constituent elements subdomains that are called Bounded Contexts.
2. the Strategic component:
    - relates to the definition of supporting architecture for each of the identified Bounded Contexts.

The Analytical component is valuable to everyone. However, the Strategic component, which uses Domain Models pattern and multi-layered software architecture, is just one of several possible supporting architectures and it is not necessary for every project to use the Strategic component when practicing DDD.