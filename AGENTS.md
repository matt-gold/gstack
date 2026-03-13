# gstack development

## Commands

```bash
bun install          # install dependencies
bun test             # run integration tests (browse + snapshot)
bun run dev <cmd>    # run CLI in dev mode, e.g. bun run dev goto https://example.com
bun run build        # compile binary to browse/dist/browse
```

## Project structure

```text
gstack/
├── browse/          # Headless browser CLI (Playwright)
│   ├── src/         # CLI + server + commands
│   ├── test/        # Integration tests + fixtures
│   └── dist/        # Compiled binary
├── ship/            # $ship skill
├── review/          # $review skill
├── plan-ceo-review/ # $plan-ceo-review skill
├── plan-eng-review/ # $plan-eng-review skill
├── retro/           # $retro skill
├── setup            # One-time setup: build binary + symlink skills
├── SKILL.md         # Top-level gstack skill entrypoint
└── package.json     # Build scripts for browse
```

## Deploying to the active skill

The active global skill lives at `~/.codex/skills/gstack/`. After making changes:

1. Push your branch.
2. Update the installed skill directory: `cd ~/.codex/skills/gstack && git pull`
3. Rebuild: `cd ~/.codex/skills/gstack && bun run build`

Or copy the binary directly:

```bash
cp browse/dist/browse ~/.codex/skills/gstack/browse/dist/browse
```
