# Agent-Browser Quick Reference

## Installation

```bash
npm install -g agent-browser
```

---

## Philosophy: Be a User, Not a Script

When testing web applications, **think and act like a real user**, not a test script:

1. **Explore naturally** - Scroll to see content, click on things to understand them
2. **Be curious** - "What does this button do?" "What happens if I scroll down?"
3. **Communicate what you see** - Describe the UI state to the user as you go
4. **Ask when stuck** - If you can't tell what's happening, ask the user (they may be watching the browser)

### What NOT to Do

- Don't take screenshots reflexively after every action
- Don't poll silently without communicating progress
- Don't execute minimal paths without exploring
- Don't assume you know the UI without looking around

### What TO Do

- Scroll to see all content before concluding
- Click on elements to understand app state
- Tell the user what you're doing during waits
- Ask the user when you're unsure if something completed

---

## Screenshots: Strategic, Not Reflexive

**Screenshots are opt-in, not default.** Only take them when there's a clear reason.

### When Screenshots ARE Useful

| Scenario | Why |
|----------|-----|
| User explicitly requests visual evidence | They asked for it |
| Documenting a bug or unexpected behavior | Need proof |
| Before/after comparison for a specific change | Demonstrating impact |
| Creating documentation or reports | Visual artifacts needed |

### When to Skip Screenshots

| Scenario | Why |
|----------|-----|
| After every click/action | Noise, wastes time |
| During polling/waiting | Same state captured multiple times |
| When user is watching `--headed` browser | They can see it themselves |
| For routine verification | Snapshot text is sufficient |

**Rule**: Don't screenshot unless the user asked OR you need to capture unexpected behavior.

---

## Waiting for Async Operations

Web apps often have operations that take time (AI generation, API calls, file processing). **agent-browser has no `waitFor` primitives** - you must poll, but do it intelligently.

### The Anti-Pattern: Silent Polling

```bash
# BAD: Silent polling that frustrates everyone
sleep 5 && agent-browser snapshot -ic
sleep 10 && agent-browser snapshot -ic
sleep 15 && agent-browser snapshot -ic
sleep 30 && agent-browser snapshot -ic  # Timeout, user says "it finished already"
```

### The Better Pattern: Communicate and Collaborate

```bash
# GOOD: Tell the user what's happening
agent-browser snapshot -ic
# "The app shows 'Processing...' - I'll check again in 10 seconds"

sleep 10
agent-browser snapshot -ic
# "Still processing. The status hasn't changed. I'll wait another 15 seconds."

sleep 15
agent-browser snapshot -ic
# "Status still shows 'Processing...'. Can you see the browser? Has it finished?"
```

### Progress Detection Strategy

1. **Identify the indicator** - What element shows progress? (spinner, status text, progress bar)
2. **Watch for change** - Is the indicator different from last snapshot?
3. **Detect completion signals** - Success message, new content appearing, indicator disappearing
4. **Ask the user if stuck** - After 2-3 polls with no change, ask if they see something you don't

### When to Ask the User

If you've polled 2-3 times with no progress indicator change:

> "The status still shows '[X]'. I can't tell if it's still working or finished. Can you see the browser window? What does it show?"

**The user watching `--headed` mode often knows before you do.**

---

## The Ref-Based Approach

Agent-browser uses a unique "ref-based" element selection:

1. Run `agent-browser snapshot -ic` to get the accessibility tree
2. Each interactive element gets a ref like `@e1`, `@e2`, `@e3`
3. Use these refs in subsequent commands: `click @e3`, `fill @e5 "text"`

This is more reliable than CSS selectors because:
- Refs are based on the accessibility tree (what screen readers see)
- They're deterministic for the current page state
- They work regardless of CSS class names or DOM structure

## Command Categories

### Lifecycle
| Command | Description |
|---------|-------------|
| `open <url>` | Navigate to URL |
| `close` | Close browser session |

### Inspection
| Command | Description |
|---------|-------------|
| `snapshot` | Get accessibility tree |
| `snapshot -i` | Interactive elements only |
| `snapshot -ic` | Interactive + compact |
| `snapshot --json` | JSON output |
| `screenshot <file>` | Capture page |

### Interaction
| Command | Description |
|---------|-------------|
| `click @ref` | Click element |
| `dblclick @ref` | Double-click |
| `fill @ref "text"` | Fill input |
| `type "text"` | Type anywhere |
| `press <Key>` | Press key (Enter, Tab, etc.) |
| `select @ref "opt"` | Select dropdown |
| `check @ref` | Check checkbox |
| `uncheck @ref` | Uncheck checkbox |
| `hover @ref` | Hover over element |
| `scroll up/down` | Scroll page |

### Data Extraction
| Command | Description |
|---------|-------------|
| `get text @ref` | Element text |
| `get html @ref` | Element HTML |
| `get value @ref` | Input value |
| `get attr @ref name` | Attribute value |
| `get title` | Page title |
| `get url` | Current URL |
| `get count "sel"` | Count elements |

### State Checks
| Command | Description |
|---------|-------------|
| `is visible @ref` | Visibility check |
| `is enabled @ref` | Enabled check |
| `is checked @ref` | Checkbox state |

## Common Patterns

### Login Flow
```bash
agent-browser open "https://app.example.com/login"
agent-browser snapshot -ic
# Find username (@e3), password (@e4), submit (@e5)
agent-browser fill @e3 "user@example.com"
agent-browser fill @e4 "password123"
agent-browser click @e5
agent-browser snapshot -ic  # Verify logged in
```

### Search and Extract
```bash
agent-browser open "https://example.com"
agent-browser snapshot -ic
# Find search box (@e2)
agent-browser fill @e2 "search query"
agent-browser press Enter
agent-browser snapshot -ic  # Get results
agent-browser get text @e5  # Extract specific result
```

### Screenshot Documentation
```bash
agent-browser open "https://example.com"
agent-browser screenshot before.png
# Make changes...
agent-browser screenshot after.png
agent-browser close
```

## Debugging Tips

1. **Use `--headed` flag** to see the browser:
   ```bash
   agent-browser open "https://example.com" --headed
   ```

2. **Check element visibility** before interacting:
   ```bash
   agent-browser is visible @e3
   ```

3. **Use full snapshots** when compact misses elements:
   ```bash
   agent-browser snapshot  # Without -c flag
   ```

4. **Add delays** for dynamic content:
   ```bash
   sleep 2  # Wait for JS to load
   agent-browser snapshot -ic
   ```

## Error Handling

Common issues and solutions:

| Error | Solution |
|-------|----------|
| "Element not found" | Re-snapshot, ref may have changed |
| "Element not visible" | Scroll to element or wait for load |
| "Element not interactive" | Use snapshot without `-i` to see all elements |
| "Timeout" | Page may be slow; increase wait time |

## Sessions

For authenticated workflows, use sessions:

```bash
agent-browser --session myapp open "https://app.example.com/login"
# Login steps...
agent-browser --session myapp close

# Later, reuse session (cookies preserved):
agent-browser --session myapp open "https://app.example.com/dashboard"
```
