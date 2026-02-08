---
name: agent-skill-creator
description: Create, update, or review Agent Skills that are portable across AI agents. Use when a user asks to author a new SKILL.md, convert existing instructions into the Agent Skills format, package scripts/references/assets, or ensure compatibility and validation against the Agent Skills specification.
---

# Agent Skill Creator

## Overview

Design Agent Skills that are spec-compliant, portable, and clear about when to activate. Prioritize concise triggers, progressive disclosure, and compatibility with both filesystem-based and tool-based agents.

## Workflow

### 0. Confirm target agent constraints

- Ask which agent(s) the skill must work with and whether they are filesystem-based or tool-based.
- Ask about constraints: shell access, network access, allowed tools, and whether scripts can execute.
- If constraints are unknown, assume conservative defaults: no shell access, no network, no external dependencies. Keep scripts optional and provide manual steps.
- Include the `compatibility` frontmatter field only when a real constraint exists.

### 1. Gather concrete usage examples

- Ask for 3-5 example user requests that should trigger the skill.
- Ask for a couple of out-of-scope examples to set boundaries.
- Convert these into the skill's `description` keywords and "Examples" section.

### 2. Define scope and resources

- Decide which content belongs in `SKILL.md` vs `references/` vs `assets/`.
- Use `references/` for long docs, schemas, or policies.
- Use `assets/` for templates or files that should be copied into outputs.
- Use `scripts/` only if the target agent can execute them; otherwise provide manual alternatives.
- Keep the skill folder minimal and avoid extra documentation.

### 3. Author `SKILL.md`

- Follow the Agent Skills spec (summary in `references/agentskills-spec.md`).
- Frontmatter must include `name` and `description` with correct constraints.
- Keep the body imperative and task-focused: steps, edge cases, examples, and outputs.
- Keep `SKILL.md` under 500 lines and move details into `references/`.
- When referencing files, use relative paths from the skill root and keep references one level deep.

### 4. Add resources

- Add reference files with focused scope and clear titles.
- If scripts are included, document dependencies and a fallback path.
- Link to any references from `SKILL.md` using relative paths.

### 5. Validate

- If available, run `skills-ref validate <skill-dir>`.
- Otherwise, manually verify:
  - `name` matches the folder and follows naming rules.
  - `description` is specific and under 1024 chars.
  - Optional fields are used only when needed.
  - File references are relative and one level deep.

## Output requirements

- Provide a concise file tree of the created skill.
- Include the final `SKILL.md` content.
- Call out any assumptions about agent capabilities.
- Provide 2-3 example prompts that should trigger the skill.

## Examples

Example 1:
Input: "Create a skill that helps agents generate weekly status updates for engineering teams."
Output: A spec-compliant skill folder with `SKILL.md` instructions, a `references/` guide for update format, and clear triggering keywords.

Example 2:
Input: "Convert our internal onboarding doc into a reusable skill for any agent."
Output: A skill that summarizes the onboarding workflow in `SKILL.md` with detailed policies in `references/` and optional templates in `assets/`.

## Response checklist

- Confirmed target agent constraints
- Defined scope and examples
- `SKILL.md` frontmatter valid and descriptive
- Progressive disclosure applied (references/assets/scripts)
- Validation completed or manual checks noted
