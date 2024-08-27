---
title:
  - Property-based Testing in Practice
author:
  - Yifeng He
theme:
  - Copenhagen
date:
  - Aug 24, 2024
---

# Disclaimer

This empirical study is based on survey/interview,
so our focus will be on discussing experiences and potential opportunities
rather than proposing new methods or experiments.


# Background: Property-based Testing (PBT)

*Properties* are executable specifications of programs.
For example, a binary tree is a *valid* BST if and only if each internal node has data grater than any in its left subtree and less than any in its right subtree.
Then, when inserting a new node to a valid BST, the BST must still be valid after the insertion.

```py
@given(x=integers(), t=trees())
def test_insert_maintains_bst(x, t):
  if is_valid_bst(t):
    assert is_valid_bst(insert(t, x))
```

---

* Generators: `integers()` and `trees()` generates valid inputs of the corresponding type.
* Pre-condition: `if is_valid_bst(t):`
  * it simply discards trees until `trees()` happens to generate a valid BST
  * the *majority* of the generated trees will be discarded, wasting precious generation time
* Shrinking: If the property ever fails during testing, a shrank/reduced failing value is presented to the user.

# Methodology

This study conducted 30 interviews with people in Jane Street (fin-tech company), where

* 26/30 are testers, who use PBT tools in their day-to-day work,
* 4/30 are maintainers, who play a role in building and maintaining PBT infrastructure.
* between 1 and 26 years of professional Software Engineering experience (median 7)
* between 1 and 20 years using PBT (median 6).

# Benefits of PBT

* **Improves Code Correctness**: used for critical code to detect hard-to-find bugs.
* **Increases Confidence**: helps developers feel more assured about code quality.
* **Finds Unexpected Bugs**: often catches edge-case errors missed by other tests.
* **Enhances Understanding**: forces developers to clarify their thinking and catch misunderstandings.
* **Serves as Documentation**: properties act as persistent, executable documentation.
* **Encourages Adoption**: PBT's usefulness encourages wider use and support within teams.

# Comparisons to Other Testing Approaches

## v.s. example-based unit testing

PBT is more concise but less transparent than example-based tests.

Unit test is 

* "often easier to understand"
* "explain what they're doing to a better degree, easier to review"
* "just print out a bunch of crap, too much stuff."

## v.s. fuzzing

PBT is used during development with strict time limits; fuzz testing is used separately for integration tests.

Fuzzing is

* forgotten and left running for a year and a half on a spare server
* running "out of band"
* outside of the normal CI and at time scales on the order of hours or days
* fuzzing tools are given *much* longer to run than PBT tools.

# Writing Specifications

* 16/30 said the process of writing specifications slowed their progress.
  * "not knowing what properties to test"
  * not compatible with "logical properties"
  * "Trying to formalize that judgment sometimes can be very difficult."
* mutable state gets in the way of writing properties
  * "makes it harder for me to think about what are the right laws"
* the choice of *when* to apply PBT
  * situations where BPT offered *high leverage*: more confidence for less work.
  * "going for the low hanging fruit"
  * "the properties are sort of obvious"

---

## Classical Properties (11/30)

* mathematical properties (e.g., the commutativity of addition), 
* properties drawn from CS theory (e.g., repeated sorting has no effect), 
* properties that naturally fall out of a data structure's invariants (e.g., the ordering condition of a
BST, the five properties of Red-Black Tree)

---

## Round-Trip Properties (11/30)

These properties check that a pair of functions are inverses of one another.
For example,

```python
@given(s=strings())
def test_roundtrip(s):
  assert s == decode(encode(s))
```

This situation is easy to notice and easy to test since the properties are incredibly succinct.
In general, for any bijective function $f: A \to B$ and any $a \in A$, $a == f^{-1}(f(a))$.

**NOTE**: round-trip is also common in fuzzing, particular when using libFuzzer fuzz targets.

---

## Catastrophic Failure Properties (7/30)

Rather than write logical specifications, 
some participants used PBT to try to provoke catastrophic failures such as assertion failures and uncaught exceptions.

* provide less confidence in code quality (there can still be logical errors, even if not crashes)
* help to rule out worst-case scenarios and they are easy to write.

**NOTE**: this is fuzzing.

---

## Differential Properties (17/30)

A "differential property" is comparing the system under test to a reference implementation.

```python
@given(xs=lists())
def test_differential(xs):
  assert my_sort(xs) == std_sort(xs)
```

* Differential testing described as a "natural place to use property based testing."
* However, PBT was challenging when a good reference implementation was not available.

# Generating Test Data

Many participants (17/30) talked about needing to generate values that satisfy some precondition.
However, there are two main challenges:

* Handwritten generators: a source of friction, time-consuming, especially for complex data types.
  * "the biggest annoyance with trying to use QuickCheck"
  * "tedious" (6/30), "high-effort" (7/30).
  * "high cost and low value"

* Derived generators
  * easy to use, fully automatic
  * only available for few languages (need to support `derive`)
  * only support simple types (for example, `int` can derive `[int]`)


## Desire for better tools

There is a need for improved tools to automate the creation of effective and well-distributed test data.

# Understanding Failure and Success

## Challenges in debugging

* the failing cases are often too large and complex (similar issue in fuzzing)
* shrinking is essential, but is not effective and informative enough for practical usage.

## Evaluating test effectiveness

Is the testing enough?

* Mutation Testing (7/30)
* Example Inspection (8/30): look at a handful of examples.
* Code Coverage (2/30) $\rightarrow$ coverage-guided PBT, but there will always be un-reached corner cases.
* Property Coverage (1/30)

There is a need for better tools to provide visibility and insights into the testing process.


# Observations

1. Property-based testing is being used successfully to build confidence in complex systems.
2. Properties are used as devices for communicating specifications.
3. Property-based tests need to be fast; they are expected to perform as well as other unit tests.
4. Property-based testing is used opportunistically
in high-leverage scenarios where properties are
readily available; developers rarely go out of
their way to write subtle specifications.
5. Developers see writing generators as a distraction, preferring to use derived generators.
6. Developers may not interrogate properties that do not find bugs,
even in cases where they acknowledge they should.

# Research Opportunities

1. Understand time constraints for property-based tests.
2. Improve support for differential and model-based testing.
3. Make more testing scenarios high leverage.
4. Streamline the process of writing well-distributed, precondition-satisfying generators.
5. Improve interfaces for shrinking.
6. Improve tools for evaluating testing effectiveness.
7. Connect evaluation of testing effectiveness and generator improvement