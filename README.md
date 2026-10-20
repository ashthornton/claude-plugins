# Claude Code skill for three.js TSL Material Export

A [Claude Code](https://claude.com/claude-code) skill that lets an agent bring a Blender material into your three.js project as a TSL node material, using the [three.js TSL Material Export](https://tools.ashthornton.dev/blender-to-tsl/) Blender add-on. With it installed, you don't have to tell the agent which add-on to use: asking for the material is enough.

```text
Bring the material on "MyObject" from the open Blender file into this project.
```

The skill is small on purpose. It finds the add-on's scripting API in Blender and follows the instructions that the installed add-on gives (`describe()["agent_guide"]`), so it stays right when the add-on updates.

## You need

- The three.js TSL Material Export add-on, installed and enabled in Blender. It's sold at https://tools.ashthornton.dev/blender-to-tsl/.
- Either the Blender MCP add-on (from Blender Lab's extension repository), enabled and connected, or Blender on the same machine for the command-line route (`blender -b file.blend --command tsl_export`).
- A three.js project that renders with `WebGPURenderer` from `three/webgpu`.

## Install

**As a plugin** (updates with `/plugin`):

```text
/plugin marketplace add ashthornton/claude-plugins
/plugin install blender-tsl@ashthornton-tools
```

Or from a shell: `claude plugin marketplace add ashthornton/claude-plugins`, then `claude plugin install blender-tsl@ashthornton-tools`. The skill is then called `blender-tsl:blender-tsl-export`.

**As a plain skill:** unzip `blender-tsl-export-skill-<version>.zip` into `~/.claude/skills/` (all your projects) or into a project's `.claude/skills/` (that project only), so that you get `~/.claude/skills/blender-tsl-export/SKILL.md`. Start a new Claude Code session, or run `/reload-skills` in an open one.

Either way, `/skills` lists it.

## Other agents

Agents that don't load Claude Code skills can read the same instructions from your project's `AGENTS.md` (or `CLAUDE.md`, `.cursorrules`, ...). Paste in [AGENTS-snippet.md](AGENTS-snippet.md).

## Licence

MIT-0 (see `LICENSE`). The add-on itself is sold separately and has its own licence.
