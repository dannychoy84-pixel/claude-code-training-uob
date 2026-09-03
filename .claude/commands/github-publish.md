---
description: Security-scan, then publish this project to GitHub with a README (incl. screenshot), About section, and GitHub Pages site
argument-hint: [github-repo-url-or-name]
---

# GitHub Publish

Publish the current project to GitHub safely. Follow these steps **in order** and do not skip the security scan — it must run and pass (or be explicitly resolved with the user) before anything is pushed.

## 1. Determine the target repository

- If an argument was given (`$ARGUMENTS`), treat it as the target repo (full URL like `https://github.com/owner/repo` or `owner/repo`).
- Otherwise, check for an existing `origin` remote with `git remote get-url origin`.
- If neither is available, ask the user for the target repo (owner/name) before proceeding. Do not guess or invent a repository.
- Confirm the authenticated `gh` account (`gh auth status`) actually matches the target owner. If it doesn't, tell the user and ask them to switch accounts (`gh auth login`) rather than silently pushing under the wrong identity.

## 2. Security scan — MUST run before any push

Scan every file that is staged, tracked, or about to become tracked (i.e. everything not already covered by `.gitignore`) for confidential data. At minimum check for:

- API keys / access tokens (AWS `AKIA...`, generic `api[_-]?key`, `secret`, `token` assignments with high-entropy values)
- Private keys / certificates (`-----BEGIN PRIVATE KEY-----`, `.pem`, `.key` files)
- Passwords, connection strings, database URLs with embedded credentials
- `.env` files or anything matching `*.env*`, `credentials.json`, `service-account*.json`
- Personal contact details that aren't meant to be public (real personal phone numbers, personal email addresses) — distinguish these from placeholder/fictional data the site is intentionally designed to show (e.g. the demo hotline `1800-XXX-XXXX`, the `@uobgroup.com` demo email pattern)
- Any hard-coded internal hostnames, IPs, or infrastructure details

Use `git diff --cached` / `git status` plus a `Grep` pass over the working tree for these patterns. If anything suspicious is found:

- **Stop.** Do not push.
- Report exactly what was found and where.
- Either add the file/pattern to `.gitignore` and unstage it, or ask the user how they want to handle it.
- Only proceed once the scan is clean or the user has explicitly confirmed the flagged content is safe to publish.

Report a one-line scan summary to the user either way ("Security scan: clean, N files checked" or what was excluded and why).

## 3. Capture a screenshot

Capture a fresh screenshot of the site so the README always reflects the current design:

- Prefer the Playwright MCP tool if it's available in this session (`browser_navigate` to the local file, then `browser_take_screenshot` / `browser_resize` for a consistent viewport).
- Otherwise fall back to the Playwright CLI, which needs no MCP approval step:
  `npx playwright screenshot --viewport-size=1280,900 index.html docs/screenshot.png`
  (use the live Pages URL instead of the local file once one exists, so the screenshot reflects what's actually deployed).
- Save it to `docs/screenshot.png`, overwriting the previous one.
- Confirm the file was written and looks correct (check its dimensions/size aren't 0) before using it in the README.

## 4. Create or update the README

Write/update `README.md` at the project root covering:

- What the project is (one or two sentences)
- The training-demo disclaimer (not affiliated with UOB)
- The live-site link (fill in the real URL if Pages is already deployed from a previous run; otherwise leave the step-6 placeholder)
- The screenshot from step 3, embedded via `![Screenshot of the site](docs/screenshot.png)`, placed near the top so it's the first thing a visitor sees
- Key features (ticket form, FAQ, etc.)
- How to run it locally (open `index.html` directly — no build step)
- Project structure (brief)

Keep it concise. Don't fabricate features that don't exist in the code.

## 5. Commit and push

- Stage the relevant files (never `git add -A` blindly — review what's staged), including `docs/screenshot.png`.
- Write a commit message describing what changed and why.
- Push to `origin` on the current branch, creating the branch upstream if needed.
- If the repo doesn't exist yet on GitHub, create it with `gh repo create <name> --public --source=. --remote=origin --push` (ask the user for public/private if not already established).

## 6. GitHub Pages via GitHub Actions

- Create `.github/workflows/pages.yml` (if it doesn't already exist) that deploys the static site (root directory) to GitHub Pages using the standard `actions/configure-pages`, `actions/upload-pages-artifact`, and `actions/deploy-pages` actions, triggered on push to the default branch.
- Enable Pages with the Actions build type: `gh api -X PUT repos/{owner}/{repo}/pages -f build_type=workflow` (create it first with POST if it doesn't exist, e.g. `gh api -X POST repos/{owner}/{repo}/pages -f build_type=workflow -f 'source[branch]=main' -f 'source[path]=/'` — adjust for the actual default branch name).
- Push the workflow file so it runs, then poll `gh run list` / `gh api repos/{owner}/{repo}/pages` until the site is built, and derive the live URL (`https://<owner>.github.io/<repo>/`).
- If the README's live-site placeholder from step 4 wasn't filled in yet, update it now with the real URL, commit, and push again.

## 7. Repo About section

- Set the repository description and the website field to the live Pages URL:
  `gh repo edit <owner>/<repo> --description "<short description>" --homepage "https://<owner>.github.io/<repo>/"`
- Add relevant topics if helpful (e.g. `claude-code`, `training-demo`), but don't over-tag.

## 8. Final report

Tell the user, concisely:
- What was scanned and whether anything was excluded
- The commit(s) pushed
- The live GitHub Pages URL
- The repo About/description update
