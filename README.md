<p align="center">
  <img src="harness_banner.png" alt="Harness Banner" width="600">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Version-1.2.0-brightgreen.svg" alt="Version">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-Apache_2.0-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Vercel_Skills-npx_skills_add-blue.svg" alt="Vercel Skills">
  <a href="https://github.com/iqbalexperience/harness/stargazers"><img src="https://img.shields.io/github/stars/iqbalexperience/harness?style=social" alt="GitHub Stars"></a>
</p>

# Harness — Agent Team Factory for AI

**English** | [한국어](README_KO.md) | [日本語](README_JA.md)

> Say **"build a harness for this project"** and Harness turns your domain into a full agent team with skills — automatically.

---

## What Is Harness?

Harness is a **meta-skill** that reads your project, picks the right team architecture, and generates:

- **Agent definitions** (specialist roles like analyst, builder, QA)
- **Skills** (procedural instructions each agent follows)
- **Orchestration** (how agents communicate and pass data)

It supports 6 team patterns: **Pipeline, Fan-out/Fan-in, Expert Pool, Producer-Reviewer, Supervisor, Hierarchical Delegation**.

---

## Installation

### Option 1 — Vercel Skills (Recommended)

Install into your current project:

```shell
npx skills add iqbalexperience/harness
```

Install globally (available in all projects):

```shell
npx skills add -g iqbalexperience/harness
```

Install from a local copy of this repo:

```shell
npx skills add .
```

> After install, the skill is available at `.agents/skills/harness/` or `~/.agents/skills/harness/` (global).

---

### Option 2 — Claude Code (Manual)

Copy the skill folder into your Claude skills directory:

```shell
# Per-project
cp -r skills/harness .claude/skills/harness

# Global (available in all projects)
cp -r skills/harness ~/.claude/skills/harness
```

Or use the Claude plugin marketplace:

```shell
/plugin marketplace add iqbalexperience/harness
/plugin install harness@harness-marketplace
```

---

## How to Use It

Once installed, open your AI agent (Claude Code, Cursor, Windsurf, Copilot, Antigravity, etc.) and type:

```
Build a harness for this project
```

Harness will:
1. Audit your codebase
2. Ask you to confirm a team plan
3. Generate agent files in `.claude/agents/` or `.agents/agents/`
4. Generate skill files in `.claude/skills/` or `.agents/skills/`

### Example Prompts

| What you want | Prompt to use |
|---|---|
| Research assistant | `Build a harness for deep research across web, academic, and community sources` |
| Website dev team | `Build a harness for full-stack web development with design, frontend, backend, and QA` |
| Code review | `Build a harness for parallel code review — architecture, security, performance, style` |
| Content creation | `Build a harness for YouTube content — research, scripting, SEO, thumbnail planning` |
| Documentation | `Build a harness that generates API docs from this codebase` |

---

## What Gets Generated

```
your-project/
├── .claude/
│   ├── agents/
│   │   ├── analyst.md      <- specialist agent roles
│   │   ├── builder.md
│   │   └── qa.md
│   └── skills/
│       ├── analyze/
│       │   └── SKILL.md    <- procedural instructions
│       └── build/
│           ├── SKILL.md
│           └── references/
```

---

## Requirements

- Claude Code with Agent Teams enabled: `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`

---

## Research Results

Harness was tested across 15 software engineering tasks (A/B comparison):

| Metric | Without Harness | With Harness |
|---|:-:|:-:|
| Average Quality Score | 49.5 | **79.3** |
| Win Rate | — | **100%** (15/15) |
| Output Variance | — | **−32%** |

Full paper: *Hwang, M. (2026). Harness: Structured Pre-Configuration for Enhancing LLM Code Agent Output Quality.*

---

## License

Apache 2.0
