---
title: "How to identify a bottleneck?"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- How can I find the bottlenecks in a job at hand?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- First short overview of typical "smoking guns" with regard to performance issues.
- Find the bottlenecks in a job at hand.

::::::::::::::::::::::::::::::::::::::::::::::::

## How to identify a bottleneck?

<!-- EPISODE CONTENT HERE -->

:::::::::::::::::::::::::::::::::::::: keypoints
- General advice on the workflow 
- Performance reports may provide an automated summary with recommendations
- Performance metrics can be categorized by the underlying hardware, e.g. CPU, memory, I/O, accelerators.
- Bottlenecks can appear by metrics being saturated at the physical limits of the hardware or indirectly by other metrics being far from what the physical limits are.
- Interpreting bottlenecks is closely related to what the application is supposed to do.
- Relative measurements (baseline vs. change)
   - system is quiescent, fixed CPU freq + affinity, warmups, ...
   - Reproducibility -> link to git course?
- Scanning results for smoking guns
- Any best practices etc.
::::::::::::::::::::::::::::::::::::::::::::::::
