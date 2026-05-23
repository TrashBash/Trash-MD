<p align="center"><img src="https://github.com/user-attachments/assets/8e6be672-43b6-442f-8fa3-10430fe0235c" style="display:block; margin:auto; width:500"></p>
<h1 align="center">[BETA] Trash MD 0.1.0</h1>

<p align="center">An intermediate between 3D Softwares and GameMaker. Import and Export 3D Models to your liking. Coming soonTM.</p>
&nbsp;

## DISCLAIMER
This project is in an unfinished state, containing bugs and unstable or unfinished features. 
As a result from burnout and the sheer size of this project while maintaining a personal life, TrashMD has been pushed to a stable enough state to be released.

Considering recent updates to GameMaker's GMRT, such as eventual IDE Plugins and native 3D support, I am unsure if I will continue development on this.

## ✨ What is TrashMD?

TrashMD is a 3D content tool mainly built for GameMaker. What started out as a simple .obj parser 
is now meant to streamline the workflow between 3D Software and GameMaker.

A singular place in which you can import files, lightly edit them, and export them to your own file formats.

## 🎯 Features

### The Basics
- Import from loads of file formats
- Work with a unified data representation of Models, Collisions and Animations
- Export to community made formats (BBMOD, SMF, DmrVBM, ColMesh ... ) and Vertex Buffers
- Install Plugins to expand the tool to your needs
- Run Scripts to automate particular tasks or batch-edit
- [Experimental] Link GameMaker Projects **(USE AT OWN RISK - THIS MODIFIES .YYP AND .GML FILES DIRECTLY)**
  - Edit positions of Room Instances
  - Select directory to automatically export to
  - Inject Code and create Objects to set-up a 3D Environment quicker using code Template files
    
### Editor & Pipeline
- Automatic Axis Correction for imported files
- Unified Command System with shortcuts and undo/redo
- Asset Browser, Outliner, Inspector, Code Editor, Animation Player
- Tracking of Export Status and State of files as well as hot-reloads
- Projects can be saved, Asset Packages can be exported and shared

### Make it your own (powered by Catspeak @katsaii)
- Plugin System
- Scripting System
- Add custom Commands, Formats, Menu Entries and more
- Templates to learn from

## Community
- Plugins and Scripts can be published and then browsed & installed from within TrashMD
- Assets can be published in the same manner and downloaded from within

---

## 📦 Format Support

| Format | Import | Export |
|---|:---:|:---:|
| glTF 2.0 | ✅ | ✅ |
| OBJ | ✅ | — |
| Collada (.dae) | ✅ | — |
| FBX | ✅ | — |
| Blender (.blend) | ✅ | — |
| PICO CAD | ✅ | — |
| Crocotile 3D | ✅ | — |
| Nintendo NITRO / NSBMD | ✅ | — |
| STL | ✅ | — |
| Blockbench | ✅ | — |
| Penguin | ✅ | 🐞 |
| BBMOD | 🐞 | 🐞 |
| SMF | 🐞 | ✅ |
| Vertex Buffer VBUFF | ✅ | ✅ |
| DmrVBM | 🐞 | ✅ |
| TMD | ✅ | ✅ |
| ColMesh | 🐞 | ✅ |
| JSON Scene | — | ✅ |
| KH2FM | 🐞 | — |

---

## 🔌 Plugins & Scripting

TrashMD is built to be extended. Install a `.tmdplugin` and you can bring
new features to the editor through a safe, permissioned API.

With plugins you can:
- Register your own importers and exporters
- Add custom commands
- Hook into context menus and events
- Build pipeline automation tailored to your project

Or use scripts to:
- Batch operate on assets
- Play around with the API with full permission set

## 🗺️ Roadmap

As mentioned before, I am very unsure if this project will see any continued work.
But if it does, I would like very much to work on the following things:

- [ ] Plenty more plugin examples and reference projects
- [ ] GameMaker CLI integration
- [ ] Stability work
- [ ] Performance improvements
- [ ] Memory management improvements
- [ ] Finish Live Shader Editor (GLSL to HLSL transpiler and hooking into the draw context is done but requires some C++ side InputLayout work to function properly)
- [ ] Vertex Quantization
- [ ] Vertex Animation Textures
- [ ] Terrain
- [ ] UI for editing Axis Profiles
- [ ] Deferred Rendering
- [ ] Collision Shapes

## Screenshots

![Image 1](https://github.com/user-attachments/assets/bb8f7be8-7cd8-47b0-a7ae-9c94bfe81153)
![Image 2](https://github.com/user-attachments/assets/83a26b15-e38d-4f60-b225-4ece4dfb0579)
![Image 3](https://github.com/user-attachments/assets/647886bb-cf58-4c83-bbce-6b106f496677)
![Image 4](https://github.com/user-attachments/assets/bb8b5849-b613-4e32-8e70-1ae19dfd4871)
![Image 6](https://github.com/user-attachments/assets/aa12cef9-df17-4383-a9e6-dac1b1c7dd9b)

https://github.com/user-attachments/assets/49e3a0b2-e62a-4a14-80b3-e208e4dca674

