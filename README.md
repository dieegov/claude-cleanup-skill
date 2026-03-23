# `/cleanup` — macOS Deep Clean for Claude Code

> One command. Gigabytes back. Zero risk to your actual files.

Your Mac accumulates **gigabytes** of cache files, stale logs, and leftover junk from browsers, editors, package managers, and apps you forgot you had. This Claude Code skill wipes it all in seconds — safely.

## What it cleans

| Category | What gets nuked |
|----------|----------------|
| **System** | `~/Library/Caches/*`, `~/Library/Logs/*`, system caches, `.DS_Store` files, DNS cache |
| **Package managers** | npm, npx, pip, Homebrew (old versions + cache) |
| **Editors** | VS Code, Cursor — cached data, extensions cache, code cache |
| **AI tools** | Claude Desktop — VM bundles, GPU cache, service workers |
| **Browsers** | Chrome — service workers, GPU cache, shader cache (never touches bookmarks, history, or extensions) |
| **Chat apps** | Slack, Discord, Zoom — cache and auto-updater bloat |
| **Media** | Spotify persistent cache |
| **Containers** | Docker dangling images and build cache (only if daemon is idle) |

After cleaning, it deep-scans for any remaining space hogs over 500 MB and asks before touching them.

## What it NEVER touches

This skill has strict safety rails hardcoded in:

- Your files — Documents, Desktop, Downloads, Work, Projects, Code
- App configs — settings, preferences, profiles, bookmarks
- App state — Local Storage, IndexedDB, databases
- Credentials — Keychain, SSH keys, GPG keys, tokens, `.env` files
- Dev environments — git repos, `node_modules`, nvm versions
- Anything not on the explicit allow-list — it doesn't improvise

## Install

**Option 1: Copy to your personal skills (recommended)**

```bash
# Clone the repo
git clone https://github.com/dancolta/claude-cleanup-skill.git

# Copy to your Claude Code skills directory
mkdir -p ~/.claude/skills/cleanup
cp claude-cleanup-skill/SKILL.md ~/.claude/skills/cleanup/SKILL.md
```

**Option 2: One-liner**

```bash
mkdir -p ~/.claude/skills/cleanup && curl -fsSL https://raw.githubusercontent.com/dancolta/claude-cleanup-skill/main/SKILL.md -o ~/.claude/skills/cleanup/SKILL.md
```

**Option 3: Project-level**

Drop the `SKILL.md` into your project's `.claude/skills/cleanup/` directory to share it with your team via git.

## Usage

In any Claude Code session, just type:

```
/cleanup
```

That's it. You'll see:
1. Current disk usage (before)
2. Each cleanup category running with bytes freed
3. Deep scan for remaining large caches
4. Summary table with total space reclaimed

Typical results: **2–15 GB freed** depending on how long it's been since your last cleanup.

## Requirements

- macOS (tested on Sonoma, Sequoia)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- Optional: `sudo` access for system-level caches and DNS flush (skipped gracefully if unavailable)

## Customization

The skill is just a markdown file — fork it and add your own cleanup targets. Some ideas:

- Xcode derived data (`~/Library/Developer/Xcode/DerivedData`)
- Android Studio caches
- JetBrains IDE caches
- Conda package cache
- Yarn/pnpm cache

Just follow the same pattern: explicit paths, `2>/dev/null` for missing dirs, and add it to the safety rules exclusion list if needed.

## How it works

Claude Code skills are markdown files that give Claude structured instructions. When you type `/cleanup`, Claude reads the `SKILL.md` and executes the cleanup steps using your terminal — showing you exactly what's happening at each step. No binaries, no background processes, fully transparent.

## License

MIT — do whatever you want with it.

## Contributing

Found a cache directory worth cleaning? Open a PR. Just make sure it:
1. Only targets **expendable caches** (things that regenerate automatically)
2. Never touches user data, configs, or credentials
3. Includes the directory in the safety rules section if there's any ambiguity
