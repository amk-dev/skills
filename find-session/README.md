# find-session

A [Claude Code](https://code.claude.com) skill that searches your conversation history to find past sessions matching a query, and returns a `claude --resume` command for each match.

## Install

```sh
npx skills add amk-dev/skills/find-session
```

## What it does

Given a search query, the skill:

1. Reads your `~/.claude/history.jsonl` to find prompts matching the query.
2. Resolves each match to its session ID under `~/.claude/projects/`.
3. Prints the matching prompt, project, timestamp, and a ready-to-run `claude --resume <session-id>` command.

Useful when you remember *what* you were working on but not *which* session it was in.
