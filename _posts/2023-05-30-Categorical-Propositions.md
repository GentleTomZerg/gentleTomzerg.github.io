---
author: GentleTomZerg
tags: logic
title: Categorical propositions
---

<!--toc:start-->

- [Four Kinds of Categorical propositions](#four-kinds-of-categorical-propositions)
  - [A: Universal Affirmative propositions](#a-universal-affirmative-propositions)
  - [E: Universal Negative propositions](#e-universal-negative-propositions)
  - [I: Particular Affirmative propositions](#i-particular-affirmative-propositions)
  - [O: Particular Negative propositions](#o-particular-negative-propositions)
- [Quality, Quantity and Distribution](#quality-quantity-and-distribution)
  - [Quality](#quality)
  - [Quantity](#quantity)
  - [General Schema](#general-schema)
  - [Distribution](#distribution)
- [The traditional square of opposition](#the-traditional-square-of-opposition)
  - [Contradictories](#contradictories)
  - [Contraries](#contraries)
    - [Contingent:star:](#contingentstar)
  - [Subcontraries](#subcontraries)
  - [Subalternation](#subalternation)
  - [The Square of Opposition](#the-square-of-opposition)
  <!--toc:end-->

# Four Kinds of Categorical propositions

## A: Universal Affirmative propositions

<center> All S is P </center>

## E: Universal Negative propositions

<center> No S is P </center>

## I: Particular Affirmative propositions

<center> Some S is P </center>

## O: Particular Negative propositions

<center> Some S is not P </center>

# Quality, Quantity and Distribution

## Quality

- Affirmative(A, I): the propositions affirms some class inclusion, whether complete or
  partial.
- Negative(E, O): the propositions denies some class inclusion, whether complete or
  partial.

## Quantity

- Universal(A, E): the propositions refers to `all members` of the class as its subjects.
- Particular(I, O): the propositions refers only to `some members` of the class designated
  by its subject.

## General Schema

<center>Quantifier + Subject + Copula + Predicate</center>

## Distribution

A proposition distributes a term if it refers to `all members` of the class
designated by that term.

- the **quantity** of any standard-form Categorical proposition **determines** whether
  its **subject term** is distributed or undistributed.
- the **quality** of any standard-form Categorical proposition **determines** whether
  its **predicate term** is distributed or undistributed.

|                                | Predicate term undistributed | Predicate term distributed |
| ------------------------------ | ---------------------------- | -------------------------- |
| **Subject term distributed**   | A: All S is P                | E: No S is P               |
| **Subject term undistributed** | I: Some S is P               | O: Some S is not P         |

# The traditional square of opposition

- **Opposition**: Standard-form categorical propositions having the **same subject**
  terms and the **same predicate** may **differ from each other in quality or in
  quantity or in both**. Any such kind of differing has been traditionally called
  `opposition`.

## Contradictories

- Two propositions are contradictories if one is the **denial** or **negation** of the other.
- They can not be both **true** and cannot be both **false**.
- Example:

  ```tex
  A: All judges are lawyers.
  O: Some judges are not lawyers.
  Both opposed in quality and quantity.
  ```

  ```tex
  E: No politicians are idealists.
  I: Some politicians are idealists.
  Both opposed in quality and quantity.
  ```

## Contraries

- Two propositions are said to be contraries if **they cannot both be true**.
- The **truth of one entails the falsity of the other**.
- **But both can be false**
- Example:

  ```tex
  P1: Texas will win the coming game with Oklahoma.
  P2: Oklahoma will win the coming game with Texas.

  The truth of P1 entails the falsity of P2.
  The truth of P2 entails the falsity of P1.
  P1 and P2 can also be false since Texas and Oklahoma could be a draw.

  A: All poets are dreamers.
  E: No poets are dreamers.
  A and E can not both be true, but can both be false(Some poets are dreamers).
  ```

### Contingent:star:

A and E cannot be regarded as contraries when **either the A or E proposition is
necessarily true**, **if either is a logical or mathematical truth.**

```tex
A: All squares are rectangles.
E: No squares are rectangles.
```

Here, E is a mathematical truth, it has no possibility to be false.
**So, A and E are not contraries. Since two propositions can only be contraries if
they can be both false**.

**`Contingent`**: Propositions that are **neither necessarily true nor necessarily
false** are said to be `contingent`.

## Subcontraries

- Two propositions are said to be `Subcontraries` if **they cannot both be false**,
- **They may both be true**.
- Examples:

  ```tex
  I: Some diamonds are precious stones.
  O: Some diamonds are not precious stones.
  ```

- :star: Contingent

  ```tex
  I: Some squares are rectangles.
  O: Some squares are not rectangles.

  Here, I can never be true, so I and O can not be Subcontraries.
  ```

## Subalternation

- Two propositions have the **same subject** and **same predicate** terms, and
  **agree in quality** (both affirming or both denying) but **differ in quantity** (one universal, the other particular). They are called `Corresponding Propositions`.

- The opposition between a universal proposition and its corresponding particular
  proposition is known as **`Subalternation`**.

- **In classic analysis, the superaltern implies the truth of the subaltern**.
- Example:

  ```tex
  A: All spiders are eight-legged animals. (superaltern of I)
  I: Some spiders are eight-legged animals. (subalternation of A)

  E: No whales are fishes. (superaltern of O)
  O: Some whales are not fishes. (subalternation of E)
  ```

## The Square of Opposition

![square of opposition](/assets/logic/square-of-opposition.jpg)

- A is given as true:E is false; I is true; O is false.
- E is given as true:A is false; I is false; O is true.
- I is given as true:E is false; A and O are undetermined.
- O is given as true:A is false; E and I are undetermined.
- A is given as false:O is true; E and I are undetermined.
- E is given as false:I is true; A and O are undetermined.
- I is given as false:A is false; E is true; O is true.
- O is given as false:A is true; E is false; I is true.\*
