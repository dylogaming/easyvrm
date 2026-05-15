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

1. With the importer panel open, set the **Source VRM Mesh** slot (in the **Apply to Character Blueprint** section) to the imported `SK_<Name>`. The Material Tweaks section reads from this asset.
2. Expand **VRM Material Tweaks**.

The tweak fields auto populate from the MICs on disk the moment you set the Source VRM Mesh slot, so you can start editing immediately. The first time the panel reads from a VRM, it also snapshots the on disk state as the **original baseline** used by **Reset to Original**.

If you ever need to re sync the panel against disk manually (after editing MICs outside Easy VRM, for example), click **Read From VRM**.

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

Two reset buttons sit in the Material Tweaks top button row:

- **Reset Defaults**: revert the in memory tweak fields to plugin defaults (white, 0.9 shade sharpness, etc.). Does not touch disk. Click **Apply All** afterwards to commit.
- **Reset to Original**: revert the in memory tweak fields to the snapshot captured the first time the panel read this VRM from disk. Click **Apply All** afterwards to commit.

The original snapshot is captured automatically on the first source select after import; you do not need to opt in.

## Repair Outfit

The **Repair Outfit** button in the Material Tweaks top button row re runs the outfit color repair pass (the same pass that runs automatically at import time). Use it if you swapped shaders, hand edited MIC colors, or imported with an older Easy VRM version and your sister cloth pieces are showing white where they should be tinted.

It only acts on sister materials within the same cloth group (Tops, Bottoms, Shoes, Onepiece kept separate). Materials with their `mtoon_Color` already set to anything non white are not touched.

## Categories merge note (advanced)

Each body group is a separate `FEasyVRMTweakStruct` in the backing UObject. The Details panel renders them as flat lists under each category header (no inner subcategories), so the same 14 parameters show up under each of the 6 categories without duplicate headers.

The **All Materials** category overrides every MIC in the folder regardless of which body group it belongs to. Use it for global passes (raising every outline width, tinting everything warmer). To combine with per group tweaks, set the global pass first, click Apply, then dial in per group tweaks.
