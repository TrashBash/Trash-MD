# Importer/Exporter API

This guide covers creating custom file format handlers for TrashMD. Importers parse files into datablocks; exporters write datablocks to files. Both are registered through the plugin system and appear alongside built-in formats.

---

## Architecture

TrashMD uses a three-layer data model:

- **Data Layer** - Raw geometry, materials, textures (`Model3D_Model`, `Model3D_Mesh`, etc.). Stored in `TMD.project.blocksDB`. This is what importers create.
- **Asset Layer** - Named entries in the Asset Browser. References datablocks by UUID. The framework creates these automatically from import products.
- **Scene Layer** - Nodes placed in the 3D viewport. References assets by UUID. Created by the framework or the user.

Importers work with the Data Layer: parse files, create datablocks, return them as products. The framework handles promoting them into assets and scene nodes.

Exporters receive an asset reference, access its datablock and dependencies, and write to a file.

---

## Importer

Every importer extends `Model3D_Importer` and implements `Read(_request)`:

```js
MyFormat_Importer = extend("Model3D_Importer", function()
{
    Read = function(_request)
    {
        var _result = CLASSES.TrashMD_ImportResult.New();

        var _buffer = _request.GetBuffer();
        if (_buffer == undefined)
        {
            return _result.Error("Failed to load file");
        }

        // Parse into plain data first
        var _parsed = global.ParseMyFormat(_buffer);

        // Create datablocks (auto-registered in blocksDB)
        var _material = CLASSES.Model3D_Material.New("default_mat");
        var _mesh = CLASSES.Model3D_Mesh.New("mesh", _material.uuid);
        _mesh.SetVertices(_parsed.vertices);

        var _model = CLASSES.Model3D_Model.New(_parsed.name);
        _model.AddMesh(_mesh);

        // Add products (dependencies first)
        _result.AddProduct(_material);
        _result.AddProduct(_model);

        return _result;
    }
});
```

### Import Flow

1. **Parse** the file via `_request.GetBuffer()` into plain data structures
2. **Create datablocks** with `CLASSES.Model3D_*.New()` (auto-registered on creation)
3. **Build relationships** between datablocks using UUID references
4. **Add products** to the result in dependency order: textures -> materials -> meshes -> rigs -> models -> animations (and for collision: collision materials -> collision meshes -> collision models)
5. **Return** the result with optional metadata via `_result.SetMetadata(key, value)`

### Companion Files

Load sidecar files (like OBJ's `.mtl`) relative to the import directory:

```js
var _mtlBuffer = _request.ReadFile("materials.mtl");
if (_mtlBuffer != undefined)
{
    // parse it
    buffer_delete(_mtlBuffer);
}
else
{
    _result.Warn("Material file not found");
}
```

### Hot-Reload Override

When the user enables hot-reload on a file and the source changes on disk, TrashMD calls `Reload(_request)` instead of `Read`. The default delegates to `Read` (full reimport).

Override `Reload` only when your format can do something cheaper than a full reimport (e.g. checksum-based skip for large files):

```js
Reload = function(_request)
{
    var _result = CLASSES.TrashMD_ImportResult.New();
    var _parsed = global.ParseMyFormat(_request.GetBuffer());
    var _prev = global.DecodeHashTable(_request.previousImportHash);

    var _i = 0;
    while (_i < array_length(_parsed.blocks))
    {
        var _block = _parsed.blocks[_i];
        if (_prev[$ _block.name] != _block.checksum)
        {
            _result.AddProduct(global.BuildDatablock(_block));
        }
        _i += 1;
    }

    _result.SetMetadata("importHash", global.EncodeHashTable(_parsed));
    return _result;
}
```

Products are matched to existing assets by type + name. Omitting a product means "nothing changed." Asset UUIDs are preserved so scene references stay valid.

---

## Exporter

Every exporter extends `Model3D_Exporter` and implements `Write(_request, _services)`:

```js
MyFormat_Exporter = extend("Model3D_Exporter", function()
{
    Write = function(_request, _services)
    {
        var _result = CLASSES.TrashMD_ExportResult.New();

        var _error = _request.Validate();
        if (_error != undefined)
        {
            return _result.Error(_error);
        }

        var _model = _request.GetDatablock();
        if (_model == undefined)
        {
            return _result.Error("No model data");
        }

        var _deps = _request.GetDependencies();

        // Build output
        var _buffer = buffer_create(1024, buffer_grow, 1);
        global.WriteMyFormat(_buffer, _model, _deps);

        var _path = _services.WriteBuffer(_buffer, _request.path);
        buffer_delete(_buffer);

        if (_path == undefined)
        {
            return _result.Error("Failed to write file");
        }

        _result.outputPath = _path;
        return _result;
    }
});
```

### Export Flow

1. **Validate** via `_request.Validate()`
2. **Get data** via `_request.GetDatablock()` for the primary datablock
3. **Get dependencies** via `_request.GetDependencies()` for materials, textures, meshes, etc.
4. **Resolve** individual UUIDs via `_request.Resolve(uuid)` as needed
5. **Write** the file using `_services.WriteBuffer()` or `_services.WriteString()`
6. **Return** the result with `_result.outputPath` set

### Export Options

```js
var _quality = _request.GetOption("quality", "high");
var _embedTextures = _request.GetOption("embedTextures", false);
```

---

## Request and Result Reference

### TrashMD_ImportRequest

| Member | Description |
|---|---|
| `path` | Full file path |
| `format` | Format definition struct |
| `options` | Import preferences from UI |
| `GetBuffer()` | Lazy-loads and returns file buffer |
| `ReadFile(relativePath)` | Load a companion file relative to import directory |
| `GetOption(key, default)` | Get option with fallback |
| `Validate()` | Returns error string or `undefined` |
| `Cleanup()` | Frees buffer (called automatically) |

### TrashMD_ImportResult

| Member | Description |
|---|---|
| `success` | Whether import succeeded |
| `products` | Datablocks produced |
| `warnings` / `errors` | Message arrays |
| `metadata` | Arbitrary key-value pairs |
| `AddProduct(datablock)` | Add a datablock to products |
| `GetProductsByType(type)` | Filter products by type string |
| `Warn(message)` / `Error(message)` | Add warning/error (returns self) |
| `SetMetadata(key, value)` | Set metadata (returns self) |

### TrashMD_ExportRequest

| Member | Description |
|---|---|
| `asset` | Primary asset to export |
| `assets` | All assets (for batch export) |
| `path` | Output file path |
| `format` | Format definition |
| `options` | Export preferences |
| `Validate()` | Returns error string or `undefined` |
| `GetOption(key, default)` | Get option with fallback |
| `GetDatablock()` | Get primary asset's datablock |
| `GetProducts()` | Scope-aware product collection. Returns `{ models, meshes, materials, textures, animations, rigs }` with dependencies included automatically |
| `Resolve(uuid)` | Resolve any UUID to its datablock |
| `GetDependencies(recursive)` | Returns `{ models, meshes, materials, textures, animations, rigs }` |

### TrashMD_ExportResult

| Member | Description |
|---|---|
| `success` | Whether export succeeded |
| `outputPath` | Actual path written |
| `warnings` / `errors` | Message arrays |
| `metadata` | Arbitrary key-value pairs |
| `Warn(message)` / `Error(message)` | Add warning/error (returns self) |
| `SetMetadata(key, value)` | Set metadata (returns self) |

### TrashMD_ExportServices

| Method | Description |
|---|---|
| `WriteBuffer(buffer, path, overwrite)` | Save buffer to file, returns actual path or `undefined` |
| `WriteString(string, path, overwrite)` | Save string to file |
| `EnsureDirectory(path)` | Creates directory tree if needed |
| `GetUniqueFilename(path)` | Appends `_1`, `_2`, etc. if file exists |

---

## Datablock Classes

Create datablocks with `CLASSES.Model3D_*.New(...)`. All datablocks auto-register in `TMD.project.blocksDB` on creation. Relationships use UUID references.

### Geometry

| Class | Type | Key Properties |
|---|---|---|
| `Model3D_Model` | `"MODEL"` | `name`, `meshes[]` (UUIDs), `rig` (UUID), `transform` |
| `Model3D_Mesh` | `"MESH"` | `name`, `material` (UUID), `vertices[]`, `transform` |
| `Model3D_Vertex` | - | `position[3]`, `normal[3]`, `texCoord[2]`, `color`, `alpha`, `boneIndices[4]`, `boneWeights[4]`, `tangent[4]` |

`Model3D_Model.New(_name)` - Top-level container. `AddMesh(_mesh)` to attach meshes.

`Model3D_Mesh.New(_name, _materialUUID)` - Geometry with a material reference. `SetVertices(_array)` and `GenerateNormals(_smooth, _overwrite)`.

`Model3D_Vertex.New()` - Set `position`, `normal`, `texCoord` arrays directly. For skinned meshes, set `boneIndices[4]` and `boneWeights[4]`.

### Materials and Textures

| Class | Type | Key Properties |
|---|---|---|
| `Model3D_Material` | `"MATERIAL"` | `name`, `diffuse`, `ambient`, `specular`, `diffuseTexture` (UUID), `metallic`, `roughness`, `opacity`, `blendMode` |
| `Model3D_Texture` | `"TEXTURE"` | `name`, `sprite` (sprite ID) |

`Model3D_Material.New(_name)` - Assign texture UUIDs to `diffuseTexture`, `normalTexture`, `metallicRoughnessTexture`, etc.

`Model3D_Texture.New(_name, _spriteId)` - Wraps a GameMaker sprite as a texture asset.

### Rigging

| Class | Type | Key Properties |
|---|---|---|
| `Model3D_Rig` | `"RIG"` | `name`, `bones[]`, `boneNames[]`, `boneNameMap{}` |
| `Model3D_Bone` | - | `name`, `index`, `parent`, `position[]`, `rotation`, `scale[]`, `restPosition`, `restRotation`, `restScale` |

`Model3D_Rig.New(_name)` - Skeleton container. `AddBone(_bone)` adds a bone and assigns its index. Link a rig to a model by setting `_model.rig = _rig.uuid`.

`Model3D_Bone.New(_name)` - Set `parent` to the parent bone's index (-1 for root bones). Set `position`, `scale` as 3-element arrays and `rotation` as a quaternion. `restPosition`, `restRotation`, `restScale` store the bind pose.

```js
var _rig = CLASSES.Model3D_Rig.New("Armature");

var _root = CLASSES.Model3D_Bone.New("Root");
_root.parent = -1;
_root.position = [0, 0, 0];
_root.scale = [1, 1, 1];
_root.restPosition = [0, 0, 0];
_root.restRotation = quaternion_create();
_root.restScale = [1, 1, 1];
_rig.AddBone(_root);

var _child = CLASSES.Model3D_Bone.New("Spine");
_child.parent = 0;
_child.position = [0, 0, 1.5];
_child.restPosition = [0, 0, 1.5];
_child.restRotation = quaternion_create();
_child.restScale = [1, 1, 1];
_rig.AddBone(_child);
```

### Animation

| Class | Type | Key Properties |
|---|---|---|
| `Model3D_Animation` | `"ANIMATION"` | `name`, `duration`, `channels[]`, `channelMap{}`, `events[]`, `targetType`, `looping` |

`Model3D_Animation.New(_name)` - An animation clip. Set `duration` (seconds), `targetType` (e.g. `ASSET_TYPE_RIG`), and `looping`. Add channels with `animation_channel_create()` and events with `AddEvent(_name, _time, _data)`.

```js
var _clip = CLASSES.Model3D_Animation.New("Walk");
_clip.duration = 1.0;
_clip.targetType = ASSET_TYPE_RIG;
_clip.looping = true;

var _times = [0, 0.5, 1.0];
var _values = [0, 0, 0, 1, 0.707, 0, 0, 0.707, 0, 0, 0, 1];
var _ch = animation_channel_create("Spine/rotation", 4, true, _times, _values, "LINEAR");
array_push(_clip.channels, _ch);
_clip.channelMap[$ "Spine/rotation"] = array_length(_clip.channels) - 1;
```

### Collision

| Class | Type | Key Properties |
|---|---|---|
| `Model3D_CollisionModel` | `"COLLISION_MODEL"` | `name`, `meshes[]` |
| `Model3D_CollisionMesh` | `"COLLISION_MESH"` | `name`, `material`, `vertices[]`, `faces[]` |
| `Model3D_CollisionFace` | - | `vertices[]`, `normal[]`, `material`, `flags`, `attribute` |
| `Model3D_CollisionVertex` | - | `position[]`, `normal[]`, `materialIndex` |
| `Model3D_CollisionMaterial` | `"COLLISION_MATERIAL"` | `name`, `friction`, `restitution`, `surfaceType`, `walkable`, `collisionLayer`, `collisionMask`, `isTrigger` |

`Model3D_CollisionModel.New(_name)` - Top-level collision container. `AddMesh(_mesh)`.

`Model3D_CollisionMesh.New(_name, _materialIndex)` - Collision geometry. `SetVertices(_vertices)` and `CreateFacesFromVertices()`. `GenerateNormals(_smooth)`.

`Model3D_CollisionVertex.New()` - `SetPosition(_x, _y, _z)` or `SetPositionArray(_pos)`. `SetNormal(_normal)`.

`Model3D_CollisionFace.New(_vertA, _vertB, _vertC)` - Triangle face. `SetMaterial(_mat)`, `SetFlags(_flags)`, `CalculateNormal()`.

`Model3D_CollisionMaterial.New(_name)` - Physics/gameplay properties. `friction`, `restitution`, `walkable`, `isTrigger`, etc.

### Relationships Example

```js
var _tex = CLASSES.Model3D_Texture.New("albedo", _spriteId);
var _mat = CLASSES.Model3D_Material.New("metal");
_mat.diffuseTexture = _tex.uuid;

var _mesh = CLASSES.Model3D_Mesh.New("body", _mat.uuid);
var _model = CLASSES.Model3D_Model.New("character");
_model.AddMesh(_mesh);
```

---

## Format Registration

Register your format so it appears in TrashMD's import/export dialogs:

```js
format_register(
    "MyFormat",
    [".myf", ".myformat"],
    "My Custom Format",
    ASSET_TYPE_MODEL,
    MyFormat_Importer,
    MyFormat_Exporter
);
```

You can register import-only or export-only formats by omitting the other class.

---

## Error Handling

Use early-return validation and `_result.Warn()` for non-fatal issues:

```js
Read = function(_request)
{
    var _result = CLASSES.TrashMD_ImportResult.New();

    var _buffer = _request.GetBuffer();
    if (_buffer == undefined)
    {
        return _result.Error("Failed to load file");
    }

    var _version = buffer_read(_buffer, buffer_u32);
    if (_version < 2)
    {
        _result.Warn("Old format version, some features may not import");
    }

    // ... import logic ...

    return _result;
}
```

---

## Best Practices

- **Separate parsing from datablock creation.** Parse into plain data first, then build datablocks. Keeps parsing testable and side-effect free.
- **Create dependencies before dependents.** Textures -> materials -> meshes -> rigs -> models -> animations. Add products in the same order.
- **Use descriptive names.** `"Character_Body"` not `"Model0"`. Names appear in the Asset Browser.
- **Use `GetOption()` for user preferences.** Lets the UI pass configuration to your handler.
- **Use `_request.Resolve(uuid)` in exporters** instead of accessing `TMD.datablocks` directly.