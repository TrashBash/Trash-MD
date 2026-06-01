# Exporting

TrashMD can export your assets to a range of formats suited for GameMaker. This page covers how exporting works, what formats are available, the export profile system, and batch operations.

---

## How to Export

- **File -> Export** - Export the current project or selection
- **Right-click an asset** in the Asset Browser or Outliner and choose **Export**

<img width="1253" height="629" alt="image" src="https://github.com/user-attachments/assets/8ad8ad9d-f1d8-4086-9230-d3058c222314" />

When exporting, you choose a target format and configure format-specific options. TrashMD remembers your export settings per asset so you can quickly re-export without reconfiguring everything.

---

## Supported Export Formats

### 3D Models

| Format | Extensions | Geometry | Materials | Textures | Animation | Rigs | Notes |
|---|---|---|---|---|---|---|---|
| TMD | `.tmdl` | Yes | Yes | Yes | Yes | Yes | TrashMD's own runtime library format. Public repo not yet available. |
| glTF 2.0 | `.glb`, `.gltf` | Yes | Yes (PBR) | Yes | Yes | Yes | Choose binary (`.glb`) or JSON+files (`.gltf`). |
| BBMOD | `.bbmod` | Yes | Yes | Yes | Yes | Yes | BlueBurn's community 3D library. Exports `.bbmat` and `.bbanim` sidecars. |
| SMF | `.smf` | Yes | Yes | Yes | Yes | Yes | Snidrs Model Format. |
| DmrVBM | `.vbm` | Yes | Yes | Yes | Yes | Yes | Sandman13sq's format with optional compression. |
| Penguin | `.derg` | Yes | Yes | Yes | No | No | DragoniteSpam's format. |
| VBUFF | `.vbuff` | Yes | No | Optional | No | No | Raw GameMaker vertex buffer data. Can export textures as PNG sidecars. |

### Collision

| Format | Extensions | Notes |
|---|---|---|
| ColMesh | `.colmesh` | TheSnidr's collision format with spatial acceleration options. |

### Scene Data

| Format | Extensions | Notes |
|---|---|---|
| Scene JSON | `.json` | JSON representation of the scene hierarchy. |

---

## Format-Specific Notes

Each format presents its options in the export dialog. Here are the notable ones:

### TMD (Public repo not yet available)

ou choose which companion files to generate (`.tmtx` textures, `.tmca` animation, `.tmcl` collision) alongside the main `.tmdl`.

Key settings:
- **Model Orientation** - Reorient exported geometry to match your target coordinate system. Built-in profiles: "Y Up, -Z Forward" (glTF/OpenGL) and "Z Up, -Y Forward" (Blender). This applies a pure rotation to geometry, transforms, bind matrices, keyframes, and collision. No mirroring, winding is preserved.
- **Texture Mode** - How textures are referenced: None, Included (embedded `.tmtx`), External `.tmtx`, by Filename, or by Asset Name. A warning shows if you disable textures with no `.tmtx` output.
- **Constant Tolerance** - How close keyframe values must be to collapse into a constant. Lower = more detail, higher = smaller file. Default 0.0001.
- **Rotation Type** - Quaternion, 3x3 Matrix, or 4x4 Matrix for animation channels.
- **Dual Quaternion Skinning** - Alternative to linear blend skinning.
- **Node Transformation Type** - 4x4 Matrix or TRS.
- **Combine Flags** - Merge meshes, textures, or models.

### BBMOD

Exports `.bbmat` (materials + PNG textures) and `.bbanim` (one per animation) as sidecars. Configurable animation optimization level and texture source (auto, datafiles, IDE sprites).

### SMF

Version 12 recommended. Options for embedding textures and toggling rig/animation export.

### DmrVBM

Configurable vertex attributes (position, normal, tangent, bitangent, color, UV, UV2, bones), optional zlib compression for textures and/or the entire file, and optional prism collision export.

### VBUFF

Select a vertex format, optionally combine models/meshes, and toggle texture export.

### ColMesh

Choose an acceleration structure for runtime lookup speed:
- **List** - No optimization
- **Octree** - 3D subdivision, best general-purpose
- **Quadtree** - 2D subdivision in XZ
- **Spatial Hash** - Grid-based, fast for evenly distributed geometry

Also configurable: region/cell size, max objects per region, double-sided triangles, smooth normals, and collision group assignment (Solid, Trigger, Level, Gravity, etc.).

### Scene JSON

Options for including invisible nodes, transforms, GM export data (object name, layer), and pretty-printing.

---

## Export Profiles

Export profiles save a set of format options for reuse. Useful when you always export with the same settings. Create and manage them in the export dialog or **Project Settings -> Export Profiles**.

---

## Batch Operations

- **Export All Dirty** - Re-export every asset modified since its last export
- **Export Scope** - Re-export assets under the current selection
- **Reimport All Dirty Sources** - Reimport assets whose source files changed on disk

Batch operations process one item per frame to keep the UI responsive, show a progress overlay, and can be cancelled. Failed items are skipped with a warning. Find these in the **File** menu or command palette.