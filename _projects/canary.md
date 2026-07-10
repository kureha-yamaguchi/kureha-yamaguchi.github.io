---
layout: page
title: project canary
description: Collecting TTPs and behavioural fingerprints from a bunch of AI agents interfacing with vulnerable web apps, and mapping them onto the MITRE ATT&CK matrix
img: assets/img/canary/event.png
importance: 3
---

**Context**: A project write-up from the BlueDot def/acc hackathon in Nov 2025. I'll be writing up a brief description of everything we achieved in 2 days, what we learnt and how one could build on our work. Currently still WIP, but will be updating this page soon!

**Code**: [https://github.com/kureha-yamaguchi/canary](https://github.com/kureha-yamaguchi/canary)

### Motivation:

Cybersecurity has a massive data problem. There is a lack of threat intelligence sharing and cybersecurity datasets, leaving cybersecurity experts in the dark and leaving organisations vulnerable if the capabilities of hackers were to rapidly increase unknowingly. This is a bottleneck in more advanced defensive techniques such as early warning system creation.

### Our solution:

1. Red herrings: lure adversaries towards our traps, away from critical assets.
2. Threat intelligence at scale: by deploying our internal red team agents at scale, we are able to uncover information on the adversary attack vector and can map TTPS onto the industry standard MITRE ATT&CK matrix.
3. Behavioural fingerprinting: By analysing behavioural patterns of our benign agent vs our malicious agent, we hope to forecast malicious/benign intent from breadcrumbs left by agents in the wild, providing early warning signals/ canaries.
