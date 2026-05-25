# Installation

Easy VRM is an editor layer on top of **VRM4U**, which does the actual `.vrm` parsing and runtime loading. Both plugins are free on Fab. You install VRM4U once, then Easy VRM, into the same project.

## Requirements

- Unreal Engine 5.7 (Windows 64 bit).
- **DYLO's VRM4U** installed in the same project. It is free on Fab and is the runtime dependency that does the heavy lifting. Upstream source: https://github.com/ruyo/VRM4U
- A project that allows Editor plugins. Blueprint only projects work; you do not need a C++ toolchain.

## Step 1: install DYLO's VRM4U

1. On Fab, get **DYLO's VRM4U** (free) and add it to your project, or add it to your Library and install it through the Epic Games Launcher.
2. This is required. Easy VRM will not function without it.

## Step 2: install Easy VRM

1. On Fab, add **Easy VRM** to the same project.
2. Easy VRM declares VRM4U as a dependency, so if you add Easy VRM first the launcher will prompt you to add VRM4U as well.

## Step 3: enable both plugins

1. Open your Unreal project.
2. Open the **Plugins** window (`Edit` to `Plugins`).
3. Confirm both **VRM4U** and **Easy VRM** are present and **Enabled**.
4. If you changed any enable state, restart the editor when prompted.

## Step 4: verify

1. Click the **Easy VRM** button on the main toolbar (or open it from `Window` to `Easy VRM`).
2. The importer panel should open as a dockable tab.
3. The panel should show the **Source VRM**, **Destination Folder**, and **Folder name** fields with no warning banner at the top.

If you see a banner reading **"DYLO's VRM4U is required but not installed"**, VRM4U is missing or disabled. See [Troubleshooting](troubleshooting.md#vrm4u-not-detected).

## Where things land on disk

- `<Project>\Plugins\EasyVRM\` is the Easy VRM plugin (editor only).
- `<Project>\Plugins\VRM4U\` is the VRM4U dependency. Keep it as long as you have VRM avatars in your project.
- Imported VRM avatars land under `<Project>\Content\Characters\<Name>\` by default. You can pick any destination folder per import.

## Uninstalling

To remove Easy VRM:

1. Close the editor.
2. Delete `<Project>\Plugins\EasyVRM\`.
3. Reopen the project.

VRM4U is independent. You can keep it (your imported avatars continue to load and run without Easy VRM's editor tools) or remove it separately. If your project references imported avatars and you delete VRM4U, those assets will fail to load, so keep VRM4U as long as you have VRM avatars in your project.
