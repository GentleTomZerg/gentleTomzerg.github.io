---
author: GentleTomZerg
tags: Software Design
title: Philosopy of Software Design
---

# Nature of Complexity

## Definition

> Complexity is anything related to the structure of a software system that
> makes it hard to understand and modify the system.

## Symptoms of complexity

- **Change amplification**
  > a seemingly simple change requires code modifications in many different places.
- **Coginitive load**
  > refers to how much a developer needs to know in order to complete a task.
- **Unknown unknows**
  > it is not obvious which pieces of code must be modified to complete a task, or what information a developer must have to carry out the task sucessfully.

## Causes of Complexity

- **Dependencies**
- **Obscurity**

## Complexity is Incremental

# Stratigic & Tactical Programming

## Tactical programming

> In the tactical approach, your main focus is to get something
> working, such as a new feature or a bug fix. At first glance this seems totally
> reasonable: what could be more important than writing code that works?
> However, tactical programming makes it nearly impossible to produce a good
> system design.

## Strategic programming

> Working code isn't enough
> Your primary goal must be to produce a great design, which also happens to work.

- Proactive Investments

  - taking extra time to find a better design rather the first one comes to mind.
  - good documentation

- Reactive Investments

      > No matter how much you invest up front,

  there will inevitably be mistakes in your design decisions.

      - Take extra time to fix design problems!!!

# Modules Should Be Deep

Modules can take many forms -> classes, subsystems, or services.

- Ideally, each module would be completely indenpendent of the others > a developer could work in any of the modules without
  knowing anything about any of the other modules

- Reality, **modules must work together by calling each others's functions or methods.** Dpendencies always exists!!!

## Modular Design

- Two parts of Module

  - interface
  - implementation

- Best Modules

  whose interfaces are much simpler than their implementations

## What's in an interface

- formal information: information that are specified explicitly in the code.
- informal information: information that are not specified in a way that can be understood or enforced by the programming language.

## Abstractions

> An
> abstraction is a simplified view of an entity, which omits **unimportant**
> details.

- Include unimportant details -> increase `coginitive load`
- Omit important details -> increase `obscurity`

## Deep Modules

> **The best modules are those that provide powerful functionality yet have simple
> interfaces.**

![Deep and Shallow Modules](/assets/software-design.assets/deep_modules.png)

## Shallow Modules

> a shallow module is one whose interface is relatively complex
> in comparison to the functionality that it provides.

Extremes Examples:

```java
private void addNullValueForAttribute(String attribute) {
    data.put(attribute, null);
}
```

## :triangular_flag_on_post:Shallow Module:triangular_flag_on_post:

> A shallow module is one whose interface is complicated relative to the
> functionality it provides. Shallow modules don’t help much in the battle
> against complexity, because the benefit they provide (not having to learn about
> how they work internally) is negated by the cost of learning and using their
> interfaces. Small modules tend to be shallow.

# Information Hiding(and Leakage)

> techniques for creating deep modules

- reduce coginitive load -> simplifies interface
- easier to evolve

## :triangular_flag_on_post:Information Leakage:triangular_flag_on_post:

> Information leakage occurs when the **same knowledge** is used in **multiple places**,
> such as two different classes that both understand the format of a particular
> type of file

- How can I recognize these classes so that this particular piece of knowledge
  only affects a single class?
- Answer1: merge them into a single class
- Answer2: create a new class that encapsulates just that information

## :triangular_flag_on_post:Temporal Decomposition:triangular_flag_on_post:

In temporal decomposition, the structure of a system corresponds to the time
order in which operations will occur.

- Examples:

  ```tex
  Application -> reads a file in a particular format -> modifies -> writes;

  Design -> Three Classes:
  1. read file
  2. perform modiifcations
  3. write out new version

  Problem: Both three class has knowledge about the file format -> results in
  information leakage.

  Solution: Combine the core mechanisms for reading and wirting files into single
  class.
  ```

- Conclusion:
  Order usually does matter, but it shouldn't be reflected in the module structure
  unless that structure is consistent with information hiding.**When design
  modules, focus on the knowledge that's needed to perform each task, not the
  order in which tasks occur.**

  > In temporal decompositon, execution order is reflected in the code
  > structure: operations that happen at different times are in different
  > methods or classes. If the same knowledge is used at different points in
  > execution, it gets encoded in multiple places, resulting in information
  > leakage.

## :triangular_flag_on_post:Overexposure:triangular_flag_on_post:

> If the API for a commonly used feature forces users to learn about other
> features that are rarely used, this increases the **cognitive load** on users who
> don't need the rarely used features.

# General-Purpose Modules are Deeper

Questions: When Designing Modules,

- General Purpose? -> might include facilities that are never actually needed.
- Special Purpose? -> you can always refactor it to make it general purpose.

## Make classess some what general-purpose
