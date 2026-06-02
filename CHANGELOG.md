# Changelog

All notable changes to Easy VRM are listed in this file.

The format follows Keep a Changelog (https://keepachangelog.com/en/1.1.0/).

## 1.1.0

### Added

- **Runtime Blueprint library** (`EasyVRMRuntime` module). New Blueprint nodes operate on a spawned `USkeletalMeshComponent` so material and physics changes can be driven at runtime in PIE and packaged builds. Nodes: `Apply Tweak to Category`, per property setters (`Set Category Lit / Shade / Outline / Emission Color`, `Set Category Outline Width / Shade Sharpness`), `Apply Style Preset` (enum dropdown), `Apply Shader Preset` (enum dropdown), `Apply VRM Physics` (struct pin with master enable, per group bools, per group strength sliders).
- **Toolbar split button**. Arrow chevron next to the Easy VRM icon opens an options menu: open the panel, Use Small Toolbar Icons, PIE Always On Top, Re-open Blueprints / asset tabs on launch, Restore editor window position on launch. The window position toggle auto-defers to the DYLO Unreal Editor plugin when both are installed.
- **Import organizer**. Imported assets are placed in typed subfolders (`Meshes/`, `Materials/`, `Textures/`, `Animations/`, `Physics/`, `Rigs/`, `Meta/`) with canonical prefixes (`SK_` becomes `SKM_`, `A_` becomes `AS_`, `POSE_` becomes `PA_`) and stripped VRM4U-internal suffixes (`_VrmMeta`, `_VrmLicense`, `_VrmAssetList`, `__Instance_`). Idempotent and redirector-safe.
- **VRM4U installed-but-disabled detection**. The missing-VRM4U dialog now distinguishes "not installed" (offers Fab listing) from "installed but disabled" (offers Enable & Restart).
- **VRM thumbnail preview** on the import file picker (reads the `.vrm` metadata via VRM4U's `GetVRMMeta` through reflection).
- **Default destination folder** changed to `/Game/EasyVRM` (was `/Game/Characters`). Each imported model still gets its own per-character subfolder.

### Changed

- **Toe retarget fix and outfit color repair moved into VRM4U itself**, removing duplication. Easy VRM keeps a manual **Repair Outfit** button for re-runs after editor-time color edits.
- **Material Tweaks section header** carries a per section status line so action results render next to the section title in regular text instead of the panel-wide status row.
- **Repair Outfit** now also fires Read From VRM automatically so the panel sliders snap to the corrected MIC values.
- **Reset to Original** and **Reset Defaults** buttons removed from the Material Tweaks panel. The flow is now `Repair Outfit` + `Read From VRM` for restoring imported state.
- **Spring physics master enable** toggle uses the VRM4U meta filter end to end so the asset's `VRMSpringMeta` array is what controls runtime behaviour. Runtime `Apply VRM Physics` keeps a per session in memory backup so re-enabling and per group changes work mid PIE without a level reload.
- **Fab compliance**: Easy VRM no longer declares VRM4U as a `.uplugin` dependency. All VRM4U interop is routed through a reflection bridge (`EasyVRMVrm4UBridge`) so the editor module has no compile-time dependency on VRM4U. Easy VRM still requires VRM4U to be present at runtime; the panel surfaces a clear missing-dependency dialog and links to the Fab listing.

### Fixed

- Eye, Hair, and Clothing material tweaks now match the body slot, not only the outline slot. Filter changed from `_SKIN_` / `_EYE_` etc. to word boundary aware tokens that catch both main and outline slots after the import organizer strips trailing underscores.
- Runtime shader swap no longer produces checkered slots. Implementation now mirrors the editor panel exactly (`SetParentEditorOnly` + `PostEditChange` + `FMaterialUpdateContext` in editor builds, reflection fallback in cooked).
- "Apply All material tweaks failed" toast no longer triggers spuriously when an empty category is iterated.
- Asset editor "quit unexpectedly" prompt no longer triggers on clean editor close (kill path now sends `WM_CLOSE` first).

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
