# pixfixer-ralphy-example

Sample project — builds Chronos, a minimal Windows desktop timer app, using
[my-ralphy](https://github.com/narinzar/my-ralphy) as the autonomous AI coding engine.

Clone this repo, run one command, watch Claude Code agents build the entire app.

---

## What gets built

Chronos is a dark-themed Python desktop timer for Windows:
- Countdown and stopwatch modes
- Digital HH:MM:SS display
- Start / Pause / Resume / Reset controls
- Progress bar and red flash on completion
- System tray support
- Keyboard shortcuts: Space, R, M, Escape
- Alert sound with mute toggle
- Always-on-top option

---

## Prerequisites (one time only)

### 1. Install Node.js
Download from https://nodejs.org — Windows 64-bit LTS installer

### 2. Install Claude Code CLI
```powershell
npm install -g @anthropic-ai/claude-code
claude --version
```

### 3. Install tsx
```powershell
npm install -g tsx
```

### 4. Install my-ralphy
```powershell
git clone https://github.com/narinzar/my-ralphy.git C:\tools\my-ralphy
cd C:\tools\my-ralphy\cli
npm install
npm link
ralphy --version
```

### 5. Install Python 3.10+ and uv
- Python: https://python.org
- uv: pip install uv

---

## Usage

### Step 1 - Clone this repo
```powershell
git clone https://github.com/narinzar/pixfixer-ralphy-example.git
cd pixfixer-ralphy-example
```

### Step 2 - Git config
```powershell
git config user.email "you@email.com"
git config user.name "Your Name"
```

### Step 3 - Python environment
```powershell
uv venv .venv
.venv\Scripts\Activate.ps1
uv pip install customtkinter pystray pillow pygame
```

### Step 4 - Run ralphy
```powershell
ralphy --json PRD.json --dry-run
ralphy --json PRD.json --parallel --max-parallel 3
```

### Step 5 - Run the app
```powershell
python main.py
```

---

## After the build

### Cleanup
```powershell
git worktree prune
Remove-Item -Recurse -Force .ralphy-worktrees
Remove-Item -Recurse -Force .ralphy
git branch | Where-Object { $_ -match "agent-" } | ForEach-Object { git branch -D $_.Trim() }
```

### Commit
```powershell
git add .
git commit -m "feat: Chronos timer app built by ralphy"
git push origin main
```

---

## Adding features later

Quick one-off:
```powershell
ralphy "add a 25-minute Pomodoro preset button"
ralphy "fix the reset button so it also stops the sound"
```

Planned features - add to PRD.json with completed false and new parallel_group number, then:
```powershell
ralphy --json PRD.json --parallel --max-parallel 3
```

---

## Decision guide

| What you want | Command |
|---------------|---------|
| Fix a bug | ralphy "fix X" |
| Tweak something small | ralphy "change X to Y" |
| Add one feature | ralphy "add Z" |
| Add 2-8 features | Add to PRD.json, run sequential |
| Add 8+ features | Add to PRD.json with parallel groups, run parallel |

---

## Git troubleshooting

Push rejected:
```powershell
git pull origin main --rebase
git push origin main
```

Force push:
```powershell
git push -u origin main --force
```

Ghost agent branches in VSCode:
```powershell
git worktree prune
git branch | Where-Object { $_ -match "agent-" } | ForEach-Object { git branch -D $_.Trim() }
```

No tasks remaining but nothing built - open PRD.json and set tasks back to completed false.

---

## How it works

my-ralphy reads PRD.json, spawns a Claude Code agent per task in an isolated
git worktree, each agent builds its task independently, everything merges back to main.
CLAUDE.md is injected into every agent session as project rules.

---

## License

MIT
