# Easy VRM

Native, in-editor VRM importer for Unreal Engine 5.

Easy VRM brings VRoid avatars into Unreal as production-ready Skeletal Meshes with a single button click. Pick a `.vrm` file and a destination folder, hit Import, and the plugin produces a SkeletalMesh, Skeleton, IK Rig, IK Retargeter, material instances, and an Animation Blueprint that retargets the UE5 Mannequin pose onto the avatar. The same panel handles material recoloring, MToon style presets, shader swaps, spring bone physics, and one click application onto an existing Character Blueprint.

## What it does

- **Import**: drag and drop `.vrm` files (or pick from disk), choose a destination folder, click Import. Produces SkeletalMesh, Skeleton, IK Rig, IK Retargeter, MToon material instances, VrmMetaObject, and an Animation Blueprint wired for UE5 Mannequin retarget.
- **Import organizer**: assets land in typed subfolders (`Meshes/`, `Materials/`, `Textures/`, `Animations/`, `Physics/`, `Rigs/`, `Meta/`) with canonical prefixes (`SKM_`, `SKEL_`, `MI_`, `T_`, `AS_`, `PA_`, etc.). Redirectors preserve existing references during transitions.
- **Material Tweaks**: per body group (Skin, Face Details, Eyes, Hair, Clothing, All Materials) sliders and color pickers for every MToon parameter that matters. Live persona preview. Save and load named presets.
- **Style presets**: five curated MToon parameter sets (Hard Cel, Soft Cel, Painterly, Realistic, Idol / Glow) that change the look without overwriting per character colors.
- **Shader swaps**: swap MToon for one of the other VRM4U parent materials (MToon + SSS, Realistic PBR, Realistic + Subsurface, Unlit) without breaking references.
- **Apply to Character Blueprint**: in one click, hook an imported VRM onto an existing UE5 Character Blueprint with retargeted animation, parented to the inherited Mannequin Mesh and visibility configured.
- **Spring bone physics**: per group toggles and strength sliders for hair, breast, and clothing physics. Updates live during PIE.
- **Toe retarget fix**: piggybacks on the VRM4U-side fix; cleanly absent here so the single source of truth lives in VRM4U.
- **Outfit color repair**: triggered automatically by VRM4U's import pipeline; Easy VRM exposes a manual **Repair Outfit** button on the panel for re-runs.
- **Toolbar dropdown**: an arrow next to the Easy VRM toolbar button opens editor preferences shortcuts (small toolbar icons, PIE always on top, re-open Blueprint tabs on launch, restore editor window position).
- **Runtime Blueprint library**: a new `EasyVRMRuntime` module exposes runtime nodes (`Apply Tweak to Category`, `Apply Style Preset`, `Apply Shader Preset`, `Apply VRM Physics`, per-property setters) usable in any Actor / Character Blueprint to drive material and physics changes on a `USkeletalMeshComponent` at runtime, in PIE or packaged builds.

## Runtime Blueprint library

Easy VRM ships two modules:

- **EasyVRMEditor** (editor-only): the importer panel, the Apply-to-Character-BP feature, all asset setup.
- **EasyVRMRuntime** (runtime + editor): a `UEasyVRMRuntimeBPLibrary` exposing Blueprint nodes that act on a spawned `USkeletalMeshComponent`. Use these to script per-character looks at runtime.

Nodes (all under category `Easy VRM Runtime`):

| Node | Purpose |
| ---- | ------- |
| Apply Tweak to Category | Apply a full `FEasyVRMRuntimeTweak` struct (14 MToon fields) to one body group |
| Set Category Lit / Shade / Outline / Emission Color | Per property colour setter. Default white = preserve imported colour |
| Set Category Outline Width / Shade Sharpness | Per property scalar setter |
| Apply Style Preset | Apply one of the five built in MToon style presets via enum dropdown. Returns the applied preset |
| Apply Shader Preset | Swap the slot's parent material to a VRM4U variant (MToon, MToon+SSS, Realistic PBR, Realistic+Subsurface, Unlit). Returns the applied preset |
| Apply VRM Physics | Single node controlling spring bone sim. `Config` struct carries master enable, per-group bools (Breast / Hair / Clothing), and per-group strength sliders. Backup-and-restore at runtime so re-enable and mid-PIE changes work without level reload |

The runtime functions mirror the editor panel's behaviour exactly when invoked in PIE (they use the same `SetParentEditorOnly` + `FMaterialUpdateContext` path for shader swap, and the same per-group filter on `VrmMetaObject.VRMSpringMeta` for physics). Cooked builds use a reflection-based fallback for the editor only paths.

## Requirements

Easy VRM is an editor layer on top of **VRM4U**, which does the actual `.vrm` parsing and runtime loading. VRM4U must be installed in the same project. Get it free on Fab as **DYLO's VRM4U**.

If VRM4U is not installed, Easy VRM shows a banner reading "DYLO's VRM4U is required but not installed" and its tools stay disabled until you add it.

## Installation

See [docs/install.md](docs/install.md) for full details.

Short version:
1. Install **DYLO's VRM4U** (free) from Fab into your project.
2. Install **Easy VRM** from Fab into the same project.
3. Enable both in the project's Plugins window if they are not already enabled, and restart the editor.
4. The **Easy VRM** button appears on the main toolbar.

## Quick start

1. Click the **Easy VRM** button on the main toolbar (or open it from Window to Easy VRM).
2. Drag a `.vrm` file onto the panel, or use **Browse** to pick one.
3. Set a destination folder (defaults to `/Game/EasyVRM`). The folder name field auto fills from the `.vrm` filename.
4. Click **Import**.

The avatar appears in the Content Browser at `Content/EasyVRM/<Name>/` with the typed subfolder layout (`Meshes/`, `Materials/`, `Textures/`, `Animations/`, `Physics/`, `Rigs/`, `Meta/`). Open `SKM_<Name>` to view the skeletal mesh, or drag it into a level to play.

To attach it to a playable character, see [docs/apply-to-character-bp.md](docs/apply-to-character-bp.md).

To script material / physics changes at runtime, see the [Runtime Blueprint library](#runtime-blueprint-library) section above.

## Documentation

| Topic | File |
| ----- | ---- |
| Installation | [docs/install.md](docs/install.md) |
| Importing a VRM avatar | [docs/importer.md](docs/importer.md) |
| Material Tweaks panel | [docs/material-tweaks.md](docs/material-tweaks.md) |
| Style presets and shader swaps | [docs/styles-and-shaders.md](docs/styles-and-shaders.md) |
| Apply to Character Blueprint | [docs/apply-to-character-bp.md](docs/apply-to-character-bp.md) |
| Spring bone physics | [docs/physics.md](docs/physics.md) |
| Troubleshooting | [docs/troubleshooting.md](docs/troubleshooting.md) |

## Engine compatibility

- Built and tested on **Unreal Engine 5.7**.
- Windows 64 bit. The editor modules are gated to Win64.
- Works in BP only projects and C++ projects alike.

## Support

For bug reports and feature requests, email dylogamingofficial@gmail.com.

## Credits

- VRM4U by Haruyoshi Yamamoto (ruyo). Required dependency, available free on Fab as DYLO's VRM4U. Upstream: https://github.com/ruyo/VRM4U
- Easy VRM by DYLO Gaming.

## License

All Rights Reserved. See [LICENSE](LICENSE).
