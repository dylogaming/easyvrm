# Style Presets and Shader Swaps

Easy VRM ships two complementary ways to change an avatar's look without re importing or hand editing materials:

- **Style presets** change MToon shading scalars (shade sharpness, outline width, rim, etc.). The MIs stay on their original MToon parent. The base character colors (skin tint, hair tint, clothing tint) are untouched.
- **Shader swaps** change the parent material itself (for example, swap MToon for the PBR variant). The MIs keep their asset paths and any compatible parameter values, so existing Blueprint references continue to work.

Both are in the Material Tweaks section of the Easy VRM panel.

## Style Presets

The **Style:** dropdown at the top of the Material Tweaks section lists five curated parameter sets:

| Name | Look |
| ---- | ---- |
| **Hard Cel** | Hard shadow line, thick outline, tight rim. Max anime. |
| **Soft Cel** | Soft shadow gradient, medium outline. Gentle anime. |
| **Painterly** | No outline, smooth gradient, broad rim glow. |
| **Realistic** | Flat smooth shading, no outline, subtle rim. Closest to PBR within MToon. |
| **Idol / Glow** | Stage lighting: soft shade, intense halo rim. |

Pick one to apply it to the in memory tweak state. Click **Apply All** to write the new state to disk.

What gets touched by a style:

- `ShadeToony` (shade sharpness)
- `ShadeShift` (shadow boundary)
- `OutlineWidth`
- `OutlineLightingMix`
- `RimLightingMix`
- `RimFresnelPower`

What does **not** get touched:

- `mtoon_Color` (lit color tints)
- `mtoon_ShadeColor` (shadow tints)
- `mtoon_OutlineColor`
- `mtoon_RimColor`
- `mtoon_EmissionColor`
- `NormalStrength`, `ReceiveShadow`, `RimLift`

This separation keeps a character's authored palette intact while letting you swap stylistic looks freely. Apply Hard Cel to a hero, then to a villain, and each keeps their own colors.

### Cycling with arrow keys

After picking a style from the menu, the panel grabs keyboard focus. Press the **Left** or **Right** arrow keys (or Up / Down) to cycle through the style list in the same order shown in the menu. The persona viewport updates live. The applied style name shows as a badge in the bottom corner of the persona viewport, with a `(preview)` suffix until you click **Apply All**.

Keyboard focus moves on the first click outside the panel; pick a style again to start cycling once more.

## Shader Swaps

The **Shader:** dropdown next to the Style dropdown lists five VRM4U parent materials worth swapping in:

| Name | Look |
| ---- | ---- |
| **MToon (default)** | Standard MToon cel shading. Full `mtoon_*` parameter set. |
| **MToon + SSS** | MToon with subsurface scattering for softer, skin like translucency. |
| **Realistic (PBR)** | UE standard PBR shading. Removes cel boundaries. Some MToon overrides may not carry over. |
| **Realistic + Subsurface** | PBR with subsurface scattering. Realistic look with soft skin shading. |
| **Unlit (flat)** | Flat shading, no lighting math. Stylized / dollhouse look. |

Pick one and click the menu item to perform the swap. The swap:

1. Scans every MaterialInstanceConstant under the VRM's import folder.
2. Reassigns each MI's `Parent` to the chosen variant.
3. Carries over any parameter values whose names match between the old parent and the new one. Names that don't match silently no op.
4. Saves every touched MI's package.

The MIs keep their asset paths and UObject identities. Blueprints, SkeletalMeshes, and any other references continue to point at the same MIs and pick up the new look automatically.

### Cycling shaders with arrow keys

Same pattern as styles: pick a shader from the menu, then use arrow keys to cycle the list. Each swap is a fresh disk operation; cycling fast still writes to disk on every step.

### Style and shader interaction

Styles and shader swaps are independent. After swapping shaders, the previously applied style's parameter values are still in memory and still apply (where the new parent exposes the same parameter names). You can swap to PBR and the outline width slider keeps working, because the PBR parent in VRM4U also exposes outline parameters.

The on screen badge tracks the **style**. Shader changes do not appear in the badge.

## Resetting

To go back to VRM4U's default MToon look without uninstalling anything:

1. Open the Material Tweaks section and use the Shader dropdown to swap back to **MToon (default)**.
2. Click **Reset to Original**. The tweak fields revert to the snapshot the panel captured the first time it read this VRM.
3. Click **Apply All** to write that state to disk.
