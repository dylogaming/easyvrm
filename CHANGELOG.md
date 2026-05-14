# Changelog

All notable changes to Easy VRM are listed in this file.

The format follows Keep a Changelog (https://keepachangelog.com/en/1.1.0/).

## 1.0.0

Initial release.

### Added

- Native, in editor VRM importer panel. Drag and drop or browse for `.vrm` files; pick a destination folder; click Import. Produces SkeletalMesh, Skeleton, IK Rig, IK Retargeter, MToon material instances, VrmMetaObject, and (optionally) a UE5 Mannequin renamed mesh variant.
- Bundled VRM4U with cold install flow. The plugin ships with VRM4U inside its own folder and installs it as a sibling on first launch. No separate install step required.
- Toolbar button and Window menu entry. Content Browser right click "Import VRM Here".
- Material Tweaks section: per body group MToon parameters (Skin, Face Details, Eyes, Hair, Clothing, All Materials) with live persona preview. Color pickers and sliders for every relevant MToon parameter.
- Style presets: curated MToon shading scalar sets that change the look without overwriting per character colors. Arrow key cycling through the preset list.
- Shader swaps: swap the parent material of every MIC in a VRM folder to a different VRM4U parent (MToon, PBR, Unlit, etc.) without breaking references. Arrow key cycling.
- Per VRM and named material presets. Per VRM presets auto load when you select the matching `.vrm` source.
- Apply to Character Blueprint: hook an imported VRM onto an existing Character BP with one click. Generates an AnimBP, adds a parented `VRM_Mesh` component, hides the inherited Mannequin Mesh, compiles and saves.
- Remove from Character Blueprint: reverses Apply, restores Mannequin visibility.
- Spring bone physics: per group toggles (Breast, Hair, Clothing) and strength sliders. Updates live during PIE.
- Auto applied toe retarget fix on every import (chains leg bones to the toe instead of the ankle, fixes the UE5.7 IK Rig toe wobble).
- Auto applied outfit color repair on every import (propagates linked cloth tints from VRoid Studio that the `.vrm` format does not preserve).
- Recent imports menu (last 8) with thumbnails.
- Section expand and collapse state persisted across editor restarts.
- Eye color quick pick swatch above the Material Tweaks section.
