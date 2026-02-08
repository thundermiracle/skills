# Agent Skills Specification Summary

Source: https://agentskills.io/specification

Use this as a quick reference. When precision matters, consult the full spec.

## Required structure

- Each skill is a folder named after the skill `name`.
- `SKILL.md` is required at the root of the skill folder.
- Optional folders: `scripts/`, `references/`, `assets/`.

## Frontmatter fields

Required:
- `name`: 1-64 characters, lowercase letters/digits/hyphens, no leading or trailing hyphen, no consecutive hyphens, and must match the folder name.
- `description`: 1-1024 characters that explain what the skill does and when to use it.

Optional:
- `license`: License name or reference to a bundled license file.
- `compatibility`: short string (1-500 chars) for hard requirements like OS, tool access, or model limitations.
- `metadata`: freeform map for non-core info (author, version, tags, etc.).
- `allowed-tools`: space-delimited list of tool names (experimental; only if you need an allowlist).

## Body guidance

- Keep instructions concise and imperative.
- Prefer progressive disclosure: put long or conditional details in `references/`.
- Keep `SKILL.md` under ~500 lines when possible.

## File references

- Use relative paths from the skill root (for example, `references/policy.md`).
- Avoid deep nesting; keep references one level deep.

## Validation

- If the `skills-ref` tool is available, run `skills-ref validate <skill-dir>`.
- Otherwise, manually check name/description constraints and folder structure.
