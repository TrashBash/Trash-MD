# API Reference

Lookup tables for plugin developers. For concepts and usage examples, see the [Plugin Guide](guide.md). For import/export specifics, see the [Importer/Exporter API](importer-api.md).

---

## Built-in Command IDs

Commands are executed via `command_execute("command.id")` or `command_execute("command.id", { params })`.

### File

| ID | Label | Undoable |
|---|---|---|
| `file.new` | New Project | No |
| `file.open` | Open Project | No |
| `file.save` | Save Project | No |
| `file.save_as` | Save Project As | No |

### Edit

| ID | Label | Undoable |
|---|---|---|
| `edit.undo` | Undo | No |
| `edit.redo` | Redo | No |

### Node

| ID | Label | Undoable |
|---|---|---|
| `node.copy` | Copy | No |
| `node.paste` | Paste | Yes |
| `node.delete` | Delete | Yes |
| `node.duplicate` | Duplicate | Yes |
| `node.rename` | Rename | Yes |
| `node.move` | Move Node | Yes |
| `node.transform` | Transform Node | Yes |
| `node.hide_selected` | Hide Selected | Yes |
| `node.show_all` | Show All | Yes |

### Selection

| ID | Label | Undoable |
|---|---|---|
| `selection.select_parent` | Select Parent | No |
| `selection.select_children` | Select Children | No |
| `selection.select_hierarchy` | Select Hierarchy | No |
| `selection.history_back` | Selection History Back | No |
| `selection.history_forward` | Selection History Forward | No |

### Asset

| ID | Label | Undoable |
|---|---|---|
| `asset.create` | Create Asset | Yes |
| `asset.delete` | Delete Asset | Yes |
| `asset.duplicate` | Duplicate Asset | Yes |
| `asset.rename` | Rename | Yes |
| `asset.open` | Open | No |
| `asset.reimport` | Re-import... | No |
| `asset.reimport.fromSource` | Reimport from source | No |
| `asset.reexport` | Re-export | No |
| `asset.hotreload.toggle` | Toggle Hot-reload | Yes |
| `asset.createScript` | Add Script | No |
| `asset.installAsPlugin` | Install as Plugin | No |
| `asset.scope.markExportable` | Mark Scope for Export | No |

### Batch

| ID | Label | Undoable |
|---|---|---|
| `asset.batch.exportAllDirty` | Export Dirty | No |
| `asset.batch.exportScope` | Re-export Scope | No |
| `asset.batch.reimportAllDirtySources` | Reimport All Dirty Sources | No |

### Viewport

| ID | Label | Undoable |
|---|---|---|
| `viewport.tool.translate` | Translate Tool | No |
| `viewport.tool.rotate` | Rotate Tool | No |
| `viewport.tool.scale` | Scale Tool | No |
| `viewport.focus_selected` | Focus Selected | No |
| `viewport.frameAll` | Frame All | No |
| `viewport.resetView` | Reset View | No |
| `viewport.toggle_ortho` | Toggle Orthographic | No |
| `viewport.toggle_edit_mode` | Toggle Edit Mode | No |
| `viewport.toggle_isolation` | Toggle Isolation | No |
| `viewport.duplicate_and_move` | Duplicate and Move | No |
| `viewport.addEmpty` | Add Empty | Yes |
| `viewport.addLight` | Add Light | Yes |
| `viewport.addCamera` | Add Camera | Yes |
| `viewport.view.front` | Front View | No |
| `viewport.view.back` | Back View | No |
| `viewport.view.left` | Left View | No |
| `viewport.view.right` | Right View | No |
| `viewport.view.top` | Top View | No |
| `viewport.view.bottom` | Bottom View | No |
| `viewport.bookmark_save_1` - `_4` | Save Camera Bookmark 1-4 | No |
| `viewport.bookmark_load_1` - `_4` | Load Camera Bookmark 1-4 | No |

### Outliner

| ID | Label | Undoable |
|---|---|---|
| `outliner.addEmpty` | Add Empty | Yes |
| `outliner.addCollection` | Add Collection | Yes |
| `outliner.paste` | Paste | Yes |

### Clip Mixer

| ID | Label | Undoable |
|---|---|---|
| `clipmixer.addTrack` | Add Track | Yes |
| `clipmixer.removeTrack` | Remove Track | Yes |
| `clipmixer.duplicateClip` | Duplicate Clip | Yes |
| `clipmixer.removeClip` | Delete Clip | Yes |
| `clipmixer.toggleSolo` | Toggle Solo | Yes |
| `clipmixer.toggleMute` | Toggle Mute | Yes |
| `clipmixer.resetSpeed` | Reset Speed | Yes |
| `clipmixer.resetTrim` | Reset Trim | Yes |
| `clipmixer.zoomToFit` | Zoom to Fit | Yes |

### Animation

`screen.play`, `screen.stop`, `screen.step`, `animation.bakeBAT` - all non-undoable.

### Code Editor

`codeeditor.new`, `codeeditor.newPlugin`, `codeeditor.open`, `codeeditor.save`, `codeeditor.saveAs`, `codeeditor.saveAsAsset`, `codeeditor.close`, `codeeditor.compile` (Run), `codeeditor.compileAndInstall`, `codeeditor.install`, `codeeditor.find`, `codeeditor.closeFind`, `codeeditor.copy`, `codeeditor.cut`, `codeeditor.paste`, `codeeditor.selectAll`, `codeeditor.toggleComment`, `codeeditor.toggleOutput` - all non-undoable.

### Other Non-Undoable Commands

**Asset Browser:** `assetbrowser.delete`, `assetbrowser.navigate_up`, `assetbrowser.select_all`.

**Log:** `log.clear`, `log.copy`, `log.copySelected`.

**Export:** `export.to_folder`, `project.export`, `project.export_all`.

**GameMaker:** `gm.discover_models`.

**Room:** `room.export`, `room.reload`, `room.close`.

**UI:** `ui.command_search`, `ui.quick_favorites`.

**Window:** `window.open.viewport`, `window.open.outliner`, `window.open.inspector`, `window.open.asset_browser`, `window.open.codeeditor`, `window.open.animplayer`, `window.open.timeline`, `window.open.log`.

### Undoable (Other)

| ID | Label |
|---|---|
| `sceneNode.create` | Create Scene Node |
| `mesh.edit` | Mesh Edit |
| `property.set` | Set Property |

---

## Events

Subscribe with `event_listen("event.name", callback)`. Plugins can also fire custom events with `event_fire("myplugin.eventname", { ... })`.

| Event | Fires when | Payload |
|---|---|---|
| `import.complete` | Import finishes successfully | `{ importFile, assets, path, format, result }` |
| `import.failed` | Import fails | `{ path, format, error }` |
| `import.assetsCreated` | Assets created from import | `{ importFile, assets, path, format }` |
| `import.reload` | Source file is hot-reloaded | `{ importFile, assets, path, format, result }` |
| `export.complete` | Export finishes successfully | `{ asset, path, format, result }` |
| `export.failed` | Export fails | `{ asset, path, format, error }` |
| `plugin.enabled` | A plugin is enabled | `{ plugin }` |
| `plugin.disabled` | A plugin is disabled | `{ plugin }` |
| `project.new` | New project created | `{ project }` |
| `project.saved` | Project saved to disk | `{ project, path }` |
| `project.loaded` | Project loaded from disk | `{ project, path }` |
| `selection.changed` | Node selection changes | `{ node, nodes }` |

---

## Context Menu Keys

Used with `CONTEXT_MENUS.addItem(key, ...)` and `CONTEXT_MENUS.removeItem(key, ...)`.

### Scene Nodes

| Key | Context |
|---|---|
| `node.EMPTY` | Empty node |
| `node.COLLECTION` | Collection node |
| `node.MODEL_INSTANCE` | Model instance |
| `node.MESH_INSTANCE` | Mesh instance |
| `node.COLLISION_INSTANCE` | Collision instance |
| `node.CAMERA` | Camera node |
| `node.LIGHT` | Light node |
| `node.ASSET_INSTANCE` | Asset instance |
| `node.GM_INSTANCE` | GameMaker instance |
| `node.ROOM_CONTAINER` | Room container |

### Assets

| Key | Context |
|---|---|
| `asset.MODEL` | Model asset |
| `asset.MESH` | Mesh asset |
| `asset.MATERIAL` | Material asset |
| `asset.TEXTURE` | Texture asset |
| `asset.ANIMATION` | Animation asset |
| `asset.RIG` | Rig asset |
| `asset.COLLISION_MODEL` | Collision model asset |
| `asset.COLLISION_MESH` | Collision mesh asset |
| `asset.COLLISION_MATERIAL` | Collision material asset |
| `asset.FILE` | File asset |
| `asset.FOLDER` | Folder asset |
| `asset.COLLECTION` | Collection asset |
| `asset.SCRIPT` | Script asset |
| `asset.REFERENCE` | Reference asset |
| `asset.SCENE_TEMPLATE` | Scene template asset |

### Panels

| Key | Context |
|---|---|
| `outliner.empty` | Empty space in Outliner |
| `viewport.empty` | Empty space in Viewport |
| `viewport.node` | Node clicked in Viewport |
| `timeline.clip` | Animation clip |
| `timeline.track` | Animation track |
| `log.entry` | Log entry |
| `codeeditor.text` | Code editor text |

---

## Inspector Tab IDs

Used with `inspector_add_panel_to_tab()`, `inspector_remove_panel_from_tab()`, `inspector_get_tab()`.

| ID | Description |
|---|---|
| `SCENE` | Scene-level properties |
| `OBJECT` | Object properties (selected node) |
| `DATA` | Polymorphic data tab (content varies by node type) |
| `MATERIAL` | Material properties |
| `GAMEMAKER` | GameMaker export settings |
| `OUTPUT` | Export output settings |
| `ACCELERATION` | Collision acceleration structure |

---

## Node Type Strings

Used with `node_get_type()`, `node_find_by_type()`, `scene_create_node()`, etc.

`EMPTY`, `COLLECTION`, `MODEL_INSTANCE`, `MESH_INSTANCE`, `LIGHT`, `CAMERA`, `COLLISION_INSTANCE`, `GM_INSTANCE`, `ROOM_CONTAINER`, `ASSET_INSTANCE`.

---

## Asset Type Strings

Used with `asset_get_all_of_type()`, `asset_create()`, etc.

`MODEL`, `MESH`, `MATERIAL`, `TEXTURE`, `ANIMATION`, `RIG`, `COLLISION_MODEL`, `COLLISION_MESH`, `COLLISION_MATERIAL`, `FILE`, `FOLDER`, `COLLECTION`, `SCRIPT`.

---

## Light Types

Used with `light_get_type()` / `light_set_type()`.

| Value | Type |
|---|---|
| `0` | Directional |
| `1` | Point |
| `2` | Spot |