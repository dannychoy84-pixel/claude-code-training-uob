# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A Claude Code training workspace for a UOB Bank class lab. There is **no application code yet** — the only content is [Training prompt.txt](Training prompt.txt), a numbered list of tasks (Task 1–12) that learners work through in order to build the project up from nothing.

Because the deliverable is built incrementally by following that brief, read `Training prompt.txt` before starting work: it is the spec, not documentation of existing code.

## The build target (Task 1)

A single self-contained `index.html` at the repo root — all CSS in one `<style>` block, all JS in one `<script>` block. **No frameworks, no build step, no external dependencies, no network calls.** It must work by double-clicking the file. There is consequently no build, lint, or test command; "run it" means opening `index.html` in a browser (or driving it with the Playwright MCP tool, added in Task 4).

Design constants specified by the brief: primary `#00539B`, accent `#E8A33D`, body text `#2C3038`, 1120px max-width container, system font stack, single column below 768px. Task 8 later revamps this to a black background with a "techy" animated look, so treat the light palette as superseded once Task 8 is done.

Every page must carry the footer disclaimer: `Training demo — not affiliated with or endorsed by United Overseas Bank Limited`.

## Constraints that carry across tasks

- **Ticket state is in-memory only.** Submitted tickets live in a JavaScript array. No `localStorage`, no `sessionStorage`, no fetch/XHR. This is a hard requirement of the brief, not a simplification.
- **Never collect credentials.** The ticket form must not accept passwords, OTPs, or card numbers, and the description field warns inline if the text looks like a credential. Keep this behaviour intact through redesigns.
- **Mock ticket reference format:** `UOB-ITSD-YYYYMMDD-####`. SLA mapping: Critical 1h / High 4h / Medium 1 business day / Low 3 business days.
- **Fictional contact data only** (hotline `1800-XXX-XXXX`, `@uobgroup.com` email pattern). Real personal details appear in the brief for the WhatsApp widget (Task 10) and notification recipient (Tasks 11–12) — those are the user's own and are intentional.
- **Security scan before publishing.** Tasks 2/3/9 push to GitHub (`https://github.com/dannychoy84-pixel`) and deploy GitHub Pages. The `/github-publish` command created in Task 3 is required to scan for confidential data *before* anything leaves the machine; do not push around it.

## Project-level `.claude/` structure the tasks build

The later tasks populate this directory — check what already exists before creating any of it:

- `.claude/commands/github-publish` — Task 3: security scan → README (with screenshot, added in Task 6) → push → repo About + Pages link → Pages via GitHub Actions.
- `.claude/skills/` — Task 7 installs `frontend-design`, `ui-ux-pro-max`, `cybersecurity-analyst` via `npx skills add`.
- `.claude/agents/` — Task 11: a Playwright-driven agent that fills and submits the ticket form, mails results to the configured notification address, and logs submissions as JSON in the repo root. Task 12: a cyber-risk scanning agent running every 5 minutes, logging health status as CSV in the repo root and alerting the same address on risk. The actual notification address is kept out of tracked files — see the (gitignored) local config the agents read it from.
- `.claude/hooks/` — final task: a 10-second dwell-time popup on the website collecting name/email/department for a Christmas event RSVP.

Note the brief numbers two different tasks "Task 11"; the agent task and the hook task are distinct.
