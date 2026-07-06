# Asset Manifest

Every file from the official Dodge the Creeps pack, where it lives, and which
module uses it. Paths are relative to the project root (`res://`).

## `art/` — sprites

| File | Used by | Animation / role |
| --- | --- | --- |
| `playerGrey_walk1.png`, `playerGrey_walk2.png` | Player (Module 1) | `walk` animation (sideways) |
| `playerGrey_up1.png`, `playerGrey_up2.png` | Player (Module 1) | `up` animation (vertical) |
| `enemyWalking_1.png`, `enemyWalking_2.png` | Mob (Module 3) | `walk` mob type |
| `enemySwimming_1.png`, `enemySwimming_2.png` | Mob (Module 3) | `swim` mob type |
| `enemyFlyingAlt_1.png`, `enemyFlyingAlt_2.png` | Mob (Module 3) | `fly` mob type |

Each character is a **2-frame pair** → one looping `SpriteFrames` animation.
The three mob animations share one `AnimatedSprite2D`; the mob script picks one at
random in `_ready()`.

## `audio/` — sound

| File | Used by | Role |
| --- | --- | --- |
| `House In a Forest Loop.ogg` | Module 6 | Background music (loops while playing) |
| `gameover.wav` | Module 6 | One-shot death sound effect |

> Tip: for the `.ogg`, enable **Loop** in the Import dock so the music repeats
> seamlessly. A `.wav` SFX should **not** loop.

## `fonts/`

| File | Used by | Role |
| --- | --- | --- |
| `Xolonium-Regular.ttf` | HUD (Module 5) | Score / message font |
| `FONTLOG.txt`, `LICENSE.txt` | — | Font license — keep when distributing |

## Import notes (Godot)

- The first time you open the project, Godot generates a `.godot/` cache and a
  `<file>.import` file next to each asset. Those are **git-ignored** — don't commit
  them; they regenerate on any machine.
- Because the assets were sorted **before** the first import, no reference paths will
  break. If you move an asset *after* importing, do it from inside Godot's FileSystem
  dock so it fixes references for you.
