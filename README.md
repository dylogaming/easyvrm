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
- **Bundled VRM4U**: ships with VRM4U inside the plugin and installs it as a sibling plugin on first launch. No separate install step.

## Installation

See [docs/install.md](docs/install.md) for the full first launch flow, including the cold install of VRM4U.

Short version: enable Easy VRM in your project's Plugins window, restart, and click Install when prompted to lay down VRM4U. Restart once more and you're ready.

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
| Installation and first launch | [docs/install.md](docs/install.md) |
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

- VRM4U by Haruyoshi Yamamoto (ruyo). MIT license. Bundled and installed automatically.
- Easy VRM by DYLO Gaming.

## License

All Rights Reserved. See [LICENSE](LICENSE).
