---
author: GentleTomZerg
tags: Software Design
title: Philosophy of Software Design
---

# Nature of Complexity

## Definition

> Complexity is anything related to the structure of a software system that
> makes it hard to understand and modify the system.

## Symptoms of complexity

- **Change amplification**
  > a seemingly simple change requires code modifications in many different places.
- **Cognitive load**
  > refers to how much a developer needs to know in order to complete a task.
- **Unknown unknowns**
  > it is not obvious which pieces of code must be modified to complete a task, or what information a developer must have to carry out the task successfully.

## Causes of Complexity

- **Dependencies**
- **Obscurity**

## Complexity is Incremental

# Strategic & Tactical Programming

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

- Ideally, each module would be completely independent of the others > a developer could work in any of the modules without
  knowing anything about any of the other modules

- Reality, **modules must work together by calling each others's functions or methods.** Dependencies always exists!!!

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

- Include unimportant details -> increase `cognitive load`
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

- reduce cognitive load -> simplifies interface
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
  2. perform modifications
  3. write out new version

  Problem: Both three class has knowledge about the file format -> results in
  information leakage.

  Solution: Combine the core mechanisms for reading and writing files into single
  class.
  ```

- Conclusion:
  Order usually does matter, but it shouldn't be reflected in the module structure
  unless that structure is consistent with information hiding.**When design
  modules, focus on the knowledge that's needed to perform each task, not the
  order in which tasks occur.**

  > In temporal decomposition, execution order is reflected in the code
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

## Make classes somewhat general-purpose

**somewhat general-purpose** means that the module's functionality should
reflect your current needs, but its interface should be general enough to
support multiple uses.

- Examples:

  App -> GUI text editor

  ```tex

  Design 1:
  void backspace(Cursor cursor);
  void delete(Cursor cursor);
  void deleteSelection(Selection selection);

  - high cognitive load
  - shallow methods -> each method only suitable for one user interface
    operation
  - user had to learn about a large number of methods
  ```

  ```tex

  Design 2:
  void insert(Position position, String newText);
  void delete(Position start, String end);
  Position changePosition(Position position, int numChars);

  now, the delete key looks like:
  text.delete(cursor, text.changePosition(cursor, 1));
  the backspace looks like:
  text.delete(text.changePosition(cursor, -1), cursor);
  ```

  Here, the details are important: user wants to know which characters are
  deleted. **When the details are important it is better to make them explicit
  and as obvious as possible.**

## How to design general-purpose class?

- What is the simplest interface that will cover all my current needs?

  > If you reduce the number of methods in an API without reducing its overall
  > capabilities, then you are probably creating more general-purpose
  > methods.

- In how many situations will this method be used? -> Don't be special
  purpose!!!
- Is this API easy to use for my current needs? -> Don't go too far!!!

# Different Layer, Different Abstraction

## :triangular_flag_on_post:Pass-through methods:triangular_flag_on_post:

When adjacent layers have similar abstractions, the problem often manifests
itself in the form of `pass-through` methods. A pass-through method is one that
**does little except invoke another method, whose signature is similar or
identical to that of the calling method**.

- make methods shallower -> increase the interface complexity; no contribution
  to the functionality of the system.

- indicates that there is a confusion over the division of responsibility
  between classes.

  ```java
  public class TextDocument ... {
      private TextArea textArea;
      private TextDocumentListener listener;
      ...
      public Character getLastTypedCharacter() {
          return textArea.getLastTypedCharacter();
      }

      public int getCursorOffset() {
          return textArea.getCursorOffset();
      }
      public void insertString(String textToInsert, int offset) {
          textArea.insertString(textToInsert, offset);
      }
      public void willInsertString(String stringToInsert, int offset) {
          if (listener != null) {
              listener.willInsertString(this, stringToInsert, offset);
          }
      }
      ...
  }
  ```

  > A pass-through method is one that does nothing except pass its arguments to
  > another method, usually with the same API as the pass-through method. This
  > typically indicates that there is not a clean division of responsibility between
  > the classes.

- Solutions:
  ![solutions-for-passthrough](/assets/software-design.assets/solutions_for_passthrough.jpg)

  - (7.1b) expose the lower level class directly to the callers of the higher
    level class.

  - (7.1c) redistribute the functionality between the classes
  - (7.1d) if the class cannot be disentangled, merge them together

## When is Duplications OK?

Having methods with the same signature is not always bad. The important thing is
that **each new method should contribute significant functionality**.

- several methods have same signature as long as each of them provides
  useful and distinct functionality.
- several methods provide different implementations of the same interface.

## Decorator Design Pattern

Decorator Design Pattern encourages API duplication across layers. The decorator
objects provides an API similar or identical to the underlying object and its
method invoke the methods of the underlying object.

- Motivation: separate special-purpose extensions of a class from a more generic
  core.
- Problem: decorator classes tend to be shallow. -> a large amount of
  boilerplate for a small amount of new functionality
- Note: easy to overuse!!!

Whether to use Decorator?

- Could you add the new functionality directly to the underlying class, rather
  than creating a decorator class?
- If the functionality is specialized for a particular use case, would it make
  sense to merge it with the use case rather than creating a separate class?
- Could you merge the new functionality with an existing decorator?
- Whether the new functionality really needs to wrap the existing
  functionality.

## Interface VS Implementation

Another application of the "different layer, different abstraction" rule is that
**the interface of a class should normally be different from its
implementation**:
the representations used internally should be different from the abstractions
that appear in the interface. -> **If the two have similar abstractions, then the
class probably isn't very deep**.

Examples:

```tex
App: text editor -> the text are stores as lines

Design 1:
getLine()
putLine()

Problem:
shallow and awkward to use, the interface getLine() has the implementations of
get line, when user use this, they have to handle the changes between lines.

Design 2:
insert() -> characters at some position
delete() -> characters at some position

Note:
the API insert() and delete() uses lines representations, but it provides a
character based interface, which makes the text class deeper and simplifies
higher level code that uses the class.
```

## Pass-through Variables

Another form of API duplication across layers is a pass-through variable, which
is a variable that is passed down through the long chain of methods.

Pass-through Variables add **complexity** because **they force all of the
intermediate methods to be aware of their existence**, even though the methods
have no use for the variables (See Figure a).

![techniques for dealing pass-through variables](/assets/software-design.assets/pass-through-variables.png)

How to solve?

- **If there is already an object shared between the topmost and bottommost
  methods.** (See Figure b) However, if there is such an object, then it may
  itself be a pass-through variable(how else does m3 get access to it).

- **Store information in a global variable.**(See Figure c) However, global
  variables make it impossible to create two independent instances of the same
  system in the same process, since access to the global variables will
  conflict. Examples: when you do testing, you want to use different
  configurations.

- **:star:Introduce a context object** (See Figure d)

  The context allows multiple
  instances of the system to coexist in a single process, each with its own
  context. The class of m3 and class of m1 stores a reference to the context
  object. Thus, the context is available everywhere, but it only appears as an
  explicit argument in constructors.

  However, it has disadvantages:

  - it may **not be obvious** why a particular variable is present, or where it
    is used.

  - Without discipline, a context can turn into a huge grab-bag of data that creates **notorious dependencies** throughout the system.
  - Contexts may also create **thread-safety** issues

# Pull Complexity Downwards
Suppose you are developing a new module, and you discover a piece of **unavoidable** complexity. Which is better?

- let the users deal with the complexity
- handle the complexity internally within the module

**It is more important for a module to have a simple interface than a simple implementation**

## Examples: Configuration Parameters

> Configuration parameters are an example of moving complexity upwards instead
of down. Rather than determining a particular behavior internally, a class can
export a few parameters that control its behavior, such as the size of a cache or
the number of times to retry a request before giving up. Users of the class must
then specify appropriate values for the parameters. Configuration parameters
have become very popular in systems today; some systems have hundreds of
them.

- Good Ways: allow user to tune system for their particular requirements and workloads.
- Bad Ways: provide an easy excuse to avoid dealing with important issues and pass them on to someone else.

## When to pull complexity downwards?
- the **complexity** being pulled down is **closely related** to the class's **existing functionality**.
- pulling the complexity down **simplifies** the class's **interface**
- the goal is to minimize overall **system** complexity

# :star:Better Together Or Better Apart

> When deciding whether to combine or separate, the goal is to reduce the complexity of the system as a whole and improve its modularity. It might appear that the best way to achieve this goal is to **divide** the system into **a large number of small components**.

Problem of Subdivision:
- Some **complexity** comes just from the **number of components** -> harder to keep track of and harder to find a desired component.
- Result in **additional code to manage the components**
- Creates **separation** -> make the developer harder to see the components at the same time or even be aware of their existence.
- Result in **duplications**

When it makes sense to separate?

## Bring together if information is shared

Examples:
```tex
App -> Http Server

Implementation:
String read(text from network socket)
Map parse(String from read())

Problem: both of the methods ended up with
considerable knowledge of the format of HTTP requests: the first method was
only trying to read the request, not parse it, but it couldn’t identify the end of the
request without doing most of the work of parsing it (for example, it had to parse
header lines in order to identify the header containing the overall request length).
```

## Bring together if it will simplify the interface
> When two or more modules are combined into a single module, it may be
possible to define an interface for the new module that is simpler or easier to use
than the original interfaces. This often happens when the original modules each
implement part of the solution to a problem. 

## Bring together to eliminate duplication
If you find the same pattern of code repeated over and over, see if you can reorganize the code to eliminate the repetition.

- Approach 1: refactor the repeated code to eliminate the repetition. -> Most effective when the replacement method has a simple signature.
- Approach 2: refactor the code so that the snippet in question only needs to be executed in one place

## :triangular_flag_on_post:Repetition:triangular_flag_on_post:
> If the same piece of code (or code that is almost the same) appears over and
over again, that’s a red flag that you haven’t found the right abstractions.


## Separate general-purpose and special-purpose code
If a module contains a mechanism that can be used for several different purposes,
then it should provide just that one general-purpose mechanism.

**Special-purpose code associated with a general-purpose mechanism should normally go in a <u>different module</u>**
Example:
```tex
App -> GUI text editor
text class -> provide general purpose operations like delete() and insert()
user interface class -> provide special purpose operations like delete the selection
```
Conclusion:
- **the <u>lower layers</u> of a system tend to be more <u>general-purpose</u> and the <u>upper layers</u> more <u>special-purpose</u>.**
- When you encounter a class that includes both general purpose and special purpose features for the same abstraction, see if the class can be separated into two classes.

## :triangular_flag_on_post:Special-General Mixture:triangular_flag_on_post:
> This red flag occurs when a general-purpose mechanism also contains code
specialized for a particular use of that mechanism. This makes the mechanism
more complicated and creates information leakage between the mechanism
and the particular use case: future modifications to the use case are likely to
require changes to the underlying mechanism as well.


