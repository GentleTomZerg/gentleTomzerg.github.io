---
author: GentleTomZerg
tags: logic
title: Categorical Syllogisms
---

<!--toc:start-->

- [Standard-Form Categorical Syllogisms](#standard-form-categorical-syllogisms)
  - [Terms of the Syllogisms: Major, Minor, and Middle](#terms-of-the-syllogisms-major-minor-and-middle)
  - [The mood of the Syllogism](#the-mood-of-the-syllogism)
  - [The figure of the Syllogism](#the-figure-of-the-syllogism)
- [The Formal Nature of Syllogistic Argument](#the-formal-nature-of-syllogistic-argument)
- [](#)
<!--toc:end-->

# Standard-Form Categorical Syllogisms

**Categorical Syllogisms** is defined as a **deductive argument** consisting of **three** categorical propositions that together
**contain exactly three terms**, **each** of which **occurs** in exactly **two** of the
constituent propositions.

- Standard-Form

  A Categorical Syllogisms is said to be `Standard-Form` when two things are
  true of it:

  1. its premises and its conclusion are all Standard-Form categorical syllogisms propositions(A,E,I or O).
  2. those propositions are arranged in a specified **standard order**.

## Terms of the Syllogisms: Major, Minor, and Middle

```tex
No heros are cowards.
Some soldiers are cowards.
Therefore some soldiers are not heros.

Major Term: hero
Minor Term: soldier
Middle Term: coward
Major Premise: No heros are cowards.
Minor Premise: Some soldiers are cowards.
```

| Term          | Parts                                                            |
| ------------- | ---------------------------------------------------------------- |
| Major Term    | The predicate term of the conclusion.                            |
| Minor Term    | The subject term of the conclusion.                              |
| Middle Term   | The term that appears in both premises but not in the conclusion |
| Major Premise | The premise containing the major term.                           |
| Minor Premise | The premise containing the minor term.                           |

**In a standard-form syllogism, the major premise is always stated first, the
minor premise second, and the conclusion last.**

## The mood of the Syllogism

**mood**: is determined by the types (A, E, I, O) of standard-form categorical
propositions it contains. For example, the mood of the syllogism below is called
**EIO**.

```tex
No heros are cowards. (E)
Some soldiers are cowards. (I)
Therefore some soldiers are not heros. (O)
```

## The figure of the Syllogism

The mood of a standard-form syllogism is not enough, by itself, to characterize
its logical form. Look at the two syllogisms shown below, they have the **same
mood AII** but the first is not valid. The difference is the **relative
positions of their middle terms**, and this is called **figure**.

$$
\begin{align*}
& \text{ All } P \text{ is } M. \\
& \text{ Some } S \text{ is } M. \\
\hline
& \therefore \text{ Some } S \text{ is } P.
\end{align*}
$$

$$
\begin{align*}
& \text{ All } M \text{ is } P. \\
& \text{ Some } M \text{ is } S. \\
\hline
& \therefore \text{ Some } S \text{ is } P.
\end{align*}
$$

- Syllogisms can have four -- and **only** four -- possible different figures.

  - First Figure

  $$
  \begin{align*}
  & M - P \\
  & S - M \\
  \hline
  & \therefore S - P
  \end{align*}
  $$

  - Second Figure

  $$
  \begin{align*}
  & P - M \\
  & S - M \\
  \hline
  & \therefore S - P
  \end{align*}
  $$

  - Third Figure

  $$
  \begin{align*}
  & M - P \\
  & M - S \\
  \hline
  & \therefore S - P
  \end{align*}
  $$

  - Fourth Figure

  $$
  \begin{align*}
  & P - M \\
  & M - S \\
  \hline
  & \therefore S - P
  \end{align*}
  $$

- **Any standard-form syllogisms is completely described when we specify its mood
  and its figure.**

- Summary

  There are $4 * 4 *4 = 64$ moods and 4 figures. So, syllogisms have $64 * 4 =
  256$ forms.

# The Formal Nature of Syllogistic Argument

Deductive Logic: aim to discriminate valid arguments from invalid ones;

Classic Logic: aim to discriminate valid syllogisms from invalid ones;

> It is
> reasonable to assume that the constituent propositions of a syllogism are all contingent—that
> is, that no one of those propositions is necessarily true, or necessarily false.

:star:**Under this assumption, the <u>validity and invalidity</u> of an syllogism depends
entirely on its <u>form</u>**.

Examples:

- AAA-1

  $$
  \begin{align*}
  & \text{All M is P} \\
  & \text{All S is M} \\
  \hline
  & \therefore \text{All S is P}
  \end{align*}
  $$

  Any syllogism of the form **AAA-1** is valid, regardless of its subject
  matter. We can substitute S, P and M to Athenians, humans and Greeks.

  $$
  \begin{align*}
  & \text{All Greeks are humans} \\
  & \text{All Athenians are Greeks} \\
  \hline
  & \therefore \text{All Athenians are humans}
  \end{align*}
  $$

---

- If **any syllogism is valid** in virtue of its form alone, any other syllogism
  having that **same form** will also be **valid**.

- If a syllogism is **invalid**, any other syllogism having that **same form** will also
  be **invalid**.

- Examples:

  $$
  \begin{align*}
  & \text{All liberals(P) are proponents of national health insurance(M)} \\
  & \text{Some members of the administration(S) are propositions of national health
  insurance(M)} \\
  \hline
  & \therefore \text{Some members of the administration are liberals}
  \end{align*}
  $$

  The best way to expose its fallacious character is to construct another
  argument that has exactly the same form but whose invalidity is immediately
  apparent.

  $$
  \begin{align*}
  & \text{All rabbits(P) are very fast runners(M)} \\
  & \text{Some horses(S) are very fast runners(M)} \\
  \hline
  & \therefore \text{Some horses are rabbits.}
  \end{align*}
  $$

#
