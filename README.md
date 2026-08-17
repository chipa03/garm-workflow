# GARM Workflow

![Version](https://img.shields.io/badge/version-1.1.0-blue) ![Works with](https://img.shields.io/badge/Works_with-grey) [![Claude Code](https://img.shields.io/badge/Claude_Code-E5582B)](https://docs.anthropic.com/en/docs/claude-code) [![Codex CLI](https://img.shields.io/badge/Codex_CLI-10A37F)](https://developers.openai.com/codex/cli/) [![OpenCode](https://img.shields.io/badge/OpenCode-1a3a5c)](https://github.com/sst/opencode)

## What is GARM?

A structured development workflow for AI coding agents that brings **memory**, **consistency**, and **reduced hallucination** (only humans should) to AI-assisted development. GARM helps you enter flow state and eat features like buttered noodles.
It covers the historical 4-phases development cycle: **P**lan, **I**mplement, **R**eview, **T**est.  
**Note:** The flow is even simpler **Plan → Implement → Release** — review and test live *inside* Implement as a testing gate and an automatic Codex review loop, every feature passes through all 4 phases with fewer commands.

GARM was initially designed for Claude Code using the [Agent Skills](https://agentskills.io/home) open standard (`SKILL.md`). Also compatible with OpenCode, Codex CLI, Mistral Vibe and more.

## Why GARM?

There are tons of AI coding workflows out there like [Superpowers](https://github.com/obra/superpowers), [BMAD](https://github.com/bmad-code-org/BMAD-METHOD), [Gastown](https://github.com/steveyegge/gastown) and countless others. They might be powerful, but overwhelming for many of us dumb asses.

Even the "simple" ones come with:

- 47 different commands & skills to memorize
- Sub-agents swarm for God-knows-what
- Multi-chapters courses (sometimes paid lol)

**GARM is different.** It's deliberately minimal:

| That's it           | Just these                                             |
| ------------------- | ------------------------------------------------------ |
| `/garm-1-plan`      | Think before you code         |
| `/garm-2-implement` | Implement, test & review |
| `/garm-3-release`   | Version, changelogs, docs, commit, tag, merge, push    |

3 core skills. 1 architecture file. 0 PhD required.

The onboarding is: copy the folder, run init, start coding. If you can count to 3, you can run GARM.

It was kept stupid simple because **the goal is to ship features, not to master a workflow**. The workflow should disappear into the background, not become a project of its own.

## Getting Started

1. Copy the `skills/` folder contents to your repo's `.claude/skills/` or whatever
2. Run `/garm-init [YourProjectName]`
3. Follow the interactive prompts
4. Review and approve the generated MIMIR.md

Et voila ! Start using the skills like `/garm-1-plan auth for this webapp`, `/garm-2-implement @auth-plan.md`, etc.

## The Heart of GARM: MIMIR.md

The `MIMIR.md` file is the **central nervous system** of this workflow. It serves as the AI agent's **long-term memory** of your codebase.

### Why MIMIR.md Matters

**1. Persistent Context Across Sessions**

AI agents have no memory between sessions. Every new conversation starts from zero. MIMIR.md solves this by providing a comprehensive, always-up-to-date snapshot of your architecture that the agent reads at the start of each task. Unlike tool-specific files like `CLAUDE.md` or `AGENTS.md`, MIMIR.md is purely about architecture. It's tool-agnostic, so it works with any agent. You can still reference it from your `CLAUDE.md` to include it in all conversations.

**2. Token Savings & Reduced Hallucination**

Without MIMIR.md, your agent must glob, grep, and read multiple files to piece together the architecture from scratch for every single session. This wastes tokens and leads to guessing: _"There's probably a utils folder..."_, _"This project likely uses Redux..."_. MIMIR.md eliminates both problems. The agent gets the full picture in one read for minimal exploration & hallucination.

**3. Balanced Detail vs Token Usage**

MIMIR.md is designed to be:

- **Detailed enough** to provide meaningful context, **concise enough** to not waste tokens
- **Structured** for quick navigation
- **Updated** after every architectural change

It's not a dump of your entire codebase, rather a curated architectural guide.

## The Init Process

The `garm-init` skill is a **script written in human language** that programmatically bootstraps the GARM workflow in any repository.

### What Init Does

1. **Creates the docs structure** - Folders for plans, changelogs, reviews, tests, memos
2. **Explores your codebase** - Identifies languages, frameworks, patterns, conventions
3. **Classifies your project** - Web frontend? CLI tool? Embedded firmware? Library?
4. **Generates MIMIR.md** - Tailored to your specific project type
5. **Customizes the skills** - Replaces placeholders with your project's specifics

### The Placeholder System

The generic GARM skills contain placeholders like:

- `[PROJECT_NAME]` - Your project's name
- `[VERSION_FILE]` - Where your version is stored (package.json, Cargo.toml, etc.)
- `[ADAPT_TO_PROJECT: ...]` - Sections to customize

Init walks you through questions and replaces these placeholders based on your answers, creating a workflow tailored to your project.

## More Skills

### `/codex-implement`

Implementation delegated to Codex CLI in a workspace-write sandbox.

### `/codex-plan-review` & `/codex-code-review`

Iterative review loops powered by Codex CLI.

### `/codex-ask`

A grounded second opinion on **anything**. garm-research uses it to brainstorm findings before presenting them.

### `/garm-review` & `/garm-test`

The Review and Test phases, reborn as on-demand support skills.

### `/garm-research`

Exploratory investigation with defined compute level. For feasibility studies and technology evaluation. Produces documented findings, not production code.

### `/garm-compact`

Run this skill to compact MIMIR.md size while preserving relevance, accuracy, and coverage through summarization and restructuring. Token calculator script included.

## Temporarily Withheld

To keep the initial surface small while the team onboards, three pieces ship later rather than now:

- **`/garm-hotfix`** — emergency bypass workflow. Until it returns, treat urgent fixes as normal features: `/garm-1-plan` → `/garm-2-implement` → `/garm-3-release`.
- **`/garm-upgrade`** — merges a newer GARM release into a project's customized skills. Needed only when upgrading an existing install.
- **`AskUserQuestion/`** — an emulation shim for agents without a native `AskUserQuestion` tool. Claude Code has one natively, so nothing is lost there; agents that lack it (e.g. Mistral Vibe) are unsupported for now.

All three are recoverable from git history. See `MIGRATION.md` before reintroducing `/garm-upgrade`.

## Multi-Agent: Using Different LLMs at Different Steps

Just like you wouldn't smell your own fart, an LLM is unlikely to catch bugs in its own implementation. Some people conduct adversarial review with a different session but still the same model, which is..._meh_. The best approach is to introduce a different model in the same reasoning ballpark as the first one, that will most likely catch what the other missed.

This multi-agent approach is **the default workflow**.  
Considering Claude as your main and Codex as the copilot:  
Fable writes the plan, 5.6 Sol reviews it, Luna implements, back to Fable who reviews and fixes the diff, runs the testing gate, then a new Sol thread reviews again the code. All in one claude code session. Writer and reviewer are never the same thread.  

```mermaid
flowchart TD
    A["<b>/garm-1-plan</b><br/>Discovery and plan draft"] --> B{"ChatGPT Sol<br/>plan review"}
    B -->|"REQUEST_CHANGES"| Bf["Fable fixes the plan"]
    Bf -->|"re-review"| B
    B -->|"APPROVED"| D["<b>/garm-2-implement</b><br/>Branch + split<br/>to-dos into batches"]
    Bf ~~~ D
    D --> E["ChatGPT Luna<br/>implements a batch"]
    E --> F["Fable reviews the delta,<br/>fixes directly"]
    F -->|"next batch"| E
    F -->|"all batches done"| G["Fable final pass<br/>+ testing gate"]
    G --> H{"ChatGPT Sol<br/>full code review"}
    H -->|"REQUEST_CHANGES"| Hf["Fable fixes + re-tests"]
    Hf -->|"re-review"| H
    H -->|"APPROVED"| K["<b>/garm-3-release</b><br/>Version bump · changelog<br/>docs/MIMIR update · docs sync<br/>commit · tag · ff-merge · push"]
    Hf ~~~ K
```

As of mid july 2026, this Fable + GPT5.6 harness combo is absolute peak.

## MCP Servers: Less Is More

Last piece of advise before your new coding quest: Every MCP server you add is extra context, extra latency, and extra confusion. Keep it minimal. The one use case where MCP genuinely shines is **up-to-date documentation**, so your agent stops hallucinating deprecated APIs/whatever. Two servers cover it: [Context7](https://github.com/upstash/context7) for current library & framework docs, and [Exa](https://github.com/exa-labs/exa-mcp-server) for web search when the answer isn't in any doc. No bloat beyond that.

## Contributing

PRs & forks are welcome

Happy hunting ! 🐺
