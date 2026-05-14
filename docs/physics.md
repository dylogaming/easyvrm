# Spring Bone Physics

VRoid avatars ship with authored spring bone data for hair, breast, and clothing. VRM4U imports this data into a `VrmMetaObject` asset and exposes a VrmSpringBone Animation Blueprint node that reads it at runtime. Easy VRM adds a per group enable and strength control on top, so you can turn hair physics off but keep clothing on (or any combination), with independent strength sliders.

## How spring bones work in VRM4U

A single `VrmMetaObject` on the SkeletalMesh holds every spring chain as a flat list. Each chain has a root bone (e.g., a hair root, a breast root, a cloth root) and a list of child bones that follow the parent with spring physics.

VRM4U also auto assigns a PostProcessAnimBlueprint to the imported SkeletalMesh. That AnimBP runs the VrmSpringBone node automatically whenever the mesh is rendered, regardless of which AnimBP is on the SK in your level. So unless you specifically clear the PostProcessAnimBlueprint, spring physics is always on.

Easy VRM works with both layers:

- The **AnimBP we generate** (when you click Apply to Character BP) contains a VrmSpringBone node wired to read the same `VrmMetaObject`, with strength driven by the panel's per group sliders.
- The **SK's PostProcessAnimBlueprint** is cleared when all three physics toggles are off, so disabling everything actually disables jiggle (otherwise the post process AnimBP would still drive it).

## Controls

The Spring Bone Physics row in the panel has three toggles and three sliders:

| Toggle | Strength Slider | What it controls |
| ------ | -------------- | ---------------- |
| **Breast Physics** | Breast Strength | Spring chains rooted at any bone whose name contains `Bust`. |
| **Hair Physics** | Hair Strength | Spring chains rooted at any bone whose name contains `Hair`. |
| **Clothing Physics** | Clothing Strength | Every other spring chain (`J_Sec_*`, cloth bones, accessories, etc.). |

Strength is a multiplier against the values VRoid Studio authored. `1.0` is the avatar's intended amount, `0.5` is half, `2.0` is double.

## How filtering works

When you change any toggle or slider, Easy VRM runs the **physics filter** against the SK's `VrmMetaObject`:

1. On first apply, the original `VrmMetaObject` is duplicated to `VrmMetaObject_<Name>_OriginalBackup` next to the SK. This is the source of truth, untouched by the filter.
2. The filter starts from the original and copies it back to the live `VrmMetaObject`.
3. Spring chains are categorized by their root bone name (Breast, Hair, or Clothing).
4. Chains in disabled groups are dropped from the list.
5. Each remaining chain has its stiffness and gravity scaled by the matching group's strength multiplier.
6. The live `VrmMetaObject` package is marked dirty and saved.
7. If all three toggles are off, the SK's `PostProcessAnimBlueprint` is cleared.
8. If any toggle is on, `PostProcessAnimBlueprint` is restored to VRM4U's default.

The filter is **idempotent**: running it twice with the same settings produces the same result. Running it with all three enabled and full strength restores the original verbatim from the backup.

## Live updates during PIE

Toggles and sliders apply immediately, even during Play in Editor. Easy VRM uses a "physics only" update path that touches just the `VrmMetaObject` and the SK's PostProcessAnimBlueprint reference; it does not recompile any Blueprint or rebuild the Animation Blueprint. So you can dial hair strength up and down while running around in PIE and see the result in real time.

This is intentional: tuning physics is a "feel it in motion" thing, and pausing PIE between every slider drag is the wrong workflow.

## Apply to Character BP integration

When you click Apply to Character BP, the current state of the three toggles is baked into the generated AnimBP:

- If any toggle is on, the AnimBP gets a VrmSpringBone node between the input pose and Output Pose, driven by the live `VrmMetaObject`.
- If all toggles are off, no VrmSpringBone node is added, and the SK's PostProcessAnimBlueprint is cleared so nothing drives jiggle from the side either.

Subsequent changes to the toggles and sliders apply live as described above; you don't need to re run Apply to Character BP after tweaking strength.

## Notes

- The categorization is name based (substring match on `Bust`, `Hair`, plus a default bucket). Custom VRoid models with non standard bone names may end up classified as Clothing. This is correct in practice for VRoid Studio exports, since cloth chains use `J_Sec_*` names that don't match the Breast or Hair patterns.
- The original backup asset (`VrmMetaObject_<Name>_OriginalBackup`) is preserved permanently. Keep it; it's the only way to reverse a filter that's been saved to disk.
- Strength multipliers are clamped at 0 to 5 in the UI. Anything above 2 tends to look like a cartoon spring, which may or may not be what you want.
- The `VrmSpringBone` node in our generated AnimBP currently uses the **max** of all enabled groups' strength multipliers as a global aggregate. Per group multipliers fully apply through the meta filter. If precise per group control matters, keep multipliers similar and tune the meta filter strengths.
