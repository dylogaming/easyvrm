# Troubleshooting

## VRM4U did not install

**Symptom**: After enabling Easy VRM and restarting the editor, the toolbar shows no Easy VRM button, or the panel opens with a yellow warning banner saying "VRM4U is not installed".

**Cause**: The cold install of VRM4U either failed, was cancelled, or its source payload is missing.

**Fix**:

1. Close the editor.
2. Check that `<Project>\Plugins\EasyVRM\ThirdParty\VRM4U_Payload\` exists and contains a `VRM4U.uplugin`. If not, the plugin install is corrupt; re install Easy VRM from your marketplace library.
3. Manually copy the `VRM4U_Payload` folder to `<Project>\Plugins\VRM4U` (sibling of EasyVRM, not nested).
4. Relaunch the editor.

You can also delete `<Project>\Plugins\VRM4U` entirely and relaunch; Easy VRM will detect the missing sibling and offer the install dialog again.

## Import fails silently with "Import task was cancelled"

**Symptom**: The status row shows "Import task was cancelled" or no status update at all; the destination folder stays empty.

**Cause**: This is a VRM4U issue when used programmatically. VRM4U's stock importer factory calls `FSlateApplication::Get().AddModalWindow` unconditionally, which UE auto cancels in automated import mode. Easy VRM uses automated import and requires a patched VRM4U.

**Fix**: Easy VRM ships with a patched VRM4U as its payload. If you have a separate VRM4U install that you didn't get from Easy VRM, replace it with the version Easy VRM bundles:

1. Close the editor.
2. Delete `<Project>\Plugins\VRM4U`.
3. Relaunch. Easy VRM offers to install its bundled VRM4U; click Install.

## Imported avatar appears with white clothing

**Symptom**: Tops or sleeves render as plain white textures, even though the source VRoid model has colored cloth.

**Cause**: VRoid Studio has a "linked cloth color" feature that lets sister cloth pieces share a tint. The `.vrm` format does not encode this link; it stores per material overrides and trusts the renderer to apply the linked color. VRM4U faithfully imports the per material value, which for the sister piece is white.

**Fix**: Easy VRM runs an automatic outfit color repair pass on every import. If yours did not, click **Read From VRM** in the Material Tweaks section, then **Apply All** to re trigger the propagation. If a specific cloth color is still white, edit the `mtoon_Color` on the affected MIC manually in the Content Browser.

## VRM character's feet wobble during retargeted locomotion

**Symptom**: When playing UE5 Mannequin animations on a VRM avatar via the generated retargeter, the toes and ankles jitter as the character moves.

**Cause**: UE5.7's auto generated IK Rig terminates leg chains at the foot bone. The UE5 Mannequin reference rig chains down to the ball / toe. This length mismatch produces the visible toe wobble.

**Fix**: Easy VRM auto applies the Yoshi UE Game Dev toe retarget fix on every import. The fix extends leg chains on both rigs to the toe bone. If yours did not (older import predating the fix), open the IK Rig assets for both the source and target and:

- Source rig: move LeftFootIK and RightFootIK goals from `foot_l` / `foot_r` to `ball_l` / `ball_r`.
- Target rig: remove the toe only retarget chains, then extend the leg chain end from `J_Bip_L/R_Foot` to `J_Bip_L/R_ToeBase`, then move the foot goals onto the toe bone.

Or re import the VRM, which triggers the auto fix.

## Material Tweaks panel is empty after selecting a VRM

**Symptom**: You set the Source VRM Mesh slot, but the Material Tweaks fields all sit at plugin defaults (white, 0.9 shade sharpness, etc.).

**Cause**: The panel reads from disk automatically when you change the source slot, so an empty panel usually means the source slot was rejected (wrong asset class, or the asset's package path does not look like a VRM import folder).

**Fix**:

1. Confirm the slot is pointing at an `SK_<Name>` asset that was produced by Easy VRM (or VRM4U with Easy VRM's naming convention). The slot is filtered to `SkeletalMesh`, but a hand authored mesh in a non standard folder will produce an empty read.
2. Click **Read From VRM** to force a manual sync. If the fields still don't populate, the VRM folder contains no MICs Easy VRM recognises; re import the VRM and try again.

## Apply to Character BP fails with "Cannot find inherited Mesh component"

**Symptom**: The Apply button reports "Cannot find inherited Mesh component on target BP".

**Cause**: The Target BP does not derive from `ACharacter` (or the parent class's mesh has been renamed). Easy VRM's apply path assumes the target is a Character BP with the standard inherited Mesh.

**Fix**: Use a BP that derives from `ACharacter` or one of its subclasses (the UE5 Third Person template BPs work out of the box). If you've authored a custom Character class with a renamed mesh, Apply to Character BP does not currently support it.

## Spring physics not changing when I toggle hair off

**Symptom**: You disable Hair Physics in the panel, but hair still jiggles in PIE.

**Cause**: The SK's `PostProcessAnimBlueprint` is what drives passive jiggle. Disabling a single category only filters the live `VrmMetaObject`; the AnimBP still reads what's left and animates it.

**Fix**: This is expected when at least one other category (Breast or Clothing) is still enabled. To fully disable hair jiggle:

- Turn Hair off (other categories can stay on). The hair chains are dropped from the `VrmMetaObject`, so VrmSpringBone has nothing to drive for them.
- Confirm the filter actually ran by reopening the panel; the Hair toggle should be off, and the change should persist.

If hair still jiggles after this, your `VrmMetaObject` may have hair chains with non standard root bone names that aren't matching the `Hair` substring. Open the meta in the Content Browser and check the spring chain root bone names.

## Panel disappears or fails to open

**Symptom**: Window to Easy VRM does nothing. The toolbar button is missing.

**Cause**: The editor module failed to load. Common reasons: VRM4U missing, VRM4U version mismatch, build error in a hot reload.

**Fix**:

1. Check `<Project>\Saved\Logs\<Project>.log` for lines starting with `LogEasyVRMBootstrap:` and `LogPluginManager: Mounting Project plugin EasyVRM`.
2. If you see `LogEasyVRMBootstrap: User declined VRM4U install`, you cancelled the install dialog. Relaunch and click Install.
3. If you see `LogPluginManager: Mounting Project plugin VRM4U` followed by errors, your VRM4U sibling is broken; delete it and relaunch.
4. If neither line appears, Easy VRM itself failed to mount. Re install Easy VRM from your marketplace library.

## Where to find logs

- Project log: `<Project>\Saved\Logs\<Project>.log`
- Easy VRM emits under category `LogEasyVRM` and `LogEasyVRMBootstrap`.
- VRM4U emits under `LogVRM`, `LogVRM4UImporter`, and `LogVRM4U`.

When reporting a bug, include the full project log with your reproduction steps.

## Reporting bugs

Email dylogamingofficial@gmail.com with:

- UE version (e.g. `5.7.4`).
- Easy VRM version (in the plugin's `EasyVRM.uplugin` `VersionName`).
- A minimal `.vrm` file that reproduces the issue, if relevant.
- The full project log from a session where the issue happens.
- A short description of what you expected to happen and what actually happened.
