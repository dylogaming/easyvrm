# Changelog

All notable changes to Easy VRM are listed in this file.

The format follows Keep a Changelog (https://keepachangelog.com/en/1.1.0/).

## 1.0.0

Initial release.

### Added

- Native, in editor VRM importer panel. Drag and drop or browse for `.vrm` files; pick a destination folder; click Import. Produces SkeletalMesh, Skeleton, IK Rig, IK Retargeter, MToon material instances, VrmMetaObject, and (optionally) a UE5 Mannequin renamed mesh variant.
- Requires VRM4U as a dependency, available free on Fab as DYLO's VRM4U. Easy VRM declares it in its plugin descriptor so the marketplace installs it alongside. If VRM4U is missing, the panel shows a banner prompting you to install it from Fab.
- Toolbar button and Window menu entry. Content Browser right click "Import VRM Here".
- Material Tweaks section: per body group MToon parameters (Skin, Face Details, Eyes, Hair, Clothing, All Materials) with live persona preview. Color pickers and sliders for every relevant MToon parameter.
- Style presets: five curated MToon shading scalar sets (Hard Cel, Soft Cel, Painterly, Realistic, Idol / Glow) that change the look without overwriting per character colors. Arrow key cycling through the list.
- Shader swaps: swap the parent material of every MIC in a VRM folder to a different VRM4U parent (MToon, MToon + SSS, Realistic PBR, Realistic + Subsurface, Unlit) without breaking references. Arrow key cycling.
- Original snapshot per VRM: the first time the panel reads a VRM's MICs, the values are captured to disk so **Reset to Original** can restore them later.
- Repair Outfit button to re run the cloth color propagation pass on demand.
- Apply to Character Blueprint: hook an imported VRM onto an existing Character BP with one click. Generates an AnimBP, adds a parented `VRM_Mesh` component, hides the inherited Mannequin Mesh, compiles and saves.
- Remove from Character Blueprint: reverses Apply, restores Mannequin visibility.
- Spring bone physics: master **Enable VRM Physics** tri state toggle plus per group sub toggles (Breast, Hair, Clothing) and strength sliders. Moving a strength slider auto enables its sub toggle. Updates live during PIE.
- Auto applied toe retarget fix on every import (chains leg bones to the toe instead of the ankle, fixes the UE5.7 IK Rig toe wobble).
- Auto applied outfit color repair on every import (propagates linked cloth tints from VRoid Studio that the `.vrm` format does not preserve).
- Recent imports menu (last 8).
- Section expand and collapse state persisted across editor restarts.
