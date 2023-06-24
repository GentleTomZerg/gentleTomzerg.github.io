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
- [Venn Diagram Technique for Testing Syllogisms](#venn-diagram-technique-for-testing-syllogisms)
  - [Example: Venn Graph for AAA-1](#example-venn-graph-for-aaa-1)
  - [Diagram the universal premise first](#diagram-the-universal-premise-first)
  - [:star: Put x at border](#star-put-x-at-border)
  - [Technique Summary](#technique-summary)
  - [:star:Why Venn Diagram can tell the validity?](#starwhy-venn-diagram-can-tell-the-validity)
- [Syllogistic Rules and Syllogistic Fallacies](#syllogistic-rules-and-syllogistic-fallacies)
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

# Venn Diagram Technique for Testing Syllogisms

The syllogism can be drawn as three overlapping circles.
![Venn-Diagram-Syllogisms](/assets/logic/venn-diagram-syllogisms.jpg)

## Example: Venn Graph for AAA-1

$$
\begin{align*}
& \text{All M is P} \\
& \text{All S is M} \\
\hline
& \therefore \text{All S is P}
\end{align*}
$$

- All M is P and All S is M
<style>
    .image-container {
        display: flex;
    }

    .image-container img {
        margin-right: 10px;
    }
</style>

<div class="image-container">
    <img src="/assets/logic/all-m-is-p.jpg" alt="All M is P">
    <img src="/assets/logic/all-s-is-m.jpg" alt="All S is M">
</div>

- Both
  <div style="text-align: center;">
    <img src="/assets/logic/both-AA.jpg" alt="All S is M">
      <p>Combine the two premises</p>
  </div>

This syllogism is valid if and only if the **two premises imply or entail the
conclusion** -- that is, if together they say what is said by the conclusion.

## Diagram the universal premise first

$$
\begin{align*}
& \text{All artists are egotists} \\
& \text{Some artists are paupers} \\
\hline
& \therefore \text{Some paupers are egotists}
\end{align*}
$$

  <div style="text-align: center;">
    <img src="/assets/logic/universal-first.jpg">
  </div>

> Had we tried to diagram the particular premise first, before the region $S\bar PM$ was shaded
> out along with $\bar S \bar P M$ in diagramming the universal premise, we would not have known whether
> to insert an x in $SPM$ or in $S \bar P M$ or in both.

## :star: Put x at border

$$
\begin{align*}
& \text{All great scientists are college graduates} \\
& \text{Some professional athletes are college graduates} \\
\hline
& \therefore \text{Some professional athletes are great scientists}
\end{align*}
$$

  <div style="text-align: center;">
    <img src="/assets/logic/where-x.jpg">
  </div>

> we may still be puzzled about where to put the x needed in order to diagram the particular
> premise. That premise is “Some professional athletes are college graduates,” so an x must be
> inserted somewhere in the overlapping part of the two circles labeled “Professional athletes”and “College graduates.” That overlapping part, however, contains two regions, SPM and
> SPM. In which of these should we put an x? The premises do not tell us, and if we make an
> arbitrary decision to place it in one rather than the other, we would be inserting more
> information into the diagram than the premises warrant—which would spoil the diagram’s use
> as a test for validity.

  <div style="text-align: center;">
    <img src="/assets/logic/x-at-border.jpg">
  </div>

**Placing an x on the line that divides the overlapping region. This indicate
that there is something that belongs in one of them, but does not indicate which
one.**

## Technique Summary

1. Label the circles of a three-circle Venn diagram with the syllogism's three
   terms.
2. :star:Diagram both premises, diagramming the universal one first if there is one
   universal and one particular.
3. :star:In diagramming a particular proposition, to put an x on a line if the
   premises do not determine on which side of the line it should go.
4. Inspect the diagram to see whether the diagram of the premises contains a
   diagram of the conclusion.

## :star:Why Venn Diagram can tell the validity?

- It was shown there that **one legitimate test of the validity or invalidity of a syllogism is to establish the validity or
  invalidity of a different syllogism that has exactly the same form**.

- Example

  $$
  \begin{align*}
  & \text{All successful people are people who are keenly interested in their work} \\
  & \text{No people who are keenly interested in their work are people whose
  attention is easily distracted when they are working} \\
  \hline
  & \therefore \text{No People whose attention is easily distracted when they are
  working are successful}
  \end{align*}
  $$

  <div style="text-align: center;">
    <img src="/assets/logic/why-valid.jpg">
  </div>

  > How does this Venn Diagram tell us that the given syllogism is valid?

  :star: We can construct a syllogism of the same form that involves objects
  that are immediately present and directly available for our inspection.

  Here is the new syllogism:

  $$
  \begin{align*}
  & \text{All points with in the unshaded part of the circle labeled P are
  points within the unshaded part of the circle labeled M.} \\
  & \text{No points within the unshaded part of the circle labeled M are points
  within the unshaded part of the circle labeled S.} \\
  \hline
  & \therefore \text{No points within the unshaded part of the circle labeled S
  are points within the unshaded part of the circle label ed P}
  \end{align*}
  $$

# Syllogistic Rules and Syllogistic Fallacies
