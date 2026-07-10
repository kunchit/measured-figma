---
name: measured-figma
description: >-
  Measured Figma implementation — turn any figma.com node into project code with
  Chrome DevTools proof. Use when user shares a Figma URL, asks to implement /
  match / sync a design, build UI from Figma, or says measured / DevTools /
  one-shot / เป๊ะ.
---

# Measured Figma

**Requires:** Figma MCP + Chrome DevTools MCP. No DevTools MCP → say so and stop; never claim **measured** without real `getComputedStyle` numbers.

Figma MCP gives **reference** output. Your job: **adapt** it to the project's stack, tokens, and components — then prove it with **measured** DevTools numbers. Never claim done from code review or screenshot alone.

## 1. Scope

From the URL extract `fileKey` and `nodeId` (`232-1313` → `232:1313`). Branch URLs use `branchKey` as fileKey.

Confirm:

- Target node (component, section, or page frame)
- Breakpoints in scope — check Figma for separate mobile/desktop frames before coding one layout
- Destination file(s) in the repo

**Completion:** stated breakpoint list + edit targets.

## 2. Pull design

In order:

1. **`get_design_context`** on the implementation node — sizes, spacing, type, shadows, reference markup.
2. **`get_variable_defs`** on the node or parent screen — color/type/spacing **tokens**.
3. **`get_metadata`** when you need child node IDs, parent section spacing, or page structure (large pages: parse saved output, don't read inline).
4. **`get_code_connect_map`** when present — mapped codebase component wins over generated markup.

Optional: **`get_screenshot`** for visual diff after measure pass.

Details: [`reference.md`](reference.md) § MCP tools.

**Completion:** spec table (px + token names) written before any CSS.

## 3. Read project

Before writing code:

- Stack + styling system (Tailwind, CSS modules, styled-components, …)
- Where **tokens** live (`globals.css`, `tokens.json`, theme file, `CLAUDE.md` mapping table)
- Grep for similar UI — copy patterns (scroll rows, section headings, cards), don't invent
- Installed primitives (shadcn, internal `Button`, `Select`, …)

Rule: Figma `category/name` → project token/class. No hex/px literals when a token exists. Missing token → stop and ask; don't guess.

**Completion:** reuse list + token mapping rule documented.

## 4. Adapt

Convert reference output to project conventions:

- Framework syntax (`.astro`, `.tsx`, Vue SFC, …)
- Semantic HTML + a11y (links, labels, alt)
- Responsive: if Figma has distinct frames per width, implement **both** paths (`md:hidden` / `hidden md:…` or project breakpoint) — don't scale desktop down
- Motion: `get_motion_context` only when user asks or design has explicit animation

**Completion:** plan names every new class/token and which existing component it replaces.

## 5. Implement

Minimum diff. One section at a time.

Layout branches — see [`reference.md`](reference.md) § Layout branches (slider, grid, stack).

**Completion:** dev server renders the section; no unrelated refactors.

## 6. Measure

**Measured** gate — mandatory:

1. Start dev server (project's command).
2. `resize_page` (or `emulate` for a device) to **each Figma frame width** in scope.
3. `navigate_page` to the section URL, then `evaluate_script` with `scrollIntoView`.
4. `evaluate_script`: `getBoundingClientRect` + `getComputedStyle` on every spec'd element (size, gap, padding, font-size, weight, line-height, radius, shadow).

Trust **computed** styles — Radix/shadcn overrides class names at runtime.

Template script: [`reference.md`](reference.md) § DevTools eval.

**Completion:** measurement JSON per breakpoint.

## 7. Report

Post **Figma | Measured** table per breakpoint. Mismatch → fix → re-measure.

±1px rounding OK. Say done only when every row matches or user accepts a documented delta.

## Failure modes

| Symptom | Fix |
|--------|-----|
| Looks right in source, wrong in browser | measure computed styles |
| Token color off | remap variable → project token |
| Mobile/desktop fight | separate implementations per Figma frame |
| Slider won't scroll | `shrink-0`, `w-max`, enough items |
| `get_metadata` too large | save to file, parse with script |
