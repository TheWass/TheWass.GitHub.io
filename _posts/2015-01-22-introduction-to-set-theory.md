---
layout: post
title: Introduction to Set Theory
date: 2015-01-22 15:10:24
modified: 2015-01-26

categories: []
tags:
- Mathematics
- set data structure

---
This is intended to serve as a companion article to the formal models section of the Requirements Engineering series. This document gives a short introduction to set theory and, in particular, the notations. See also the Wikipedia page on [Set (mathematics)](http://en.wikipedia.org/wiki/Set_(mathematics)).  

* * *

# Notation

A set is a collection of distinct objects without regard to order. The mathematical definition of a set matches well with the set data structure in computer science. They are typically notated using curly brackets: `{4, 1, 3, 2}` is a set. Duplicate values are implicitly discarded and order does not matter: `{4, 2, 1, 3} == {1, 2, 3, 4} == {1, 4, 3, 4, 2, 1}` Ellipses indicate that the set continues in the obvious way: `{1, 2, 3, ..., 99, 100}` is the set of the first hundred positive integers. Sets can also be notated by an expression, and assigned to a variable: `F = {2n+1 : n is an integer; and 0 =< n =< 5}` In this notation, the colon (:) means "such that". This set can be interpreted as: F is the set of all numbers of the form 2n+1, such that n is a whole number in the range from 0 to 5 inclusive. Also notice that lower case letters denote some element, whereas upper case letters indicate a set. The empty set `{}` has a special notation: ∅. The empty set is still considered a set, just with zero elements.

# Membership

An element is said to be a member if it is contained within the set. This is notated by ∈. Consider: `a = 4` and `A = {4, 2, 3, 1}`. Therefore: `a ∈ A`. This is read as "a is in A." The 'fingers' of the symbol always point to the set: `A ∋ a` This is read as "A contains a."  
 Consider two sets: `A = {4, 1, 2, 3}` and `B = {2, 1, 3, 4, 5}`. If every member of set A is also a member of set B (as in this case), then A is said to be a subset of B. This is written as `A ⊆ B`. Alternatively, we can write `B ⊇ A`, read as B is a superset of A. Notice the bar under each. This indicates equivalency: if `B = {2, 1, 3, 4}` then `A ⊆ B` and `B ⊆ A`.

# Cardinality

The cardinality of set is the number of members contained within that set. For instance, the cardinality of `A = {4, 2, 3, 1}` is 4\. Cardinality is notated as `|A|`. In this case: `|A| = 4`.

# Special Sets

*   ℙ is the set of all primes: `{2, 3, 5, 7, 11, 13, 17, ...}`
*   ℕ is the set of all natural numbers: `{0, 1, 2, 3, 4, 5, 6, ...}` *Sometimes 0 is omitted.
*   ℤ is the set of all integers: `{..., −2, −1, 0, 1, 2, ...}`
*   ℚ is the set of all rational numbers: `{a/b : a, b ∈ Z, b ≠ 0}`
*   ℝ is the set of all real numbers, rational and irrational numbers
*   ℂ is the set of all complex numbers: `{a + bi : a, b ∈ ℝ}`

# Operations

These are some fundamental operations for constructing new sets from given sets.  
 For all these examples, lets let `A={0, 1, 2, 3}` and `B={2, 3, 4, 5}`

## Union

Notation: `A ∪ B = {0, 1, 2, 3, 4, 5}`  
 The union operation combines the two sets together in one set. The new set contains all elements from both parent sets. This can be likened to the OR function in computer science.

## Intersection

Notation: `A ∩ B = {2, 3}`  
 The intersection operation finds elements in both sets that are equivalent and creates a new set. The new set contains only elements found in both parent sets. This can be likened to the AND function in computer science.

## Compliment

Notation: `A' = {5, 6, 7, 8, ...}`  
 The compliment operation involves one set, and includes everything _not_ in that set. This usually must be identified with some domain, or some universal set.

## Subtraction

Notation: `A - B = {0, 1}`  
 The subtraction operation takes both sets and excludes all elements in one set, leaving unique elements in the other. A - B includes all elements in set A that do not exist in set B. Also notice: `B - A = {4, 5}`

## Cross Product

Notation: `A x B = {(0,2), (0,3), (0,4), (0,5), (1,2), (1,3), (1,4), (1,5), (2,2), (2,3), (2,4), (2,5), (3,2), (3,3), (3,4), (3,5)}`  
 The cross product combines both sets in a non-destructive manner. It associates every element in one set with every other element in the other set. This is also called the Cartesian product.

# Predicate Logic

I'll end with a little bit of predicate logic. Specifically, a mathematical method of 'iterating' over a set. There are two notations:  
 ∀ means for all or for each, and ∃ means there exists at least one. So, `∀x: P(x)` means that P(x) is true for all x. `∃x: P(x)` means there is at least one x such that P(x) is true.  
 `∀ n ∈ ℕ: 2n ≥ n` translates to: "For every n in the set of natural numbers, 2n is greater or equal to n."  
 `∃ n ∈ ℕ: n/2 ∈ ℕ` translates to: "There exists an n in the set of natural numbers, where n/2 is also in the set of natural numbers (n is even)."

* * *
