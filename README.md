# UOB · IT Service Desk (Training Demo)

A single self-contained mockup of an internal IT Support Service Desk site, built as a Claude Code training exercise.

> **Training demo — not affiliated with or endorsed by United Overseas Bank Limited.**

## Live site

https://dannychoy84-pixel.github.io/claude-code-training-uob/

![Screenshot of the UOB IT Service Desk homepage](docs/screenshot.png)

## Features

- Sticky navigation with a responsive hamburger menu on mobile
- Hero banner with quick-access buttons and three quick-info cards (hotline, hours, response time)
- An IT support ticket form with full client-side validation (required fields, Staff ID pattern, corporate email domain check, live character counters, and a warning if the description looks like it contains a password/OTP/credential)
- Mock ticket generation (`UOB-ITSD-YYYYMMDD-####`) with a priority-based SLA response, all handled in memory — no storage, no network calls
- A searchable FAQ accordion covering 8 common IT support questions

## Running it

No build step, no dependencies. Just open [`index.html`](index.html) directly in a browser.

## Project structure

```
index.html   — the entire site: markup, CSS, and JS in one file
CLAUDE.md    — guidance for Claude Code when working in this repo
```

## Claude Code tooling used in this project

- **MCP**: Playwright, registered at the project level in [`.mcp.json`](.mcp.json) for browser automation and screenshots.
- **Command**: [`/github-publish`](.claude/commands/github-publish.md) — security-scans, then publishes the site (README, screenshot, GitHub Pages, repo About).
- **Skills** (installed via [`npx skills`](https://skills.sh), not committed — see below to reinstall):
  - [`frontend-design`](https://github.com/anthropics/skills)
  - [`ui-ux-pro-max`](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)
  - [`cybersecurity-analyst`](https://github.com/rysweet/amplihack)

To reinstall the skills after cloning:

```bash
npx skills add https://github.com/anthropics/skills --skill frontend-design
npx skills add https://github.com/nextlevelbuilder/ui-ux-pro-max-skill --skill ui-ux-pro-max
npx skills add https://github.com/rysweet/amplihack --skill cybersecurity-analyst
```

> **Windows note:** the `amplihack` repo contains a file literally named `:memory:` at its root, which is an invalid filename on NTFS, so `npx skills add` fails to clone it on Windows. Work around it with a targeted checkout instead:
> ```bash
> git clone --no-checkout https://github.com/rysweet/amplihack.git amplihack-tmp
> cd amplihack-tmp && git checkout HEAD -- .claude/skills/cybersecurity-analyst
> ```
> then copy `.claude/skills/cybersecurity-analyst` into this project's `.claude/skills/`.
