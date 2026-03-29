---
name: agents-bridge
description: Invoke Claude Code, OpenAI Codex, or Google Gemini CLI to delegate tasks to other AI agents. Use this skill whenever the user wants to call another AI CLI, send a prompt to a different model, compare outputs across agents, resume a session with another agent, or bridge work between multiple AI coding assistants.
---

# Agents Bridge

Delegate tasks to Claude Code, OpenAI Codex, or Google Gemini CLI from within this session.

## Workflow

1. **Pick the target CLI** based on the user's request (claude, codex, or gemini).
2. **Build the command** using the reference below. Always use non-interactive mode (`-p` / `exec` / `-p`).
3. **Long messages go through files.** Write the prompt to a temp file and pipe it in, or redirect output to a file. Never paste walls of text into the conversation — give the user a file path instead.
4. **Record the session ID** from the CLI's output. When the user wants to continue the conversation, resume by that exact session ID — not by "last" or "latest".
5. **Report back** to the user: summarize what happened, give them the output file path, and note the session ID for future resume.

---

## Claude Code (`claude`)

**Install:** `npm i -g @anthropic-ai/claude-code`

| Task | Command |
|------|---------|
| Interactive | `claude` |
| One-shot prompt | `claude -p "your prompt"` |
| Pipe input | `cat file.py \| claude -p "review this"` |
| Select model | `claude --model opus` / `claude --model sonnet` / `claude --model haiku` |
| Resume specific session | `claude --resume <session-id>` |
| JSON output | `claude -p --output-format json "prompt"` |

**Key flags:**

```
-p, --print                 Non-interactive, print and exit
--model <model>             Aliases: opus, sonnet, haiku. Full names: claude-opus-4-6, claude-sonnet-4-6, claude-haiku-4-5
-c, --continue              Continue most recent conversation (current dir)
-r, --resume [id|term]      Resume a session (picker or by ID/search)
--output-format <fmt>       text | json | stream-json
--system-prompt <prompt>    Override system prompt
--max-budget-usd <amount>   Cap API spend (only with -p)
--allowedTools <tools...>   Restrict tool access
--permission-mode <mode>    default | plan | auto | bypassPermissions
--effort <level>            low | medium | high | max
```

---

## OpenAI Codex (`codex`)

**Install:** `npm i -g @openai/codex` or `brew install --cask codex`

First run: `codex login`

| Task | Command |
|------|---------|
| Interactive | `codex` |
| One-shot (stdout only) | `codex exec "your prompt" 2>/dev/null` |
| Pipe input | `echo "explain" \| codex exec - 2>/dev/null` |
| Select model | `codex -m gpt-5.4` / `codex -m gpt-5.4-mini` / `codex -m gpt-5.3-codex` |
| Thinking effort | `codex -c model_reasoning_effort='"high"' "prompt"` |
| Resume specific session | `codex exec resume <session-id> 2>/dev/null` |

> **Note:** Append `2>/dev/null` to `codex exec` to suppress intermediate steps and keep only the final output on stdout.

**Key flags:**

```
-m, --model <model>             Models: gpt-5.4, gpt-5.4-mini, gpt-5.3-codex, gpt-5.3-codex-spark
-c model_reasoning_effort='"<level>"'  Thinking effort: none | minimal | low | medium | high | xhigh
-a, --ask-for-approval <mode>   untrusted | on-request | never
--full-auto                     Low-friction auto mode (sandboxed)
-s, --sandbox <policy>          read-only | workspace-write | danger-full-access
-C, --cd <dir>                  Set working directory
-i, --image <file>              Attach image (repeatable)
--search                        Enable web search
--json                          JSONL output (with exec)
```

---

## Google Gemini (`gemini`)

**Install:** `npm i -g @google/gemini-cli` or `brew install gemini-cli`

First run authenticates via Google OAuth (free tier: 60 req/min, 1000 req/day).

| Task | Command |
|------|---------|
| Interactive | `gemini` |
| One-shot prompt | `gemini -p "your prompt"` |
| Pipe input | `cat file.py \| gemini -p "review this"` |
| Select model | `gemini -m pro` / `gemini -m flash` / `gemini -m flash-lite` |
| Resume specific session | `gemini -r <session-id>` |
| Resume + new prompt | `gemini -r <session-id> "continue here"` |

**Model aliases:** `auto` (default, routes between pro/flash), `pro` (gemini-3-pro-preview), `flash` (gemini-3-flash-preview), `flash-lite` (gemini-2.5-flash-lite). Full names like `gemini-2.5-pro`, `gemini-3.1-pro-preview` also accepted.

**Key flags:**

```
-p, --prompt                    Non-interactive, print and exit
-m, --model <model>             Model name or alias
-r, --resume <id|"latest"|N>    Resume a session
--approval-mode yolo            Auto-approve all tool actions
--sandbox                       Run in Docker/Podman container
--include-directories <dirs>    Add extra dirs to workspace
--output-format <fmt>           json | stream-json
-d, --debug                     Verbose logging
```

---

## Side-by-Side Comparison

| Feature | Claude | Codex | Gemini |
|---------|--------|-------|--------|
| Non-interactive flag | `-p` | `exec` subcommand | `-p` |
| Model flag | `--model` | `-m` | `-m` |
| Resume by session ID | `--resume <id>` | `resume <id>` | `-r <id>` |
| Output format | `--output-format` | `--json` | `--output-format` |
| Auto-approve | `--permission-mode auto` | `--full-auto` | `--approval-mode yolo` |
| Sandbox | built-in permissions | `--sandbox` | `--sandbox` |

---

## Common Recipes

### Ask all three the same question

```bash
claude -p "explain the main function in src/index.ts"
codex exec "explain the main function in src/index.ts" 2>/dev/null
gemini -p "explain the main function in src/index.ts"
```

### Code review via pipe

```bash
git diff | claude -p "review this diff"
git diff | codex exec "review this diff" 2>/dev/null
git diff | gemini -p "review this diff"
```

### Resume a specific session

```bash
claude --resume <session-id>           # Claude: resume by session ID
codex exec resume <session-id> 2>/dev/null  # Codex: resume by session ID
gemini -r <session-id>                 # Gemini: resume by session ID
```
