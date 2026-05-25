# Easy VRM

Native, in-editor VRM importer for Unreal Engine 5.

Easy VRM brings VRoid avatars into Unreal as production-ready Skeletal Meshes with a single button click. Pick a `.vrm` file and a destination folder, hit Import, and the plugin produces a SkeletalMesh, Skeleton, IK Rig, IK Retargeter, material instances, and an Animation Blueprint that retargets the UE5 Mannequin pose onto the avatar. The same panel handles material recoloring, MToon style presets, shader swaps, spring bone physics, and one click application onto an existing Character Blueprint.

## What it does

- **Import**: drag and drop `.vrm` files (or pick from disk), choose a destination folder, click Import. Produces SkeletalMesh, Skeleton, IK Rig, IK Retargeter, MToon material instances, VrmMetaObject, and an Animation Blueprint wired for UE5 Mannequin retarget.
- **Material Tweaks**: per body group (Skin, Face Details, Eyes, Hair, Clothing, All Materials) sliders and color pickers for every MToon parameter that matters. Live persona preview. Save and load named presets.
- **Style presets**: five curated MToon parameter sets (Hard Cel, Soft Cel, Painterly, Realistic, Idol / Glow) that change the look without overwriting per character colors.
- **Shader swaps**: swap MToon for one of the other VRM4U parent materials (MToon + SSS, Realistic PBR, Realistic + Subsurface, Unlit) without breaking references.
- **Apply to Character Blueprint**: in one click, hook an imported VRM onto an existing UE5 Character Blueprint with retargeted animation, parented to the inherited Mannequin Mesh and visibility configured.
- **Spring bone physics**: per group toggles and strength sliders for hair, breast, and clothing physics. Updates live during PIE.
- **Toe retarget fix**: auto-applies the standard fix for the UE5.7 IK Rig toe wobble issue on every import.
- **Outfit color repair**: post-import pass that propagates linked cloth tints from VRoid Studio's color linking feature, which the `.vrm` format does not preserve.

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
3. Set a destination folder (defaults to `/Game/Characters`).
4. Click **Import**.

The avatar appears in the Content Browser at `Content/Characters/<Name>/`. Open `SK_<Name>` to view the skeletal mesh, or drag it into a level to play.

To attach it to a playable character, see [docs/apply-to-character-bp.md](docs/apply-to-character-bp.md).

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
