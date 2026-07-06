# Learning Plan — Dodge the Creeps (Godot 4)

This is an **enhanced** version of the official "Your First 2D Game" tutorial.
Every module keeps the same build target but adds explicit **learning objectives**,
the **concepts behind the clicks**, **checkpoints**, **reflection questions**, and
**stretch goals** so you leave with transferable knowledge — not just a finished game.

> **Rule of this repo:** you write every line of code and build every scene yourself.
> This document tells you *what* to build and *why*, names the nodes and APIs to
> reach for, and asks you to explain your choices — but it never hands you the code.

---

## How to work through a module

1. **Read the objectives** — know what "done" means before you touch the editor.
2. **Study the concepts** — these are the ideas the tutorial glosses over.
3. **Build it yourself** — implement the steps; look up API names in the Godot docs.
4. **Hit the checkpoint** — run the game and confirm the observable behavior.
5. **Answer the reflection questions** out loud or in `PROGRESS.md`. If you can't,
   you didn't learn it yet — go back.
6. **Attempt at least one stretch goal** before moving on.

Estimated time: **6–10 hours total**, one module per sitting.

---

## Concept map (how the pieces connect)

```
                        SceneTree (the running game)
                               │
        ┌──────────────────────┼───────────────────────┐
     Main.tscn             (autoload)                  input map
   (orchestrator)          singletons               (named actions)
        │
   ┌────┼─────────┬──────────────┬─────────────┐
 Player        Timers          HUD          MobSpawn Path
 (Area2D)   (spawn/score)   (CanvasLayer)   (Path2D+PathFollow2D)
   │             │              │                   │
 input →      emit on       signals ←── connect ──┐ │
 movement     timeout       (score, start)        │ instances
   │                                              │ Mob.tscn
 collision ── emit "hit" signal ──────────────────┘ (RigidBody2D)
```

Keep this map open. Each module lights up one part of it.

---

## Module 0 — Project setup & mental model

**Objective:** create a clean Godot 4 project and understand *what a Godot project
actually is* before adding a single node.

### Concepts to internalize
- **Project vs. scene vs. node.** A *node* is one object with one job. A *scene* is a
  reusable tree of nodes saved to disk (`.tscn`). A *project* is a folder with a
  `project.godot` config plus all its scenes and assets.
- **`res://`** is the project root path. Every asset reference is relative to it.
- **Composition over inheritance.** Godot games are built by *nesting* small scenes,
  not by deep class hierarchies. This is the single most important idea in the engine.

### Build steps
> The repo is already scaffolded for you: `project.godot` exists, the folder layout
> (`art/ audio/ fonts/ scenes/ scripts/`) is created, and the official assets are
> sorted (see [`ASSETS.md`](ASSETS.md)). Your job here is to *understand* that setup,
> not recreate it.
1. **Import** the project into Godot (Project Manager → Import → pick `project.godot`).
2. Open the FileSystem dock and confirm the assets sit under `art/`, `audio/`, `fonts/`.
3. Open **Project Settings** and find the window size (480×720) and stretch mode that
   `project.godot` already sets — understand what each does.
4. Note the `.godot/` cache Godot just generated and why it's git-ignored.

### Checkpoint
Project opens, assets are visible in the FileSystem dock, and an empty main window
runs (F5) without errors (Godot will prompt for a main scene — cancel for now).

### Reflection
- Why does Godot save scenes as separate files instead of one big project file?
- What is the difference between the *FileSystem* dock and the *Scene* dock?

### Stretch
- Put the whole project under version control and make your first commit *now*.
- Skim `project.godot` in a text editor — can you find the settings you changed?

---

## Module 1 — The Player scene (nodes & animation)

**Objective:** build the player as a **self-contained, reusable scene** with a
collision shape and a two-state sprite animation.

### New Godot concepts
- **`Area2D`** as the player root — detects overlaps *without* physics push-back.
- **`AnimatedSprite2D`** + **`SpriteFrames`** resource — frame-based animation.
- **`CollisionShape2D`** — the *hitbox*, separate from the *visual*.

### Concepts to internalize
- **Why the root node type matters.** The root defines what the scene *is*. Player is
  an `Area2D` because we care about "did something touch me?", not realistic physics.
- **Visual vs. collision are decoupled.** The sprite and the hitbox are different
  nodes on purpose — you can tune each independently.
- **Node ordering / the tree** determines draw order and signal flow.

### Build steps
1. Create the Player scene with an `Area2D` root; rename it meaningfully.
2. Add `AnimatedSprite2D`; build a `SpriteFrames` resource with two animations:
   - `walk` ← `art/playerGrey_walk1.png` + `playerGrey_walk2.png`
   - `up` ← `art/playerGrey_up1.png` + `playerGrey_up2.png`
3. Scale the sprite down if needed; add a `CollisionShape2D` sized to the body.
4. Save as its own scene under `scenes/`.

### Checkpoint
Opening the Player scene and pressing play on the animation shows the character
animating; the collision shape roughly matches the sprite.

### Reflection
- If you later wanted a "shield" visual, would you add a node or a new scene? Why?
- What breaks if the `CollisionShape2D` has no shape assigned?

### Common pitfalls
- Forgetting to actually assign a shape resource to `CollisionShape2D`.
- Confusing `AnimatedSprite2D` (frames) with `Sprite2D` (single image).

### Stretch
- Add a third animation state and preview it.
- Try `Sprite2D` + `AnimationPlayer` instead and note the trade-offs.

---

## Module 2 — Coding the Player (input, vectors, motion)

**Objective:** make the player move from keyboard input, stay on screen, and pick the
correct animation — all frame-rate independent.

### New Godot concepts
- **`_ready()`** vs **`_process(delta)`** — lifecycle callbacks.
- **`Input.is_action_pressed()`** and the **Input Map** (named actions, not raw keys).
- **Signals** — connect the player's `body_entered` to react to collisions.
- **`hide()` / `queue_free()`** and emitting your own signal.

### Concepts to internalize
- **Vectors as direction + magnitude.** Build a movement `Vector2`, then
  **`.normalize()`** it so diagonal movement isn't faster than straight movement.
  *Understand why the raw diagonal is √2 faster.*
- **`delta` and frame-rate independence.** Multiply movement by `delta` so speed is in
  *pixels per second*, not *pixels per frame*. This is a universal game-dev idea.
- **Clamping to bounds** using the viewport/screen size so the player can't leave.
- **The Input Map indirection** — decoupling "move right" from a specific key is the
  observer/abstraction pattern applied to input.

### Build steps
1. Define input actions (`move_left/right/up/down`) in the Input Map.
2. In `_process(delta)`, assemble a velocity vector from the pressed actions.
3. Normalize, scale by a `speed` export var and `delta`, apply to `position`.
4. Choose animation + flip based on velocity direction.
5. `clamp()` the position to the screen rectangle.
6. Add a `start()` helper and a signal to broadcast when the player is hit.

### Checkpoint
Player moves in 8 directions at consistent speed regardless of frame rate, animates
correctly, and cannot leave the screen.

### Reflection
- Why multiply by `delta`? What visibly happens if you forget?
- Why normalize the vector? Prove the √2 problem to yourself with numbers.
- Why use an Input Map action instead of checking `KEY_D` directly?

### Common pitfalls
- Normalizing a zero vector (guard against it).
- Putting movement in `_physics_process` vs `_process` — know which and why.

### Stretch
- Add an `@export var speed` and tune it live in the Inspector.
- Add acceleration/deceleration instead of instant velocity.

---

## Module 3 — The Enemy / Creep (instancing & randomness)

**Objective:** build a mob scene that spawns with a **random type, direction, and
speed**, moves on its own, and **cleans itself up** when off-screen.

### New Godot concepts
- **`RigidBody2D`** — physics-driven movement (contrast with the player's `Area2D`).
- **`VisibleOnScreenNotifier2D`** — fire a signal when the node leaves the view.
- **`randi()` / `randf_range()`** and randomizing an animation from a list.

### Concepts to internalize
- **Two bodies, two jobs.** Player = `Area2D` (we script its motion). Mob =
  `RigidBody2D` (the physics engine moves it via `linear_velocity`). Knowing *when* to
  let physics drive is a real design decision.
- **Object lifecycle & memory.** Every spawned mob must eventually `queue_free()` or
  they accumulate forever — your first taste of managing object lifetimes.
- **Randomness for variety** — seeding, ranges, and picking from a set.

### Build steps
1. Create the Mob scene: `RigidBody2D` root, `AnimatedSprite2D` (3 mob types —
   `walk`/`swim`/`fly` from the `enemyWalking`, `enemySwimming`, `enemyFlyingAlt`
   PNG pairs in `art/`), `CollisionShape2D`, `VisibleOnScreenNotifier2D`.
2. In `_ready()`, pick a random animation from the sprite frames' animation list.
3. Connect the notifier's `screen_exited` signal to `queue_free()`.

### Checkpoint
Dropping a few mob instances into a test scene shows them drifting, each possibly a
different type, and disappearing (freeing) when they leave the screen.

### Reflection
- Why is the mob a `RigidBody2D` but the player an `Area2D`?
- What happens to performance/memory if you never call `queue_free()`?
- Where does a mob's velocity actually come from — the mob or the spawner?

### Stretch
- Add a rare "fast" mob type with different speed range.
- Log the live node count to prove mobs are actually being freed.

---

## Module 4 — Main scene (composition, spawning, the game loop)

**Objective:** wire everything together — spawn mobs on a timer along a path, run the
score, and manage start/game-over states.

### New Godot concepts
- **Instancing scenes from code** (`PackedScene` → `.instantiate()` → `add_child()`).
- **`Timer`** nodes — a spawn timer and a score timer.
- **`Path2D` + `PathFollow2D`** — spawn mobs at random points around the edge and
  aim them inward.
- **`start_position` / `Marker2D`** for placing the player.

### Concepts to internalize
- **Composition in practice.** The Main scene *owns* instances of Player, Mob, HUD,
  and Timers. This is the concept map made real.
- **Timers as the game's clock.** Decoupling "when to spawn" from `_process` keeps the
  loop readable and tunable.
- **Randomized spawn geometry.** Using a `PathFollow2D` progress ratio to get an
  edge point, then rotating the mob's direction, is a reusable spawner pattern.
- **Game state** — start, playing, game over — even without a formal state machine.

### Build steps
1. Build `Main.tscn` composing Player, a `Path2D` (with a `PathFollow2D` spawn point)
   around the screen edge, a `MobTimer`, `ScoreTimer`, `StartTimer`, and HUD (next module).
2. On `MobTimer.timeout`, instance a mob, place it on a random path point, set its
   direction/velocity, and `add_child()` it.
3. On `ScoreTimer.timeout`, increment score.
4. Write `new_game()` and `game_over()` to start/stop timers and reset state.
5. Connect the player's "hit" signal to `game_over()`.

### Checkpoint
Pressing play spawns a continuous stream of mobs from the edges heading roughly toward
the play field; touching one ends the game; score climbs while alive.

### Reflection
- Why instance mobs from code instead of placing them in the editor?
- What does `PathFollow2D` give you that manual random coordinates don't?
- Trace one mob's lifetime: who creates it, who moves it, who frees it?

### Common pitfalls
- Adding mobs as children of the wrong node (breaks cleanup or z-order).
- Forgetting to randomize `PathFollow2D.progress_ratio` each spawn.

### Stretch
- Make spawn rate ramp up over time (difficulty curve).
- Add a max-mob cap and observe how it changes feel.

---

## Module 5 — HUD (UI, signals, and the observer pattern)

**Objective:** build a heads-up display with a score label, a start button, and
transient messages — communicating with the game **only through signals**.

### New Godot concepts
- **`CanvasLayer`** — UI that ignores camera/world transforms.
- **Control nodes**: `Label`, `Button`, containers, anchors.
- **Custom signals** (`signal start_game`) and connecting them.
- **`Group`s** and `get_tree().call_group()` for broadcast messaging.

### Concepts to internalize
- **The observer pattern is the heart of Godot.** The HUD doesn't know about the Main
  scene's internals; it just *emits* `start_game` and *listens* for score updates.
  Loose coupling like this is what makes scenes reusable.
- **UI layout via anchors**, not hard-coded pixel positions.
- **Why a `CanvasLayer`** — so the HUD stays put while the world scrolls/zooms.

### Build steps
1. Build the HUD scene on a `CanvasLayer`: score `Label`, message `Label`,
   `StartButton`, and a message `Timer`. Apply `fonts/Xolonium-Regular.ttf` to the
   labels/button via a theme or font override.
2. Add `show_message()`, a game-over sequence, `update_score()`, and a `start_game`
   signal emitted by the button.
3. In Main, connect HUD's `start_game` → `new_game()`, and update the HUD's score on
   each `ScoreTimer.timeout`.

### Checkpoint
A start button begins the game and hides itself; the score updates live; "Game Over"
shows on death and the button returns after a short delay.

### Reflection
- Why should the HUD *not* directly read the game's score variable?
- What is the advantage of `start_game` being a signal vs. a direct function call?
- When would you use a `Group` broadcast instead of a one-to-one signal connection?

### Stretch
- Add a high-score that persists across runs (a first look at saving data).
- Add a pause menu on a second `CanvasLayer`.

---

## Module 6 — Polish & Export (audio, config, shipping)

**Objective:** turn a prototype into something you can hand to a friend — background,
music, sound effects, a quit key, and an actual exported build.

### New Godot concepts
- **`AudioStreamPlayer`** for music and one-shot SFX.
- **`ParallaxBackground`** or a simple `ColorRect`/texture background.
- **Input Map** actions for non-movement keys (e.g., quit).
- **Autoload / singletons** (optional) for global game state or audio.
- **Export templates** and **export presets** (Windows/Linux/macOS/Web).

### Concepts to internalize
- **Feedback loops.** Audio and visual feedback are *game feel*, not decoration — they
  tell the player what happened.
- **Autoload = a globally accessible node.** Understand when a singleton is the right
  tool (global music) vs. a code smell (everything global).
- **Building for distribution.** Export templates, platform presets, and the
  difference between running in-editor and a standalone binary.

### Build steps
1. Add a background layer behind gameplay.
2. Add music (`audio/House In a Forest Loop.ogg` — enable **Loop** in the Import
   dock; play on `new_game`, stop on `game_over`) and a death SFX
   (`audio/gameover.wav`, one-shot).
3. Add a `quit`/`ui_cancel` action to exit.
4. (Optional) Create an autoload for shared state or a music manager.
5. Install export templates and export a build for at least one platform;
   also try a **Web (HTML5)** export and run it in a browser.

### Checkpoint
The exported build runs *outside* the editor, with sound and background, and can be
zipped and shared.

### Reflection
- What actually goes into an exported build vs. what stays in the project?
- Why might the Web export behave differently (audio autoplay, performance)?
- Which of your global-ish variables *deserved* to be an autoload?

### Stretch
- Add a settings toggle to mute audio.
- Publish the Web build to itch.io or GitHub Pages and share the link.

---

## Capstone challenges (prove mastery)

Pick a few once the base game runs. Each forces you to reuse concepts without a guide:

1. **Power-ups:** spawn a rare pickup (`Area2D`) that grants temporary invulnerability.
2. **Difficulty curve:** mob speed and spawn rate scale with elapsed time.
3. **Lives system:** 3 hits instead of instant death; HUD shows hearts.
4. **State machine:** refactor start/playing/game-over into an explicit state enum.
5. **Juice:** screen shake, hit flash, particle burst on death.
6. **Save system:** persist high score to `user://` and load it on launch.
7. **New enemy behavior:** a homing creep that steers toward the player.

For each: write one paragraph in `PROGRESS.md` explaining *which nodes and concepts*
you reused and what was new.

---

## Self-assessment: you've mastered this project when you can…

- [ ] Explain why the player is an `Area2D` and the mob a `RigidBody2D`.
- [ ] Explain `delta` and vector normalization to someone else.
- [ ] Add a new scene and instance it from code without looking it up.
- [ ] Connect two scenes using only signals, with no direct references.
- [ ] Describe the full lifetime of a spawned mob from creation to free.
- [ ] Export a runnable build from scratch.

When every box is checked, start your **second** game from an empty project — no
tutorial.
