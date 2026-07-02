---
name: visualizer
description: Produce high-quality SVG/HTML visuals (architecture diagrams, flowcharts, charts, mockups, interactive widgets) following a strict flat-design system. Framework-agnostic — works in any agent that can render SVG or HTML (WorkBuddy inline panels, web chat bubbles, IDE preview panes, or exported to .svg files). This skill should be used when the user wants to SEE rather than READ a concept — architecture diagrams, flowcharts, comparison tables-as-visuals, process illustrations, data charts, or any request containing words like "show me", "visualize", "diagram", "draw", "chart", "illustrate". Also use proactively for educational/explanatory requests, architecture & systems design discussions, and when a user hands over a spec that names a visual artifact.
agent_created: true
---

# Visualizer

## Overview

Produce high-quality SVG/HTML visuals following a strict flat-design system. Output is raw SVG or HTML code — render it via whatever mechanism the host agent provides (inline panel, chat bubble, preview pane, file export). Framework-agnostic: the design knowledge applies anywhere SVG/HTML can render.

## When to Use

### Explicit triggers
Trigger when the user says "show me", "visualize", "diagram", "draw", "chart", "illustrate", "graph", "what does X look like" — anything where the intent is to *see* rather than *read*, AND no file keyword (Artifact, file, export) appears, AND no connected MCP tool handles it.

### Proactive triggers (no explicit ask needed)
- **Educational/teaching requests** — "explain X", "teach me X", "讲解 X". Always visualize educational topics; diagrams beat walls of text.
- **Data shape** — "compare X vs Y" / "show me the data" where a chart is clearer than prose.
- **Architecture & systems** — "help me design/architect X" where a diagram anchors the conversation.
- **Specification triggers** — the user hands over a noun-phrase spec naming a visual artifact ("comparison table of REST vs GraphQL", "state machine for order processing", "contact form with name/email/message"). The spec IS the request; render it.

### When NOT to use
- One-sentence dictionary-style lookups ("what is a stack")
- The user explicitly says "explain in text" or "no diagram"
- A screenshot, existing chart, or reference image is already available to cite
- Pure math formulas or code snippets — LaTeX or code blocks render better than SVG

## Core Workflow

### Step 1: Detect theme
Determine dark or light theme before generating. Sources, in priority order:
1. Host-injected user context (e.g., `<user_info>` with `IDE Theme: dark`).
2. Explicit user statement ("I'm in dark mode", "use light colors").
3. Default: **dark theme** (safer — most developer tools default to dark).

Theme controls which color stops to use. See `references/color-palette.md` for the full hex table and theme mapping.

### Step 2: Choose output format and host adapter
Select the rendering mechanism based on host capability. This single table replaces separate "format" and "adapter" lookups:

| Host / capability | Render mechanism | Theme source | Fallback if unavailable |
|-------------------|------------------|--------------|-------------------------|
| WorkBuddy (inline SVG panel) | `read_me(modules)` then `show_widget(widget_code, title, loading_messages)` | `<user_info>` `IDE Theme` field | Inline SVG in response, or write `.svg` file |
| WorkBuddy (inline HTML panel) | `show_widget` with HTML fragment | `<user_info>` `IDE Theme` field | Same as above |
| Web chat (custom) | Inject SVG into chat bubble DOM | App theme state | Write `.svg` file |
| IDE plugin | SVG preview pane | IDE theme API | Write `.svg` file |
| Markdown-only host (Claude Code, Cursor, GitHub) | mermaid.js code block | Assume dark | Write `.svg` file |
| Static export / report | Write `.svg` or `.html` file | Hardcode per request | — |

**WorkBuddy adapter detail:**
```
read_me(modules: ["diagram"])           # load design module first
show_widget(widget_code: <svg>..., title: "...", loading_messages: ["...","..."])
```
If `read_me` / `show_widget` are unavailable, fall back to inline SVG in the response or write to a file.

**Mermaid fallback:** See `references/mermaid-syntax.md` for code-block syntax, theme directives, and supported diagram types. Always use mermaid `erDiagram` for database schemas, never SVG.

### Step 3: Generate the visual
Apply all rules in "Critical Rules" and the appropriate "Diagram Type Guidance" below. Reference `assets/example-flowchart.svg` and `assets/example-structural.svg` for complete worked examples of dark-theme SVG.

### Step 4: Multi-visualization responses
For complex topics, break into a series of smaller visuals rather than one dense diagram. **Always add prose between visuals** — never stack multiple outputs back-to-back without text. Each interstitial paragraph explains what the next visual shows and connects it to the previous one.

## Critical Rules

### Output format
- Produce SVG (for diagrams/illustrations) or HTML fragment (for widgets/charts). Never use image-generation language — this skill produces code, not generated images.
- Never expose machinery: no "let me load the module." Use a natural preamble: "Here's a diagram of that flow."

### Theme & readability (MANDATORY)
- Match the host theme. Dark theme → dark backgrounds with light readable text. Light theme → light backgrounds.
- Default to dark if unknown.
- **Dark theme**: use 800 fill + 200 stroke + 100/200 title text.
- **Light theme**: use 50 fill + 600 stroke + 800/600 title text.
- NEVER use default SVG black text fill on a dark background — it will be invisible.
- For exact hex values per ramp, load `references/color-palette.md`.

### Flat design (MANDATORY)
- No gradients, drop shadows, blur, glow, or neon effects.
- No emoji — use CSS shapes or SVG paths.
- Solid flat fills only. Two font weights only: 400 regular, 500 bold. Never 600/700.
- Typography: h1=15px, h2=14px, h3=13px (all weight 500). Body=13px weight 400, line-height 1.6.
- No font-size below 11px.

### Streaming-friendly
- Output streams token-by-token in most hosts. Structure code so useful content appears early.
- HTML order: `<style>` (short, <15 lines) → content HTML → `<script>` last.
- SVG order: `<defs>` (markers) → visual elements immediately.
- Prefer inline `style="..."` over `<style>` blocks — inputs/controls must look correct mid-stream.
- Gradients/shadows/blur flash during streaming DOM diffs — use solid flat fills.

### SVG technical constraints
- viewBox fixed to `"0 0 680 H"`. 680 is the standard width — must not change. `width="100%"` on root `<svg>`.
- H = bottommost element y + 20px. Calculate, do not guess.
- Safe area: x=40 to x=640, y=40 to y=(H-40). Background transparent.
- One SVG per output unit.
- 0.5px strokes for diagram borders and edges.
- Every `<path>`/`<polyline>` used as connector MUST have `fill="none"`.
- No rotated text.
- No `<!-- comments -->` or `/* comments */` (waste tokens, break streaming).
- No `position: fixed` in HTML.

### Arrow marker (include in every SVG `<defs>`)
```svg
<defs>
  <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5"
    markerWidth="6" markerHeight="6" orient="auto-start-reverse">
    <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke"
      stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  </marker>
</defs>
```

## Complexity Budget (hard limits)
- Box subtitles: ≤5 words.
- Colors: ≤2 ramps per diagram (excluding c-gray neutrals).
- Horizontal tier: ≤4 boxes at full width (~140px each).

## Font Size Calibration

| Text | Chars | Weight | Size | Rendered width |
|------|-------|--------|------|----------------|
| Title | 12 | 500 | 14px | ~88px |
| Subtitle | 16 | 400 | 13px | ~104px |
| Body | 24 | 400 | 13px | ~156px |
| Caption | 20 | 400 | 12px | ~120px |

Box width formula: `rect_width = max(title_chars × 7, subtitle_chars × 6) + 24`

## Diagram Type Routing

| User intent | Type | What to draw |
|-------------|------|-------------|
| "how do LLMs work" | Illustrative | Token row, stacked layer slabs, attention threads |
| "transformer architecture" | Structural | Labelled boxes: embedding, attention heads, FFN, layer norm |
| "what are the training steps" | Flowchart | Forward → loss → backward → update |
| "TCP handshake sequence" | Flowchart | SYN → SYN-ACK → ACK |
| "how does TCP work" | Illustrative | Two endpoints, numbered packets in flight, an ACK returning |
| "explain the Krebs cycle / event loop" | HTML stepper | Click through stages. Never a ring. |
| "draw the database schema" | mermaid.js | `erDiagram` syntax. Not SVG. See `references/mermaid-syntax.md`. |

## Diagram Type Guidance

### Flowchart (sequential processes, cause-and-effect, decision trees)
- Spacing: 60px minimum between boxes, 24px padding inside, 12px text-to-edge.
- Prefer single-direction flows. Max 4-5 nodes per diagram.
- Cycles don't get drawn as rings — build an HTML stepper instead. Only fall back to linear SVG with curved return arrow when there's one input and one output.
- Same-height nodes for same content type (single-line=44px, two-line=56px).
- Every `<text>` inside a box needs `dominant-baseline="central"`.

### Structural diagram (containment matters)
- Outermost container: large rounded rect, rx=20-24, lightest fill (50 stop in light / 800 in dark), 0.5px stroke.
- Inner regions: medium rounded rects, rx=8-12, next shade fill (100-200 in light / 900 in dark).
- 20px minimum padding inside every container. Max 2-3 nesting levels.
- Database schemas/ERDs → use mermaid.js `erDiagram`, NOT SVG.

### Illustrative diagram (building intuition)
- Physical subjects: draw simplified cross-sections/cutaways.
- Abstract subjects: invent spatial metaphors (transformer = stacked horizontal slabs; hash = funnel scattering into buckets).
- Color encodes intensity, not category. Warm ramps = heat/energy/active. Cool = cold/calm/dormant.
- Prefer interactive over static. If the real system has a control, give the diagram that control.
- One `<linearGradient>` per diagram permitted (continuous physical properties only).
- CSS `@keyframes` animation permitted (transform/opacity only, loops <2s). Wrap in `@media (prefers-reduced-motion: no-preference)`.

## Pre-built SVG Classes

Decision rule: **If running in WorkBuddy, use these classes. Otherwise, ignore this section and inline all styles.**

| Class | Description |
|-------|-------------|
| `class="t"` | sans 14px primary |
| `class="ts"` | sans 12px secondary |
| `class="th"` | sans 14px medium (500) |
| `class="box"` | neutral rect (bg-secondary fill, border stroke) |
| `class="node"` | clickable group with hover effect |
| `class="arr"` | arrow line (1.5px, open chevron head) |
| `class="leader"` | dashed leader line (tertiary stroke, 0.5px) |
| `class="c-{ramp}"` | colored node — apply to `<g>` or rect/circle/ellipse (not paths) |

## Accessibility
- HTML widgets: begin with a visually-hidden `<h2 class="sr-only">` containing a one-sentence summary.
- SVG widgets: use `role="img"` with `<title>` and `<desc>` as first children.

## CDN Allowlist (CSP-enforced in most hosts)
External resources may ONLY load from: `cdnjs.cloudflare.com`, `esm.sh`, `cdn.jsdelivr.net`, `unpkg.com`.

## File Export Mode

When the user wants a downloadable file, or the host can't render inline:
1. Generate the SVG per all rules above.
2. Wrap in a minimal HTML file with `<!DOCTYPE>`, `<html>`, `<head>`, `<body>` for `.html`, or save raw `.svg`.
3. Use the file-write tool the host provides.
4. Suggested filename: `<descriptive-title>.svg` (kebab-case, ASCII only).

## Bundled Resources

| Resource | Purpose | When to load |
|----------|---------|---------------|
| `references/color-palette.md` | Full 9×7 hex table, theme mapping, worked examples | When generating SVG and needing exact color values |
| `references/mermaid-syntax.md` | Mermaid code-block syntax, theme directives, supported types, patterns | When falling back to mermaid for markdown-only hosts |
| `assets/example-flowchart.svg` | Complete dark-theme flowchart reference | When unsure how a finished flowchart should look |
| `assets/example-structural.svg` | Complete dark-theme nested structural diagram reference | When unsure how a finished structural diagram should look |

## Common Pitfalls

- **Wrong viewBox**: must be `"0 0 680 H"`. Changing 680 breaks all coordinate calculations.
- **Dark text on dark background**: in dark theme, always use 800 fill + light (100/200) text. Default SVG text fill is black — invisible on dark.
- **Stacking visuals without prose**: always write a connecting paragraph between multiple visuals.
- **Using emoji**: forbidden. Use SVG paths or CSS shapes instead.
- **Forgetting `fill="none"` on connector paths**: they'll render as filled shapes.
- **Title not unique**: title doubles as download filename and disambiguator; make it specific.
- **Defaulting to SVG in a markdown-only host**: check the host adapter table first — use mermaid fallback for terminal agents.
- **Hardcoding theme**: always detect theme first; never assume light.
- **Over-triggering**: if a one-sentence text answer suffices, do not force a diagram. See "When NOT to use".
