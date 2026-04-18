# Dark Mode Toggle — Design

**Date:** 2026-04-18
**Scope:** `index.html` and `zta-architecture.html`

## Overview

Add a manual, session-only dark mode to both pages. A toggle button in each page's header flips between the existing light palette and a new dark palette. No system-preference detection, no `localStorage` persistence — pages always load in light mode and reset on reload.

## User-Facing Behavior

- Both pages load in light mode by default.
- A 36×36 icon-only button sits in the header, right side, with a hairline border matching existing interactive elements.
- The button shows a Lucide `moon` icon when the page is in light mode and a Lucide `sun` icon when the page is in dark mode.
- Clicking the button flips the theme. The choice is session-only and per-page; reloading either page returns to light mode.
- Theme transitions run at 200ms on `background-color` and `color`; `prefers-reduced-motion` disables the transition.

## Architecture

Both files already declare their color palette as CSS custom properties inside `:root`. Dark mode is implemented by adding a single `:root[data-theme="dark"]` block that overrides the same tokens. Every existing rule that reads `var(--bg)`, `var(--surface)`, etc. flips automatically — no component-level CSS changes required.

Theme state lives on `document.documentElement`:

- Light mode: no `data-theme` attribute.
- Dark mode: `data-theme="dark"`.

A single vanilla-JS click handler on the toggle button toggles the attribute and swaps the button's inline SVG icon between Lucide `moon` and `sun`.

## Dark Palette

Shared tokens (both files):

| Token | Light | Dark |
|---|---|---|
| `--bg` | `#F7F7F5` | `#0A0A0A` |
| `--surface` | `#FFFFFF` | `#141414` |
| `--surface-alt` | `#FAFAF8` | `#1A1A1A` |
| `--text` | `#0A0A0A` | `#F5F5F5` |
| `--muted` | mid-gray | `#9CA3AF` |
| `--border` | `#E5E7EB` | `#262626` |
| `--border-strong` | `#111111` | `#F5F5F5` |
| `--accent` | `#0F3460` | `#6FA8FF` |
| `--accent-soft` | `#ECEFF5` | `#16233A` |
| `--accent-ink` | `#071B37` | `#B8CEF0` |
| `--warn` | `#B45309` | `#F59E0B` |
| `--warn-soft` | `#FDF6EC` | `#2A1F08` |
| `--ok` | `#15643D` | `#34D399` |
| `--ok-soft` | `#E8F2EC` | `#0B2419` |
| `--danger` | `#9F1239` | `#F87171` |
| `--danger-soft` | `#FBEAEE` | `#2A0D14` |

Additional tokens in `index.html` (category colors): each `--cat-*` stays the same hue but gets a lifted lightness for dark mode; each `--cat-*-soft` switches from a light tint to a dark tint of the same hue. Exact values:

| Token | Light | Dark |
|---|---|---|
| `--cat-identity` / `-soft` | `#0F3460` / `#ECEFF5` | `#6FA8FF` / `#16233A` |
| `--cat-control` / `-soft` | `#5B21B6` / `#F1ECFB` | `#A78BFA` / `#1F1530` |
| `--cat-pep` / `-soft` | `#B45309` / `#FDF6EC` | `#F59E0B` / `#2A1F08` |
| `--cat-data` / `-soft` | `#0E7490` / `#E5F3F6` | `#38BDF8` / `#0A2530` |
| `--cat-observe` / `-soft` | `#15643D` / `#E8F2EC` | `#34D399` / `#0B2419` |
| `--cat-ai` / `-soft` | `#9F1239` / `#FBEAEE` | `#F87171` / `#2A0D14` |

The accent is lifted from navy `#0F3460` to `#6FA8FF` because the original is unreadable on a near-black surface. The `-soft` variants invert from light tints to dark tints of the same hue.

## Toggle Button

- Placement: header, right side of each page.
- Size: 36×36.
- Border: 1px hairline matching `--border`.
- Background: `--surface` (transparent-feeling on both themes).
- Icon: inline Lucide SVG, 18×18, `stroke="currentColor"`, so it inherits `--text`.
- Hover: background shifts to `--surface-alt`.
- Focus: visible focus ring using the existing focus-ring style on the page.
- `aria-label="Toggle dark mode"` and `aria-pressed` reflects state.

## Transitions

On `body`, `.card`-like surfaces, and any element that renders a themed background or border, add:

```css
transition: background-color 200ms ease, color 200ms ease, border-color 200ms ease;
```

Wrap this in `@media (prefers-reduced-motion: no-preference)` so reduced-motion users get an instant flip.

## SVG Audit

Both files contain inline SVG diagrams. Any hard-coded light-palette hex values inside SVGs that must flip with the theme get replaced with either `currentColor` or a CSS variable. SVGs that are color-agnostic (using `currentColor` already) need no change. This audit happens during implementation.

## Out of Scope

- System-preference detection (`prefers-color-scheme`).
- Persistence via `localStorage` or cookies.
- Cross-page theme sharing.
- Any new components, layout changes, or content changes.
- Refactors unrelated to dark mode.

## Success Criteria

1. Both files still open as single self-contained `.html` files with no external dependencies beyond what already exists.
2. Clicking the toggle in either file flips every themed surface, text color, border, and accent color without visual glitches.
3. Reloading the page returns to light mode.
4. Contrast on dark mode meets WCAG AA for body text (≥4.5:1) and large text (≥3:1).
5. Focus rings and hover states remain visible and meet contrast in both themes.
6. `prefers-reduced-motion: reduce` suppresses the transition.
