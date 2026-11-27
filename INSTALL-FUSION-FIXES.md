# Opening Fusion 360 files in FreeCAD: installing the fixed InventorLoader

The official InventorLoader add-on can't open Fusion 360 files saved by current versions of Fusion. On FreeCAD 1.x the import also silently does nothing. This version fixes that. No programming is needed: you install it with FreeCAD's own Addon Manager and paste one line once.

**You need** FreeCAD 1.0 or newer. Tested with FreeCAD 1.1.4 and the 27.1 weekly build on macOS.

## 1. Remove the official InventorLoader (if you have it)
1. In FreeCAD open **Tools → Addon Manager**.
2. Find **InventorLoader**, click it, then click **Uninstall**.
3. Restart FreeCAD.

## 2. Tell the Addon Manager where this version lives
1. Open the preferences: **FreeCAD → Settings…** (or **Preferences…**) on macOS, **Edit → Preferences…** on Windows and Linux.
2. Choose **Addon Manager** in the list on the left.
3. Under **Custom repositories**, click **+** and enter:
   - **Repository URL:** `https://github.com/ledoent/InventorLoader`
   - **Branch:** `fusion-fixes`
4. Click **OK**, then **OK** again to close the preferences.

## 3. Install it
1. Open **Tools → Addon Manager** again.
2. Find **InventorLoader**, the copy from the custom repository, and click **Install**.
3. If FreeCAD offers to install Python dependencies, accept.
4. Restart FreeCAD.

## 4. Install the helper libraries (once)
Fusion files saved since 2025 use a compression method that the Python built into FreeCAD can't read yet. This step adds the small libraries InventorLoader needs, only for your user, without changing FreeCAD itself.

1. Open **View → Panels → Python console**.
2. Copy the whole line below, paste it into the console and press **Enter**:

```python
import os, sys, FreeCAD; from pip._internal.cli.main import main as pip; d = os.path.join(FreeCAD.getUserAppDataDir(), "AdditionalPythonPackages", "py%d%d" % sys.version_info[:2]); os.makedirs(d, exist_ok=True); pip(["install", "--no-deps", "--target", d, "olefile", "xlrd", "xlwt", "xlutils", "ezdxf", "zstandard"]); print("Done - restart FreeCAD")
```

3. Wait until it prints **Done - restart FreeCAD**. This takes under a minute and needs an internet connection.
4. Restart FreeCAD.

## 5. Open a Fusion file
1. **File → Open…** and pick a `.f3d` (single design) or `.f3z` (assembly archive) file.
2. When asked how to import, click **convert to STEP**. FreeCAD remembers the choice.
3. The parts appear as solids named after the file or Fusion component.

## What to expect
- **Works:** solid parts from current Fusion files, with accurate size and volume. An `.f3z` archive brings in every part, each named after its Fusion component.
- **Not yet:**
  - **Threads** come in as a separate set of faces, not as part of the solid.
  - **The design history** (timeline, sketches, parameters) isn't imported.
  - **Assembly positions:** in an `.f3z`, each part sits where it was modelled in its own file, not necessarily where the assembly placed it.
- **Not working?** Open **View → Panels → Report view**. Error messages appear there.

## Going back to the official version
1. Remove the custom repository in **Preferences → Addon Manager** (select it, click **−**).
2. Uninstall InventorLoader in the Addon Manager, restart, and install it again from the normal list.

## Problems and feedback
Please comment on the pull request that proposes these fixes to the original project: https://github.com/jmplonka/InventorLoader/pull/116
