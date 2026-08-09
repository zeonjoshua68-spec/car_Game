# car_Game

# Hover-Car Racer

A 3D hover-car racing game built with **TL-Engine**, made as coursework for the *Games Concepts* module (CO1301). The player races a hovering car around a multi-lap circuit against two AI-controlled opponents, dodging obstacles, banking through corners, and managing a damage-and-boost system, all while the entire track layout is loaded in from a plain text file.

---

## What is TL-Engine?

[TL-Engine](https://tl-engine.co.uk/) ("Teach-Learn Engine") is a lightweight 3D graphics engine built on top of DirectX, designed specifically for teaching games programming. It strips away most of the low-level boilerplate of a full engine (device setup, shaders, render pipelines) and exposes a small, beginner-friendly C++ API centred on a handful of core object types:

- **`I3DEngine`** — the engine itself: creates the window, runs the render loop, and reads keyboard/mouse input.
- **`IMesh`** — a 3D model loaded once from a `.x` file (geometry + default texture).
- **`IModel`** — a positioned *instance* of a mesh in the world. Many models can share one mesh.
- **`ICamera`** — a view into the 3D world; can be freely positioned and rotated.
- **`ISprite`** / **`IFont`** — 2D overlays drawn on top of the 3D scene, used here for the HUD.

Because it hides most of the graphics-API complexity, TL-Engine lets students focus on game logic, physics, and collision detection — which is exactly what this project is built around. It's used across a number of UK university games programming courses as a first step before moving on to full engines or raw DirectX/OpenGL.

---

## Features

**Racing & game flow**
- Full state machine: *Waiting to Start → Countdown (3, 2, 1, Go!) → Racing → Finished / Game Over*
- Multi-lap circuit racing (configurable number of laps) with live lap and stage dialogue
- Checkpoints must be crossed in order; a temporary cross marker appears under each one once it's passed
- Live race position, shown against the player and every AI car
- Race winner and final time displayed at the end, with a one-key restart

**Car physics**
- Momentum-based hover-car movement (thrust, drag, momentum — all frame-rate independent)
- Reverse thrust is deliberately weaker than forward thrust
- A boost system: hold Space for extra speed, but hold it too long and it overheats, locking out until it cools down
- Steering gets progressively more sluggish as the car takes damage
- Procedural hover animation — a gentle idle bobble, banking into turns, nose-tilt under acceleration, and a springy "hop" after a collision

**Collision & damage**
- Custom sphere-vs-box and sphere-vs-sphere collision functions, each working out *which axis* was hit so the car bounces realistically off the correct side
- The car (and AI cars) take damage from collisions, with a brief invulnerability window so a single hit only costs health once
- Health affects both steering and whether the boost can be used at all
- Bombs scattered on the track detonate on contact, switching from a small idle fizzle to a full particle explosion

**AI opponents**
- Two non-player cars follow a shared waypoint path around the circuit
- Each eases its speed up and down approaching corners rather than driving at one flat speed
- AI cars take damage from collisions too, and visibly lean when sufficiently damaged

**Visual effects (custom particle system)**
- Exhaust flame particles while boosting
- Constantly-burning fire particles on trackside lamps and on top of water tanks
- Smoke trailing from the player's car once health drops low
- All particles are simple pooled billboards recycled on a timer — no engine particle system, built from scratch

**Data-driven track**
- The entire track — isles, walls, checkpoints, tanks, waypoints, lamps, and bombs — is loaded at runtime from a plain-text `track.txt` file (`Type  X  Z  RotationY  Y` per line), so the layout can be edited without touching the code

**Camera**
- Manual chase camera: mouse-look (pitch clamped so it can't flip upside down), zoom and strafe with the arrow keys, smoothly follows the car
- First-person cockpit view
- Top-down overview
- Instant reset to the default view

---

## Controls

| Key | Action |
|---|---|
| `W` / `S` | Accelerate / reverse |
| `A` / `D` | Turn left / right |
| `Space` | Hold to boost (watch the overheat warning!) |
| `Mouse` | Look around (chase camera) |
| `Arrow keys` | Zoom / strafe the chase camera |
| `1` | Chase camera |
| `2` | First-person camera |
| `3` | Top-down camera |
| `R` | Restart the race (once finished or destroyed) |
| `Esc` | Quit |

---

## Project structure

```
assignment_Two_Games_Concept.cpp   # all game source code (single file)
track.txt                          # track layout, read in at startup
media/                             # meshes, textures, fonts, ui_backdrop.jpg
```

### `track.txt` format

Each line describes one object in the scene:

```
Type          X     Z    RotationY   Y
Checkpoint    0      0    0           0
Wall         -10    48    0           0
Tank          20   110    0           0
TankObstacle   0    20    0           0
Lamp          15    40   45           0
Bomb          -5    70    0           0
```

Adding, removing, or repositioning track pieces is just a matter of editing this file — no recompiling needed.

---

## Technical notes

- Everything is contained in a **single `.cpp` file**, as required by the coursework brief.
- Movement, collisions, and animation all use **frame-time scaling**, so the game runs at a consistent speed regardless of frame rate.
- A minimal `Vector2D` structure (with `Add` and `Scale`) drives the momentum physics.
- Collision resolution works by **directly manipulating the momentum vector's X/Z components** — no full vector-reflection maths, just a simple, readable axis-flip — matching the assignment's explicit requirement to "resolve collisions by manipulating the components of the momentum vector."
- Scene objects, non-player cars, and every particle effect are stored in plain parallel arrays, kept in sync by index, rather than an object-oriented class hierarchy — a deliberate, simpler style for a first games-programming project.

---

## Building

This project requires the TL-Engine SDK (Windows/Visual Studio only). To build:

1. Install TL-Engine (see [tl-engine.co.uk](https://tl-engine.co.uk/)).
2. Create a new TL-Engine project in Visual Studio and drop `assignment_Two_Games_Concept.cpp` in as the source file.
3. Place `track.txt` and the `media` folder alongside the compiled `.exe`.
4. Build and run.

---

## Author

Coursework project for *Games Concepts* (CO1301).
