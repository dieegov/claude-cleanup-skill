---
name: cleanup
description: Deep clean macOS caches, logs, and unnecessary files to free disk space. Safe cleanup only — never deletes configs, user data, or important files.
disable-model-invocation: true
---

# macOS Deep Cleanup

Run a comprehensive cleanup of caches and unnecessary files on macOS. **Safety first — only delete expendable caches, never configs or user data.**

## Step 1: Pre-scan — show current disk usage

Run `df -h /` to show available disk space before cleanup.

## Step 2: Run all safe cleanup commands

Execute these cleanup commands, suppressing errors for directories that don't exist. Track bytes freed per category.

### User Caches
```bash
rm -rf ~/Library/Caches/* 2>/dev/null
```

### User Logs
```bash
rm -rf ~/Library/Logs/* 2>/dev/null
```

### npm / npx cache
```bash
npm cache clean --force 2>/dev/null
rm -rf ~/.npm/_npx/* 2>/dev/null
rm -rf ~/.npm/_logs/* 2>/dev/null
```

### Homebrew cleanup
```bash
brew cleanup --prune=all 2>/dev/null
```

### pip cache
```bash
pip3 cache purge 2>/dev/null
```

### VS Code caches
```bash
rm -rf ~/Library/Application\ Support/Code/Cache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Code/CachedData/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Code/CachedExtensions/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Code/CachedExtensionVSIXs/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Code/Code\ Cache/* 2>/dev/null
```

### Cursor caches (same structure as VS Code)
```bash
rm -rf ~/Library/Application\ Support/Cursor/Cache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Cursor/CachedData/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Cursor/CachedExtensions/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Cursor/Code\ Cache/* 2>/dev/null
```

### Claude Desktop app caches
```bash
rm -rf ~/Library/Application\ Support/Claude/vm_bundles/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Claude/Cache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Claude/GPUCache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Claude/Code\ Cache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Claude/Service\ Worker/CacheStorage/* 2>/dev/null
```

### Chrome caches (NOT profile data, NOT extensions, NOT bookmarks)
```bash
rm -rf ~/Library/Application\ Support/Google/Chrome/Default/Service\ Worker/CacheStorage/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Google/Chrome/*/Service\ Worker/CacheStorage/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Google/Chrome/Default/Application\ Cache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Google/Chrome/Default/GPUCache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Google/Chrome/GrShaderCache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Google/Chrome/ShaderCache/* 2>/dev/null
```

### Spotify cache
```bash
rm -rf ~/Library/Application\ Support/Spotify/PersistentCache/* 2>/dev/null
```

### Slack cache
```bash
rm -rf ~/Library/Application\ Support/Slack/Cache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Slack/Code\ Cache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Slack/GPUCache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/Slack/Service\ Worker/CacheStorage/* 2>/dev/null
```

### Discord cache
```bash
rm -rf ~/Library/Application\ Support/discord/Cache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/discord/Code\ Cache/* 2>/dev/null
rm -rf ~/Library/Application\ Support/discord/GPUCache/* 2>/dev/null
```

### Zoom cache
```bash
rm -rf ~/Library/Application\ Support/zoom.us/AutoUpdater/* 2>/dev/null
```

### Docker (only if Docker is not running)
```bash
# Only run if Docker daemon is NOT active
docker system prune -f 2>/dev/null
```

### macOS system caches (requires sudo — skip if not available)
```bash
sudo rm -rf /Library/Caches/* 2>/dev/null
sudo rm -rf /private/var/log/asl/*.asl 2>/dev/null
```

### DNS cache flush
```bash
sudo dscacheutil -flushcache 2>/dev/null
sudo killall -HUP mDNSResponder 2>/dev/null
```

### .DS_Store files (home directory)
```bash
find ~ -name ".DS_Store" -maxdepth 4 -delete 2>/dev/null
```

## Step 3: Deep scan for large space hogs

After running the above, do a quick scan for any other large cache-like directories that may have been missed:

```bash
du -sh ~/Library/Application\ Support/*/Cache* ~/Library/Application\ Support/*/GPUCache ~/Library/Application\ Support/*/Service\ Worker 2>/dev/null | sort -rh | head -10
```

If any are over 500 MB, report them to the user and ask before deleting.

## Step 4: Post-scan — show results

Run `df -h /` again and calculate total space freed. Present a summary table showing:
- Space freed per category
- Total space freed
- Current available disk space

## SAFETY RULES — DO NOT VIOLATE

- NEVER delete anything under ~/Documents, ~/Desktop, ~/Downloads, ~/Work, ~/Projects, ~/Code
- NEVER delete config files (anything named config.json, .env, preferences, settings, profiles, bookmarks, etc.)
- NEVER delete ~/Library/Application Support/*/Local Storage (contains app state)
- NEVER delete ~/Library/Application Support/*/IndexedDB (contains app data)
- NEVER delete ~/Library/Application Support/*/databases
- NEVER delete ~/Library/Keychains or anything auth/credential related
- NEVER delete SSH keys, GPG keys, or tokens
- NEVER delete git repositories or .git directories
- NEVER delete node_modules (user may need them for active projects)
- NEVER delete nvm Node versions (user manages these manually)
- ONLY delete directories explicitly listed above — do not improvise
- If unsure whether something is safe, ASK the user first
