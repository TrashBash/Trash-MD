# Keyboard Shortcuts

Shortcuts in TrashMD are context-sensitive. They only activate when the relevant panel has focus. For example, pressing `Delete` in the Outliner deletes a scene node, while pressing it in the Asset Browser deletes an asset.

You can customize all shortcuts in **Edit -> Preferences -> Keybinds**. TrashMD also supports chord bindings, where a command has multiple key combinations (for example, both `F3` and `Ctrl+Shift+P` open the command palette).

---

## Global

These shortcuts work regardless of which panel has focus.

| Shortcut | Action |
|---|---|
| Ctrl+N | New project |
| Ctrl+O | Open project |
| Ctrl+S | Save project |
| Ctrl+Shift+S | Save project as |
| Ctrl+Z | Undo |
| Ctrl+Y | Redo |
| Ctrl+E | Export project |
| Tab | Toggle edit mode |
| F3 | Open command palette |
| Ctrl+Shift+P | Open command palette (alternate) |
| Shift+Q | Quick Favorites |

---

## Viewport

Active when the 3D viewport is focused.

### Tools

| Shortcut | Action |
|---|---|
| T | Translate tool |
| R | Rotate tool |
| S | Scale tool |

When a modal transform is active, press `X`, `Y`, or `Z` to constrain to an axis. Hold `Shift` for finer control. Hold `Ctrl` during translate to snap to other node origins.

### Navigation

| Shortcut | Action |
|---|---|
| F | Focus on selected |
| Numpad 1 | Front view |
| Ctrl+Numpad 1 | Back view |
| Numpad 3 | Right view |
| Ctrl+Numpad 3 | Left view |
| Numpad 7 | Top view |
| Ctrl+Numpad 7 | Bottom view |
| Numpad 5 | Toggle orthographic/perspective |

### Scene

| Shortcut | Action |
|---|---|
| Delete | Delete selected node |
| Shift+D | Duplicate and move |
| Space | Play/pause animation |
| / | Toggle isolation mode |
| Ctrl+I | Toggle isolation mode (alternate) |

### Camera Bookmarks

| Shortcut | Action |
|---|---|
| Ctrl+1 through Ctrl+4 | Save camera bookmark 1-4 |
| Alt+1 through Alt+4 | Load camera bookmark 1-4 |

---

## Outliner

Active when the scene tree is focused.

| Shortcut | Action |
|---|---|
| Delete | Delete selected node |
| Shift+Delete | Delete selected asset |
| F2 | Rename |
| Ctrl+C | Copy |
| Ctrl+V | Paste |
| Ctrl+D | Duplicate node |
| H | Hide selected |
| Alt+H | Show all |
| Ctrl+R | Reimport asset from source |
| Ctrl+Shift+E | Re-export asset |
| Alt+Left | Selection history back |
| Alt+Right | Selection history forward |
| [ | Select parent |
| ] | Select children |
| Shift+] | Select entire hierarchy |

---

## Asset Browser

Active when the asset browser is focused.

| Shortcut | Action |
|---|---|
| Delete | Delete asset |
| Backspace | Navigate up one folder |
| Ctrl+A | Select all |
| Ctrl+D | Duplicate asset |

---

## Code Editor

Active when the code editor is focused.

| Shortcut | Action |
|---|---|
| Ctrl+N | New script |
| Ctrl+Shift+N | New plugin |
| Ctrl+O | Open file |
| Ctrl+S | Save |
| Ctrl+Shift+S | Save as |
| Ctrl+Alt+S | Save as project asset |
| Ctrl+W | Close document |
| Ctrl+F | Find |
| Escape | Close find bar |
| F4 | Toggle output panel |
| F5 | Compile and run |
| F6 | Install as plugin |
| Ctrl+Shift+F5 | Compile and install |

---

## Timeline

Active when the timeline/clip mixer is focused.

| Shortcut | Action |
|---|---|
| Delete | Remove clip |
| Ctrl+D | Duplicate clip |
| S | Toggle solo |
| M | Toggle mute |
| Home | Zoom to fit |

---

## Log

Active when the log panel is focused.

| Shortcut | Action |
|---|---|
| Ctrl+C | Copy selected entries |
| Ctrl+Shift+Delete | Clear log |

---

## Customizing Shortcuts

Open **Edit -> Preferences -> Keybinds** to view and change any shortcut. You can search for specific commands, change the key binding, choose whether the shortcut triggers on press, release, or click, and enable or disable individual bindings.

Use **Restore Defaults** to reset a single shortcut, or **Restore All Defaults** to reset everything back to the original bindings.