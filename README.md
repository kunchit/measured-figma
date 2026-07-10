# Measured Figma

Cursor plugin: turn a Figma node into project code, then **prove** it with Chrome DevTools measurements.

Figma MCP output is **reference**. This skill forces: adapt to your stack → measure computed styles → report Figma vs Measured. Never claim done from screenshot or class names alone.

## Requirements

| Tool | Why |
|------|-----|
| [Figma MCP](https://cursor.com/docs) (`get_design_context`, `get_variable_defs`, …) | Pull design specs |
| Chrome DevTools MCP (or CDP) | Resize viewport + `getComputedStyle` |
| Project **companion** token map | Map Figma vars → your CSS/Tailwind |

Auth: your Figma account / token. Plugin ships **no** credentials.

## Install

### Local (dev / try)

```bash
mkdir -p ~/.cursor/plugins/local
ln -s "$(pwd)" ~/.cursor/plugins/local/measured-figma
```

Reload Cursor → Customize → confirm `measured-figma` listed.

## Usage

1. Paste a Figma URL, or run `/measured-figma`.
2. Agent follows: Scope → Pull → Read project → Adapt → Implement → **Measure** → Report.
3. Done only when Figma \| Measured table matches (±1px OK).

## Project companion (required for pixel accuracy)

Skill is stack-agnostic. Each consuming repo should document token mapping — see [`examples/companion.md`](examples/companion.md).

Paste a short map into `CLAUDE.md` / `AGENTS.md` / a Cursor rule. Without it, process still runs; token mapping is weaker.

## Layout

```text
measured-figma/
├── .cursor-plugin/plugin.json
├── skills/measured-figma/
│   ├── SKILL.md          # process (7 steps)
│   └── reference.md      # URL parse, layout branches, eval template
├── examples/companion.md # template for consuming projects
├── assets/logo.svg
└── README.md
```

## License

MIT
