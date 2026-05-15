# Importing a VRM Avatar

The Easy VRM importer wraps VRM4U's asset importer with a quality of life panel that handles destination paths, naming, optional generated assets, and post import fixes in one place.

## Opening the panel

Three ways:

- Click the **Easy VRM** button on the main editor toolbar (in the Play toolbar section).
- Use **Window** to **Tools** to **Easy VRM**.
- Right click a folder in the Content Browser and choose **Import VRM here...**. This opens the panel with the destination prefilled.

The panel is dockable. Drag the tab to dock it next to the Content Browser, Details, or anywhere else.

## Importing

### 1. Pick a source VRM file

The **Source VRM file** row offers three ways to pick a file:

- **Drag and drop** a `.vrm` file from File Explorer onto the panel anywhere.
- Click **Browse...** and pick a `.vrm` file from disk. The folder you pick becomes the new library.
- Use the source dropdown to pick from `.vrm` files already in the library folder. Click **Refresh** next to **Browse...** to rescan after you add new files to that folder.

The panel remembers your last 8 imported files in the **Recent** menu, sitting to the right of the source dropdown.

A thumbnail of the avatar appears once a valid `.vrm` is selected.

### 2. Pick a destination folder

The **Import to:** row holds the destination path. The text box is read only; click the `...` button to open a folder picker. The default is `/Game/Characters`. The panel remembers your last destination across editor restarts.

You can also click **Use Current Folder** to set the destination to whatever folder is selected in the Content Browser.

### 3. Choose a folder name

To the right of the destination path is a `/` separator and a **subfolder name** text box. It defaults to the `.vrm` file's base name with spaces and special characters stripped. This name is used:

- as the subfolder under the destination,
- as the suffix on all generated assets (`SK_<Name>`, `SKEL_<Name>`, `RTG_<Name>`, etc.).

Final destination: `<Destination>/<FolderName>/`.

### 4. Advanced settings

To the right of the **Import to:** label is a small gear icon (⚙). Click it to open the advanced settings popup, which exposes:

- **Generate UE5 Mannequin-renamed SkeletalMesh (for retargeting)**: when on, VRM4U also produces a second SkeletalMesh with bones renamed to match the UE5 Mannequin skeleton (`pelvis`, `spine_01`, etc.). The original VRM SkeletalMesh is still produced. Useful for retargeting UE5 animations onto VRoid avatars. Default on.

### 5. Import

Click **Import**. Status updates appear in the status row directly below. On success, the panel:

- Selects the new folder in the Content Browser.
- Lists every created asset in the result list.
- Records the import as "last successful" so the **Undo Last Import** button is available.
- Adds the source file to the **Recent** list.
- Auto loads the per VRM material preset (if one is saved next to the source file) and snapshots a tweak baseline.

## What gets created

For a folder name `Hero`, you'll see (at minimum):

| Asset | Description |
| ----- | ----------- |
| `SK_Hero` | SkeletalMesh. The main asset most workflows reference. |
| `SK_Hero_UE5Mannequin` | UE5 Mannequin renamed variant, if you enabled it. |
| `SKEL_Hero` | Skeleton asset. |
| `PHYS_Hero` | Physics asset. |
| `IK_Hero` | IK Rig for the VRM skeleton. |
| `IK_UE5Mannequin` | IK Rig for the UE5 Mannequin (shared, only created once). |
| `RTG_Hero` | IK Retargeter that maps UE5 Mannequin pose to the VRM skeleton. |
| `VrmMetaObject_Hero` | VRM4U metadata asset (spring bones, blend shapes, etc.). |
| `M_<various>` | Per material MaterialInstanceConstants for skin, eyes, hair, clothing, face details. |
| `T_<various>` | Imported textures. |

Easy VRM also runs these post import passes automatically:

- **Toe retarget fix**: adjusts the auto generated IK Rigs to chain leg bones to the toe instead of the ankle, eliminating the visible toe wobble that affects UE5.7's stock VRM IK Rigs.
- **Outfit color repair**: where VRoid Studio's linked cloth color feature left a sibling material white, copies the correct tint from the donor material in the same cloth group (top, bottom, shoes, dress). Black corsets and brown bottoms are protected because they live in separate groups.

## Undo last import

The **Undo Last Import** button (next to **Import**) deletes the most recent import's folder, including every asset inside it. It only acts on imports made in the current editor session and is the right thing to use if you imported with the wrong name, into the wrong folder, or want to try different flags on the same file.

If something outside the imported folder references one of the deleted assets, UE will surface a reference confirmation dialog before the delete. You can cancel safely.

## Drag and drop notes

- Multiple `.vrm` files at once: not supported in the current version. Drag one at a time.
- Drag from the OS, not from inside UE. Dragging from the Content Browser does not select a `.vrm` source.

## Persistence

The panel remembers, per project user:

- Last source VRM and destination folder.
- Last folder name.
- Generate Mannequin renamed mesh toggle.
- Recent imports list (up to 8).
- Section expand and collapse state for every collapsible region.
- Spring bone physics master toggle, sub toggles, and strength values.

These are stored in `Saved/Config/<Platform>/EditorPerProjectUserSettings.ini` under the `[EasyVRM]` section.
