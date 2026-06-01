# Getting Started

## What is TrashMD?

TrashMD is a 3D asset pipeline tool built for GameMaker developers. Instead of writing one-off format converters, you can import models from plenty file formats, inspect and edit them, and export data directly into your GameMaker project.

Whether you're working with glTF files from Blender, FBX exports from a DCC tool, pixel-art models from Crocotile, or voxel data from Blockbench, TrashMD gives you one place to manage it all.

### Who is it for?

TrashMD is aimed at GameMaker developers who work with 3D assets. If you need to get models, materials, textures, animations, or collision data into your GameMaker project, this tool attempts to handle the pipeline.

---

## Installation

Download the latest release from the [GitHub releases page](https://github.com/TrashBash/Trash-MD/releases). Run the installer or extract the .zip to a location of your choice, then launch TrashMD.

There are no additional dependencies to install. TrashMD runs on Windows and comes with everything it needs.

---

## First Launch

When you open TrashMD for the first time, you'll see the main workspace with several panels arranged around a central 3D viewport.

<img width="1411" height="804" alt="image" src="https://github.com/user-attachments/assets/12118bfb-0970-4e90-a7ea-39fe160a0f2d" />

The default layout includes:

- **Viewport** (center) - The 3D view where you see and interact with your scene
- **Outliner** (right) - The scene tree showing all nodes in your project
- **Inspector** (left) - Properties for whatever you have selected
- **Asset Browser** (left bottom) - All imported assets organized by type
- **Log** (right bottom) - System messages, warnings, and errors

You can rearrange these panels. See [Interface Overview](interface.md) for details on each panel.

---

## Your First Workflow

Here's a quick walkthrough of the basic import-to-export pipeline.

### 1. Create a New Project

Go to **File -> New Project** (or press `Ctrl+N`). Choose a location and name for your project. TrashMD saves projects as `.tmdproj` files.

### 2. Import a Model

Go to **File -> Import** and select a 3D model file. TrashMD supports formats like `.glb`, `.gltf`, `.fbx`, `.obj`, `.blend`, `.bbmod`, and many more. See [Importing](importing.md) for the full list.

<img width="1349" height="601" alt="image" src="https://github.com/user-attachments/assets/ba6d91f2-a368-497c-bc33-5f58d070e377" />

When you import a file, TrashMD creates assets in your project (models, materials, textures) and can optionally place them into your scene automatically. The import dialog lets you choose how to handle this.

After importing, you should see your model in the viewport and its assets in the Asset Browser.

### 3. Look Around

Use the viewport to inspect your model:

- **Middle mouse button** - Orbit the camera
- **Shift + Middle mouse** - Pan the camera
- **Scroll wheel** - Zoom in and out
- **Numpad .** (or `F` key) - Focus the view on your selection

The model's nodes appear in the Outliner on the right. Click a node to select it, and its properties will show up in the Inspector on the left.

### 4. Adjust Materials

If your model came with materials, you can tweak them in the Inspector. Select a mesh node, then look for the material properties. You can change colors, adjust roughness and metallic values for PBR materials, swap textures, and more.

See [Materials](materials.md) for a full breakdown of what you can do with materials.

### 5. Export

When you're ready to export, go to **File -> Export** and choose your target format.
Each format has its own export settings. See [Exporting](exporting.md) for details on each format and its options.

---

## Preferences

You can customize TrashMD through **Edit -> Preferences** - themes, grid settings, FPS limit, undo depth, default file paths, and more. See [Interface Overview](interface.md#preferences) for details.