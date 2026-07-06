# Dodge the Creeps — A Learning-First Godot 4 Project

> A hands-on re-build of the Godot official **"Your First 2D Game"** tutorial,
> restructured to **maximize what you learn** rather than just what you ship.

This repository is my personal learning journal for building a complete arcade-style
2D game in **Godot 4**. The player character dodges an endless swarm of enemies
("creeps") for as long as possible while a score counts up.

The goal is **not** to copy-paste a finished game. The goal is to understand every
node, signal, and line of GDScript well enough to build the next game without a
tutorial.

---

## How this repo is used

- **I write 100% of the game code and scenes myself.** No code is generated for me.
- The `docs/` folder holds the **learning plan**, a **glossary**, and a **progress
  tracker** — these are the guardrails, not the answers.
- Each module in the plan ends with a **checkpoint**, **reflection questions**, and
  **stretch goals** designed to push understanding past the tutorial's baseline.

If you are reading this and want to follow along: work through
[`docs/LEARNING_PLAN.md`](docs/LEARNING_PLAN.md) top to bottom, one module per sitting.

---

## What you build

| System | You learn |
| --- | --- |
| Player scene + animation | Nodes, scenes, `AnimatedSprite2D`, the SceneTree |
| Player script | Input handling, vectors, frame-rate-independent movement, clamping |
| Enemy (creep) | Instancing, `RigidBody2D`, randomization, self-destruction |
| Main scene | Composition, timers, spawning, `PathFollow2D`, game loop |
| HUD | UI nodes, `signals` (observer pattern), `CanvasLayer`, `Group` messaging |
| Polish | Audio, input maps, autoload/singletons, and **exporting** a shareable build |

---

## Tech stack

- **Engine:** Godot **4.x** (4.2 or newer recommended)
- **Language:** GDScript
- **Assets:** the official Dodge the Creeps art/audio pack (imported by you)
- **License:** [MIT](LICENSE)

---

## Repository layout

```
dodge-the-creeps/
├── README.md              ← you are here
├── LICENSE
├── .gitignore             ← Godot-aware ignore rules
├── project.godot          ← Godot project manifest (makes the folder importable)
├── art/                   ← sprites & images (player + 3 enemy types)
├── audio/                 ← music (.ogg) & sound effects (.wav)
├── fonts/                 ← Xolonium font (used by the HUD) + its license
├── scenes/                ← your .tscn scene files (Player, Mob, Main, HUD…)
├── scripts/               ← your .gd scripts
└── docs/
    ├── LEARNING_PLAN.md   ← the 7-module enhanced curriculum
    ├── ASSETS.md          ← every asset file, where it lives & what uses it
    ├── GLOSSARY.md        ← every Godot / GDScript term, defined
    └── PROGRESS.md        ← checklist + reflection log
```

The `art/`, `audio/`, `scenes/`, and `scripts/` folders start empty (tracked via
`.gitkeep`) and fill up as you build each module.

---

## Getting started

1. Install **Godot 4.x** (standard build — no C# needed) from
   <https://godotengine.org/download>.
2. Clone this repo and open the folder in the Godot **Project Manager**
   (once `project.godot` exists).
3. Open [`docs/LEARNING_PLAN.md`](docs/LEARNING_PLAN.md) and start at **Module 0**.
4. After each module, update [`docs/PROGRESS.md`](docs/PROGRESS.md).

---

## Reference

- Official tutorial: <https://docs.godotengine.org/en/stable/getting_started/first_2d_game/index.html>
- Godot GDScript reference: <https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/index.html>

## Credits

Built by [lorendw7](https://github.com/lorendw7) as a Godot learning exercise.
Based on the official Godot Engine documentation (CC BY 3.0) and its Dodge the
Creeps demo assets.
