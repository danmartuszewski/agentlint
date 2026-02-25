<p align="center">
  <img src="icon.svg" height="128">
</p>

<h1 align="center">agentlint</h1>

<p align="center">
  A <a href="https://docs.anthropic.com/en/docs/claude-code">Claude Code</a> skill that lints AI agent context files.
</p>

Your `CLAUDE.md`, `AGENTS.md`, or `.cursorrules` might be making your agent worse. Research shows that most context files reduce task success rates while increasing costs by 20%+.

agentlint checks your file against 10 rules derived from that research and tells you what to fix.

## The research

Based on ["Evaluating AGENTS.md"](https://arxiv.org/abs/2602.11988) (Gloaguen et al.), which tested context files across multiple coding agents and benchmarks. Verbose context files hurt performance. Only minimal, non-obvious constraints help.

See [FINDINGS.md](FINDINGS.md) for the full breakdown.

## Install

### Global (available in all projects)

```bash
mkdir -p ~/.claude/skills/agentlint
curl -o ~/.claude/skills/agentlint/SKILL.md \
  https://raw.githubusercontent.com/danmartuszewski/agentlint/main/.claude/skills/agentlint/SKILL.md
```

### Per project

```bash
# From your project root
mkdir -p .claude/skills/agentlint
curl -o .claude/skills/agentlint/SKILL.md \
  https://raw.githubusercontent.com/danmartuszewski/agentlint/main/.claude/skills/agentlint/SKILL.md
```

## Usage

In Claude Code, run:

```
/agentlint
```

It will:
1. Find your context file (CLAUDE.md, AGENTS.md, .cursorrules, etc.)
2. Check it against the 10 rules below
3. Report issues with severity and specific fixes
4. Offer to rewrite the file

## The 10 rules

| # | Rule | Severity | Why |
|---|------|----------|-----|
| 1 | No codebase overviews | HIGH | Agents don't find files faster with them |
| 2 | No redundancy with existing docs | HIGH | Duplicated info actively hurts performance |
| 3 | Minimize requirements | HIGH | Every extra line increases cost without improving results |
| 4 | Specify non-obvious tooling | MEDIUM | Agents follow tool instructions with 1.6-2.5x higher fidelity |
| 5 | Document non-obvious constraints | MEDIUM | Only include what agents can't discover alone |
| 6 | No exploration guidance | MEDIUM | Agents explore fine on their own |
| 7 | No generic coding guidelines | MEDIUM | LLMs already know best practices |
| 8 | Prefer imperative over descriptive | LOW | Direct commands over narrative explanations |
| 9 | Keep it short | LOW | Target under 800 words |
| 10 | Never auto-generate the whole file | HIGH | LLM-generated files consistently decrease performance |

## TL;DR

> Include only what agents cannot discover on their own. Everything else is noise.

## License

MIT
