# Measured Figma — reference

## URL parsing

| URL pattern | fileKey | nodeId |
|-------------|---------|--------|
| `figma.com/design/:fileKey/...?node-id=1-2` | `:fileKey` | `1:2` |
| `.../design/:fileKey/branch/:branchKey/...` | `:branchKey` | from query |
| `figma.com/board/:fileKey/...` | FigJam — `get_figjam` |
| `figma.com/slides/:fileKey/...` | Slides — slides tools |
| `figma.com/make/:makeFileKey/...` | Make — `makeFileKey`, node `0:1` |

## MCP tools

| Tool | When |
|------|------|
| `get_design_context` | Primary — implement from this |
| `get_variable_defs` | Token names → project map |
| `get_metadata` | Structure, child IDs, parent spacing |
| `get_code_connect_map` | Use mapped source components |
| `get_screenshot` | Visual compare after measure |
| `get_motion_context` | Animation only when needed |
| `search_design_system` | Figma library components (write path) |

## Layout branches

### Horizontal slider / peek

Figma row wider than frame → third item clipped.

```html
<div class="overflow-x-auto [scrollbar-width:none] [&::-webkit-scrollbar]:hidden">
  <div class="flex w-max gap-{n}">
    <item class="shrink-0 w-[{figma-w}] h-[{figma-h}]" />
  </div>
</div>
```

Edge bleed: `-mx-{page-pad} px-{page-pad}` so row extends to screen edge.

### Grid / bento

Use Figma `col-span` / `row-span` from metadata. Desktop grid often `hidden md:grid`.

### Section stack

Heading frame + content frame — gap = content.y − (heading.y + heading.height) from metadata.

## DevTools eval

Adapt selectors. Run per breakpoint after `scrollIntoView`.

```javascript
() => {
  const pick = (sel) => document.querySelector(sel);
  const rect = (el) => (el ? el.getBoundingClientRect() : null);
  const cs = (el) => (el ? getComputedStyle(el) : null);
  const round = (n) => (n == null ? null : Math.round(n));

  // --- customize these ---
  const root = pick("[data-figma-section]") ?? pick("section");
  const card = root?.querySelector("a, [data-card]");
  const title = root?.querySelector("h2, h3");
  const next = card?.nextElementSibling;

  const cr = rect(card);
  const nr = rect(next);
  return {
    viewport: { w: window.innerWidth, h: window.innerHeight },
    card: cr ? { w: round(cr.width), h: round(cr.height) } : null,
    gap: cr && nr ? round(nr.left - cr.right) : null,
    radius: cs(card)?.borderRadius,
    shadow: cs(card)?.boxShadow,
    title: title
      ? {
          fontSize: cs(title)?.fontSize,
          lineHeight: cs(title)?.lineHeight,
          fontWeight: cs(title)?.fontWeight,
        }
      : null,
  };
}
```

Add `data-figma-section` on the root element during implement — makes re-measure reliable.

## Viewport checklist

| Figma frame width | DevTools resize |
|-------------------|-----------------|
| 375 | 375 × 812 |
| 390 | 390 × 844 |
| 768 | 768 × 1024 |
| 1440 | 1440 × 900 |

Match the frame you pulled specs from — not a generic "mobile".

## Token mapping (generic)

1. `get_variable_defs` → `text/primary`, `surface/brand`, `font/size/14`, …
2. Find project equivalent: CSS var, Tailwind `@theme`, design-tokens package.
3. Convention: `--color-text-primary` → `text-text-primary` (project-specific — read `CLAUDE.md` or theme file).
4. Raw fills in Figma with no variable → document hex in report; prefer adding token over inline hex.

## Report template

```markdown
### {Section} @ {width}px

| Property | Figma | Measured |
|----------|-------|----------|
| Card W×H | 124×186 | … |
| Gap | 8px | … |
| … | … | … |
```
