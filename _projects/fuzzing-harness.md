---
layout: page
title: fuzzing harness generator for patch completeness
description: Automatically generating a set of fuzzing harnesses conditioned on the root cause of the reported vulnerability to surface sibling bugs in candidate patches for patch completeness testing.
img: assets/img/fuzzing_harness/harness_gen_tp.png
importance: 2
---

**Code**: [https://github.com/kureha-yamaguchi/vuln-patch](https://github.com/kureha-yamaguchi/vuln-patch)

### Motivation:

Project Zero's tracking of in-the-wild zero-days reveals 1 in 4 detected exploits could plausibly have been prevented by a more complete fix that closed the root cause, not just the reported PoC. Unfortunately, patch completeness testing is time-consuming, difficult to automate and varies in quality. This is because the process demands deep contextual understanding of the codebase to reason about the full space of variant inputs and code paths reaching the root cause of a vulnerability. Recent work on AI-generated patches at the DARPA AIxCC challenge finds 20--40\% of its 630 AI-generated patches to be semantically incorrect---they compile, block the known exploit, and pass existing tests, but do not properly close the root cause. Therefore, as frontier models accelerate both bug discovery and patch generation, the binding constraint shifts to patch validation. In light of this growing strain on human reviewers, it is important to create tools that can improve the speed and quality of patch completeness testing.

### Our solution so far

We present an automated pipeline for such testing. The system takes in \{PoC or patch diff\} + \{triggering tests\} + \{extracted reachable-functions set\} and generates \{a set of fuzzing harnesses conditioned on the root cause of the reported vulnerability\}. During evaluation, a fuzzing engine is run over the set of harnesses, with a single crash within the set indicating a positive case (overfitting patches) and no crash indicating a negative case (semantically correct patches). We use libraries like fuzz-introspector and javalang to efficiently extract required contextual understanding of the codebase most relevant to the reported bug, which the LLM can leverage. Unlike previous work by OSS-Fuzz-Gen, whose harnesses are optimised for naïve code coverage of a named target function, our harnesses are optimised for the coverage of the extracted root-cause neighborhood to surface sibling bugs.

We validate against a ground truth dataset of APR tool generated patches with known correct/ overfitting labels from the ASSERT-KTH dataset, given a bug from the Defects4J collection across five Java projects (Chart, Closure, Lang, Math, and Time). Using GPT 5.4 as the LLM within our pipeline, we achieve a classification performance of 80\% F1 score for crashing bugs in this Java validation set. Analysis of the run logs reveals several ``false positives'' to be genuine still-open sibling bugs (e.g. Lang 44's incomplete \texttt{createNumber} fix). Given the lack of existing datasets beyond ASSERT-KTH/Defects4J upon which we can perform further validation, we have constructed our own, harvesting $\sim5 0$ verified CVE sibling pairs from Project Zero’s public surfaces spanning a variety of codebase families (e.g. JS engines, browser renderers, standalone libs, kernel/ GPU drivers).

{% include figure.liquid path="assets/img/fuzzing_harness/harness_gen_tp.png" class="img-fluid rounded z-depth-1" %}

### Lessons learned

- For patch completeness testing, a valid harness should fulfill two criteria: compile and trigger the buggy code to crash. A plausible-looking harness that never drives the vulnerable path can produce false negatives.
- Maximise coverage of the root-cause neighbourhood by providing context on (i) the other harnesses within the generated set, and (ii) for each function touched by the patch, the set of functions reachable from it in the call graph.
- Semantic bugs require a different approach to crashing bugs. A crash-only oracle is structurally blind to semantic bugs that generate wrong outputs without throwing, so exceptions must be designed deliberately.

### Future Work

Under future work, we will further validate our classification performance on our hand-crafted dataset. We also seek to extend our pipeline to non-crashing semantic bugs via lifted assertions and metamorphic relations which currently remains an open problem. We further plan to contribute to the open-source OSS-CRS framework, making it easier for maintainers to perform variant analysis on bugs present in OSS-Fuzz style projects.
