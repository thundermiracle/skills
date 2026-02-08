# Decision Tree (New Project Setup)

## Purpose

Question templates to quickly confirm project type, target, and constraints.

## Flow

- First confirm whether it is a CLI tool or a non-CLI app.
- If not CLI, confirm whether it is a web app.
- If it is a web app, ask for "lightweight" vs "standard".
- In any branch, confirm target users, distribution format, and runtime environment as needed.
- If external information is not accessible, prioritize extra assumptions by asking the user.

## Question templates

- Is this a CLI tool or an application?
- If CLI: Do you want a single binary, installer, or package distribution?
- If app: Is it a web app?
- If web: Do you want a lightweight or standard setup?
- Any constraints on target users or runtime environment (OS/browser/device)?

## Summary output template

- Type: CLI / App / Web
- Language: Rust 2024 / TypeScript
- Stack: (based on branch)
- Latest-version policy: latest stable / latest LTS
