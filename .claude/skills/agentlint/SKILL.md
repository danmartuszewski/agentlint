---
description: Lint and improve AI agent context files (CLAUDE.md, AGENTS.md, .cursorrules, COPILOT.md) based on empirical research. Use when reviewing, auditing, or creating context files.
---

# agentlint

Analyze and improve AI agent context files (CLAUDE.md, AGENTS.md, .cursorrules, COPILOT.md, etc.) based on empirical research findings.

## When to use

Invoke this skill with `/agentlint` when you want to:
- Review and improve an existing agent context file
- Create a new, effective context file from scratch
- Audit a file for common anti-patterns that hurt agent performance

## Instructions

When the user invokes this skill, follow these steps:

### Step 1: Locate the file

If the user provides a path, use it. Otherwise, search for common context files in the current project root:
- `CLAUDE.md`, `.claude/CLAUDE.md`
- `AGENTS.md`, `.github/AGENTS.md`
- `.cursorrules`, `.cursor/rules`
- `COPILOT.md`, `.github/copilot-instructions.md`
- `CODEBASE.md`, `CONTEXT.md`
- `rules.md`, `.windsurfrules`

If multiple files exist, ask the user which one to analyze. If none exist, ask if they want to create one.

### Step 2: Analyze against research-backed rules

Read the file and evaluate it against each rule below. For each violation found, note the specific lines/sections and explain why it's problematic.

---

## Rules

These rules are derived from "Evaluating AGENTS.md" (Gloaguen et al., 2025), an empirical study that tested context files across multiple coding agents and benchmarks.

### RULE 1: No codebase overviews
**Severity: HIGH**

Remove sections that describe the project's directory structure, list folders/components, or explain the overall architecture. Research shows agents do NOT find relevant files faster with these overviews - they just explore more broadly, increasing cost by 20%+ without improving success rates.

**Bad:**
```
## Project Structure
src/
  components/ - React UI components
  utils/ - Helper functions
  api/ - Backend API routes
```

**Good:** Omit entirely. Agents discover structure naturally through their tools.

**Exception:** If your project has a genuinely unusual or misleading structure that would confuse a human developer too (e.g., `src/` contains deployment scripts, not source code), a single clarifying sentence is acceptable.

### RULE 2: No redundancy with existing docs
**Severity: HIGH**

Remove anything that duplicates information already in README.md, CONTRIBUTING.md, package.json, pyproject.toml, Makefile, or docs/. When context files are redundant with existing documentation, they actively hurt performance. The study showed a 2.7% improvement when existing docs were removed and only the context file remained - proving redundancy is the core problem.

**Check for overlap with:**
- README.md (setup instructions, project description)
- CONTRIBUTING.md (contribution guidelines)
- Package manifests (dependencies, scripts)
- CI configs (test commands, build steps)
- Existing inline code comments

### RULE 3: Minimize requirements ruthlessly
**Severity: HIGH**

The paper's primary recommendation: "include only minimal requirements." Every additional instruction increases inference cost and encourages broader (not better) exploration. Each line in the file should pass this test:

> "Can the agent figure this out on its own by reading the code?"

If yes, remove it. Only keep what agents CANNOT discover through normal tool use.

### RULE 4: Specify non-obvious tooling
**Severity: MEDIUM**

Agents follow tool-specific instructions with high fidelity (1.6-2.5x usage increase). This is the highest-value content for context files. Include:
- Package manager preference (e.g., "use uv, not pip" or "use pnpm, not npm")
- Test runner and specific test commands
- Linter/formatter configuration
- Build tooling that isn't obvious from config files
- Repository-specific CLI tools

**Good:**
```
- Use `uv` for all Python package operations, not pip
- Run tests with `pytest -x --tb=short`
- Use `biome` for formatting, not prettier
```

### RULE 5: Document non-obvious constraints
**Severity: MEDIUM**

Include constraints that would surprise a competent developer reading the codebase for the first time:
- Unusual environment requirements
- Non-standard workflows (e.g., "always run migrations before tests")
- Hidden dependencies between modules
- Known gotchas or traps in the codebase
- Required environment variables not documented elsewhere

**Bad:** "Functions should have docstrings" (obvious convention)
**Good:** "The payment module uses a custom ORM - do not use SQLAlchemy directly in src/billing/"

### RULE 6: No exploration guidance
**Severity: MEDIUM**

Remove instructions that tell agents how to explore the codebase (e.g., "start by reading X", "familiarize yourself with Y"). Agents explore naturally and effectively on their own. Adding exploration guidance increases steps taken without improving outcomes.

**Bad:**
```
Before making changes:
1. Read the README
2. Understand the project structure
3. Review existing tests
```

**Good:** Omit entirely. Let the agent decide its own exploration strategy.

### RULE 7: No generic coding guidelines
**Severity: MEDIUM**

Remove universal best practices that any competent LLM already knows:
- "Write clean, readable code"
- "Add error handling"
- "Follow SOLID principles"
- "Use meaningful variable names"
- "Add types/docstrings to new code"

Only include coding conventions that are specific to YOUR project and deviate from standard practices.

### RULE 8: Prefer imperative over descriptive
**Severity: LOW**

Frame instructions as direct commands, not descriptions. Agents follow actionable directives more reliably than narrative explanations.

**Bad:** "The project uses a monorepo structure with packages managed by turborepo, where each package has its own test suite that can be run independently."

**Good:** "Run package tests with `turbo test --filter=<package>`"

### RULE 9: Keep it short
**Severity: LOW**

The most effective developer-written files in the study averaged 641 words across ~10 sections. Aim for:
- Under 800 words total
- Each section: 2-5 lines max
- Prefer bullet points over paragraphs
- No section should require scrolling to read

If your file exceeds 1000 words, it almost certainly contains content that should be removed per rules 1-7.

### RULE 10: Never auto-generate the whole file
**Severity: HIGH**

LLM-generated context files consistently decreased performance across all agents tested. Stronger models did NOT generate better files. Always write context files by hand, informed by actual experience working with agents in the repository. Use AI to help edit or trim, but not to generate from scratch.

---

### Step 3: Generate report

Present findings in this format:

```
## agentlint report

**File:** <path>
**Word count:** <count> (target: <800)
**Sections:** <count>

### Issues found

#### [SEVERITY] Rule N: <rule name>
Lines/section affected: <reference>
Problem: <brief explanation>
Suggestion: <specific fix>

...

### Score: X/10
<one-line summary>
```

### Step 4: Offer to fix

After presenting the report, ask: "Want me to apply these fixes?" If yes, edit the file applying all suggestions. When rewriting, follow these principles:
- Remove before adding
- When in doubt, cut it
- Every line must earn its place
- Test the "can the agent figure this out?" question for each remaining line
