# ccx — Claude Code Extension

Personal Claude Code plugin: custom agents for development workflows.

## Agents

### `pr-explainer`
Analyzes a PR and produces a structured review document including:
- Code context and module overview
- Change analysis with before/after rationale
- Concrete code examples
- Mermaid UML diagrams

**Usage:**
```
Task(subagent_type="ccx:pr-explainer", prompt="Explain PR #42")
```
Or just ask Claude: "use pr-explainer to explain PR #42"

### `pr-review-validator`
Validates the output of `pr-explainer` and writes the approved document to:
```
{project}/.ccx/review/{pr-name}.md
```

**Typical flow:**
1. `pr-explainer` analyzes the PR and produces a review document
2. `pr-review-validator` checks completeness (12-point checklist)
3. If approved, the document is saved to `.ccx/review/`

## Installation

Add as a marketplace in Claude Code:
```
/plugin add-marketplace https://github.com/jaeyoungs/ccx
```

Then install:
```
/plugin install ccx@ccx
```
