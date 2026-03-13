---
name: review
description: |
  Pre-landing PR review. Analyze the current branch's diff against main for
  structural issues that tests do not catch, with special attention to SQL
  safety, LLM trust boundaries, and concurrency risks.
---

# Pre-Landing PR Review

Run this skill when the user explicitly invokes `$review` or asks for a structural review of the current branch against main.

## Step 1: Check branch

1. Run `git branch --show-current` to get the current branch.
2. If on `main`, output: **"Nothing to review — you're on main or have no changes against main."** and stop.
3. Run `git fetch origin main --quiet && git diff origin/main --stat` to check if there's a diff. If no diff, output the same message and stop.

## Step 2: Read the checklist

Read the review checklist from `.codex/skills/review/checklist.md` if it exists. Otherwise read `~/.codex/skills/gstack/review/checklist.md`.

If the file cannot be read, stop and report the error. Do not proceed without the checklist.

## Step 3: Get the diff

Fetch the latest main to avoid false positives from a stale local main:

```bash
git fetch origin main --quiet
```

Run `git diff origin/main` to get the full diff. This includes both committed and uncommitted changes against the latest main.

## Step 4: Two-pass review

Apply the checklist against the diff in two passes:

1. **Pass 1 (CRITICAL):** SQL & Data Safety, LLM Output Trust Boundary
2. **Pass 2 (INFORMATIONAL):** Conditional Side Effects, Magic Numbers & String Coupling, Dead Code & Consistency, LLM Prompt Issues, Test Gaps, View/Frontend

Follow the output format specified in the checklist. Respect the suppressions. Do not flag items listed in the "DO NOT flag" section.

## Step 5: Output findings

Always output all findings, both critical and informational.

- If CRITICAL issues are found: output all findings, then ask the user directly in chat about each critical issue one at a time with the problem, your recommended fix, and options `(A) Fix it now`, `(B) Acknowledge`, `(C) False positive — skip`.
- After all critical questions are answered, output a summary of what the user chose for each issue. If the user chose `A` on any issue, apply the fixes. If they chose only `B` or `C`, leave the code unchanged.
- If only non-critical issues are found: output findings. No further action is needed.
- If no issues are found: output `Pre-Landing Review: No issues found.`

## Important Rules

- Read the full diff before commenting. Do not flag issues already addressed in the diff.
- Read-only by default. Only modify files if the user explicitly chooses "Fix it now" on a critical issue. Never commit, push, or create PRs.
- Be terse. One line problem, one line fix. No preamble.
- Only flag real problems. Skip anything that's fine.
