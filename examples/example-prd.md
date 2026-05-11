---
title: Dark mode for Glyph editor
owner: J. Park
status: Draft v3
updated: 2026-05-09
target: 2026 Q3
---

# Dark mode for Glyph editor

A user-selectable dark theme for Glyph, the open-source code editor, applied consistently across editor surface, chrome, and embedded panels.

## Problem

Glyph ships only a light theme. Roughly 41% of users work in low-light environments according to the latest opt-in environment survey, and the most upvoted issue in the public tracker over the last twelve months is the request for a built-in dark theme. Third-party color extensions partially fill the gap, but they only re-skin the editor surface — chrome, settings panels, and the diff viewer stay light, which produces a jarring two-tone window that several long-form contributors have flagged as worse than no dark mode at all.

## Critical decision

The load-bearing choice for this release is **theme tokens, not theme stylesheets**: every color is referenced through a CSS custom property, and the dark theme is a single set of property overrides at `:root[data-theme="dark"]`. The alternative — shipping two parallel stylesheets — was rejected because it doubles maintenance and because third-party themes (which Glyph already supports) would have to be authored twice. With tokens, third-party themes become a single declaration block instead of two.

## Decisions

Three follow-on decisions support the token approach.

### Decision 1 — Use a token taxonomy, not raw colors

Selection: introduce a fixed set of named tokens (`--surface`, `--surface-elevated`, `--ink`, `--ink-muted`, `--accent`, `--rule`) and require every component to reference them. Disallow raw hex in component CSS.

Why: a token vocabulary makes the diff between light and dark a single 30-line block. It also gives third-party theme authors a stable contract — they override tokens, not internal class names.

Cost: refactoring existing components to consume tokens is a one-time cost (~1 week of cleanup). After that, any new color must be added to the taxonomy first.

### Decision 2 — Default to system preference, allow manual override

Selection: on first launch, read `prefers-color-scheme` and set the theme accordingly. The user's explicit choice in Settings overrides system preference and persists in editor config (not in the rendered document).

Why: matches the platform behavior most users already expect. Manual override is necessary because some users want dark mode on a light desktop (and vice versa) for ergonomic reasons.

Cost: introduces a small precedence rule (user choice beats system) that must be documented and tested. No runtime cost.

### Decision 3 — Defer syntax-highlighting palette to a separate effort

Selection: the dark theme uses the existing syntax palette unchanged for V1. A retuned syntax palette ships in a follow-up.

Why: syntax highlighting has its own contrast and accessibility concerns that warrant separate review. Bundling it into this PRD would expand scope and delay shipping the chrome-level dark mode users are actually asking for.

Cost: V1 dark mode will look "almost right but not quite" on heavily-syntax-highlighted files. A note in the release announcement sets the expectation.

## Risks

- **Token coverage gaps** — any component that still uses raw hex will render with a light surface against the dark chrome, producing the same two-tone effect we're trying to eliminate. Mitigation: a lint rule rejects raw color literals in component CSS, run in CI before merge.
- **Contrast regressions** — color combinations that pass WCAG AA in light mode can fail in dark mode at the inverted lightness. Mitigation: add a contrast check to the visual-regression suite, gate release on AA pass for all token pairings.
- **Third-party theme breakage** — existing third-party themes that target internal class names rather than tokens will not adapt automatically. Mitigation: publish a migration guide and a one-release deprecation window before removing the old class-name selectors.

## Out of scope

This release explicitly does not include the items below. Each is tracked separately and may ship later, but is not blocking V1.

- Per-file or per-language theme overrides
- A theme editor UI inside Glyph (users who want custom themes still hand-edit CSS files)
- Retuned syntax-highlighting palette (see Decision 3)
- High-contrast accessibility theme (separate accessibility track)
- Automatic theme switching by time of day
