# ccx — Claude Code Extension

Custom agents for PR review workflows. Produces comprehensive, human-readable PR review documents with code context, before/after analysis, and UML diagrams.

## Installation

Add as a marketplace in Claude Code:
```
/plugin marketplace add youngslab/ccx
```

Then install:
```
/plugin install ccx@ccx
```

## Agents

### `pr-analyst`
Analyzes a PR and produces a structured review document including:
- Summary of what changed and why
- Changed files table with change type and reason
- Code context per module/domain
- Before/after change analysis with concrete code examples
- Mermaid UML diagrams (sequence, class, or flowchart)
- Potential review focus areas and open questions

**Input:** PR number, branch name, or raw diff
**Output:** Markdown review document, then handed off to reviewers

**Usage:**
```
Task(subagent_type="ccx:pr-analyst", prompt="Analyze PR #42")
```
Or: `"use pr-analyst to analyze PR #42"`

### `arch-reviewer`
Validates technical accuracy of a review document against the actual codebase.
- Extracts and verifies every factual claim (VERIFIED / INCORRECT / UNVERIFIABLE)
- Checks architectural layer/module identification, design pattern names, dependency flows
- Identifies missing upstream/downstream context
- Verdict: **APPROVED** / **APPROVED WITH NOTES** / **REJECTED**

Read-only — Write and Edit are blocked.

### `doc-reviewer`
Validates clarity, structure, and completeness of a review document.
- Assesses logical flow, heading consistency, and navigation
- Evaluates clarity of terms, examples, and diagrams for the intended audience
- Checks completeness: what changed, why, and what to focus on
- Verdict: **APPROVED** / **APPROVED WITH SUGGESTIONS** / **REJECTED**

Read-only — Write and Edit are blocked.

## Workflow

```
pr-analyst → [arch-reviewer ∥ doc-reviewer] → final document
```

1. `pr-analyst` analyzes the PR and produces a review document
2. `arch-reviewer` and `doc-reviewer` run in parallel
3. If either returns **REJECTED**, the document is revised and re-reviewed
4. Once both approve, the final document is written to:

```
{project}/.ccx/review/{pr-slug}.md
```

## License

MIT
