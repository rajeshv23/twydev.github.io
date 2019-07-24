---
title: "Domain-Driven Design Fundamentals"
toc: true
toc_label: "Chapters"
categories:
  - Online Course
tags:
  - domain driven design
---

**Domain-Driven Design Fundamentals** - Steve Smith and Julie Lerman, accessed on PluralSight 2019
{: .notice--primary}

Great course that steps through the DDD process with a simple appointment scheduling software. Dog pictures used in the demo is a bonus. 

___

## Introduction

### Focus of DDD

1. Interaction with Domain Experts
2. Focus on a single sub-domain at a time
3. Implementation of sub-domains

### Resources

- DDDCommunity.org
- Domain-Driven Design: Tackling Complexity in the Heart of Software, by Eric Evans
- Applying Domain-Driven Design and Patterns: with examples in C# and .NET, by Jimmy Nilsson
- Implementing Domain-Driven Design, by Vaughn Vernon

## Modeling Problems in Software

1. First step is to understand as much of the business domain as possible from domain experts.
2. Ideally all important business domain terms are captured (nouns and verbs especially)
3. With sufficient terms captured, we can start to see exact same terms in the business domain used 
	- by different people to mean different things
	- by different people to achieve different goals
4. This is when we can start setting up Bounded Context to group and divide up the business domain according to specific purposes.
	- now it is fine for the same term may exist in separate bounded contexts and carry different meaning
5. Consider how the separate bounded context would interact with each other via Context Mapping
6. Create the Ubiquitous Language for each bounded context, used throughout conversations and code.

**Key Terms**

- **Problem Domain** is the spcific problem the software is trying to solve.
- **Core Domain** is the key differentiator for the business. Something the software MUST do well.
- **Sub-Domains** are the separate applications or features that the software must support or interact with.
- **Bounded Context** is a specific responsibility, with explicit boundaries that separate it from other parts of the system.