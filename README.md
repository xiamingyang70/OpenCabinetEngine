# OpenCabinetEngine for FreeCAD

[![tests](https://img.shields.io/badge/pytest-4%20passed-brightgreen)](https://github.com/xiamingyang70/opencabinet)
[![license](https://img.shields.io/badge/license-MIT-blue)](LICENSE)

Generate **structurally verified** wardrobes inside FreeCAD — backed by the
[opencabinet](https://github.com/xiamingyang70/opencabinet) deterministic solver.

Not "draw a box and hope": every generated cabinet passes hard structural gates
before you see a single board —

- **Zero interference** — exact solid boolean checks between all boards
- **Closed dimension chains** — every seam and partition lands where the math says
- **Real back-panel grooves** — cut into the solids, not drawn as lines

## Why

Hand-writing a parametric cabinet spec is error-prone. The #1 failure mode:
column widths that don't put door seams on partition centers. This addon
**solves column widths for you** so every door seam lands exactly where it must.

If your inputs can't produce a valid cabinet, you get the failing rule report —
never a broken model.

## Install

1. Download [`OpenCabinetEngine.zip`](../../releases) (or clone this repo).
2. Copy the `OpenCabinetEngine/` folder into your FreeCAD user modules directory:

   | OS | Path |
   |----|------|
   | Windows | `%APPDATA%\FreeCAD\Mod\` |
   | Linux | `~/.local/share/FreeCAD/Mod/` |
   | macOS | `~/Library/Application Support/FreeCAD/Mod/` |

3. Restart FreeCAD.

> Backed by [opencabinet](https://github.com/xiamingyang70/opencabinet) engine **v0.2.0** — now with cut-list (BOM) generation, stock utilisation and oversize checks.

Works with FreeCAD 1.x. No Python setup needed — the solver is vendored inside.

## Use

1. Switch to the **OpenCabinet Engine** workbench.
2. Click **Generate Wardrobe** in the toolbar.
3. Fill in width / height / depth / bay count, and pick a function per bay
   (hanging rod or shelves).
4. Click **Generate** — boards appear in the active document with grooves cut
   and semantic properties attached.

## Example

Width 1800 × height 2200 × depth 580, 3 bays (shelves):

- 21 boards, all gates PASS
- Column widths solved to 572.6 / 582.8 / 572.6 mm so both door seams sit
  exactly on partition centers (tolerance 1.0 mm; actual ≈ 0.02 mm)

## How it works

```
panel inputs ──> spec_builder.v1 ──> opencabinet solver ──> board_graph
                    (seam-aligned        (hard gates:           │
                     column widths)       interference,          ▼
                                          dimension chains)   groover
                                                                 │
                                        FreeCAD solids ◀────────┘
                                        (Part::Feature + cuts)
```

- `oc_panel.py` — task panel (simplified inputs, gate reports)
- `src/opencabinet/spec_builder.py` — inputs → complete cabinet spec
- `src/opencabinet/oc_build.py` — board graph → FreeCAD solids
- `src/opencabinet/` — vendored solver (solver / groover / contract / verify)

## License

MIT — same as [opencabinet](https://github.com/xiamingyang70/opencabinet).
