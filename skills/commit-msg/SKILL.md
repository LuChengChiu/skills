---
name: commit-msg
description: Generates a Conventional Commits message from the current repository's staged (or unstaged) changes. Only use when the user explicitly invokes `/commit-msg`; do not auto-trigger from natural-language requests like "write a commit message" — let those go to default behavior.
metadata:
  author: luchengchiu
  version: "1.0.0"
---

# Commit Message

Generate a Conventional Commits message from the repository's current changes. Display it for the user to review — never execute `git commit`.

## How It Works

1. Run `git status` and `git diff --staged` to inspect what's about to be committed.
2. If nothing is staged, run `git diff` against the working tree and include a note in the output: `(based on unstaged changes — run \`git add\` before committing)`.
3. Run `git log -50 --pretty=format:%s` to learn the project's scope vocabulary. When picking a scope, prefer one that's already in active use rather than inventing a new one.
4. If the working directory isn't a git repository (or git commands fail with "not a git repository"), ask the user to paste the diff manually.
5. Compose a Conventional Commits message that follows the format spec below.
6. Display the message for the user to review. **Do not run `git commit`.**

If the user includes hints in the same prompt (e.g. "make this a fix not a feat", "add scope `api`", "include a breaking change footer"), honor them — those hints override defaults.

## Format Structure

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Commit Type (REQUIRED)

Use lowercase for the type and keep the subject line under 50 characters:

- `feat` — A new feature (correlates with MINOR in SemVer)
- `fix` — A bug fix (correlates with PATCH in SemVer)
- `refactor` — Code restructuring without functional changes
- `docs` — Documentation only changes
- `style` — Code formatting, missing semi-colons, whitespace (no code change)
- `test` — Adding or correcting tests
- `perf` — Performance improvements
- `chore` — Build process, dependencies, tooling updates
- `ci` — Continuous integration changes
- `build` — Changes that affect the build system or external dependencies
- `revert` — Reverts a previous commit

## Subject Line (REQUIRED)

- Use imperative mood (e.g. "add" not "added" or "adds")
- Do not capitalize the first word after the colon
- No period at the end
- Keep under 50 characters
- Answer: "What does this commit do?"

Examples:
- `feat: add lazy load implementation for images`
- `fix: prevent racing of requests`
- `docs: correct spelling of changelog`
- `feat(api): add ability to parse arrays`

## Body (OPTIONAL but RECOMMENDED)

- Begin one blank line after the subject
- Explain *what* and *why*, not *how*
- Wrap at 72 characters per line
- Use bullet points for multiple items
- Be specific

## Footer (OPTIONAL)

Use footers for:

**1) Breaking changes (MAJOR version in SemVer)**

Use a `BREAKING CHANGE` footer (preferred for tooling compatibility):

```
BREAKING CHANGE: environment variables now take precedence over config files
```

**2) References to issues/tickets**

```
Closes #123
Refs: #456, #789
Reviewed-by: Name
```

## Full Example

```
fix: prevent racing of requests

Introduce a request id and a reference to latest request. Dismiss
incoming responses other than from latest request.

Remove timeouts which were used to mitigate the racing issue but are
obsolete now.

Reviewed-by: Z
Refs: #123
```

## Example with Breaking Change (footer-only)

```
feat: drop support for Node 6

This allows us to use async/await and other modern JavaScript features
that significantly improve code readability and maintainability.

BREAKING CHANGE: use JavaScript features not available in Node 6

Refs: #456
```

## Guidelines for Quality Commits

1. **Be a journalist** — make commits self-documenting
2. **Future-proof** — write for your future self who won't remember context
3. **Be direct** — eliminate filler words
4. **Don't assume** — reader may not have background
5. **Tell, don't just show** — code isn't always self-explanatory
6. **One commit = one logical change** — split unrelated changes

## Constraints

- Do **not** execute `git commit`. Only generate and display the message for review.
- Do **not** add attribution lines (e.g. `Co-Authored-By:`) at the end of the message.
- Use lowercase for type and description (except `BREAKING CHANGE` in footer).
- Follow the Conventional Commits specification strictly so automated tooling (semantic-release, changelog generators) can parse the output.
