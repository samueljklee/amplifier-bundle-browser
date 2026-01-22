# Amplifier Browser Bundle

> Web browser automation for AI agents using [agent-browser](https://agent-browser.dev)

Give your Amplifier agents the ability to browse the web, fill forms, extract data, and capture screenshots—all through natural language.

## Why Browser Automation? (The Gap)

**Amplifier and Claude Code already have web capabilities** via fetch/curl. So why do you need a browser?

### What Fetch/Curl CAN'T Do

| Limitation | Why It Matters |
|------------|----------------|
| **No JavaScript execution** | Modern apps (React/Vue/Angular) render content client-side. Fetch returns `<div id="root"></div>` |
| **No authentication flows** | Can't fill login forms, handle OAuth, or manage sessions |
| **No visual verification** | Can't "see" if a page looks right—only gets HTML |
| **No interaction** | Can't click buttons, fill forms, or navigate multi-step flows |
| **Bot detection** | Many sites block simple HTTP requests |

### Real Examples of the Gap

```bash
# This returns an empty shell (JS-rendered content):
curl https://status.vercel.com

# This gets blocked or returns different content:
curl https://notion.so/pricing

# This can't log in and see your dashboard:
curl https://app.yourcompany.com/dashboard
```

**Browser automation solves all of these.**

## Quick Start

### 1. Install agent-browser

```bash
npm install -g agent-browser
```

### 2. Add the bundle

```bash
# As an app bundle (available in all sessions)
amplifier bundle add git+https://github.com/samueljklee/amplifier-bundle-browser --app
```

### 3. Try the demo

```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=@browser:recipes/examples/quick-demo.yaml
```

Or in a session:
```
"Use browser-operator to go to news.ycombinator.com and tell me the top 5 stories"
```

## Agents

### `browser:browser-operator`
General-purpose browser automation. The Swiss Army knife for web interaction.

**Best for:** Form filling, navigation, data extraction, one-off tasks

```
"Go to github.com/trending and tell me the top 5 repositories"
"Fill the signup form with name=Test, email=test@example.com"
"Log into staging.myapp.com and verify the dashboard loads"
```

### `browser:web-researcher`
Research-focused agent optimized for finding and synthesizing information.

**Best for:** Documentation lookup, competitive research, fact-finding

```
"Research the pricing of Stripe, Square, and PayPal"
"Find the system requirements for Docker Desktop"
"Check what's on the OpenAI status page"
```

### `browser:visual-documenter`
Screenshot and visual documentation specialist.

**Best for:** QA evidence, change tracking, visual audits, documentation

```
"Screenshot our checkout flow step by step"
"Capture the competitor's pricing page for reference"
"Document what our landing page looks like right now"
```

## Example Recipes

### Quick Demo (Try First!)
```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=@browser:recipes/examples/quick-demo.yaml
```
Fetches live Hacker News content to show browser automation working.

### Verify Deployed App
```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=@browser:recipes/examples/verify-deployed-app.yaml \
  context='{"app_url": "https://staging.myapp.com", "login_email": "test@example.com", "login_password": "testpass"}'
```
Logs into your app, navigates key pages, screenshots and reports issues.

### Check Status Page
```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=@browser:recipes/examples/check-status-page.yaml \
  context='{"status_url": "https://status.openai.com"}'
```
Checks JS-rendered status pages that `curl` can't read.

### Monitor Competitor Pricing
```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=@browser:recipes/examples/monitor-competitor-pricing.yaml \
  context='{"pricing_url": "https://competitor.com/pricing"}'
```
Extracts pricing info from pages that may be A/B tested or dynamically rendered.

### Extract Dynamic Content
```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=@browser:recipes/examples/extract-dynamic-content.yaml \
  context='{"url": "https://app.example.com/dashboard", "extract": "all metrics and their values"}'
```
Gets content from JS-rendered pages where fetch returns empty HTML.

## All Recipes

| Recipe | Purpose | Key Input |
|--------|---------|-----------|
| `examples/quick-demo.yaml` | Demo browser working | (none) |
| `examples/verify-deployed-app.yaml` | Test your deployed app | `app_url`, `login_*` |
| `examples/check-status-page.yaml` | Check service status | `status_url` |
| `examples/extract-dynamic-content.yaml` | Scrape JS-rendered pages | `url`, `extract` |
| `examples/monitor-competitor-pricing.yaml` | Get competitor prices | `pricing_url` |
| `competitive-research.yaml` | Multi-competitor analysis | `competitors[]` |
| `visual-audit.yaml` | Multi-page screenshots | `base_url`, `pages[]` |
| `form-automation.yaml` | Fill web forms | `form_url`, `form_data` |

## Use Cases

| Scenario | Agent | Why Browser Needed |
|----------|-------|-------------------|
| **Verify staging deployment** | browser-operator | Need to log in and see rendered UI |
| **Check service status** | web-researcher | Status pages are JS-rendered |
| **Research competitor pricing** | web-researcher | Prices are dynamic/A/B tested |
| **Document UI for QA** | visual-documenter | Need screenshots, not HTML |
| **Fill support tickets** | browser-operator | Need to interact with forms |
| **Test auth flows** | browser-operator | OAuth/SSO requires real browser |

## How It Works

This bundle uses [agent-browser](https://agent-browser.dev) under the hood—a headless browser CLI designed specifically for AI agents.

**Key features:**
- **Ref-based selection**: Elements are identified by accessibility tree refs (`@e1`, `@e2`) rather than fragile CSS selectors
- **AI-first design**: Optimized for LLM interaction patterns
- **Session management**: Cookies and auth state persist within sessions

**Architecture:**
```
Your Prompt → Amplifier Agent → bash tool → agent-browser CLI → Chromium
                                                    ↓
                                          Screenshots, data, results
```

## Composing with Other Bundles

Add browser capability to your own bundles:

```yaml
# your-bundle.yaml
includes:
  - bundle: git+https://github.com/samueljklee/amplifier-bundle-browser#subdirectory=behaviors/browser.yaml
```

This adds browser agents without requiring the full foundation dependency.

## Requirements

- [agent-browser](https://agent-browser.dev) (`npm install -g agent-browser`)
- Node.js 18+
- Amplifier with bash tool (included in foundation bundle)

## Troubleshooting

### "agent-browser: command not found"
```bash
npm install -g agent-browser
```

### "Element not found"
The page changed since the snapshot. The agent will re-snapshot automatically.

### "Timeout" errors
Page is slow to load. The agent handles retries, but very slow pages may need manual waits.

### Pages not rendering correctly
Some pages need `--headed` mode for debugging:
```bash
agent-browser open "https://example.com" --headed
```

## Contributing

Contributions welcome! Ideas:

- [ ] Additional agents: `form-tester`, `accessibility-auditor`, `screenshot-differ`
- [ ] More recipes: E2E testing, monitoring, data pipelines
- [ ] Better error recovery patterns
- [ ] Viewport/responsive testing support

## License

MIT

---

Built for the [Amplifier](https://github.com/microsoft/amplifier) ecosystem.
