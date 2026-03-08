# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

CCX is a personal Claude Code plugin providing specialized agents for PR review workflows. It is a pure markdown/configuration project — no build, compile, or test steps exist.

## Architecture

Three agents defined in `agents/` work in a pipeline:

1. **`pr-analyst`** (Opus) — Analyzes a PR and produces a structured markdown review document with code context, before/after examples, and Mermaid UML diagrams.
2. **`arch-reviewer`** (Opus) — Validates technical accuracy of the document against the actual codebase. Read-only.
3. **`doc-reviewer`** (Sonnet) — Validates clarity, structure, and completeness of the document. Read-only.

### Workflow

```
pr-analyst → [arch-reviewer ∥ doc-reviewer] → final document
```

Both reviewers run in parallel. If either returns `REJECTED`, the document is revised and re-reviewed until both return `APPROVED` or `APPROVED WITH NOTES`.

Final output is written to: `{project}/.ccx/review/{pr-slug}.md`

## Plugin Configuration

`.claude-plugin/plugin.json` defines the plugin metadata. Agents are discovered from `agents/*.md`.

## Agent Design Constraints

- `arch-reviewer` and `doc-reviewer` have Write/Edit blocked — they are read-only validators.
- `arch-reviewer` must verify every factual claim with Grep/Glob/Read/Bash before rendering a verdict.
- `doc-reviewer` rejects only for structural or clarity issues, never for technical content.
- `pr-analyst` must produce concrete before/after code examples and at least one Mermaid diagram.
- No speculation about intent — derive everything from commit messages and actual code.
