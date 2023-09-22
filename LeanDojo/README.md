# [LeanDojo: Theorem Proving with Retrieval-Augmented Language Models](https://leandojo.org/)

## Summary

This paper introduces a new open LLM theorem proving framework, LeanDojo,
which consists toolkits, datasets, and benchmarks.
The benchmarks are built on the Lean theorem prover by exploiting its
mathematical library, Mathlib, and the premises of the theorems.
This paper also presents a LLM theorem prover, ReProver,
based on the LeanDojo framework and code retrieval techniques from the dataset.

## Pros

1. The first open-source LLM theorem prover.
2. The dataset is build on program analysis on Lean proofs/programs,
   not just the code itself as static representation.
3. Efficient. ReProver takes 120 GPU hours to train and evaluate, v.s. more than 1k GPU hours for previous works.

## Cons

1. No comparison with other LLM-based theorem provers, only comparied with GPT4.
2. Code analysis part of the framework does not support function/theorem's full name.
3. Did not solve the issue of unique fully qualified names of functions/lemmas (similar to name mangling in C++).

## Q&A
