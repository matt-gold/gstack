# gstack

**gstack turns Codex into a small team of workflow specialists you can invoke on demand.**

This repository was forked from the original `garrytan/gstack` and is now maintained as a Codex-native variant. The original git history is preserved.

It packages six Codex skills plus a fast Playwright-backed browser CLI:

| Skill | Use it for |
|------|-------------|
| `$plan-ceo-review` | Rethink the problem and pressure-test product scope |
| `$plan-eng-review` | Lock architecture, data flow, diagrams, and test plans |
| `$review` | Find structural bugs and trust-boundary issues before merge |
| `$ship` | Merge main, run tests, push, and open a PR |
| `$browse` | Drive a real browser for QA, docs, or deployment checks |
| `$retro` | Generate an engineering retrospective from git history |

## Codex model

This repo is now ported for Codex conventions:

- Skills live in `~/.codex/skills/gstack/` or `.codex/skills/gstack/`
- Skill discovery happens through Codex skills, not Claude-only slash commands
- Explicit invocation uses `$skill-name`
- Project guidance goes in `AGENTS.md`, not `CLAUDE.md`

Codex does not support custom slash commands like `/ship` or `/browse` the way Claude Code did. The equivalent is explicit skill invocation such as `$ship` or `$browse`.

## Install

Requirements:

- Codex
- Git
- Bun v1.0+

### Install globally

```bash
git clone https://github.com/garrytan/gstack.git ~/.codex/skills/gstack
cd ~/.codex/skills/gstack
./setup
```

### Add to a project

```bash
mkdir -p .codex/skills
cp -Rf ~/.codex/skills/gstack .codex/skills/gstack
rm -rf .codex/skills/gstack/.git
cd .codex/skills/gstack
./setup
```

### Add project guidance

Add a `gstack` section to your repo's `AGENTS.md` telling Codex:

- prefer `$browse` for browser automation
- available skills are `$plan-ceo-review`, `$plan-eng-review`, `$review`, `$ship`, `$browse`, `$retro`
- if the browser binary is missing, run `cd .codex/skills/gstack && ./setup`

## What setup does

`./setup`:

- builds `browse/dist/browse` if needed
- symlinks each skill into the surrounding `skills/` directory
- keeps everything inside `.codex/`

Nothing is added to your PATH and nothing runs in the background until you use `$browse`.

## Usage

Examples:

```text
$plan-ceo-review
I want to add seller photo upload to the listing flow.
```

```text
$plan-eng-review
Review this implementation plan before I write code.
```

```text
$review
```

```text
$ship
```

```text
$browse
Open staging.example.com, log in, and verify the signup flow.
```

```text
$retro 14d
```

You can also let Codex pick a skill implicitly if your request matches, but explicit `$skill` invocation is the most reliable way to force a specific workflow.

## Browser

`$browse` uses the compiled `browse` binary in this repo. It starts a persistent local Chromium daemon on first use, then subsequent commands are fast. State persists across calls for about 30 minutes of idle time.

Technical details live in [BROWSER.md](/Users/mattgold/Code/gstack/BROWSER.md).

## Repo layout

```text
gstack/
├── browse/            # Persistent browser CLI + tests
├── plan-ceo-review/   # Product/founder planning skill
├── plan-eng-review/   # Technical planning skill
├── review/            # Pre-merge review skill
├── ship/              # Shipping workflow skill
├── retro/             # Engineering retrospective skill
├── setup              # Build + symlink installer
└── AGENTS.md          # Repo guidance for working on gstack itself
```

## Development

```bash
bun install
bun test
bun run dev goto https://example.com
bun run build
```

When editing the browser binary, rebuild before copying it into an installed skill directory.
