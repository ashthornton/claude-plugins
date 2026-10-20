## Blender materials → three.js

To bring a material (or a mesh with its material) from Blender into this project, use the "three.js TSL Material Export" Blender add-on's scripting API. Don't write your own node-tree converter.

- In Blender's Python (for example the Blender MCP's `execute_blender_code`, which returns the variable `result`), find the API with:

  ```python
  import bpy, sys
  tsl = next((m for n, m in sys.modules.items() if n.endswith(".blender_to_tsl.api")
              and n[:-len(".api")] in bpy.context.preferences.addons), None)
  result = tsl.describe() if tsl else None
  ```

  Then follow `describe()["agent_guide"]` step by step.
- If `tsl` is `None`, the add-on isn't installed or enabled. Stop and tell me. It's at https://tools.ashthornton.dev/blender-to-tsl/.
- Without a connection to Blender, run `blender -b <file.blend> --command tsl_export --describe` and follow the `agent_guide` in the JSON on the line starting `TSL_RESULT `. `--command tsl_export --help` lists the flags. In Git Bash on Windows, put `MSYS_NO_PATHCONV=1` in front.
