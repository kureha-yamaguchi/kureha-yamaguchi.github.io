---
layout: page
title: fuzzing harness generator for patch completeness
description: Automatically generating a set of fuzzing harnesses conditioned on the root cause of a vulnerability to surface sibling bugs in overfitting patches.
img: assets/img/fuzzing_harness/harness_gen_tp.png
importance: 2
---

Accepted at the Applied ML for Cyber Security Conference 2026; project still in progress

**Code**: [https://github.com/kureha-yamaguchi/vuln-patch](https://github.com/kureha-yamaguchi/vuln-patch)


### Motivation:

Project Zero's tracking of in-the-wild zero-days reveals 1 in 4 detected exploits could plausibly have been prevented by a more complete fix that closed the root cause, not just the reported PoV. Unfortunately, patch completeness testing is time-consuming, difficult to automate and varies in quality. This is because the process demands deep contextual understanding of the codebase to reason about the full space of variant inputs and code paths reaching the root cause of a vulnerability. Recent work on AI-generated patches at the DARPA AIxCC challenge finds 20-40\% of its 630 AI-generated patches to be semantically incorrect; they compile, block the known exploit, and pass existing tests, but do not properly close the root cause. Therefore, as frontier models accelerate both bug discovery and patch generation, the bottleneck shifts to patch validation. In light of this growing strain on human reviewers, it is important to create tools that can improve the speed and quality of patch completeness testing.

### Developing pipeline on ASSERT-KTH/Defects4J ground truths:

We present an automated pipeline for such testing. The system takes in \{PoV\} + \{extracted call graph\} and generates \{a set of fuzzing harnesses conditioned on the root cause of the reported vulnerability\}. During evaluation, a fuzzing engine is run over the set of harnesses, with a single crash within the set indicating a positive case (overfitting patches) and no crash indicating a negative case (semantically correct patches). We use libraries like fuzz-introspector and javalang to efficiently extract required contextual understanding of the codebase most relevant to the reported bug, which the LLM can leverage. Unlike previous work by OSS-Fuzz-Gen, whose harnesses are optimised for naïve code coverage of a named target function, our harnesses are optimised for the coverage of the root-cause region to surface sibling bugs.

{% include figure.liquid path="assets/img/fuzzing_harness/harness_schem.png" class="img-fluid rounded z-depth-1" %}

(where $\mathbb{R}$ be the root-cause region, $\mathbb{F}(H_N)$ is the fuzzer coverage from the naïve unconditioned harness set and $\mathbb{F}(H_R)$ is the fuzzer coverage from the root-cause conditioned harness set.)

We validate against a ground truth dataset of APR tool generated patches with known correct/ overfitting labels from the ASSERT-KTH dataset, given a bug from the Defects4J collection across five Java projects (Chart, Closure, Lang, Math, and Time). Using GPT 5.4 as the LLM within our pipeline, we achieve a classification performance of 80% F1 score for crashing bugs in this Java validation set. Analysis of the run logs reveals several "false positives" to be genuine still-open sibling bugs (e.g. Lang 44's incomplete createNumber fix). Given the lack of existing datasets beyond ASSERT-KTH/Defects4J upon which we can perform further validation, we have constructed our own, harvesting ~50 verified CVE sibling pairs from Project Zero’s public surfaces spanning a variety of codebase families (e.g. JS engines, browser renderers, standalone libs, kernel/ GPU drivers).

{% include figure.liquid path="assets/img/fuzzing_harness/harness_gen_tp.png" class="img-fluid rounded z-depth-1" %}


## Contribution to OSS-CRS

We have packaged the C/C++ side of the pipeline as a demo bug-finding CRS for [OSS-CRS](https://github.com/ossf/oss-crs), the OpenSSF framework for running Cyber Reasoning Systems against OSS-Fuzz projects. The wrapper lives in [`oss-crs/`](https://github.com/kureha-yamaguchi/vuln-patch/tree/main/oss-crs) of our repository and is registered as `crs-vuln-patch-variant` (type: bug-finding; languages: C, C++; sanitizer: AddressSanitizer; modes: full and delta).

The tool takes a crashing input (PoV) plus the pre-patch source tree and produces a small set of fuzzing harnesses conditioned on the bug's root cause. Run against a candidate patch, the set says whether the patch is complete: if any harness still crashes the patched build, the fix missed a sibling of the original bug and the patch is deemed incomplete. 

The tool is meant for OSS-Fuzz projects, so we iterate on the ARVO corpus of reproducible OSS-Fuzz bugs, which ships Docker images for both the vulnerable and the "patched" state of each bug. We split the corpus into dev (20 pairs), validation (20 pairs) and test (30 pairs); dev and validation are used for iteration, and test is run once at the end. Classification is scored as F1 over positives (patched builds known to be incomplete) and negatives (builds labelled complete) at a fuzzing budget of 900 s per harness. Results are 88% F1 on the dev split and 84% on the validation split.

We have also exercised the full OSS-CRS flow (`prepare`, `build-target`, `run`, `artifacts`) end to end on muparser and libxml2, with the vulnerable source pinned to historical ARVO cases; in both cases the generated harnesses reach the root cause through a different path than the original PoV, and the submitted report bundles reproduce the sanitizer crash when rebuilt by hand in the target image. 


### Future Work:

We are seeking to extend our pipeline to non-crashing semantic bugs via lifted assertions and metamorphic relations which currently remains an open problem. We are also developing some cool granular fuzzing coverage metrics! Watch this space.