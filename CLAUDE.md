# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 1. Project at a Glance

**KumarNet Captive Portal** — an pfSense & OPNsense captive portal for a personal home/guest WLAN. The product is **three self-contained HTML files**:

| File          | Role              | Accent                             | Tone                         |
| ------------- | ----------------- | ---------------------------------- | ---------------------------- |
| `portal.html` | Login page        | violet-cyan `oklch(0.78 0.16 280)` | Friendly, over-engineered    |
| `error.html`  | Auth-failure page | amber-red `oklch(0.72 0.18 28)`    | Theatrical "security breach" |
| `logout.html` | Logout page       | mint `oklch(0.80 0.13 175)`        | Dramatic farewell            |

There is **no build step, no package manager, no framework, no node_modules**. Each HTML file inlines all CSS, JavaScript, and SVG. The portal must run fully offline — guests have no internet until they authenticate.

## 2. Quick Start (Windows / PowerShell)

```powershell
python -m http.server 8000     # serve repo on http://localhost:8000
start portal.html               # open page directly in default browser
```

Open these locally during dev:

- `http://localhost:8000/portal.html`
- `http://localhost:8000/error.html`
- `http://localhost:8000/logout.html`

pfSense & OPNsense template variables (`$PORTAL_ACTION$`, etc.) appear as raw strings during local dev — **this is expected**. Do not "fix" them.

There is **no test suite**. Validate manually:

1. Chromium first (Edge/Chrome) — full API support
2. Firefox — graceful degradation of Chromium-only APIs
3. Safari (if available) — minimal API surface
4. Mobile (DevTools device emulation, `<= 600px`)
5. Landscape narrow (`max-height: 500px + landscape`)

## 3. pfSense & OPNsense Template Variables

Replaced at runtime by pfSense & OPNsense — **never remove or rename**:

| Variable            | File(s)       | Purpose                               |
| ------------------- | ------------- | ------------------------------------- |
| `$PORTAL_ACTION$`   | all three     | Form submit / link target             |
| `$PORTAL_REDIRURL$` | `portal.html` | Hidden input — post-auth redirect     |
| `$PORTAL_ZONE$`     | `portal.html` | Hidden input — captive portal zone ID |
| `$PORTAL_MESSAGE$`  | `error.html`  | Server-provided error text            |

When editing forms or links, preserve these placeholders verbatim.

## 4. Architecture

### 4.1 Visual stack (shared across all three pages)

Bottom → top, by `z-index`:

| z-index | Layer                       | What                                    |
| ------- | --------------------------- | --------------------------------------- |
| 0       | `.bg-caustic`               | Radial gradient atmosphere              |
| 1       | `.bg-blob.a` / `.bg-blob.b` | Two drifting `filter: blur()` blobs     |
| 2       | `<canvas id="network-bg">`  | Particle mesh animation (JS)            |
| 3       | `.bg-grain`                 | Inline SVG `feTurbulence` noise overlay |
| 5+      | `.card`                     | Glassmorphism content card              |

### 4.2 Particle canvas

Each page runs an O(n²) neighbor-scan loop that draws lines between nearby particles. Tuning knobs:

- `N` — particle count, scaled by viewport area
- `maxD` — max neighbor distance for line drawing
- Mobile (`<= 600px`): reduced `N`
- `error.html`: canvas is **completely hidden** on `<= 600px` for performance
- `requestAnimationFrame` loop is gated behind `!prefers-reduced-motion`

### 4.3 Per-page JS responsibilities

| Page          | Unique behavior                                                                                                                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `portal.html` | System-info panel (UA parse, `navigator.connection`, `getBattery()`, `hardwareConcurrency`, `deviceMemory`, `Intl.DateTimeFormat`); rotating subtitles & tips; pre-filled `guest` username |
| `error.html`  | 5-min countdown timer (decorative — does nothing on 00:00); rotating fake threats; fake incident ID / IP / location triangulation; shaking card animation                                  |
| `logout.html` | Sequentially revealed fake session stats; rotating sad subtitles & farewell quotes                                                                                                         |

## 5. Design System

### 5.1 Colors — OKLCH only

All color values use **OKLCH** and are declared as CSS custom properties in `:root`. **Never hardcode color literals outside `:root`.**

Shared tokens (identical across all three pages):

- `--bg-0` … `--bg-3` — background gradient stops
- `--glass-fill`, `--glass-edge`, `--glass-shadow` — card surfaces

Per-page accent token (`--accent`) is the only color that differs between files.

### 5.2 Typography — system stack only

- `--f-sans` — UI font (system stack)
- `--f-mono` — mono font (system stack)

**No Google Fonts. No CDN requests. No `@font-face` with external URLs.** The portal must work with zero network access.

### 5.3 Indentation & naming

- 4-space indentation in HTML, CSS, and JS (match existing files)
- Semantic class names: `.card`, `.bg-grain`, `.system-info`, `.bg-blob`
- CSS grouped by function, not by selector type

## 6. Browser APIs (with fallback expectations)

Used only where available. Missing APIs render nothing — **never throw, never alert**.

| API                                      | Used for                | Notes                          |
| ---------------------------------------- | ----------------------- | ------------------------------ |
| `navigator.connection`                   | Network type & downlink | Chromium-only                  |
| `navigator.getBattery()`                 | Battery level           | Chromium-only, returns Promise |
| `navigator.hardwareConcurrency`          | CPU core count          | Widely supported               |
| `navigator.deviceMemory`                 | RAM (rounded)           | Chromium-only                  |
| `screen.width/height + devicePixelRatio` | Display info            | Universal                      |
| `Intl.DateTimeFormat`                    | Timezone                | Universal                      |
| User-agent regex                         | OS/browser detection    | No library — regex only        |

When adding a new API call, wrap it in a feature detect (`if ('connection' in navigator)`) and silently omit the row if unavailable.

## 7. Hard Constraints

- ✅ Each file remains a **single self-contained HTML document** — inline all CSS, JS, SVG
- ✅ **Zero external network requests** (fonts, scripts, images, analytics, telemetry)
- ✅ Fake telemetry (incident IDs, locations, session stats, threats) stays **clearly theatrical** — no plausibly-real data
- ✅ `@media (prefers-reduced-motion: reduce)` disables **all** animations including canvas
- ✅ Mobile (`max-width: 600px`) and landscape narrow (`max-height: 500px + landscape`) must remain usable
- ❌ No build tooling, bundlers, or transpilation
- ❌ No real credentials, no auth logic — that's pfSense & OPNsense's job
- ❌ No tracking, no analytics, no third-party scripts

## 8. Working with Claude — Tips for this Repo

- **Prefer `Edit` over `Write`.** Files are large (~30-40 KB each); rewriting them blows context.
- **Touch all three files when changing shared tokens.** `--bg-*` and `--glass-*` are duplicated, not imported.
- **Test the canvas after JS changes.** Mobile particle count and `error.html`'s canvas-hide breakpoint are easy to break.
- **Keep commits small and focused.** Recent style: `Fix mobile background canvas rendering`, `Remove external font requests for captive portal`. Imperative subject, one concern per commit.
- **Screenshots help PR reviews.** For visual changes, attach before/after captures.
- **The README is in German**, CLAUDE.md and AGENTS.md are in English. When updating user-facing copy, German wins.

## 9. Deployment

1. **Services → Captive Portal → Zones** in pfSense & OPNsense
2. Edit the target zone → **Templates** tab
3. Upload `portal.html`, `error.html`, `logout.html`
4. Save

No other files needed. No reload step beyond pfSense & OPNsense's normal save.

## 10. Related Documentation

- `README.md` — user-facing project description (German, includes the FAQ and feature humor)
- `AGENTS.md` — short repository guidelines, partially overlaps with this file
