# [PanGu-Coder2: Boosting Large Language Models for Code with Ranking Feedback](https://arxiv.org/abs/2307.14936)

## Summary

This paper proposes a new Code-LLM training framework for code generation tasks.
The framework is a compilation of two ideas:

1. Instruction Tuning, and
2. Ranking Feedback.

The first part introduces the pipeline of the framework, namely,
sampling from instruction tuning results,
ranking responses of the model and the human expert,
and fine-tuning with ranking as a reward model.
The second part compares the performance of the framework with two state-of-the-art (SOTA) models,
analyzing the results on some specific problems in the dataset.

## Pros

1. Interesting idea of using software testing results to train a code generation model.
2. The framework is simple and easy to implement.

## Cons

1. The ranking process is missing details.
   For example, is the percentage of _passing partial tests_
   the same for all cases? What is the quality of the passed tests (coverage or other metrics)?
2. The paper does not mention the level of the human expert.
3. The paper is missing Ablation Study on ranking.
   What if one of unittest or heuristic is removed?
4. The Case Study part concludes that
   "currently code LLMs still fall short of human-like proficiency in tackling
   **complex programming requirements**," which was the focus of AlphaCode.
   AlphaCode is not compared with the models in this part.

## Q&A

1. Where do the unittests come from?
   - From the HumanEval dataset.
2. How do they decide which one to choose and which one to reject?
   - Not covered in detail, but I guess the response with the higher score is chosen.
3. RL details?
   - The reward function is unittests + heuristic.
   - The agent/model's response to reward and state is unknown.
