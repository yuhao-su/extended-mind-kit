# AI CLI Quick Reference

A quick reference for invoking Claude Code, OpenAI Codex, and Google Gemini from the command line.

## Usage Guidelines

- **Long output must go through files, not chat messages.** If the CLI output or your response would be long, write it to a file and tell the user the file path — do not paste walls of text into the conversation.
- **Use session ID to resume conversations with the target CLI.** Each CLI maintains session history. When iterating on a task, always resume by session ID (not "last") so you return to the exact conversation. Record the session ID from the initial run output.

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
| One-shot (last message only) | `codex exec -o <path> "your prompt"` |
| Pipe input | `echo "explain" \| codex exec -o <path> -` |
| Select model | `codex -m gpt-5.4` / `codex -m gpt-5.4-mini` / `codex -m gpt-5.3-codex` |
| Thinking effort | `codex -c model_reasoning_effort='"high"' "prompt"` |
| Resume specific session | `codex exec resume <session-id> -o <path>` |

> **Note:** Always use `-o <file>` with `codex exec` (including resume) to capture only the final message. Without it, `exec` prints all intermediate steps which can be very noisy.

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
-o, --output-last-message <f>   Write final message to file
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
codex exec -o <path> "explain the main function in src/index.ts"
gemini -p "explain the main function in src/index.ts"
```

### Code review via pipe

```bash
git diff | claude -p "review this diff"
git diff | codex exec -o <path> "review this diff"
git diff | gemini -p "review this diff"
```

### Resume a specific session

```bash
claude --resume <session-id>           # Claude: resume by session ID
codex exec resume <session-id> -o <path>  # Codex: resume by session ID
gemini -r <session-id>                 # Gemini: resume by session ID
```
