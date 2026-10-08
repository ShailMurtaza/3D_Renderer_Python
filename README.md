# 3D Renderer

A simple wireframe 3D renderer written in Python using **Pygame** for drawing and **NumPy** for vector math. It loads `.obj` models, transforms them in 3D, clips them against the viewing frustum, and projects them onto a 2D window in real time.

## Features

- **OBJ model loading** — parses vertices and faces into NumPy arrays, rendering only the wireframe edges
- **3D transformations** — rotation around X/Y/Z axes and translation, all applied via 4×4 matrices
- **Perspective projection** — 90° FOV projection matrix with perspective divide
- **Frustum clipping** — 3D Cohen-Sutherland line clipping against all six frustum planes (near plane clipped first)
- **HUD** — live FPS counter and current rotation angles
- **Delta-time based controls** — movement feels the same regardless of frame rate
- Bundled sample models: `cube.obj`, `sphere.obj`, `teapot.obj`, `airboat.obj`, `13463_Australian_Cattle_Dog_v3.obj`

## Requirements

- Python 3.x
- [pygame](https://www.pygame.org/)
- [numpy](https://numpy.org/)

```bash
pip install pygame numpy
```

## Usage

```bash
python main.py
```

To switch models, edit the commented-out lines at the top of `main.py`:

```python
# POINTS, EDGES = parse_obj_to_numpy("models/cube.obj")
# POINTS, EDGES = parse_obj_to_numpy("models/sphere.obj")
POINTS, EDGES = parse_obj_to_numpy("models/teapot.obj")
```

## Controls

| Key | Action |
| --- | --- |
| `W` / `S` | Translate along Y axis (up / down) |
| `A` / `D` | Translate along X axis (left / right) |
| `Q` / `E` | Translate along Z axis (forward / back) |
| `I` / `K` | Rotate around X axis |
| `J` / `L` | Rotate around Y axis |
| `U` / `O` | Rotate around Z axis |
| Mouse wheel | Zoom in / out |
| Close window | Quit |

## Project structure

```
3D_Renderer/
├── main.py            # Entry point: render loop, drawing, HUD
├── events.py          # Keyboard/mouse input handling, transformation state
├── transformations.py # Matrix math: projection, rotation, translation, centering
├── clip.py            # 3D Cohen-Sutherland line clipping against the frustum
├── obj_loader.py      # OBJ parser -> (vertices, edges) NumPy arrays
└── *.obj              # Sample models
```

## How it works

Each frame the renderer runs this pipeline:

1. **Load** vertices and edges from an OBJ file (`obj_loader.py`)
2. **Center** the object at the origin (mean of all vertices)
3. **Transform** — apply rotations and translations as 4×4 matrices (`transformations.py`)
4. **Project** — multiply by the perspective projection matrix
5. **Clip** — discard/cut edges against the frustum with Cohen-Sutherland (`clip.py`)
6. **Divide & map** — perspective divide (`x/w`, `y/w`), then map NDC coordinates to screen pixels
7. **Draw** — draw each surviving edge as a 1px line with Pygame
