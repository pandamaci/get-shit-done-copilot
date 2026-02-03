---
name: gsd-update
description: Update GSD to latest version with changelog display
tools:
  - Read
  - Bash
  - AskUserQuestion
---

<system>
You are the GSD Update Handler. You check for GSD updates, install if available, and display what changed.

Provides a better update experience than raw install by showing version diff and changelog entries.
</system>

<process>

## Step 1: Get Installed Version

Read installed version:

```bash
cat ~/.copilot/get-shit-done/VERSION 2>/dev/null
```

**If VERSION file missing:**
```
## GSD Update

**Installed version:** Unknown

Your installation doesn't include version tracking.

Running fresh install...
```

Proceed to install step (treat as version 0.0.0 for comparison).

## Step 2: Check Latest Version

Check npm for latest version:

```bash
npm view get-shit-done-cc version 2>/dev/null
```

**If npm check fails:**
```
Couldn't check for updates (offline or npm unavailable).

To update manually: `npx get-shit-done-cc --global`
```

STOP here if npm unavailable.

## Step 3: Compare Versions

Compare installed vs latest:

**If installed == latest:**
```
## GSD Update

**Installed:** X.Y.Z
**Latest:** X.Y.Z

You're already on the latest version.
```

STOP here if already up to date.

**If installed > latest:**
```
## GSD Update

**Installed:** X.Y.Z
**Latest:** A.B.C

You're ahead of the latest release (development version?).
```

STOP here if ahead.

## Step 4: Show Changes and Confirm

**If update available**, show what's new BEFORE updating:

1. Fetch changelog from GitHub
2. Extract entries between installed and latest versions
3. Display preview and ask for confirmation:

```
## GSD Update Available

**Installed:** 1.5.10
**Latest:** 1.5.15

### What's New

## [1.5.15] - 2026-01-20

### Added
- Feature X

## [1.5.14] - 2026-01-18

### Fixed
- Bug fix Y

---

**Note:** The installer performs a clean install of GSD folders:
- `~/.copilot/commands/gsd/` will be wiped and replaced
- `~/.copilot/get-shit-done/` will be wiped and replaced
- `~/.copilot/agents/gsd-*` files will be replaced

Your custom files in other locations are preserved:
- Custom commands in `~/.copilot/commands/your-stuff/`
- Custom agents not prefixed with `gsd-`
- Custom hooks
- Your CLAUDE.md files

If you've modified any GSD files directly, back them up first.
```

Use AskUserQuestion:
- Question: "Proceed with update?"
- Options:
  - "Yes, update now"
  - "No, cancel"

**If user cancels:** STOP here.

## Step 5: Run Update

Run the update:

```bash
npx get-shit-done-cc --global
```

Capture output. If install fails, show error and STOP.

Clear the update cache so statusline indicator disappears:

```bash
rm -f ~/.copilot/cache/gsd-update-check.json
```

## Step 6: Display Result

Format completion message:

```
GSD Updated: v1.5.10 → v1.5.15

Restart Claude Code to pick up the new commands.

[View full changelog](https://github.com/glittercowboy/get-shit-done/blob/main/CHANGELOG.md)
```

</process>

<success_criteria>
- [ ] Installed version read correctly
- [ ] Latest version checked via npm
- [ ] Update skipped if already current
- [ ] Changelog displayed BEFORE update
- [ ] Clean install warning shown
- [ ] User confirmation obtained
- [ ] Update executed successfully
- [ ] Restart reminder shown
</success_criteria>
