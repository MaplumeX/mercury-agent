# Release v1.1.11

## ☿ Mercury Agent v1.1.11

**Token Saver Mode, full Skill System, and standalone binaries.**

The biggest release since the 1.1.x line began — Mercury now ships as a single executable on every major OS, learns new tricks via user-defined skills, and gives you a live status bar with token usage at the bottom of every session.

### What's New

- **Token Saver Mode** (#69) — opt-in mode that aggressively trims session context so long-running agents don't bleed tokens. Pairs with a redesigned bottom status bar showing provider, model, and live token usage, plus per-step spinners (one per tool call) instead of a single global one.
- **Skill System** (#67) — Mercury can now load user-defined behaviors as markdown files in `~/.mercury/skills/`. Skills get routed to on demand based on keyword/semantic match and inject their instructions only when relevant.
- **Screenshot skill** — built-in skill for capturing website screenshots with configurable viewport and dark/light mode support.
- **Standalone binaries** (#61) — `mercury` now ships as a single executable for:
  - macOS arm64 / x64
  - Linux x64 / arm64
  - Windows x64

  One-line installers are available per OS; no Node runtime required. The install widget on [mercuryagent.sh](https://mercuryagent.sh) detects your platform automatically.
- **Domain migration** — Mercury's home is now [mercuryagent.sh](https://mercuryagent.sh) (was `mercury.cosmicstack.org`).
- **Chinese translations** for README, ARCHITECTURE, and CHANGELOG (#53).

### Bug Fixes

- **Skill routing no longer fans out to 10 skills** (#68) — when the router is genuinely uncertain, you now get a clean numbered picker (`#1`, `#2`, …) instead of every weakly-matching skill firing at once.
- **No more spurious ambiguity prompts** from incidental keyword overlap. The matcher now requires real signal before asking you to disambiguate.
- **Release asset names aligned with published binaries** (#63) — fixes one-line installer scripts that were pointing at the wrong filenames in earlier GitHub releases.
- **Shell permission guard now checks each segment independently** (#48) — addresses a reported advisory where a combined shell pattern could slip a disallowed command past the permission check when chained with `;`, `&&`, or `|`.

### Maintenance

- Removed the `anonymous-file-uploader` skill — no longer needed.
- `pino` upgraded 9.14.0 → ^10.3.1 (#51).
- Spinner polish, docs refreshes, and a cleaner status line throughout the TUI.

### Install / Upgrade

**npm:**
```
npm install -g @cosmicstack/mercury-agent@1.1.11
```

**Standalone binary:** download from the release assets below (checksums in `checksums.txt`), or use the one-line installer from [mercuryagent.sh](https://mercuryagent.sh).

### Migration from v1.1.9

No breaking changes. Skill System and Token Saver Mode are both opt-in. Existing configs, providers, and `~/.mercury/` data carry over unchanged.

> `1.1.10` was skipped to keep numbering aligned across publish channels.

---

**Full Changelog**: https://github.com/cosmicstack-labs/mercury-agent/compare/v1.1.9...v1.1.11
