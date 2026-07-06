# Glossary — Godot 4 & GDScript terms

A quick-reference for every term used in the [Learning Plan](LEARNING_PLAN.md).
If you can define each of these in your own words, you understand the project.

## Core engine

| Term | Meaning |
| --- | --- |
| **Node** | The smallest building block; one object with one responsibility. |
| **Scene** | A saved tree of nodes (`.tscn`), reusable as a unit. |
| **SceneTree** | The live, running hierarchy of all active scenes/nodes. |
| **Root node** | The top node of a scene; defines what the scene fundamentally *is*. |
| **Instancing** | Creating a live copy of a scene, in the editor or from code. |
| **`PackedScene`** | A scene loaded as a resource, ready to `.instantiate()`. |
| **`res://`** | Path to the project root (read-only at runtime). |
| **`user://`** | Per-user writable path for saves/config. |
| **Composition** | Building complex behavior by nesting small scenes, not deep inheritance. |

## Nodes used in this project

| Node | Role here |
| --- | --- |
| **`Area2D`** | Detects overlaps without physics response (the player). |
| **`RigidBody2D`** | Physics-driven body moved by the engine (the mobs). |
| **`AnimatedSprite2D`** | Plays frame-based animations from a `SpriteFrames` resource. |
| **`CollisionShape2D`** | Defines a node's hitbox, independent of its visual. |
| **`VisibleOnScreenNotifier2D`** | Emits a signal when the node enters/exits the view. |
| **`Timer`** | Fires a `timeout` signal after an interval; drives spawning/scoring. |
| **`Path2D` / `PathFollow2D`** | A curve and a point that slides along it — used to place spawns. |
| **`Marker2D`** | An invisible position reference (e.g., player start). |
| **`CanvasLayer`** | A layer that renders independent of the world camera (the HUD). |
| **`Control` / `Label` / `Button`** | UI nodes with anchor-based layout. |
| **`AudioStreamPlayer`** | Plays music or sound effects. |

## Scripting

| Term | Meaning |
| --- | --- |
| **GDScript** | Godot's built-in Python-like scripting language. |
| **`_ready()`** | Called once when a node enters the tree — setup goes here. |
| **`_process(delta)`** | Called every rendered frame; `delta` = seconds since last frame. |
| **`_physics_process(delta)`** | Called on the fixed physics tick — use for physics. |
| **`delta`** | Time since the last frame; multiply movement by it for frame-rate independence. |
| **Signal** | A message a node emits; others *connect* to react (observer pattern). |
| **`emit_signal` / `signal`** | Declare and fire custom events. |
| **Input Map** | Named input actions (e.g., `move_left`) decoupled from physical keys. |
| **`@export`** | Exposes a variable to the Inspector for live tuning. |
| **`queue_free()`** | Safely deletes a node at the end of the frame. |
| **`Group`** | A tag on nodes enabling broadcast calls via the SceneTree. |
| **Autoload / Singleton** | A node loaded globally and reachable from anywhere. |

## Math & concepts

| Term | Meaning |
| --- | --- |
| **`Vector2`** | A 2D (x, y) value used for position, direction, and velocity. |
| **Normalize** | Scale a vector to length 1 — keeps direction, removes magnitude. |
| **The √2 problem** | Un-normalized diagonal input moves ~1.41× faster than straight. |
| **Clamp** | Constrain a value to a min/max range (keeps player on screen). |
| **Frame-rate independence** | Movement measured per-second (via `delta`), not per-frame. |
| **RNG** | Random number generation: `randi()`, `randf_range()`, seeding. |
| **Object lifecycle** | Create → live → free; unmanaged nodes leak memory. |
| **Observer pattern** | Emitters and listeners communicate without direct references. |

## Shipping

| Term | Meaning |
| --- | --- |
| **Export template** | Engine binary for a target platform, needed to build. |
| **Export preset** | Per-platform build config (name, icon, features). |
| **HTML5 / Web export** | A browser-playable build (WASM + canvas). |
