# Why there's no hook file here for the Christmas-popup feature

The training brief's final task asks for a `.claude/hooks` entry that pops up a dialog "after user stay on the website for more than 10 sec." That behavior — detecting how long a *website visitor* has been on the page and showing them a dialog — cannot be implemented as a Claude Code hook.

Claude Code hooks (`.claude/settings.json`'s `hooks` key) are shell commands the **Claude Code CLI** runs in response to its own lifecycle events on this machine — `PreToolUse`, `PostToolUse`, `SessionStart`, `Stop`, and similar. They have no connection to, or visibility into, a browser session on a deployed static site. There is no hook event that corresponds to "a visitor has been looking at the page for 10 seconds," and nothing under `.claude/hooks` could ever run inside a visitor's browser — it runs on the developer's machine while Claude Code itself is active.

The actual feature — a 10-second dwell-time popup announcing the Christmas celebration with a name/email/department RSVP — is implemented where it can actually work: as client-side JavaScript in [`index.html`](../../index.html), in the same in-memory-only style as the ticket form (no `localStorage`, no network calls). Search that file for `EVENT ANNOUNCEMENT MODAL` to find it.
