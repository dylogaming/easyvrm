# Material Tweaks

The Material Tweaks section of the Easy VRM panel lets you adjust every MToon shading parameter on an imported avatar without leaving the importer. Changes preview live in a built in viewport and apply to the actual MaterialInstanceConstants on disk when you click **Apply All**.

## Layout

Three columns:

1. **Left column**: per body group properties for **Skin**, **Face Details**, **Eyes**.
2. **Middle column**: live persona style preview viewport with the avatar rotating on a turntable. Zoom, orbit, and frame controls.
3. **Right column**: per body group properties for **Hair**, **Clothing**, and **All Materials** (the global override).

Each body group is a collapsible category with 14 MToon parameters:

| Parameter | What it does |
| --------- | ----------- |
| **Lit Color** | Multiplies the base albedo. `mtoon_Color`. |
| **Shade Color** | Color used in shadow. `mtoon_ShadeColor`. |
| **Shade Shift** | Where the shadow boundary lands. `mtoon_ShadeShift`. |
| **Shade Sharpness** | How hard the shadow edge is. `mtoon_ShadeToony`. |
| **Normal Strength** | Normal map intensity. `mtoon_NormalScale`. |
| **Receive Shadow** | How much cast shadow lands on the surface. `mtoon_ReceiveShadowRate`. |
| **Outline Width** | Outline thickness. `mtoon_OutlineWidth`. |
| **Outline Color** | Outline tint. `mtoon_OutlineColor`. |
| **Outline Lit Mix** | How much the outline blends with the lit color. `mtoon_OutlineLightingMix`. |
| **Rim Color** | Rim light tint. `mtoon_RimColor`. |
| **Rim Lit Mix** | Rim intensity. `mtoon_RimLightingMix`. |
| **Rim Fresnel** | Rim falloff curve. `mtoon_RimFresnelPower`. |
| **Rim Lift** | Rim base brightness. `mtoon_RimLift`. |
| **Emission Color** | Self emission (HDR, use values above 1 for glow). `mtoon_EmissionColor`. |

## Workflow

### Open the panel on an existing import

1. With the importer panel open, set the **Source VRM SkeletalMesh** slot (in the **Apply to Character Blueprint** section) to the imported `SK_<Name>`. The Material Tweaks section reads from this asset.
2. Expand **VRM Material Tweaks**.
3. Click **Read From VRM**. The tweak fields populate with the current values on disk.

If a per VRM preset exists at `<VrmFolder>/EasyVRM_MatPreset.json`, it auto loads on selection. The first time the panel reads from a VRM it also snapshots the on disk state as the **original baseline** (used by **Reset to Original**).

### Make changes

Drag sliders, click color swatches, edit numeric fields. The persona preview updates live (interactive drag updates included). The actual MICs on disk are not touched until you click **Apply All**.

### Apply

Click **Apply All**. Every MI in the VRM folder is updated and saved.

The apply pass is **smart and surgical**:

- It compares the current tweak values against the baseline captured on the last disk read.
- Parameters that match the baseline (you didn't touch them) are not pushed.
- This preserves per slot tints. VRM4U's hair tone variation, for example, lives in MID overrides that inherit from a parent MIC. If we pushed every value to every MID, those variations would get flattened. Easy VRM only writes the ones you changed.

After Apply, the persona viewport stays overlaid with the in memory tweak state, which now matches disk.

### Reset

Three reset paths, all under the Material Tweaks section:

- **Reset**: revert the in memory tweak fields to engine defaults (white, 0.9 shade sharpness, etc.). Does not touch disk.
- **Reset to Original**: revert the in memory tweak fields to the snapshot captured the first time you read this VRM from disk. The "original" snapshot is preserved across sessions in the auto saved per VRM preset.
- Click **Apply All** after either reset to write the new state to disk.

## Presets

### Per VRM preset (automatic)

Click **Save Per VRM**. Writes `EasyVRM_MatPreset.json` next to the VRM source file (the `.vrm` on disk, not the imported folder). The next time you select that VRM in the importer, the preset auto loads.

Click **Load Per VRM** to manually re read it without selecting the VRM again.

### Named presets (library)

Click **Save As Named** to save the current tweak state under a chosen name (no file extension required). The preset is saved into the project's `Saved/EasyVRM/Presets/` folder.

Click **Load Named Preset** to open a menu of every named preset in the library. Pick one to apply.

Named presets are project local. Copy the `Saved/EasyVRM/Presets/` folder into a new project to share them.

## Eye color quick pick

The **Eye Color** color swatch above the tweak section is a shortcut that drives `Eyes.LitColor`. Click it to open the color picker, drag to live preview, release to commit. Equivalent to expanding **Eyes** in the right column and editing **Lit Color** there.

## Categories merge note (advanced)

Each body group is a separate `FEasyVRMTweakStruct` in the backing UObject. The Details panel renders them as flat lists under each category header (no inner subcategories), so the same 14 parameters show up under each of the 6 categories without duplicate headers.

The **All Materials** category overrides every MIC in the folder regardless of which body group it belongs to. Use it for global passes (raising every outline width, tinting everything warmer). To combine with per group tweaks, set the global pass first, click Apply, then dial in per group tweaks.
