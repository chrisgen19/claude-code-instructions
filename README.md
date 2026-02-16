# Claude Code Instructions

Personal Claude Code configuration — global preferences, coding standards, and custom skills for use across all projects.

## What's Included

```
claude-code-instructions/
├── CLAUDE.md                        # Global instructions & coding preferences
├── README.md                        # Setup guide (this file)
└── skills/
    └── explain-code/
        └── SKILL.md                 # Custom skill: explain code with diagrams
```

| File | Purpose |
|------|---------|
| `CLAUDE.md` | Coding standards, tech stack preferences, git conventions, communication style, and workflow rules that apply to every project |
| `skills/explain-code/SKILL.md` | Custom `/explain-code` slash command that explains code using analogies, ASCII diagrams, and step-by-step walkthroughs |

---

## Setup Guide

### Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed and authenticated
- Git installed

### Step 1: Clone This Repo

```bash
git clone <your-repo-url> claude-code-instructions
```

### Step 2: Copy CLAUDE.md to Global Location

The global `CLAUDE.md` lives inside `~/.claude/` and applies to **all** your projects.

#### macOS / Linux / WSL

```bash
# Create the directory if it doesn't exist
mkdir -p ~/.claude

# Copy the global instructions
cp claude-code-instructions/CLAUDE.md ~/.claude/CLAUDE.md
```

#### Windows (PowerShell)

```powershell
# Create the directory if it doesn't exist
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude"

# Copy the global instructions
Copy-Item "claude-code-instructions\CLAUDE.md" "$env:USERPROFILE\.claude\CLAUDE.md"
```

#### Windows (Command Prompt)

```cmd
mkdir "%USERPROFILE%\.claude"
copy "claude-code-instructions\CLAUDE.md" "%USERPROFILE%\.claude\CLAUDE.md"
```

### Step 3: Copy Skills to Global Location

Skills placed in `~/.claude/skills/` are available as slash commands in **every** project.

#### macOS / Linux / WSL

```bash
# Copy the skills directory
cp -r claude-code-instructions/skills/* ~/.claude/skills/
```

#### Windows (PowerShell)

```powershell
# Copy the skills directory
Copy-Item -Recurse "claude-code-instructions\skills\*" "$env:USERPROFILE\.claude\skills\"
```

### Step 4: Verify

Open Claude Code in any project and check:

```bash
claude
```

- Your global preferences from `CLAUDE.md` should be active automatically
- Type `/explain-code` to verify the skill is available

---

## Global `.claude/` Directory Structure

After setup, your `~/.claude/` directory should look like this:

```
~/.claude/
├── CLAUDE.md                 # Global instructions (from this repo)
├── settings.json             # Global settings (permissions, hooks, env vars)
├── keybindings.json          # Custom keyboard shortcuts
├── skills/                   # Custom slash commands (available everywhere)
│   └── explain-code/
│       └── SKILL.md
└── projects/                 # Auto-generated per-project memory
    └── <project-hash>/
        └── memory/
            └── MEMORY.md
```

### File Locations by OS

| File | macOS / Linux / WSL | Windows |
|------|---------------------|---------|
| Global instructions | `~/.claude/CLAUDE.md` | `C:\Users\<You>\.claude\CLAUDE.md` |
| Global settings | `~/.claude/settings.json` | `C:\Users\<You>\.claude\settings.json` |
| Skills directory | `~/.claude/skills/` | `C:\Users\<You>\.claude\skills\` |
| Keybindings | `~/.claude/keybindings.json` | `C:\Users\<You>\.claude\keybindings.json` |

---

## How Skills Work

Skills are custom slash commands you can invoke in Claude Code. They give Claude specific instructions for a particular task.

### Using a Skill

In any Claude Code session, type the skill name as a slash command:

```
/explain-code
```

Claude will load the skill's instructions and follow them for that interaction. You can also pass arguments:

```
/explain-code src/lib/auth.ts
```

### Included Skill: `/explain-code`

Explains code using analogies, diagrams, and step-by-step walkthroughs.

**When it activates:** When you ask "how does this work?" or need code explained.

**What it does:**
- For **simple code** — gives an analogy, walks through the logic, highlights common gotchas
- For **complex code** — adds ASCII/Mermaid diagrams, references actual file paths, and suggests what to explore next

**Example usage:**

```
> /explain-code src/middleware.ts
> /explain-code  (then ask about a specific function)
> How does the authentication flow work?  (auto-invoked based on description)
```

### Creating Your Own Skills

#### 1. Create a Skill Directory

Each skill gets its own folder inside `~/.claude/skills/`:

```bash
mkdir -p ~/.claude/skills/your-skill-name
```

#### 2. Create `SKILL.md`

The `SKILL.md` file defines the skill. It requires YAML frontmatter and markdown instructions:

```markdown
---
name: your-skill-name
description: Short description of what this skill does and when to use it.
---

Your instructions for Claude go here.

Tell Claude exactly what to do when this skill is invoked.
```

#### 3. Frontmatter Options

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `name` | string | **required** | The slash command name (e.g., `review-pr` becomes `/review-pr`) |
| `description` | string | **required** | When Claude should use this skill. Also shown in autocomplete |
| `user-invocable` | boolean | `true` | Set to `false` to hide from the slash command menu |
| `disable-model-invocation` | boolean | `false` | Set to `true` to prevent Claude from auto-invoking this skill |
| `allowed-tools` | array | — | Tools Claude can use without asking (e.g., `Read`, `Grep`, `Bash`) |
| `argument-hint` | string | — | Hint shown in autocomplete (e.g., `[filename]`, `[PR number]`) |

#### 4. Example: Custom Review Skill

```bash
mkdir -p ~/.claude/skills/review
```

`~/.claude/skills/review/SKILL.md`:

```markdown
---
name: review
description: Reviews code for bugs, security issues, and style problems. Use when the user asks for a code review.
argument-hint: [file or directory]
---

Review the provided code focusing on:

1. **Bugs** — Logic errors, off-by-one, null checks, race conditions
2. **Security** — XSS, SQL injection, exposed secrets, missing input validation
3. **Performance** — N+1 queries, unnecessary re-renders, missing memoization
4. **Style** — Naming, code organization, readability

Format your review as:

### Summary
One-line overall assessment.

### Issues Found
- **[severity]** file:line — Description of the issue

### Suggestions
- Improvements that aren't bugs but would make the code better
```

#### 5. Skill Placement Options

| Location | Scope | Who Can Use It |
|----------|-------|----------------|
| `~/.claude/skills/` | Global (personal) | You, in every project |
| `<project>/.claude/skills/` | Project | Anyone working on that project |

### Using Variables in Skills

Skills support variable substitution:

| Variable | Description |
|----------|-------------|
| `$ARGUMENTS` | Everything the user typed after the slash command |
| `$ARGUMENTS[0]` or `$0` | First argument |
| `$ARGUMENTS[1]` or `$1` | Second argument |

Example in a skill:

```markdown
---
name: test
description: Run tests for a specific file
argument-hint: [filename]
---

Run the tests for `$ARGUMENTS`. If no arguments provided, run all tests.
```

Usage: `/test src/utils/auth.test.ts`

---

## Updating Your Configuration

When you make changes to this repo, re-copy the updated files:

### Quick Update Script (macOS / Linux / WSL)

```bash
# From the repo directory
cp CLAUDE.md ~/.claude/CLAUDE.md
cp -r skills/* ~/.claude/skills/
echo "Claude Code config updated."
```

### Quick Update Script (Windows PowerShell)

```powershell
# From the repo directory
Copy-Item "CLAUDE.md" "$env:USERPROFILE\.claude\CLAUDE.md" -Force
Copy-Item -Recurse -Force "skills\*" "$env:USERPROFILE\.claude\skills\"
Write-Host "Claude Code config updated."
```

You can also symlink instead of copying so changes are always in sync:

#### macOS / Linux / WSL (Symlink)

```bash
# Remove existing files first
rm -f ~/.claude/CLAUDE.md
rm -rf ~/.claude/skills

# Create symlinks
ln -s "$(pwd)/CLAUDE.md" ~/.claude/CLAUDE.md
ln -s "$(pwd)/skills" ~/.claude/skills
```

#### Windows (PowerShell as Admin)

```powershell
# Remove existing files first
Remove-Item "$env:USERPROFILE\.claude\CLAUDE.md" -Force -ErrorAction SilentlyContinue
Remove-Item "$env:USERPROFILE\.claude\skills" -Recurse -Force -ErrorAction SilentlyContinue

# Create symlinks (requires admin)
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.claude\CLAUDE.md" -Target "$PWD\CLAUDE.md"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.claude\skills" -Target "$PWD\skills"
```

---

## How Configuration Loads

Claude Code reads instructions in this priority order (highest first):

| Priority | Source | Scope |
|----------|--------|-------|
| 1 | `./CLAUDE.local.md` | Personal project overrides (gitignored) |
| 2 | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Project-level (shared via git) |
| 3 | `~/.claude/CLAUDE.md` | Global user preferences (this repo) |

This means project-level `CLAUDE.md` files can override your global settings for specific projects. Your global config acts as the baseline for all projects.

---

## Troubleshooting

### Skill not showing up

- Verify the file exists at `~/.claude/skills/<name>/SKILL.md`
- Check that the YAML frontmatter has `name` and `description` fields
- Make sure `user-invocable` is not set to `false`
- Restart Claude Code (`/exit` then `claude`)

### CLAUDE.md not loading

- Verify the file is at `~/.claude/CLAUDE.md` (not `~/.claude/.claude/CLAUDE.md`)
- Check file permissions: `ls -la ~/.claude/CLAUDE.md`
- On Windows, confirm the path is `C:\Users\<YourUsername>\.claude\CLAUDE.md`

### Changes not taking effect

- If using symlinks, verify they're not broken: `ls -la ~/.claude/`
- If copying files, make sure you copied the latest version
- Claude Code reads config at session start — restart the session after changes
