---
layout: page
title: playbooks for AI red and blue teaming
description: Summarising the playbooks for AI red and blue teaming developed by my team at The Alan Turing Institute and collaborators from The MITRE Corporation.
img: assets/img/red_blue/overview.png
importance: 4
---

**Context**: My team at The Alan Turing Institute and The MITRE Corporation collaborated to develop frameworks for securing AI systems. I'll try to distill the main findings from the playbooks produced for Proceedings of SPIE as a result of this collaboration, Raney et al. 2024 and Tan et al. 2024.

**Paper**: [An AI Red Team Playbook](https://doi.org/10.1117/12.3021906), [An AI Blue Team Playbook](https://doi.org/10.1117/12.3021908)

### Motivation:

The playbooks were motivated by the gulf between state of the art and state of the practice in AI security. The state of the art in adversarial ML literature often consists of novel standalone adversarial and mitigation techniques. These methods deal with toy problems rather than real systems, and we lack a framework to make them realizable. On the other hand, the state of the practice in a fiercely competitive landscape is that we are deploying AI systems faster than they can be security tested and defended. With developers under pressure to deliver on functionality and performance as quickly as possible, security is too often left as an afterthought.([source](https://atlas.mitre.org/studies/AML.CS0004))

### Takeaway 1: We need iteration and collaboration between security and development teams before deployment

Our AI red and blue teaming playbooks aim to take systems away from current practice, where in the best-case scenario, we incorporate AI security measures after the initial design & development phase. By describing the AI red teaming and blue teaming process as part of a larger framework known as Build-Attack-Defend, we define an iterative and collaborative process between the AI system development and security teams, **tackling organizational and communications silos**. It presents a paradigm in which the builders of systems, referred to as the yellow team, learn from and work dynamically with the red and blue security teams, moving away from the current state where **“Yellow builds it. Red breaks it. Blue complains about it. Yellow Ignores it. Management hides it.”**

{% include figure.liquid path="assets/img/red_blue/overview.png" class="img-fluid rounded z-depth-1" width="80%"%}

### Takeaway 2: AI red teaming ≠ adversarial ML ≠ pentesting

If we consider a linear scale of security practices from creative/theoretical to prescribed/practical, adversarial ML and penetration testing can be thought to lie on either side of the scale, with AI red teaming lying somewhere in the middle. Penetration testing can be thought of as a box ticking exercise for identifying vulnerabilities using well established tactics, techniques, and procedures. On the other hand, adversarial ML is an academic field establishing cool ways to attack an AI model with math. It helps us (i) measure progress of machine learning algorithms towards human-level abilities and (ii) understand the edge cases of machine learning algorithms. But these are not the goals of AI red teaming, which are to **simulate how a real adversary that will attack the system**.

{% include figure.liquid path="assets/img/red_blue/venn.png" class="img-fluid rounded z-depth-1" width="80%"%}

For a given objective, it considers: what’s the easiest way to achieve this goal? We need to think about the human side as well as just the technical which is what this xkcd comic is getting at. Unlike adversarial ML, red teaming takes an exercise-based approach to work with the system designers and developers in identifying & mitigating vulnerabilities using both established and novel techniques.

{% include figure.liquid path="assets/img/red_blue/xkcd.png" class="img-fluid rounded z-depth-1" width="50%"%}

### Takeaway 3: Attacks and defenses need to be adapted to the target system and threat model

In a realistic setting, you can’t just plug and play an attack/ defence method proposed in literature. It is not one size fits all so need to be adapted to the specific target system and threat model. This bounds the red team/ blue team exercises with operational constraints, defined by the **adversary objective** (e.g. integrity violation), **knowledge** (e.g. white-box) and **capability** (e.g. bounded perturbations).

### Takeaway 4: We need to consider the whole ML system, not just the ML model, including dependencies and cyber-attack surfaces

A system is only as secure as its weakest link, and often these vulnerabilities lie in third party software or trusted computing infrastructure and processes. As such, we need to **consider the whole ML system, not just the ML model**.

{% include figure.liquid path="assets/img/red_blue/system.png" class="img-fluid rounded z-depth-1" width="30%" %}

### Takeaway 5: There are important differences between AI red teaming and AI blue teaming

What are the big differences between red and blue teaming?

- The red team only needs to attack once for success; blue team needs to defend every attempt for success
- The blue team needs to balance security requirements with the system's functional and performance requirements. Often times, these two requirements are at tug of war with each other.

### Takeaway 6: Rigorous and correct defence evaluation is imperative to validate any robustness claims

Quoting Nicolas Carlini, “**the purpose of a defense evaluation is to fail to show the defense is wrong**”. The blue team should rigorously check the validity of their robustness claims through various tests, e.g. perform ablation studies, increase the perturbation budget to verify it strictly increases attack success rate.
