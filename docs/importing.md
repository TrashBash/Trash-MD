# Importing

TrashMD can import 3D models, textures, collision data, and animations.

---

## How to Import

There are a couple ways to bring files into your project:

- **File -> Import** - Opens a file dialog where you can select one or more files
- **Drag and drop** - Drop files directly onto the TrashMD window

<!-- screenshot: import-dialog.png -->

### Import Strategy

When you import a file, TrashMD asks how you want to handle the result:

- **Assets only** - Create asset entries (models, materials, textures) in the Asset Browser without adding anything to the scene. Useful when you want to organize before placing.
- **Auto-instantiate** - Create the assets and also place them into the scene automatically. This is the faster option when you just want to see the model right away.

### Axis Correction

Different 3D tools use different coordinate systems. TrashMD can automatically apply axis corrections during import so your models appear with the right orientation. It is planned so you can configure this per-format. For now set a global default in **Edit -> Preferences -> Import Defaults**.

---

## Supported Import Formats

### 3D Models

| Format | Extensions | Geometry | Materials | Textures | Animation | Rigs | Notes |
|---|---|---|---|---|---|---|---|
| glTF 2.0 | `.glb`, `.gltf` | Yes | Yes (PBR) | Yes | Yes | Yes | Industry standard. `.glb` is the single-file binary variant. |
| FBX | `.fbx` | Yes | Yes | Yes | Yes | Yes | Autodesk binary format (v7.x). Supports embedded textures. |
| Blender | `.blend`, `.blend1` | Yes | Yes | Yes | No | No | Direct Blender file import. Animation and rigging not yet wired. |
| Wavefront OBJ | `.obj` | Yes | Yes (basic) | Yes | No | No | Reads companion `.mtl` files for materials and textures. |
| Collada | `.dae` | Yes | Yes | Yes | No | No | XML-based interchange format. Buggy - not recommended. |
| BBMOD | `.bbmod` | Yes | Yes | Yes | Yes | Yes | BlueBurn's community 3D library for GameMaker. Reads `.bbmat` and `.bbanim` sidecars. |
| SMF | `.smf` | Yes | Yes | Yes | Yes | Yes | Snidrs Model Format for GameMaker. |
| Penguin | `.derg` | Yes | Yes | Yes | No | No | DragoniteSpam's format. |
| DmrVBM | `.vbm` | Yes | Yes | Yes | Yes | Yes | Sandman13sq's Blender-to-GameMaker format. |
| TMD | `.tmdl` | Yes | Yes | Yes | No | Yes | TrashMD's own runtime library format. Also reads `.tmtx` and `.tmcl`. |
| VBUFF | `.vbuff` | Yes | No | No | No | No | Raw GameMaker vertex buffer data. |
| Crocotile 3D | `.crocotile`, `.c3dp` | Yes | Yes | Yes | No | No | Pixel-art 3D tile editor. Defaults to point-filter and alpha-mask. |
| Blockbench | `.bbmodel` | Yes | Yes | Yes | No | No | Low-poly voxel modeling tool. |
| PicoCAD | `.txt` | Yes | Yes | Yes | No | No | PICO-8 3D modeling tool. Embeds palette texture. |
| STL | `.stl` | Yes | No | No | No | No | 3D printing format. Triangles only. |
| Nintendo NSBMD | `.nsbmd` | Yes | Yes | Yes | Yes | Yes | Nintendo DS model format. See companion files below. |
| Kingdom Hearts 2 FM | `.map` | Yes | Yes | Yes | No | No | Square-Enix proprietary format. |

### Collision Formats

| Format | Extensions | Notes |
|---|---|---|
| ZCB | `.zcb` | Zelda: Spirit Tracks collision data. |
| KH 358/2 Days | `.z` | Kingdom Hearts: 358/2 Days collision. |

### Textures and Images

Standard image files (`.png`, `.jpg`, `.jpeg`, `.bmp`, `.tga`) can be imported as texture assets.

### Nintendo DS Companion Files

When importing NSBMD models, TrashMD can scan for companion files in the same directory:

- `.nsbtx` - Texture data with palette support
- `.nsbca` - Character (skeletal) animation
- `.nsbta` - Texture animation
- `.nsbtp` - Texture pattern animation
- `.nsbva` - Visibility animation

---

## Format-Specific Notes

Most formats present their options in the import dialog. Here are the ones worth calling out:

### FBX

Notable: unit mode (auto-detect or force a scale factor, default 0.01 for cm-to-m), toggling materials/textures/animations/skins individually, flip texcoord Y, and generating missing normals.

### OBJ

Can split meshes by `o` (object) or `g` (group) tags. Automatically reads companion `.mtl` files from the same directory.

### Blender

Direct `.blend` file import. You can toggle materials, textures, and empties. Animation and rigging are not yet supported. Generates missing normals if needed.

### Crocotile 3D

Defaults to point-filter sampling and alpha-mask cutout, matching the pixel-art style of Crocotile projects. Can import prefabs and generate normals (smooth or flat).

### NSBMD (Nintendo DS)

Can scan the same directory for companion files: NSBTX (textures), NSBCA (character animation), NSBTA (texture animation), NSBTP (texture patterns), NSBVA (vertex animation). Optionally select a reference rig for bone reordering.

### VBUFF

Requires you to select the vertex format layout to interpret the raw buffer data correctly.

---

## Reimporting and Hot-Reload

TrashMD tracks the source file path for every import. To update an asset after modifying the original file, select it and press **Ctrl+R** (or right-click -> Reimport). Your scene setup and overrides are preserved.

TrashMD can also detect when source files change on disk and offer to reimport them automatically, which is useful when iterating between a modeling tool and TrashMD.

---

## Import as Collision

You can import mesh data as collision geometry rather than as a renderable model. Use the context menu or import dialog to specify that the geometry should be treated as collision data.

---

## Batch Import

Select multiple files in the import dialog to import them all at once. For batch reimporting changed sources, see [Batch Operations](exporting.md#batch-operations).