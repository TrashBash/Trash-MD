# Materials

Materials define how surfaces look when rendered. Every mesh in TrashMD references a material, and materials combine a shading model, color properties, and textures to produce the final appearance. This page covers the material editor, shading models, and textures.

---

## Material Editor

Select a mesh node or material asset to see its material properties in the Inspector. The material editor is organized into sections for basic properties, textures, and advanced settings.

<img width="392" height="765" alt="image" src="https://github.com/user-attachments/assets/5620a14c-2054-452d-b423-c21860cc5cbc" />

---

## Shading Models

Each material uses one of three shading models. The shading model determines which properties are available and how lighting is calculated.

### PBR (Physically Based Rendering)

TrashMD supports two PBR workflows:

**Metallic-Roughness** (the default, used by glTF and most modern formats):

- **Albedo** - Base color of the surface
- **Metallic** - How metallic the surface is
- **Roughness** - How rough the surface is
- **Ambient Occlusion** - Simulates soft shadows in crevices
- **Emission** - Self-illumination color and intensity

**Specular-Glossiness** (used by older PBR content and some game engines):

- **Specular** - The reflectance color of the surface
- **Glossiness** - How smooth the surface is

Both workflows also support:

- **Subsurface Scattering** - Amount, color, and thickness for materials like skin or wax
- **Cavity** - Micro-occlusion strength for fine surface detail
- **Anisotropy** - Directional stretching of specular highlights (for brushed metal, hair, etc.)

### Phong

The Phong model is a traditional shading approach.

Key properties:

- **Diffuse**
- **Ambient**
- **Specular**
- **Shininess**
- **Emission** - Self-illumination color and intensity

### Unlit

Unlit materials skip lighting calculations entirely and display the base color or texture as-is.

---

## Common Material Properties

Regardless of shading model, all materials have:

- **Opacity** - Surface transparency
- **Alpha Mode** - Opaque (no transparency), Mask (binary cutout at a threshold), or Blend (smooth alpha)
- **Blend Mode** - Opaque, Transparent, Additive, or Multiply
- **Cull Mode**

---

## Textures

### Texture Types

| Texture Slot | Used By | Description |
|---|---|---|
| Albedo / Diffuse | PBR, Phong | Base color texture. Maps surface color across the mesh. |
| Normal | PBR, Phong | Adds surface detail without extra geometry. Encodes per-pixel surface direction as RGB values. |
| Metallic-Roughness | PBR (Metal/Rough) | Combined texture where the blue channel stores metallic and the green channel stores roughness. |
| Specular | PBR (Spec/Gloss) | Reflectance color for the specular-glossiness workflow. |
| Glossiness | PBR (Spec/Gloss) | Smoothness map (inverse of roughness) for the specular-glossiness workflow. |
| Ambient Occlusion | PBR | Stores pre-computed soft shadow information. |
| Cavity | PBR | Micro-occlusion for fine surface detail. |
| Emissive | PBR, Phong | Defines which parts of the surface glow and in what color. |
| Opacity | All | Per-pixel opacity when using alpha blending or masking. |
| Scatter Thickness | PBR | Thickness map for subsurface scattering. |
| Anisotropic Direction | PBR | Direction map for anisotropic specular highlights. |

### Additional Texture Controls

- **Normal Strength** (0.0-1.0), **Flip Normal Y/X** (switch between DirectX/OpenGL conventions)
- **Texture Filter** - Point (pixel-art) or linear (smooth)
- **Texture Offset**, **Repeats**, and **Flips** for UV transforms

Assign textures in the Inspector by clicking a slot to pick from your project assets, or drag from the Asset Browser.

---

## Material Overrides

You can override material properties per-node without changing the base material asset. This lets the same material appear on multiple objects with per-instance tweaks (like different colors). Overrides are set in the Inspector and take priority over the base material.
