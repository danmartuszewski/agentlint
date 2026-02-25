# Research findings

Based on "Evaluating AGENTS.md: Are Repository-Level Context Files Helpful for Coding Agents?" by Gloaguen, Mündler, Müller, Raychev, and Vechev (2025).

Paper: https://arxiv.org/abs/2602.11988

## Summary

Context files (AGENTS.md, CLAUDE.md, .cursorrules, etc.) often reduce task success rates while increasing inference costs by 20%+. Generating elaborate context files is counterproductive.

## Quantitative results

| Metric | LLM-generated files | Developer-written files |
|--------|---------------------|------------------------|
| Performance change | -0.5% to -2% | +4% avg on AGENTbench |
| Cost increase | 20-23% | up to 19% |
| Additional steps per task | 2.45-3.92 | varies |
| Reasoning token increase | 14-22% | 2-20% |

## Key findings

### 1. Codebase overviews don't help

Agents don't discover relevant files faster when given directory listings or architecture descriptions. They just explore more broadly, consuming more resources. 100% of Sonnet-generated and 95-99% of other LLM-generated files included overviews, all wastefully.

### 2. Redundancy is the core problem

When existing documentation (.md files, docs/) was removed from repositories, LLM-generated context files improved performance by 2.7%. The files aren't inherently bad - they're bad because they duplicate existing docs.

### 3. Tool instructions work well

When context files mention specific tools (pytest, uv, grep, repo-specific CLIs), agents use those tools 1.6-2.5x more frequently. This is the most useful thing you can put in a context file.

### 4. LLM-generated files always underperform

Across all agents, all benchmarks, and all generating models (including the strongest available), LLM-generated files consistently hurt or showed negligible benefit. Stronger models don't generate better context files either.

### 5. Developer-written files provide modest value

Hand-written files outperformed LLM-generated ones across all four agents tested. Developers naturally write shorter, more targeted content focused on actual pain points.

### 6. More exploration != better results

Context files caused agents to execute more tests, read more files, and search more broadly. This increased activity did not translate to higher success rates - just higher costs.

## TL;DR

Include only what agents cannot discover on their own. Everything else is noise.
