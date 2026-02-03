# Terminal Setup for Claude Code

**Tip #7**: Terminal setup matters - Ghostty, color-coded tabs, voice dictation.

---

## Terminal: Ghostty

The Claude Code team uses [Ghostty](https://ghostty.org/) for:
- Better Unicode rendering
- Consistent font rendering
- Fast performance
- Native macOS feel

```bash
# Install via Homebrew
brew install --cask ghostty
```

Other solid options: iTerm2, Warp, Kitty

---

## Color-Coded Tabs per Worktree

When running parallel sessions (Tip #1), color-code your tabs so you know which branch you're in at a glance.

### iTerm2 Setup

1. Go to Preferences → Profiles
2. Create profiles for each worktree:
   - `main` - Default color
   - `feature-a` - Blue tab
   - `feature-b` - Green tab
   - `feature-c` - Orange tab
3. Set each profile's tab color under "Colors"

### Ghostty Setup

In `~/.config/ghostty/config`:
```
# Different background tints for different directories
# (Ghostty detects working directory)
```

### Quick Aliases

Add to `~/.zshrc`:
```bash
# Jump between worktrees with one keystroke
alias za="cd ~/projects/myapp-feature-a && claude"
alias zb="cd ~/projects/myapp-feature-b && claude"
alias zc="cd ~/projects/myapp-feature-c && claude"
```

---

## Voice Dictation

**This is underrated.** Speaking yields richer prompts than typing.

### macOS Built-in

Press `fn` twice to start dictation. Works everywhere, including terminal.

**Why it's better:**
- You explain more context when speaking
- Less friction = more detailed prompts
- Captures nuance you'd skip when typing

### Example

**Typed prompt** (rushed):
```
fix the login bug
```

**Dictated prompt** (richer):
```
There's a bug in the login flow where if a user enters their
password wrong three times, they get locked out but the error
message just says "something went wrong" instead of telling
them they're locked out and when they can try again.
```

The dictated version gives Claude much more to work with.

---

## Recommended Shell Setup

### Zsh Configuration

```bash
# ~/.zshrc

# Better history
HISTSIZE=10000
SAVEHIST=10000
setopt SHARE_HISTORY

# Quick worktree navigation
alias wt="git worktree list"
alias wta="git worktree add"
alias wtr="git worktree remove"

# Claude shortcuts
alias c="claude"
alias cc="claude --continue"
alias cr="claude --resume"

# Parallel session aliases (customize per project)
alias za="cd ../myproject-a && claude"
alias zb="cd ../myproject-b && claude"
alias zc="cd ../myproject-c && claude"
```

### Git Configuration

```bash
# ~/.gitconfig additions

[alias]
    # Quick worktree creation
    wt-new = "!f() { git worktree add ../$1 -b $1; }; f"

    # See all worktrees with their branches
    wts = worktree list
```

---

## Project Structure for Parallel Work

```
~/projects/
├── myapp/              # Main worktree (main branch)
├── myapp-feature-a/    # Worktree for feature branch a
├── myapp-feature-b/    # Worktree for feature branch b
└── myapp-hotfix/       # Worktree for urgent fixes
```

Create with:
```bash
cd ~/projects/myapp
git worktree add ../myapp-feature-a feature-a
git worktree add ../myapp-feature-b feature-b
```

---

## Claude Code Configuration

### CLAUDE.md in Project Root

Every project should have a `CLAUDE.md` that tells Claude:
- Project context
- Code style preferences
- Common commands
- Things to avoid

See [/meta-rule](skills/meta-rule/SKILL.md) for how to build this over time.

### Skills Directory

```
~/.claude/
├── skills/           # Your custom skills
│   ├── grill/
│   ├── elegant-redo/
│   └── ...
├── CLAUDE.md         # Global rules (apply to all projects)
└── settings.json     # Claude Code settings
```

---

## Recommended VS Code / Cursor Extensions

If you use an IDE alongside Claude Code:

- **GitLens** - See git blame inline
- **Error Lens** - See errors inline (matches what Claude sees)
- **Todo Tree** - Track TODOs Claude leaves

---

## Verification

Test your setup:

```bash
# Check Claude is working
claude --version

# Check skills are loaded
claude
> /grill
# Should see the grill skill activate

# Check worktrees
git worktree list
```
