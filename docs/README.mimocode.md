# Superpowers for MiMoCode

[MiMoCode](https://github.com/mimo-ai/mimocode) is an AI-powered coding agent
CLI built by the Xiaomi MiMo Team. It helps developers with software engineering
tasks through interactive conversation and tool execution.

Complete guide for using Superpowers with MiMoCode.

## Installation

Add superpowers to the `plugin` array in your `mimocode.jsonc` (global or project-level):

```jsonc
{
  "plugin": ["superpowers@git+https://github.com/sdkwork-ai/sdkwork-superpowers.git"]
}
```

Restart MiMoCode. The plugin installs through MiMoCode's plugin manager and
registers all skills.

Verify by asking: "Tell me about your superpowers"

MiMoCode uses its own plugin install. If you also use Claude Code, Codex, or
another harness, install Superpowers separately for each one.

### Migrating from the old symlink-based install

If you previously installed superpowers using `git clone` and symlinks, remove the old setup:

```bash
# Remove old symlinks
rm -f ~/.config/mimocode/plugins/superpowers.js
rm -rf ~/.config/mimocode/skills/superpowers

# Optionally remove the cloned repo
rm -rf ~/.config/mimocode/superpowers

# Remove skills.paths from mimocode.jsonc if you added one for superpowers
```

Then follow the installation steps above.

## Usage

### Finding Skills

Use MiMoCode's native `skill` tool to list all available skills:

```
use skill tool to list skills
```

### Loading a Skill

```
use skill tool to load superpowers/brainstorming
```

### Personal Skills

Create your own skills in `~/.config/mimocode/skills/`:

```bash
mkdir -p ~/.config/mimocode/skills/my-skill
```

Create `~/.config/mimocode/skills/my-skill/SKILL.md`:

```markdown
---
name: my-skill
description: Use when [condition] - [what it does]
---

# My Skill

[Your skill content here]
```

### Project Skills

Create project-specific skills in `.mimocode/skills/` within your project.

**Skill Priority:** Project skills > Personal skills > Superpowers skills

## Updating

MiMoCode installs Superpowers through a git-backed package spec. Some MiMoCode
and Bun versions pin that resolved git dependency in a lockfile or cache, so a
restart may not pick up the newest Superpowers commit. If updates do not appear,
clear MiMoCode's package cache or reinstall the plugin.

To pin a specific version, use a branch or tag:

```jsonc
{
  "plugin": ["superpowers@git+https://github.com/sdkwork-ai/sdkwork-superpowers.git#v5.0.3"]
}
```

## How It Works

The plugin does two things:

1. **Injects bootstrap context** via the `experimental.chat.system.transform` hook, adding superpowers awareness to every conversation.
2. **Registers the skills directory** via the `config` hook, so MiMoCode discovers all superpowers skills without symlinks or manual config.

### Tool Mapping

Skills written for Claude Code are automatically adapted for MiMoCode:

- `TodoWrite` → `todowrite`
- `Task` with subagents → MiMoCode's `@mention` system
- `Skill` tool → MiMoCode's native `skill` tool
- File operations → Native MiMoCode tools

## MiMoCode vs OpenCode

MiMoCode is derived from OpenCode. Key differences for Superpowers:

| Aspect | OpenCode | MiMoCode |
|--------|----------|----------|
| Config directory | `~/.config/opencode/` | `~/.config/mimocode/` |
| Config file | `opencode.json` | `mimocode.jsonc` |
| Plugin directory | `.opencode/plugins/` | `.mimocode/plugins/` |
| Skills directory | `~/.config/opencode/skills/` | `~/.config/mimocode/skills/` |
| Binary command | `opencode` | `mimo` |
| Plugin install | `opencode plugin <module>` | `mimo plugin <module>` |

The plugin API and hooks are identical between OpenCode and MiMoCode.

## Troubleshooting

### Plugin not loading

1. Check MiMoCode logs: `mimo run --print-logs "hello" 2>&1 | grep -i superpowers`
2. Verify the plugin line in your `mimocode.jsonc` is correct
3. Make sure you're running a recent version of MiMoCode

### Windows install issues

Some Windows MiMoCode builds have upstream installer issues with git-backed
plugin specs, including cache paths for `git+https` URLs and Bun not finding
`git.exe` even when it works in a normal terminal. If MiMoCode cannot install
the plugin, try installing with system npm and pointing MiMoCode at the local
package:

```powershell
npm install superpowers@git+https://github.com/sdkwork-ai/sdkwork-superpowers.git --prefix "$HOME\.config\mimocode"
```

Then use the installed package path in `mimocode.jsonc`:

```jsonc
{
  "plugin": ["~/.config/mimocode/node_modules/superpowers"]
}
```

### Skills not found

1. Use MiMoCode's `skill` tool to list available skills
2. Check that the plugin is loading (see above)
3. Each skill needs a `SKILL.md` file with valid YAML frontmatter

### Bootstrap not appearing

1. Check MiMoCode version supports `experimental.chat.system.transform` hook
2. Restart MiMoCode after config changes

## Getting Help

- Report issues: https://github.com/sdkwork-ai/sdkwork-superpowers/issues
- Main documentation: https://github.com/sdkwork-ai/sdkwork-superpowers
- MiMoCode docs: https://github.com/mimo-ai/mimocode
