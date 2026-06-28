---
name: find-session
description: Search Claude Code conversation history to find past sessions matching a query, and return resume commands for them. Use when the user wants to find, locate, or resume an earlier Claude Code session by topic, project, or what they were working on.
argument-hint: <search query>
---

# Find Session

Search through the user's Claude Code conversation history to find sessions matching their query, and return a `claude --resume` command for each match.

## Instructions

1. **Read the history file** at `~/.claude/history.jsonl`. Each line is a JSON object with:
   - `display`: the prompt/message text
   - `timestamp`: Unix timestamp in milliseconds
   - `project`: the project directory path

2. **Search for matches** in the `display` field that relate to the user's query.

3. **For each match, find the session ID** by:
   - Converting the project path to the encoded folder name (replace `/` with `-`)
   - Looking in `~/.claude/projects/<encoded-path>/` for session files
   - Each `.jsonl` file in that directory is a session — the filename (without `.jsonl`) is the session ID
   - Read the first few lines of recent session files to find which one contains messages around that timestamp

4. **Output results** in this format for each match. The resume command must `cd` into the session's project directory first, because `claude --resume` only works from that directory:
   ```
   ---
   Prompt: <the matching prompt text>
   Project: <project path>
   Session ID: <session-id>
   Timestamp: <human readable date>
   Resume command: cd "<project path>" && claude --resume <session-id>
   ```

5. **Limit to 10 most relevant matches**, sorted by relevance to the query, then by recency.

6. **Surface the most likely match first** and present its ready-to-run command on its own so the user can copy it directly:
   ```
   cd "<project path>" && claude --resume <session-id>
   ```

7. **Automatically copy that top command to the clipboard** using the OS-appropriate tool, and tell the user it's been copied:
   - macOS: `printf '%s' 'cd "<project path>" && claude --resume <session-id>' | pbcopy`
   - Linux (Wayland): `... | wl-copy` — or X11: `... | xclip -selection clipboard`
   - Windows: `... | clip`

   Use the command for the current platform. If no clipboard tool is available, skip this step and just show the command.

If no matches are found, say so and suggest alternative search terms.
