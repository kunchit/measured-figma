# Project companion — Figma → tokens

Copy into your repo (`CLAUDE.md`, `AGENTS.md`, or `.cursor/rules/figma-tokens.mdc`).
Skill stays generic; **this file** is project-specific.

## Token map

| Figma variable | Project class / token |
|----------------|------------------------|
| `text/primary` | `text-text-primary` |
| `text/secondary` | `text-text-secondary` |
| `surface/brand` | `bg-surface-brand` |
| `divider` | `border-divider` |
| `font/size/14` | `text-sm` |
| `shadow` | `shadow-elevated` |

Rule: Figma `category/name` → your utility. No hex when a token exists. Missing token → stop and ask.

## Where tokens live

- Theme / CSS: `src/styles/globals.css` (or `tokens.json`, …)
- Primitives: `src/components/ui/` (or your design system path)

## Dev server

```bash
# your command → URL agent should open for measure
npm run dev   # http://localhost:3000
```

## Breakpoints you use

| Name | Width |
|------|-------|
| mobile | 375 |
| desktop | 1440 |
