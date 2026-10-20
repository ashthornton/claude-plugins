---
name: blender-tsl-export
description: Bring a Blender material (shader node tree), or a mesh with its material, into a three.js project as a TSL node material module, using the "three.js TSL Material Export" Blender add-on through the Blender MCP or Blender's command line. Use when the user asks to export, convert, port or bring a material, shader or object from Blender (the open file or a .blend) into three.js, TSL, WebGPU, react-three-fiber or their web project, even if they don't name the add-on.
license: MIT-0
---

# Blender material → three.js TSL module

The "three.js TSL Material Export" add-on does the conversion and carries the step-by-step instructions for the version that's installed. This skill only finds it. Don't write your own node-tree converter, and don't copy the add-on's code into Blender.

## 1. Find the add-on's API

If you have a tool that runs Python in Blender (the Blender MCP's `execute_blender_code`, which returns whatever you assign to `result`), run:

```python
import bpy, sys
tsl = next((m for n, m in sys.modules.items() if n.endswith(".blender_to_tsl.api")
            and n[:-len(".api")] in bpy.context.preferences.addons), None)
result = tsl.describe() if tsl else {"ok": False, "error": "add-on not enabled",
    "blend_file": bpy.data.filepath,
    "similar_addons": [a for a in bpy.context.preferences.addons.keys() if "tsl" in a.lower()]}
```

Look the API up the same way in every later call (`tsl` doesn't persist between calls). There's no fixed module name to import: the package name depends on where the add-on was installed from.

## 2. Follow the add-on's guide

`describe()["agent_guide"]` is the workflow: inspect the project, export, read the report, wire it in, verify. Follow it step by step. `describe()["module_contract"]` describes the generated module, and `describe()["options"]` / `["functions"]` the API. Mention `describe()["exporter_version"]` in your report.

Throughout:
- Every API function returns JSON and never raises. Check `ok` and show `error` to the user.
- Don't save, rename or otherwise change the user's .blend. The export functions work on temporary copies.
- Tell the user about every issue with severity `warning` or `error` in the report.

## When something's missing

**The lookup gives `None`** (`"add-on not enabled"`): stop and tell the user the three.js TSL Material Export add-on isn't installed or enabled in this Blender. It's available from https://tools.ashthornton.dev/blender-to-tsl/. Once installed, enable it in **Edit ▸ Preferences ▸ Add-ons**, then ask again. If `similar_addons` lists a `blender_to_tsl` package, it's enabled but too old to have the scripting API, so ask them to update it.

**No tool can reach Blender** (no Blender MCP, or it isn't connected): use Blender's command line instead. It runs the same API on a saved .blend:

1. Find Blender: `blender` on `PATH`, the `BLENDER` environment variable, or the standard install folder (`C:/Program Files/Blender Foundation/Blender */blender.exe`, `/Applications/Blender.app/Contents/MacOS/Blender`). Ask the user if you can't find it.
2. Ask which .blend to use if it isn't obvious. Unsaved changes in an open Blender aren't in the file on disk, so say so.
3. Get the guide: `blender -b <file.blend> --command tsl_export --describe`. The result is the one output line that starts with `TSL_RESULT `, followed by JSON. Then follow its `agent_guide`, using the command line wherever it says to call a function: `--command tsl_export --help` lists the flags (`--list`, `--material`, `--out`, `--textures`, `--glb`, `--three-import`, `--tsl-import`, `--texture-base-url`, ...).
4. In Git Bash on Windows, put `MSYS_NO_PATHCONV=1` in front of the command, or arguments like `--texture-base-url /textures` get rewritten to `C:/Program Files/Git/textures`.
5. If Blender says `tsl_export` isn't a known command, the add-on isn't enabled in that Blender's profile: tell the user as above.

If neither works, tell the user what you need (the Blender MCP connected, or a path to Blender and the .blend) rather than exporting some other way.
