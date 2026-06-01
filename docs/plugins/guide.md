# Plugin Guide

TrashMD plugins are scripts written in a GML-like language (powered by GMLspeak/Catspeak) that can register commands, add context menu items, react to events, create custom importers/exporters, extend the inspector, create custom windows, and manipulate the scene.

---

## Getting Started

A plugin is a folder containing at least one `__main__.tmdplugin` file:

```
my_plugin/
  __main__.tmdplugin      <- Entry point (required)
  helpers.tmdplugin       <- Optional support file
```

To create one:

1. Create a folder with a `__main__.tmdplugin` inside it
2. Add a `@plugin` header with metadata
3. Implement `register()` and `unregister()` lifecycle functions
4. Place the folder in the plugins directory, or use the code editor workflow below

### Scripts vs Installed Plugins

Both are the same format. The difference is where they live:

- **Script asset** - Lives in your `.tmdproj`, shows in the Asset Browser. Good for project-specific automations.
- **Installed plugin** - Lives in the plugins directory, loaded across all projects. Shows in Preferences -> Plugins.

A script becomes installable when it declares a `@plugin` header. From the code editor:

- **F5** runs it directly (preview without installing)
- **F6** installs it as a plugin
- **Ctrl+Alt+S** saves the buffer as a project script asset

F5 honors the same permissions the plugin would get at install time. Scripts without a `@plugin` header run with full access (the editor status bar shows Sandbox vs Trusted).

### Templates

TrashMD ships with plugin templates covering common patterns: commands, context menus, inspector tabs, panel injection, custom windows, animation creation, animation export, and rigged animation. Create a new script in the code editor and pick a template to start from.

---

## Plugin Header

Every `__main__.tmdplugin` must start with a `@plugin` block:

```js
/*
@plugin
name: My Plugin
description: A short description of what this plugin does.
version: 1.0.0
author: YourName
permissions: core, commands, context
*/
```

| Field | Required | Description |
|---|---|---|
| `name` | Yes | Display name |
| `description` | Yes | Brief description |
| `version` | Yes | Semantic version (e.g. `1.0.0`) |
| `author` | Yes | Author name |
| `permissions` | Yes | Comma-separated permission list |
| `min_version` | No | Minimum TrashMD version required |

---

## Permissions

Plugins declare which APIs they need. `core` is always granted.

| Permission | Grants access to |
|---|---|
| `core` | `CLASSES`, `LOG`, `WARN`, `extend`, math, string, array, struct, JSON, type-checking, color functions |
| `commands` | Command registry (`COMMANDS`) |
| `context` | Selection, scene context (`CONTEXT`), context menus (`CONTEXT_MENUS`) |
| `events` | Pub/sub event bus (`EVENTS`) |
| `formats` | File format registry (`FILE_FORMATS`) |
| `filesystem` | File and buffer I/O, sandboxed to plugin directory |
| `tmd` | Full TrashMD internals: `TMD`, `DATA`, scene creation, node transforms, light/camera/material properties, quaternion/matrix math |

Default permissions (if none specified): `core`, `commands`, `context`, `formats`.

---

## Lifecycle

Every plugin must define two top-level functions:

```js
register = function()
{
    command_register("myplugin.action", MyCommand);
    class_register("MyCmd", MyCommand);
    LOG("My Plugin: ready");
}

unregister = function()
{
    class_unregister("MyCmd");
    LOG("My Plugin: disabled");
}
```

`register()` is called when the plugin is enabled. `unregister()` is called when it's disabled.

### Automatic Cleanup

When you register through the **utility wrappers** (`class_register`, `command_register`, `format_register`, `event_listen`, `editor_register_symbol`, `inspector_register_tab`, `inspector_add_panel_to_tab`, `inspector_register_data_layout`, `interface_register`), the host tracks the registration and automatically reverses it on disable or if `register()` throws partway through. Your `unregister()` only needs to handle plugin-specific teardown (closing files, deleting data, etc.).

Direct registry calls (`COMMANDS.Register(...)`, `EVENTS.EventAddListener(...)`, etc.) bypass tracking and won't be cleaned up automatically.

---

## Creating Commands

Commands are how plugins add functionality. A command extends `TrashMD_Command` and provides an ID, label, `Poll()`, and `Execute()`.

```js
MyCommand = extend("TrashMD_Command", function()
{
    self.id       = "myplugin.greet";
    self.label    = "Say Hello";
    self.category = "My Plugin";

    self.Poll = function()
    {
        var _node = selection_get();
        if (_node == undefined)
        {
            return undefined;
        }
        return { node: _node };
    }

    self.Execute = function(_params)
    {
        LOG("Hello, " + node_get_name(_params.node) + "!");
    }
});
```

`Poll()` returns a params struct when the command is available, or `undefined` to disable it. `Execute()` receives whatever `Poll()` returned.

### Undoable Commands

For operations that support Ctrl+Z, set `self.undoable = true` and implement `Undo`:

```js
self.undoable = true;

self.Execute = function(_params)
{
    var _node = _params.node;
    var _oldName = node_get_name(_node);
    node_set_name(_node, "Renamed");
    return { node: _node, oldName: _oldName };
}

self.Undo = function(_undoData)
{
    node_set_name(_undoData.node, _undoData.oldName);
}
```

### Registering Commands

```js
command_register("myplugin.greet", MyCommand);  // auto-tracked (preferred)
command_execute("myplugin.greet");               // execute by ID
```

---

## Context Menus

With the `context` permission, add items to right-click menus:

```js
register = function()
{
    contextmenu_add_command_item("node.MODEL_INSTANCE", "myplugin.action");
}
```

For items that aren't tied to a registered command, use a callback instead:

```js
contextmenu_add_command("node.MODEL_INSTANCE", "Count Children", function()
{
    var _sel = selection_get();
    if (_sel != undefined)
    {
        LOG("Children: " + string(node_get_child_count(_sel)));
    }
});
```

Context menu keys determine where items appear. Each node type has its own key: `"node.MODEL_INSTANCE"`, `"node.LIGHT"`, `"node.CAMERA"`, etc. Asset keys follow the same pattern: `"asset.MODEL"`, `"asset.MATERIAL"`, etc. Panel keys include `"outliner.empty"`, `"viewport.empty"`, and others. See [API Reference](api-reference.md#context-menu-keys) for the full list.

---

## Events

With the `events` permission, subscribe to system or custom events:

```js
var _handle = event_listen("import.complete", function(_args)
{
    LOG("Imported: " + string(_args.path));
});

// Unsubscribe later
event_unlisten(_handle);

// Fire custom events for inter-plugin communication
event_fire("myplugin.myevent", { data: "payload" });
```

See [API Reference](api-reference.md#events) for built-in event names and payloads.

---

## Inspector Customization

With the `tmd` permission, plugins can create custom inspector tabs, inject panels into existing tabs, and register data layouts. The inspector uses a **property composition pattern** - define panels by calling methods like `.Bool()`, `.Float()`, `.Color()` on a `TrashMD_UIPanel` base class. Properties bind to the selected object automatically and create undoable commands when edited.

### Custom Inspector Tabs

Define panel classes, register them, and compose them into a tab:

```js
MyPanel = extend("TrashMD_UIPanel", function()
{
    self.name = "My Properties";
    self.SetNodeContext();

    var _table = self.PropertyTable("Props");
    _table.StringInput("name", "Name");
    _table.Bool("visible", "Visible");
});

register = function()
{
    class_register("MyPanel", MyPanel);

    inspector_register_tab({
        id:       "MY_TAB",
        name:     "My Tab",
        category: "object",
        priority: 150,
        panels:   ["MyPanel"]
    });
}
```

Use `SetTransformContext()` instead of `SetNodeContext()` for panels that edit position, rotation, or scale.

Tab config fields:

| Field | Default | Description |
|---|---|---|
| `id` | (required) | Unique tab identifier |
| `name` | (required) | Display name |
| `category` | `"data"` | `"global"`, `"object"`, `"data"`, or `"material"` |
| `priority` | `500` | Sort order (lower = further left) |
| `typeFilter` | `[]` | Node types to show for. Empty = all. |
| `panels` | `[]` | CLASSES keys to compose into the tab |
| `renderCallback` | -- | Custom ImGui render function (see [ImGui Widgets](#imgui-widgets)) |

### Panel Injection

Add a panel to a built-in tab without replacing it:

```js
inspector_add_panel_to_tab("OBJECT", "MyPanel", "append");
```

Position: `"append"` (default), `"prepend"`, or `"after:PanelName"`. Remove with `inspector_remove_panel_from_tab("OBJECT", "Panel Name")`.

Built-in tab IDs: `SCENE`, `OBJECT`, `DATA`, `MATERIAL`, `GAMEMAKER`, `OUTPUT`, `ACCELERATION`.

### Property Composition

Available on `self` when extending `TrashMD_UIPanel`:

**Widgets:** `.Bool(id, label)`, `.Int(id, label)`, `.IntSlider(id, label, min, max)`, `.Float(id, label)`, `.Float2(id, label)`, `.Float3(id, label)`, `.Float4(id, label)`, `.FloatSlider(id, label, min, max)`, `.Color(id, label)`, `.String(id, label)`, `.StringInput(id, label)`, `.Combo(id, label, options)`, `.Image(id, label)`, `.TextureSlot(id, label)`, `.TextMultiline(id, label)`.

**Actions:** `.Label(label, text)`, `.Button(label, buttonLabel, callback)`.

**Layout:** `.Separator()`, `.SameLine()`, `.PropertyTable(name)` (2-column label|widget layout), `.PropertyList(name)` (vertical list).

**Context:** `.SetNodeContext()` (selected node), `.SetTransformContext()` (node's transform), `.SetNodeDataContext()` (node's datablock), `.SetTypeRequest(type)` (limit to a node type).

### Data Layouts

Register layouts for the polymorphic Data tab per node type:

```js
inspector_register_data_layout("MY_TYPE", { name: "My Data" }, ["MyDataPanel"]);
```

### Query Functions

`inspector_get_tabs()` returns all tab ID strings. `inspector_get_tab("OBJECT")` returns `{ id, name, category, priority }`.

---

## Custom Windows

With the `tmd` permission, plugins can register custom dockable windows using `imgui_*` widget functions.

```js
DrawMyTool = function(_context)
{
    imgui_text("My Tool");
    imgui_separator();
    if (imgui_button("Do Something"))
    {
        LOG("Clicked!");
    }
}

register = function()
{
    interface_register("MY_TOOL", "My Tool", global.DrawMyTool);
    interface_open("MY_TOOL");
}
```

`interface_open("MY_TOOL")` opens or focuses the window. `interface_close("MY_TOOL")` closes it. `interface_get_types()` lists all registered types. `workspace_get_current()` returns the active workspace name.

Windows are dockable and closeable by the user. `interface_register` is auto-cleaned on disable.

### ImGui Widgets

Available inside `renderCallback` and custom window render functions (requires `tmd`):

**Text:** `imgui_text`, `imgui_text_colored(text, color)`, `imgui_text_wrapped`, `imgui_text_disabled`, `imgui_bullet_text`.

**Layout:** `imgui_separator`, `imgui_spacing`, `imgui_same_line`, `imgui_new_line`, `imgui_indent` / `imgui_unindent`.

**Input:** `imgui_button(label)`, `imgui_small_button`, `imgui_checkbox(label, checked)`, `imgui_input_text(label, value)`, `imgui_input_float(label, value)`, `imgui_input_int(label, value)`, `imgui_slider_float(label, value, min, max)`, `imgui_slider_int(label, value, min, max)`, `imgui_color_edit3(label, color)`, `imgui_color_edit4(label, color, alpha)`.

**Structure:** `imgui_collapsing_header(label)`, `imgui_tree_node(label)` / `imgui_tree_pop()`, `imgui_begin_table(id, columns)` / `imgui_end_table()`, `imgui_table_next_row()` / `imgui_table_next_column()` / `imgui_table_setup_column(label)`, `imgui_begin_combo(label, preview)` / `imgui_end_combo()`, `imgui_selectable(label, selected)`.

**Utility:** `imgui_push_id(id)` / `imgui_pop_id()`, `imgui_is_item_hovered()`, `imgui_is_item_clicked()`, `imgui_tooltip(text)`.

Interactive widgets return the new value (or `true` on click for buttons). Container widgets (`begin_*`) return `true` if open - only call the matching `end_*` when they return `true`.

---

## Class Registration and Editor Symbols

Register class blueprints so they appear in autocomplete and can be used by other plugins:

```js
class_register("MyCommandClass", MyCommand);
class_unregister("MyCommandClass");
```

For helper functions or constants that aren't classes, register editor symbols:

```js
editor_register_symbol("my_helper", "function", "Apply the standard pose preset.");
editor_register_symbol("MY_CONSTANT", "macro", "A useful constant.");
```

`kind` is one of `"function"`, `"macro"`, `"keyword"`, `"enum"`. The description shows on hover in the code editor.

---

## Filesystem Access

With the `filesystem` permission, read and write files sandboxed to your plugin's directory:

```js
var _path = plugin_directory + "data.json";

// Write
var _json = json_stringify(myData);
var _buf = buffer_create(string_byte_length(_json), buffer_fixed, 1);
buffer_write(_buf, buffer_text, _json);
buffer_save(_buf, _path);
buffer_delete(_buf);

// Read
if (file_exists(_path))
{
    var _buf = buffer_load(_path);
    var _str = buffer_read(_buf, buffer_text);
    buffer_delete(_buf);
    var _data = json_parse(_str);
}
```

Available: `file_exists`, `file_delete`, `file_copy`, `file_rename`, `directory_exists`, `directory_create`, `buffer_*`, `filename_*`.

---

## Multi-File Plugins

Split larger plugins across multiple `.tmdplugin` files:

```
my_plugin/
  __main__.tmdplugin     <- Entry point (loaded last)
  helpers.tmdplugin      <- Loaded first (alphabetical order)
  utils.tmdplugin        <- Loaded first (alphabetical order)
```

Support files load alphabetically before `__main__`. All files share the same global scope. Only `__main__` gets the `@plugin` header.

---

## Utility Functions

TrashMD provides flat utility functions that wrap the deeper API. Instead of `TMD.project.sceneRoot.FindChildByName("x")`, you write `node_find_by_name("x")`. Functions are gated by permission level.

### Node (`core`)

Identity and hierarchy functions work on any node struct you already have a reference to.

**Identity:** `node_get_name` / `node_set_name`, `node_get_type`, `node_get_uuid`, `node_is_visible` / `node_set_visible`, `node_is_enabled` / `node_set_enabled`.

**Hierarchy:** `node_get_parent` / `node_set_parent`, `node_get_children`, `node_get_child_count`, `node_has_children`, `node_add_child`, `node_remove_child`, `node_clone(_node, [_deep])`, `node_find_parent_by_type`, `node_get_siblings`, `node_get_index`, `node_get_depth`, `node_get_root`, `node_get_path`.

**Traversal:** `node_for_each(_root, _callback)`, `node_count(_root)`, `node_count_by_type(_root)`.

### Selection (`context`)

`selection_get()` / `selection_get_all()`, `selection_set` / `selection_set_all`, `selection_add` / `selection_remove` / `selection_clear`, `selection_highlight(_node)`, `selection_count()`, `selection_history_back` / `selection_history_forward`.

### Tool, Timeline, Commands, Events, Formats (`context` / `commands` / `events` / `formats`)

**Tool/Timeline:** `tool_get()`, `frame_get()` / `frame_set(...)`, `frame_get_start()` / `frame_get_end()` / `frame_set_range(...)`.

**Commands:** `command_execute(_id, [_params])`, `command_exists` / `command_get`, `command_get_all` / `command_get_by_category`, `command_undo` / `command_redo`, `command_can_undo` / `command_can_redo`.

**Events:** `event_listen(_name, _callback)`, `event_unlisten(_handle)`, `event_fire(_name, [_args])`.

**Formats:** `format_get_all()`, `format_register(_name, _extensions, _description, _category, _importerClass, _exporterClass)`.

### Project and Scene (`tmd`)

**Project:** `project_get_name()`, `project_get_scene()` / `project_get_assets()`, `project_is_dirty()` / `project_mark_dirty()`, `project_find_by_uuid(_uuid)`.

**Scene creation:** `scene_create_node(_type, _name, [_parent])`, `scene_instantiate_asset(_asset, [_parent])`.

**Node search:** `node_find_by_name(_name, [_root])`, `node_find_by_type(_type, [_recursive], [_root])`.

**Programmatic import:** `import_submit(_result, [_parent], [_autoInstantiate])` registers all datablocks from a `TrashMD_ImportResult` in the project and creates asset nodes. Set `_autoInstantiate` to `true` to place model assets into the scene. Use this when building geometry, animations, or rigs programmatically rather than through an importer.

### Node Transform (`tmd`)

`node_get_position` / `node_set_position(_node, _x, _y, _z)`, `node_get_rotation` / `node_set_rotation`, `node_get_scale` / `node_set_scale`, `node_get_world_position`, `node_get_forward_vector`, `node_get_local_matrix` / `node_get_world_matrix`, `node_set_rotation_quaternion(_node, _x, _y, _z, _w)`, `node_set_from_matrix(_node, _matrix)`, `node_copy_transform(_dest, _src)`.

**Material overrides:** `node_set_material_override(_node, _prop, _val)`, `node_get_material_override(...)`, `node_clear_material_overrides(...)`.

**Asset link:** `node_get_asset(_sceneNode)`, `node_get_datablock(_sceneNode)`.

### Assets and Datablocks (`tmd`)

**Assets:** `asset_get_data` / `asset_has_data`, `asset_find_by_uuid`, `asset_get_all_of_type(_type)`, `asset_get_all`, `asset_is_used_in_scene`, `asset_get_unused`, `asset_create(_type, _name)`, `asset_create_material`, `asset_create_texture`, `asset_create_model`, `asset_create_animation`, `asset_create_rig`.

**Datablocks:** `datablock_get(_type, _uuid)`, `datablock_find(_uuid)`, `datablock_exists(...)`, `datablock_get_all()`.

### Animation (`tmd`)

`animation_channel_create(_targetPath, _targetType, _isQuaternion, _times, _values, [_interpolation])` creates an animation channel. `_targetPath` is a string like `"BoneName/rotation"` or `"NodeName/translation"`. `_targetType` is the value stride (3 for vec3, 4 for quaternion). `_interpolation` is `"LINEAR"` or `"STEP"`.

`animation_event_create(_name, _time, [_data])` creates a named event at a point in the animation timeline.

Constants: `ANIM_TARGET_NODE` (for node-targeted animations), `ASSET_TYPE_ANIMATION`, `ASSET_TYPE_RIG`.

### Light, Camera, Material Properties (`tmd`)

**Light:** `light_get_type` / `set_type` (0=directional, 1=point, 2=spot), `light_get_color` / `set_color`, `light_get_intensity` / `set_intensity`, `light_get_range` / `set_range`, `light_get_direction` / `set_direction`, `light_get_spot_angle` / `light_set_spot_angle(_node, _inner, _outer)`.

**Camera:** `camera_get_fov` / `set_fov`, `camera_get_znear` / `set_znear`, `camera_get_zfar` / `set_zfar`, `camera_is_ortho` / `set_ortho`, `camera_set_clip_planes(_node, _near, _far)`.

**Material** (works on `Model3D_Material` datablocks): `material_get/set_shading_model`, `_diffuse`, `_ambient`, `_specular`, `_emission`, `_opacity`, `_shininess`, `_metallic`, `_roughness`, `_alpha_mode`, `_alpha_cutoff`, `_blend_mode`, `_cull_mode`. Texture slots: `material_set/get_diffuse_texture`, `_normal_texture`, `_metal_rough_texture`, `_occlusion_texture`, `_emissive_texture` (also `_get_*_uuid` variants).

### GM Export Properties (`tmd`)

`node_gm_get/set_object`, `_layer`, `node_gm_is/set_export_enabled`, `node_gm_get/set_export_children`.

### Math (`tmd`)

**Quaternion:** `quaternion_create`, `quaternion_from_euler` / `to_euler`, `quaternion_from_axis_angle`, `quaternion_from_matrix` / `to_matrix`, `quaternion_slerp`, `quaternion_lerp`, `quaternion_multiply`, `quaternion_normalize`, `quaternion_inverse`, `quaternion_conjugate`, `quaternion_dot`, `quaternion_length`, `quaternion_rotate_vector`, `quaternion_to_array` / `from_array`.

**Matrix:** `matrix_build(x,y,z,rx,ry,rz,sx,sy,sz)`, `matrix_build_identity`, `matrix_multiply`, `matrix_lerp`, `matrix_transpose`, `matrix_get_translation`, `matrix_get_scale`, `matrix_get_quaternion`, `matrix_build_trsmatrix`, `matrix_build_normalmatrix`, `matrix_get_determinant`, `matrix_orthogonalize`.

---

## Known Limitations

### No closures over outer locals

Inner functions cannot access `var` declarations from parent functions. Each function has its own isolated local scope.

```js
var _captured = "hello";
self.MyMethod = function()
{
    LOG(_captured);  // ERROR: _captured is undefined
}
```

### Global prefix for nested function calls

Top-level functions must be called with `global.FunctionName()` from inside `Execute` or other nested methods. Interface constants (`LOG`, `CONTEXT`, `COMMANDS`, etc.) work without a prefix.

```js
MyHelper = function(_val)
{
    LOG(string(_val));
}

// Inside Execute:
self.Execute = function(_params)
{
    global.MyHelper(42);  // works
    // MyHelper(42);      // does NOT work
}
```

### `self` context in Execute

When the command system calls `Execute`, `self` may not refer to your command struct. Pass needed data through `_params` (from `Poll`) instead of relying on `self`.

### Closure capture happens at `extend()` time

Variables read from the surrounding scope in an `extend()` constructor are captured by value when the constructor runs, not when methods are later invoked. Move values onto `self.*` if you need them to track changes.

### `extend()` inheritance

- `extend("Parent", fn)` clones the parent blueprint, then runs the child constructor with `self` bound to the clone.
- Methods assigned to `self.X` replace the parent's `X`. No virtual dispatch beyond the slot you write.
- Call parent methods via `self.__parent.MethodName(...)`. There is no implicit `super`.
- Avoid side-effecting global state inside the constructor (it runs once per `extend()` and again for every `New()`).

### Recovering from ERROR state

A plugin that throws inside `register()` lands in ERROR state. The host rolls back any partial registrations. To recover: fix the source, then click "Reload" in the plugin manager.

---

## Complete Example

```js
/*
@plugin
name: Hello World
description: Greets the selected node.
version: 1.0.0
author: YourName
permissions: core, commands, context, tmd
*/

FormatGreeting = function(_name)
{
    return "Hello, " + string(_name) + "!";
}

GreetCommand = extend("TrashMD_Command", function()
{
    self.id       = "hello.greet";
    self.label    = "Greet Selected Node";
    self.category = "Hello";

    self.Poll = function()
    {
        var _node = selection_get();
        if (_node == undefined)
        {
            return undefined;
        }
        return { node: _node };
    }

    self.Execute = function(_params)
    {
        var _name = node_get_name(_params.node);
        LOG(global.FormatGreeting(_name));
    }
});

register = function()
{
    class_register("GreetCmd", GreetCommand);
    command_register("hello.greet", GreetCommand);
    LOG("Hello World: ready");
}

unregister = function()
{
    class_unregister("GreetCmd");
    LOG("Hello World: disabled");
}
```