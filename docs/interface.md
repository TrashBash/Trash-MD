# Interface Overview

TrashMD's interface is built around dockable panels that you can rearrange to suit your workflow. 

<!-- screenshot: default-layout.png -->

---

## Viewport

The central 3D view where you see and interact with your scene.

<img width="848" height="522" alt="image" src="https://github.com/user-attachments/assets/fa350246-80e9-47e8-bfca-3875afbf1817" />

### Camera

- **Middle mouse** - Orbit
- **Shift + Middle mouse** - Pan
- **Scroll wheel** - Zoom
- **F** - Focus on selection
- **Numpad 1/3/7** - Front/Right/Top view (hold Ctrl for the opposite)
- **Numpad 5** - Toggle perspective/orthographic
- **Ctrl+1-4** / **Alt+1-4** - Save/load camera bookmarks

### Rendering Modes

Switch between Final, Normals, Color ID from the viewport toolbar.

### Overlays

The toolbar has toggles for grid, gizmos, origin axes, wireframe, bounding boxes, skeleton, vertex colors, textures, shading, tonemapping, and selection outline.

### Transform Tools

- **T** - Translate, **R** - Rotate, **S** - Scale
- Press **X/Y/Z** to constrain to an axis
- Hold **Shift** for finer control, **Alt** to snap to grid, **Ctrl** to snap to a nearby node origin
- **Shift+D** - Duplicate and move

### Modes

- **Edit mode** (Tab) - Work with collision mesh faces directly. Box-select faces and right-click to assign collision materials.
- **Isolation mode** (/ or Ctrl+I) - Hides everything except the selected node and its children. Press again to exit.
- **Placement mode** - When placing assets, a ghost preview follows your cursor. Click to place.

---

## Outliner

The scene tree showing all nodes in your project. Nodes are nested by parent-child relationships with expand/collapse, visibility toggles (eye icon), and type icons.

<img width="284" height="359" alt="image" src="https://github.com/user-attachments/assets/714eb9c1-4150-43a1-a4d7-9470fd835eb3" />

**Tabs:** Main Scene (always present) and Room tabs (one per loaded GameMaker room).

**Interactions:** Click to select, Shift+Click for range, Ctrl+Click to toggle, F2 to rename, drag to reparent, right-click for context menu. The search bar at the top filters by name.

**Navigation:** Alt+Left/Right for selection history, [ for parent, ] for children, Shift+] for entire hierarchy.

---

## Inspector

Shows properties for the current selection. The content adapts based on what you've selected - a model node shows transforms and materials, a light shows color and intensity, etc. Properties are organized in collapsible sections.

<img width="377" height="497" alt="image" src="https://github.com/user-attachments/assets/3fe2e06f-cf36-48c8-994e-7f99e59aa0cc" />

---

## Asset Browser

All imported assets in your project, organized into folders.

<img width="648" height="285" alt="image" src="https://github.com/user-attachments/assets/d98c6676-af1e-4f20-b997-30bbf66834c4" />

- **View modes** - List or grid (32-128px icon size)
- **Sort** by name or type, **filter** by asset type, **search** by name (optionally recursive)
- **Navigation** - Breadcrumb bar, back/forward buttons, Backspace to go up, sidebar folder tree
- **Actions** - Right-click for context menu (export, reimport, duplicate, rename, delete, etc.), drag-drop to move or instantiate

---

## Code Editor

Where you write and test plugin scripts using GMLspeak (a GML-like scripting language).

<img width="988" height="588" alt="image" src="https://github.com/user-attachments/assets/5362e230-682b-4fa0-b968-5a798ae09bbf" />

Supports multi-document tabs, syntax highlighting, find/replace, autocomplete, bracket matching, and auto-indentation. Press **F5** to compile and run, **F6** to install as a plugin, **Ctrl+Alt+S** to save as a project asset. Error messages link to specific source lines.

The status bar shows **Sandbox** vs **Trusted** mode. See the [Plugin Guide](plugins/guide.md) for details.

---

## Animation Player and Timeline

The Animation Player provides playback controls (play/pause, step, speed, loop) for imported animations. The Timeline extends this with multi-track clip arrangement, trimming, blending, solo/mute, and animation events.

---

## Log Panel

Displays color-coded system messages (info, warning, error, success, debug). Filter by type, Ctrl+C to copy, Ctrl+Shift+Delete to clear.

---

## Command Palette

Press **F3** (or **Ctrl+Shift+P**) to open a searchable list of every available action. Type to filter, Enter to execute. Right-click an entry to add it to **Quick Favorites** (opened with **Shift+Q**).

---

## Plugin Browser

Browse and install community plugins from within TrashMD. The Plugin Browser connects to the TrashMD-Plugins repository on GitHub and shows available plugins with version info, descriptions, and one-click install. Installed plugins can be enabled, disabled, or removed from **Edit -> Preferences -> Plugins**. See the [Plugin Guide](plugins/guide.md) for writing your own.

## Asset Library

Browse and download ready-made assets from the TrashMD-Assets repository on GitHub. Downloaded assets are cached locally and can be imported into any project.

---

## Workspaces

Two presets are available from the **View** menu:

- **General** - Default layout with viewport, inspector, asset browser, outliner, and log
- **Scripting** - Code editor prominent, suited for plugin development

---

## Preferences

Open **Edit -> Preferences**. Organized into three sections:

**General** - Interface options (tooltips, fonts, confirm on close), viewport settings (FPS limit, MSAA, VSync, grid configuration, gizmo visibility), and animation defaults (FPS, snap to frames, looping, auto-keyframe).

**Customization** - Keybinds editor (see [Keyboard Shortcuts](shortcuts.md)), theme selection, and plugin list.

**Advanced** - System settings (coordinate system axes, log depth, undo stack size, asset cache cap), file paths (temp, cache, projects), and default import/export vertex attributes.