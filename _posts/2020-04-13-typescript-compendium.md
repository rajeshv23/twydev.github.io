---
title: "My TypeScript Compendium"
toc: true
toc_label: "Chapters"
categories:
  - Collection
tags:
  - typescript
---

**Structure and Interpretation of Computer Program** *The Big Ideas Behind Reliable, Scalable, and Maintainable Systems* - Gerald Jay Sussman, Hal Abelson, 2017
{: .notice--primary}

*to be written*

___

# Preface
Here are my favorite quotes from the preface, and the reason why I picked up this book.

> It doesn’t matter much what the programs are about or what applications they serve. What does matter is how well they perform and how smoothly they fit with other programs in the creatio of still greater programs.
*Alan J. Perlis*

> First, we want to establish the idea that a computer language is not just a way of getting a computer to perform operations but rather that it is a novel formal medium for expressing ideas about methodology. Thus, programs must be written for people to read, and only incidentally for machines to execute. Second, we believe that the essential material to be addressed by a (computer-science) subject at this level is not the syntax of particular programming-language constructs, nor clever algorithms for computing particular functions efficiently, nor even the mathematical analysis of algorithms and the foundations of computing, but rather the techniques used to control the intellectual complexity of large software systems.
*Sussman and Abelson*

# Chapter 1: Building Abstractions with Procedure

> The contrast between function and procedure is a reflection of the general distinction between describing properties of things and describing how to do things, or, as it is sometimes referred to, the distinction between declarative knowledge and imperative knowledge. In mathematics we are usually concerned with declarative (what is) descriptions, whereas in computer science we are usually concerned with imperative (how to) descriptions.

The example provided in the book uses the square-root function as an example. We can define square-root at a function of input variable X, that is greater than or equals to zero, and will be equal to value of X when squared. This is a concise definition to check if the square-root of X is correct, but is totally useless when we try to compute the square-root.

> So a procedure definition should be able to suppress detail. The users of the procedure may not have written the procedure themselves, but may have obtained it from another programmer as a black box. A user should not need to know how the procedure is implemented in order to use it

This actually ties in with Clean Code. Because cleanly named procedures are good abstractions that promotes the construction of even more complex procedures by other engineers.

## 
