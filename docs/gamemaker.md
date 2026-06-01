# GameMaker Integration

TrashMD is built for GameMaker developers, and its GM integration lets you link a GameMaker project, set up scene nodes as GM instances, and export directly to room files.

> **Work in progress.** The GM integration modifies your GameMaker project's `.yyp`, `.yy`, and `.gml` files directly. Back up your project before syncing. Features may be incomplete and have bugs.

---

## Linking a GameMaker Project

Go to **Project Settings** and select your GameMaker project's `.yyp` file. You can link multiple GM projects to a single TrashMD project, with one designated as the active project.

Once linked, TrashMD scans the project and discovers sprite assets, room definitions, and project structure. You can also run "Discover GameMaker Project" from the command palette to auto-import models from a configured path and match them to GM objects by name.

---

## GM Instance Nodes

Any scene node can carry GameMaker export properties, editable in the Inspector:

| Property | Description |
|---|---|
| Object Name | Which GM object to instantiate (e.g. `obj_tree`) |
| Layer | Which room layer to place on (e.g. `Instances`) |
| Export Enabled | Whether this node exports to a GM room (default: on) |
| Export Children | Whether children also export (default: on) |

---

## Room Export

The room export workflow converts your TrashMD scene into GameMaker room instance data:

1. **Link a GM project** (see above)
2. **Set node properties** - Assign object names and layers to the nodes you want to export
3. **Choose a target room** - Pick from the rooms discovered in your GM project
4. **Export** - TrashMD writes instance data to the room's `.yy` file

Exported data includes position, rotation, scale, object reference, and layer assignment for each node.

Export only writes instance data. It does not create GM objects, sprites, or layers - those must already exist in your GameMaker project.

---

## Room Containers

Room Container nodes represent a GameMaker room in the scene tree. They hold the hierarchy of nodes that map to a specific room file, and appear as tabs in the Outliner alongside the Main Scene.

---

## Bidirectional Sync

TrashMD supports syncing in both directions:

**TrashMD -> GameMaker:** Export scene nodes as room instances with positions, rotations, object references, layers, and creation code.

**GameMaker -> TrashMD:** Import existing room instances back as scene nodes, pulling in positions, rotations, and creation code.

The GM Sync dialog provides granular control over this process: choose a 3D library (BBMOD, SMF, etc.), toggle which components to sync (models, scenes, code), preview changes with a dry-run before committing.

---

## Sidecar Files

TrashMD writes a `trashmd.sidecar.json` file in a `TrashMD/` folder inside your GM project directory. This stores:

- Asset manifest and fingerprints (skip-if-unchanged on re-export)
- Object-to-model mappings
- Room instance tracking
- Sync configuration

This enables incremental updates. Only changed data is re-exported rather than the entire project.

---

## Code Generation

The sync system can auto-generate GameMaker resources to get you started:

- **Model object** - A GM object with data containers for per-instance 3D state
- **Camera object** - A camera controller with orbit controls
- **Scene loader script** - GML script that reads a `scene.json` file and instantiates all 3D models in a room

Generated object and script names are customizable and validated as GML identifiers. The system registers new resources in the `.yyp` automatically.
