---
title: "Designing Data-Intensive Applications"
toc: true
toc_label: "Chapters"
categories:
  - Book Review
tags:
  - data systems
  - software design
---

**Designing Data-Intensive Applications** *The Big Ideas Behind Reliable, Scalable, and Maintainable Systems* - Martin Kleppmann, 2017
{: .notice--primary}

I like the opening quote from Alan Kay. While we dive into a mad rush to implement new technologies, we forgot about the fundamental principles that make good software and applications.

___

# Preface
This book aims to help everyone understand which kind of data storage and processing technology is appropriate for which purpose. The focus is on software architecture. Very important in our current industry filled with buzzwords and hype.

# Foundations of Data Systems

## Chapter 1: Reliable, Scalable, and Maintainable Applications
This book focus on three primary concerns:

### 1. Reliability
The system should continue to work correctly (performing the correct function at desired level of performance) even in the face of adversity (hardware or software faults, and even human error).

**Faults** - fault is not failure. Fault is deviation from specifications, which may lead to the entire system coming to a stop (failure). And it is impossible to totally *prevent faults from happening*, therefore fault-tolerant designs aims to *prevent faults that had already happened from resulting in failure*.

**Hardware Faults** - with the popularity of cloud computing, there is a growing trend of relying on software fault-tolerance techniques to mitigate hardware faults, since users no longer directly deal with the hardware but still have to manage any outages in cloud services and computing instances.

**Software Errors** - Some tricky bugs will only surface under a specific and unusual set of circumstances. There is no quick solution since this is the nature of software development. We may try to mitigate this by being careful with our design, thorough testing, process isolation, allowing processes to crash and restart, and always monitor system behavior.

**Human Errors** - TBC

### 2. Scalability
As the system grows (in data, traffic, or complexity), there are ways to deal with the growth.

3. Maintainability - New engineers and operations team trying to maintain the current software behavior or extending it should be able to work productively.

# Distributed Data

# Derived Data