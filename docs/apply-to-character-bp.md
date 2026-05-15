# Apply to Character Blueprint

The Apply to Character Blueprint section lets you hook an imported VRM avatar onto an existing UE Character Blueprint in one click. After Apply, the BP renders the VRM mesh in place of the Mannequin, retargets all of the Mannequin's animations onto the VRM skeleton, and is ready to play.

## When to use this

- You have a working UE5 Third Person template character (or any other ACharacter derived Blueprint) with its own input, camera, and gameplay logic.
- You want to swap the Mannequin SkeletalMesh for a VRM avatar without redoing any of that work.
- Multiple variants are useful (different VRMs, same gameplay BP). Apply hooks one VRM at a time. To switch, click **Remove from Character BP** and apply a different one.

## Setup

The section lives in the Easy VRM panel below the importer. The left column holds two asset slots and the Apply / Remove buttons; the right column holds the spring bone physics controls (see [physics.md](physics.md)).

Left column layout:

```
[ Source VRM Mesh slot ]
[ Target Character Blueprint slot ]
[ Apply ]   [ Remove ]
```

1. **Source VRM Mesh**: pick `SK_<Name>` from a previously imported VRM. The panel auto fills this from your last import; you can change it to any other `SK_<Name>` in the project.
2. **Target Character Blueprint**: pick the Character Blueprint you want to apply the VRM onto. Must inherit `ACharacter`. Persists across editor restarts so subsequent applies on the same BP are one click.

Click **Apply**.

## What Apply does

For Target BP `BP_ThirdPersonCharacter` and VRM folder name `Hero`:

1. **Generates an Animation Blueprint** at `<VrmFolder>/ABP_BP_ThirdPersonCharacter_Hero`.
   - Single **Retarget Pose From Mesh** node wired to the Output Pose.
   - `RetargetFrom = ParentSkeletalMeshComponent` (reads pose from whatever component this AnimBP's component is attached to).
   - `IKRetargeterAsset = RTG_Hero` (the IK Retargeter the importer built).
2. **Adds (or updates) a `VRM_Mesh` SkeletalMeshComponent** on the Target BP, parented to the inherited Mannequin Mesh component (under the BP's components tree).
   - `SkeletalMesh = SK_Hero`
   - `AnimClass = ABP_BP_ThirdPersonCharacter_Hero`
   - `VisibilityBasedAnimTickOption = AlwaysTickPoseAndRefreshBones`
   - `RelativeLocation` and `RelativeRotation` zeroed (the VRM's root sits at the same world transform as the Mannequin root).
3. **Hides the inherited Mannequin Mesh**. The plugin modifies the Target BP's CDO (Class Default Object) to set `bHiddenInGame = true` and `VisibilityBasedAnimTickOption = AlwaysTickPoseAndRefreshBones` on the inherited Mesh. UE serializes this as a property delta against the parent class CDO and it survives recompiles.
4. **Compiles and saves** both the AnimBP and the Target BP packages.

After Apply, hitting **Play** in the editor shows the VRM avatar in place of the Mannequin, animating the Mannequin's existing locomotion (idle, walk, run, jump, etc.) retargeted onto the VRM skeleton.

## Why "child of inherited Mesh" matters

The VRM component is parented to the Mannequin Mesh component, not to the root scene component. Reasons:

- The Mannequin Mesh is what drives the pose. Parenting the VRM to it means the VRM's `ParentSkeletalMeshComponent` resolves to the Mannequin automatically. No manual wiring.
- Any locomotion that animates the Mannequin Mesh directly (root motion, montages, IK on the Mannequin) reaches the VRM through the retargeter.
- The Mannequin Mesh is hidden, not destroyed. If something in the BP graph references it (most templates do, e.g. for damage feedback), those references continue to resolve.

If you ever need to inspect the components, expand the Target BP, drill into the Components panel, and you'll see `VRM_Mesh` nested under `Mesh (Inherited)`.

## Optional flags

When you click **Apply**, the panel honors any toggles set in the **Spring Bone Physics** section:

- Breast, Hair, and Clothing physics toggles
- Per group strength sliders

These configure the AnimBP's VrmSpringBone node and also filter the SK's `VrmMetaObject` so disabled groups don't drive jiggle in PIE.

See [physics.md](physics.md) for the details.

## Remove from Character BP

The **Remove** button next to **Apply** undoes the application:

- Removes the `VRM_Mesh` SkeletalMeshComponent from the Target BP.
- Restores the inherited Mannequin Mesh visibility (clears `bHiddenInGame` on the CDO).
- Compiles and saves the BP.

It leaves the generated AnimBP and the imported VRM assets on disk (other BPs may reference them, and they cost almost nothing to keep). Re running **Apply** afterwards is fine and produces the same result.

## Notes and gotchas

- **Asset slot path quirk**: the source SK slot returns the OBJECT path (`/Game/X/SK_Y.SK_Y`). Easy VRM strips the `.SK_Y` suffix before deriving the AnimBP short name, since UE rejects '.' in asset names. You'll see a clean `ABP_Target_VrmName` regardless.
- **AnimBP name length**: the AnimBP name is `ABP_<TargetBP>_<VrmName>`. UE's asset name limit applies; if both names are very long, shorten the VRM folder name during import.
- **Multiple Applies on the same BP with different VRMs**: each new Apply replaces the `VRM_Mesh` component's SkeletalMesh and re points the AnimBP. The previous AnimBP and SK assets are left on disk.
- **Retargeter quality**: the `RTG_<Name>` produced by VRM4U is a sensible default but not perfectly tuned for every VRoid avatar. Easy VRM auto applies the toe retarget fix on import; for other animation issues (finger spread, head tilt), edit `RTG_<Name>` in the editor as you would any retargeter.
- **Character physics**: the inherited Mannequin Mesh's capsule and physics asset stay in charge of character movement. The VRM mesh is purely a visual layer on top.
