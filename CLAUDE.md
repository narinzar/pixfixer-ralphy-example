# CLAUDE.md - Chronos Timer App Build Instructions

## Your job
Work through every unchecked task in PRD.json from top to bottom.

For each task:
1. Implement it fully - no stubs, no placeholders, no TODOs
2. Mark it done in PRD.json: change completed false to completed true
3. Move immediately to the next task without asking
4. If a task fails twice, mark it with a note and move on

Do not stop until every task is completed.

## Stack
- UI: customtkinter dark theme only
- Tray: pystray + Pillow
- Sound: pygame mixer only
- Threading: Python threading module
- Python 3.10+ on Windows
- Never use raw tkinter widgets

## Project structure
main.py is entry point only
src/timer.py is Timer class with no UI imports
src/ui.py is main window importing from src/
src/tray.py is pystray integration
src/sound.py is pygame sound

## CRITICAL: How to add UI features
Every new UI feature must go in its own file under src/.
Each feature module exposes a single attach(app) function.
The only allowed change to ui.py is one import plus one attach call per feature.
Never add widget code or logic directly into ui.py.
Hook into timer events by appending never by overwriting:
app.on_complete_hooks.append(my_handler)
app.on_tick_hooks.append(my_tick_handler)

## Rules
- Never ask for permission before editing or creating files
- Never explain what you are about to do just do it
- Never hardcode paths use pathlib.Path(__file__).parent
- Always load assets relative to the script location
- Never touch .env *.lock PRD.json except to mark tasks complete
- All code must run on Windows with Python 3.10+

## Done when
Every task in PRD.json is marked completed true.
Final message: Chronos is ready. Run with: python main.py
