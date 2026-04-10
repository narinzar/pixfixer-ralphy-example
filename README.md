# pixfixer-ralphy-example

Sample project — builds **Chronos**, a minimal Windows desktop timer app, using
[my-ralphy](https://github.com/narinzar/my-ralphy) as the autonomous AI coding engine.

Clone this repo, run one command, watch Claude Code agents build the entire app from scratch.

---

## What gets built

Chronos is a dark-themed Python desktop timer for Windows:
- Countdown and stopwatch modes
- Digital HH:MM:SS display with monospace font
- Start / Pause / Resume / Reset controls
- Progress bar and red flash on countdown completion
- System tray support (minimizes to tray on close)
- Keyboard shortcuts: Space, R, M, Escape
- Alert sound with mute toggle
- Always-on-top option

Built with Python + CustomTkinter. Runs with `python main.py`, no installer needed.

---

## Prerequisites (one time only)

### 1. Install Node.js
Download from https://nodejs.org — Windows 64-bit LTS `.msi` installer.

### 2. Install Claude Code CLI
```powershell
npm install -g @anthropic-ai/claude-code
claude --version
```

### 3. Install tsx and bun
```powershell
npm install -g tsx
npm install -g bun
```

### 4. Install my-ralphy
```powershell
git clone https://github.com/narinzar/my-ralphy.git C:\tools\my-ralphy
cd C:\tools\my-ralphy\cli
npm install
bun run build
npm link
ralphy --version
```

### 5. Install Python 3.10+ and uv
- Python: https://python.org
- uv: `pip install uv`

---

## Usage

### Step 1 — Clone this repo
```powershell
git clone https://github.com/narinzar/pixfixer-ralphy-example.git
cd pixfixer-ralphy-example
```

### Step 2 — Git config
```powershell
git config user.email "you@email.com"
git config user.name "Your Name"
```

### Step 3 — Python environment
```powershell
uv venv .venv
.venv\Scripts\Activate.ps1
uv pip install customtkinter pystray pillow pygame
```

### Step 4 — Preview tasks
```powershell
ralphy --json PRD.json --dry-run
```

### Step 5 — Build
```powershell
# Parallel (recommended)
ralphy --json PRD.json --parallel --max-parallel 3
```

### Step 6 — Run the app
```powershell
python main.py
```

---

## CRITICAL: Editing PRD.json

> **Always create and edit PRD.json in VSCode — never with PowerShell Out-File
> or Set-Content.** PowerShell adds a BOM character that breaks Node.js JSON parsing.
> VSCode saves UTF-8 without BOM by default.

If your PRD.json was created with PowerShell and shows a JSON error, fix it with:
```powershell
node -e "const fs=require('fs'); const data=JSON.parse(fs.readFileSync('PRD.json','utf8').replace(/^\uFEFF/,'')); fs.writeFileSync('PRD.json', JSON.stringify(data, null, 2), 'utf8'); console.log('Fixed')"
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

### Quick one-off change
```powershell
ralphy "add a 25-minute Pomodoro preset button"
ralphy "fix the reset button so it also stops the sound"
ralphy "change the clock font size to 48px"
ralphy "add a lap button in stopwatch mode"
```

### Planned features
Open `PRD.json` in VSCode, append new tasks with `"completed": false` and a new `parallel_group` number, save, then run:
```powershell
ralphy --json PRD.json --parallel --max-parallel 3
```

Ralphy skips all `"completed": true` tasks automatically.

---

## Decision guide

| What you want | What to do |
|---------------|------------|
| Fix a bug | `ralphy "fix X"` |
| Tweak something small | `ralphy "change X to Y"` |
| Add one feature | `ralphy "add Z"` |
| Add 2–8 features | Add to PRD.json in VSCode, run sequential |
| Add 8+ features | Add to PRD.json with parallel groups, run parallel |

---

## Git troubleshooting

### Push rejected
```powershell
git pull origin main --rebase
git push origin main
```

### Force push on fresh repo
```powershell
git push -u origin main --force
```

### Ghost agent branches in VSCode source control
```powershell
git worktree prune
git branch | Where-Object { $_ -match "agent-" } | ForEach-Object { git branch -D $_.Trim() }
```
Then click refresh in the VSCode source control panel.

### No tasks remaining but nothing built
Open `PRD.json` in VSCode and set the tasks you want back to `"completed": false`, save, rerun.

### PRD.json shows "Unexpected token" or "not valid JSON"
Created with PowerShell — has BOM encoding. Fix:
```powershell
node -e "const fs=require('fs'); const data=JSON.parse(fs.readFileSync('PRD.json','utf8').replace(/^\uFEFF/,'')); fs.writeFileSync('PRD.json', JSON.stringify(data, null, 2), 'utf8'); console.log('Fixed')"
```

### ralphy version errors or binary not found
```powershell
cd C:\tools\my-ralphy\cli
npm install
bun run build
npm link
ralphy --version
```

---

## Repo structure

```
pixfixer-ralphy-example/
├── PRD.json      ← task plan (never delete, keep adding to it)
├── CLAUDE.md     ← AI rules injected into every agent session
├── README.md     ← this file
├── .gitignore
├── main.py       ← built by agents
├── requirements.txt  ← built by agents
├── assets/       ← built by agents
│   ├── alert.wav
│   └── chronos.ico
└── src/          ← built by agents
    ├── __init__.py
    ├── timer.py
    ├── ui.py
    ├── tray.py
    └── sound.py
```

---

## How it works

my-ralphy reads PRD.json, spawns a Claude Code agent per task in an isolated git worktree,
each agent builds its task independently, everything merges back into main.
CLAUDE.md is injected into every agent session as project rules.

```
PRD.json → ralphy → agent 1 (worktree 1) → task done → merge
                 → agent 2 (worktree 2) → task done → merge
                 → agent 3 (worktree 3) → task done → merge
                 → main branch (your app)
```

---

## License

MIT
