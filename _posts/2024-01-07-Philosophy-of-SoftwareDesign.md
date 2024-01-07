---
author: GentleTomZerg
tags: Software Design
title: Philosopy of Software Design
---

# Nature of Complexity
## Definition
> Complexity is anything related to the structure of a software system that
makes it hard to understand and modify the system. 

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
working, such as a new feature or a bug fix. At first glance this seems totally
reasonable: what could be more important than writing code that works?
However, tactical programming makes it nearly impossible to produce a good
system design.

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
- Ideally, each module would be completely indenpendent of the others
    > a developer could work in any of the modules without
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
abstraction is a simplified view of an entity, which omits **unimportant**
details. 
- Include unimportant details -> increase `coginitive load`
- Omit important details -> increase `obscurity`

## Deep Modules
> **The best modules are those that provide powerful functionality yet have simple
interfaces.**

![Deep and Shallow Modules](/assets/software-design.assets/deep_modules.png)

## Shallow Modules
>  a shallow module is one whose interface is relatively complex
in comparison to the functionality that it provides. 

Extremes Examples:
```java
private void addNullValueForAttribute(String attribute) {
    data.put(attribute, null);
}
```
## :triangular_flag_on_post:Shallow Module:triangular_flag_on_post:

> A shallow module is one whose interface is complicated relative to the
functionality it provides. Shallow modules don’t help much in the battle
against complexity, because the benefit they provide (not having to learn about
how they work internally) is negated by the cost of learning and using their
interfaces. Small modules tend to be shallow.





