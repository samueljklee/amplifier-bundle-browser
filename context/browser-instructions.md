# Agent-Browser Quick Reference

## Installation

```bash
npm install -g agent-browser
```

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
