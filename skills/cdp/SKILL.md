---
name: cdp
description: Interact with local Chrome browser session via CDP CLI (only on explicit user approval after being asked to inspect, debug, or interact with a page open in Chrome)
---

# CDP (Chrome DevTools Protocol)

Lightweight Chrome DevTools Protocol CLI. Connects directly via WebSocket — no Puppeteer, works with 100+ tabs, instant connection.

## Prerequisites

- Chrome (or Chromium, Brave, Edge, Vivaldi) with remote debugging enabled: open `chrome://inspect/#remote-debugging` and toggle the switch
- Node.js 22+ (uses built-in WebSocket)
- If your browser's `DevToolsActivePort` is in a non-standard location, set `CDP_PORT_FILE` to its full path

## Commands

**IMPORTANT:** All commands must be run from the skill directory. Always use this pattern:

```bash
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs <command> [args]
```

Or create an alias/npm script for convenience.

### List open pages

```bash
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs list
```

### Take a screenshot

```bash
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs shot <target> [file]
```

Captures the **viewport only**. Scroll first with `eval` if you need content below the fold. Output includes the page's DPR and coordinate conversion hint (see **Coordinates** below).

### Accessibility tree snapshot

```bash
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs snap <target>
```

### Evaluate JavaScript

```bash
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs eval <target> <expr>
```

> **Watch out:** avoid index-based selection (`querySelectorAll(...)[i]`) across multiple `eval` calls when the DOM can change between them (e.g. after clicking Ignore, card indices shift). Collect all data in one `eval` or use stable selectors.

### Other commands

```bash
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs html    <target> [selector]
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs nav     <target> <url>
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs net     <target>
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs click   <target> <selector>
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs clickxy <target> <x> <y>
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs type    <target> <text>
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs loadall <target> <selector> [ms]
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs evalraw <target> <method> [json]
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs open    [url]
cd C:/Users/Jan/.claude/plugins/cache/janhlj-marketplace/chrome-cdp/1.0.1/skills/cdp && node scripts/cdp.mjs stop    [target]
```

## Coordinates

`shot` saves an image at native resolution: image pixels = CSS pixels × DPR. CDP Input events (`clickxy` etc.) take **CSS pixels**.

```
CSS px = screenshot image px / DPR
```

`shot` prints the DPR for the current page. Typical Retina (DPR=2): divide screenshot coords by 2.

## Tips

- Prefer `snap --compact` over `html` for page structure.
- Use `type` (not eval) to enter text in cross-origin iframes — `click`/`clickxy` to focus first, then `type`.
- Chrome shows an "Allow debugging" modal once per tab on first access. A background daemon keeps the session alive so subsequent commands need no further approval. Daemons auto-exit after 20 minutes of inactivity.
