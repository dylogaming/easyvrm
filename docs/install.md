# Installation and First Launch

Easy VRM ships as a single Unreal Engine plugin, with VRM4U bundled inside. On first launch the plugin offers to copy VRM4U next to itself as a sibling plugin. You do not need to download VRM4U separately, and you do not need internet access for the install.

## Requirements

- Unreal Engine 5.7 (Windows 64 bit).
- About 250 MB of free disk space (Easy VRM itself plus the VRM4U sibling that gets installed).
- A Project that allows Editor plugins. Blueprint only projects work; you do not need a C++ toolchain.

## Step 1: enable the plugin

1. Open your Unreal project.
2. Open the **Plugins** window (`Edit` to `Plugins`).
3. In the **Installed** to **Importers** category, find **Easy VRM**.
4. Tick **Enabled**.
5. When prompted, restart the editor.

## Step 2: first launch (VRM4U cold install)

The first time Easy VRM starts up after install, it checks whether VRM4U is already installed as a sibling plugin. If not, a dialog appears:

> Easy VRM uses VRM4U under the hood and needs to install it as a sibling plugin.
>
> Click 'Install' to copy the bundled VRM4U to:
>   `<YourProject>\Plugins\VRM4U`
>
> The editor will need to restart afterwards. No internet or download is required.

Click **Install**. The plugin copies the bundled VRM4U payload to the sibling slot, then offers to restart the editor.

After the restart, Easy VRM detects VRM4U as present and loads its full editor module. The **Easy VRM** button appears on the main toolbar, and the **Window** menu gains an **Easy VRM** entry.

If VRM4U is already present (for example, you had it installed manually), Easy VRM detects it and skips the install dialog entirely.

## Step 3: verify

1. Click the **Easy VRM** button on the toolbar.
2. The importer panel should open as a dockable tab.
3. The panel should show the **Source VRM**, **Destination Folder**, and **Folder name** fields without any "VRM4U missing" warning at the top.

If you see a warning banner reading "VRM4U is not installed", VRM4U did not install successfully. See [Troubleshooting](troubleshooting.md#vrm4u-did-not-install).

## Where things land on disk

- `<Project>\Plugins\EasyVRM\` is the plugin itself.
- `<Project>\Plugins\VRM4U\` is the sibling plugin Easy VRM installed for you. Treat it as part of Easy VRM. Do not delete it unless you are removing Easy VRM entirely.
- Imported VRM avatars land under `<Project>\Content\Characters\<Name>\` by default. You can pick any destination folder per import.

## Uninstalling

To remove Easy VRM:

1. Close the editor.
2. Delete `<Project>\Plugins\EasyVRM\`.
3. If you no longer need VRM avatars to load at runtime, also delete `<Project>\Plugins\VRM4U\`. If you keep VRM4U, your imported avatars continue to work without Easy VRM's editor tools.
4. Reopen the project.

If your project references imported avatars and you delete VRM4U, those assets will fail to load. Keep VRM4U as long as you have VRM avatars in your project.
