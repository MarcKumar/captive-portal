# Repository Guidelines

## Project Structure & Module Organization

This repository is a static captive portal for pfSense. The three root HTML files are the product:

- `portal.html`: primary login page
- `error.html`: failed-authentication/error state
- `logout.html`: logout/exit page

`README.md` documents behavior and deployment. `.vscode/` holds editor settings only. There is no `src/`, build output, or asset pipeline, so keep each page self-contained with inline CSS, JavaScript, and embedded assets where practical.

## Build, Test, and Development Commands

There is no build step or package manager in this project.

- `python -m http.server 8000`: serve the repo locally for browser testing
- `start portal.html`: open the main page directly on Windows for quick checks
- `git diff`: review visual or behavioral changes before committing

For pfSense deployment, upload `portal.html`, `error.html`, and `logout.html` to the captive portal zone.

## Coding Style & Naming Conventions

Use 4-space indentation in HTML, CSS, and JavaScript, matching the existing files. Prefer semantic sectioning and clear class names such as `.card`, `.bg-grain`, or `.system-info`. Keep styles grouped by function and use CSS custom properties in `:root` for theme values. Avoid external font, script, or asset dependencies unless absolutely necessary; these pages should stay portable and easy to upload.

## Testing Guidelines

There is no automated test suite. Validate changes manually in Chromium-based browsers first, then spot-check Firefox and Safari behavior where possible. Test desktop and mobile layouts, form submission flow, animations, and fallback behavior for optional browser APIs such as `navigator.connection` or `getBattery()`. If a change affects all pages, verify all three files before opening a PR.

## Commit & Pull Request Guidelines

Recent history favors short, imperative commit subjects such as `Fix mobile background canvas rendering` or `Remove external font requests for captive portal`. Follow that pattern and keep each commit focused on one change. PRs should include a concise description, note which pages were touched, summarize manual testing, and attach screenshots or short recordings for visual updates.

## Security & Deployment Notes

Do not add real credentials, tracking, or external calls. Preserve offline-friendly behavior and keep fake telemetry clearly theatrical. pfSense uploads work best when each page remains a standalone HTML document.
