# GitGPT — Talk to Git in Plain English

> **"Create a repo named ABC in the D drive"** → GitGPT figures out the exact command, shows it to you, explains what it does, and runs it — only after you say yes.

GitGPT is a command-line tool that bridges the gap between what you *mean* and what Git *needs*. Instead of memorizing flags and syntax, just describe what you want to do.

---

## What It Does

- **Natural language → Git command** — Type a sentence, get the right command back
- **Always explains itself** — Every command comes with a plain-English explanation
- **Asks before it acts** — Confirms before executing anything (except harmless follow-ups)
- **OS-aware** — Generates the correct syntax for Windows, macOS, or Linux automatically
- **Warns you about danger** — Destructive operations get a clear `DANGER` notice before you can run them
- **AI-powered conflict resolver** — When merges go wrong, it reads both sides and suggests how to fix them

---

## Demo

```
$ gitgpt "create a repo named myapp in C:\Projects"

╭─ Generated Chained Command ──────────────────────────────╮
│ mkdir C:\Projects\myapp && cd C:\Projects\myapp && git init │
╰──────────────────────────────────────────────────────────╯

╭─ 💡 Command Explanation ─────────────────────────────────╮
│ Creates the myapp folder and initializes a Git repository  │
│ inside it.                                                 │
╰──────────────────────────────────────────────────────────╯

? Do you want to execute this command? (Y/n)
```

---

## Installation

**Prerequisites:** Python 3.8+, Git installed and on your PATH

```bash
# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/gitgpt-cli.git
cd gitgpt-cli

# 2. Install the package
pip install -e .

# 3. Add your Gemini API key
echo 'GEMINI_API_KEY="your-key-here"' > .env
```

Get a free API key at [Google AI Studio](https://aistudio.google.com/).

---

## Usage

### Translate a command

```bash
gitgpt "show me my last 5 commits"
gitgpt "undo my last commit but keep the files"
gitgpt "create a new branch called feature-login and switch to it"
gitgpt "push my changes to origin"
```

### Resolve merge conflicts

```bash
gitgpt resolve
```

GitGPT will scan for conflicts, show you both sides, suggest a resolution with a confidence level (HIGH / MEDIUM / LOW), and let you choose: accept the AI fix, keep your version, keep theirs, or skip.

---

## How It Works

```
Your plain-English input
        │
        ▼
   Gemini 2.5 Flash
   (with OS context + current branch + git status baked in)
        │
        ▼
   JSON response
   { command, explanation, warning, next_step, auto_execute }
        │
        ▼
   Rich terminal UI
   (coloured panels, danger warnings, spinner)
        │
        ▼
   Your confirmation  ──►  subprocess.Popen  ──►  live output
```

The prompt is built dynamically — it includes your current OS, branch name, and working tree status so the AI always generates context-appropriate commands.

---

## Project Structure

```
gitgpt/
├── main.py          # CLI entry point, conflict resolution orchestration
├── api_client.py    # Gemini API calls for both commands and conflict resolution
├── executor.py      # Terminal UI + command execution + conflict file patching
├── prompts.py       # Dynamic prompt builders (OS-aware + conflict resolver)
└── __init__.py
pyproject.toml       # Package config + dependencies
.env                 # Your API key (never commit this)
```

---

## Dependencies

| Package | Purpose |
|---|---|
| `google-generativeai` | Gemini API client |
| `rich` | Coloured panels, spinners, formatted terminal output |
| `questionary` | Interactive confirm/select prompts |
| `python-dotenv` | Loads the API key from `.env` |

---

## Safety

- GitGPT **always asks for confirmation** before running a command
- Commands flagged as destructive (`git reset --hard`, force pushes, etc.) display a red `DANGER WARNING` panel — `auto_execute` is hardcoded to `false` for these
- The `.env` file is in `.gitignore` by default — keep your API key out of version control

---

## Built With

- [Gemini 2.5 Flash](https://deepmind.google/technologies/gemini/) — fast, instruction-following model with JSON mode
- [Rich](https://github.com/Textualize/rich) — terminal formatting
- [Questionary](https://github.com/tmbo/questionary) — interactive CLI prompts

---

## Contributing

Pull requests are welcome. If you find a case where the generated command is wrong or the conflict resolver misses something, open an issue with the input and what you expected.

---

## License

MIT
