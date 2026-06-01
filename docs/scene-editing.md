# Scene Editing

TrashMD organizes your project into three layers: the **data layer** (raw geometry, materials, textures), the **asset layer** (named entries in the Asset Browser), and the **scene layer** (nodes placed in the 3D viewport).

---

## Scene Hierarchy

The scene is a tree of nodes. Every node has a parent (except the root), and can have any number of children. This hierarchy determines how transforms cascade. Moving a parent node moves all of its children along with it.

You manage the hierarchy in the [Outliner](interface.md#outliner) panel. Drag a node onto another to reparent it. The Outliner displays the tree with expand/collapse controls, icons for each node type, and visibility toggles.

---

## Node Types

### Model Instance

An instance of a 3D model asset placed in the scene. Multiple model instances can reference the same underlying asset data, so changing the asset updates every instance. Each instance has its own transform, and you can apply per-instance material overrides.

### Mesh Instance

A standalone geometry node. Similar to a Model Instance but referencing a single mesh rather than a full model with potentially multiple meshes.

### Light

A light source. Three types: **Directional** (scene-wide, like sunlight), **Point** (emits in all directions from a position), and **Spot** (cone of light with inner/outer angles). Each has color, intensity, and type-specific properties. Gizmo visualizations show influence area in the viewport.

### Camera

Defines a view into the scene with field of view, near/far clip planes, and perspective/orthographic toggle.

### Collision

Geometry used for runtime collision detection rather than rendering. See [Collision Meshes](#collision-meshes) below.

### GM Instance

Represents a GameMaker object that will be exported to a room. Properties for object name and layer. See [GameMaker Integration](gamemaker.md).

### Room Container

Represents a GameMaker room. Holds the scene hierarchy that maps to a specific room file. See [GameMaker Integration](gamemaker.md).

---

## Creating and Deleting Nodes

Right-click in the Outliner or viewport and use the **Create** submenu, or use the command palette (F3). To place a model, drag it from the Asset Browser into the viewport or Outliner. Delete with **Delete**, or **Shift+Delete** to also remove the underlying asset.

---

## Selection

Click to select, Shift+Click for range, Ctrl+Click to toggle. TrashMD keeps a selection history - navigate with **Alt+Left/Right**. Hierarchy shortcuts: **[** for parent, **]** for children, **Shift+]** for entire hierarchy.

---

## Transforms

Every scene node has a position, rotation, and scale. Edit directly in the Inspector or use viewport tools: **T** (translate), **R** (rotate), **S** (scale).

When a modal transform is active: press **X/Y/Z** to constrain to an axis, hold **Shift** for finer control, **Alt** to snap to grid, **Ctrl** to snap to the nearest visible node origin on screen.

The viewport gizmo supports world and local space orientation. Press **Shift+D** to duplicate and immediately begin moving.

---

## Visibility and Isolation

**H** hides selected nodes, **Alt+H** shows all. You can also toggle per-node visibility with the eye icon in the Outliner.

**Isolation mode** (/ or Ctrl+I) hides everything except the selected node and its children. Press again to exit.

---

## Collision Meshes

Collision meshes are geometry used for runtime collision detection, separate from renderable models. They appear in the Outliner like any other node.

### Importing Collision

Import a collision format directly (`.zcb`, `.z`) or convert an existing model mesh to collision geometry using the import-as-collision workflow.

### Editing Collision

In edit mode (**Tab** with a collision node selected), box-select individual faces and right-click to assign collision materials. The collision material editor in the Inspector lets you define material properties for your runtime collision system.

### Supported Formats

Import from: ZCB (`.zcb`), KH 358/2 Days (`.z`). Export to: ColMesh with configurable acceleration structures. See [Exporting - ColMesh](exporting.md#colmesh) for options.

TrashMD includes collision presets for common group/material configurations.