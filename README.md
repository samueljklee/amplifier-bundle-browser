# Amplifier Browser Bundle

> Web browser automation for AI agents using [agent-browser](https://agent-browser.dev)

Give your Amplifier agents the ability to browse the web, fill forms, extract data, and capture screenshots—all through natural language.

## Why This Bundle?

| Without Browser Bundle | With Browser Bundle |
|------------------------|---------------------|
| "I can't access websites" | "I found the pricing on their site..." |
| "Please paste the content" | "Let me check their documentation..." |
| "I can't see the UI" | "Here's a screenshot of the issue..." |

## Quick Start

### 1. Install agent-browser

```bash
npm install -g agent-browser
```

### 2. Add the bundle

```bash
# As a composable bundle
amplifier bundle add git+https://github.com/anthropics/amplifier-bundle-browser

# Or as an app bundle (available in all sessions)
amplifier bundle add git+https://github.com/anthropics/amplifier-bundle-browser --app
```

### 3. Use it

```
"Use web-researcher to find the API rate limits from OpenAI's documentation"

"Use visual-documenter to screenshot our landing page"

"Use browser-operator to fill out the contact form with my details"
```

## Agents

### `browser:browser-operator`
General-purpose browser automation. The Swiss Army knife for web interaction.

**Best for:** Form filling, navigation, data extraction, one-off tasks

```
"Go to github.com/trending and tell me the top 5 repositories"
"Fill the signup form with name=Test, email=test@example.com"
```

### `browser:web-researcher`
Research-focused agent optimized for finding and synthesizing information.

**Best for:** Documentation lookup, competitive research, fact-finding

```
"Research the pricing of Stripe, Square, and PayPal"
"Find the system requirements for Docker Desktop"
```

### `browser:visual-documenter`
Screenshot and visual documentation specialist.

**Best for:** QA evidence, change tracking, visual audits, documentation

```
"Screenshot our checkout flow step by step"
"Capture the competitor's pricing page for reference"
```

## Recipes

Pre-built workflows for common tasks:

### Competitive Research
```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=@browser:recipes/competitive-research.yaml \
  context='{"competitors": ["stripe.com", "square.com"], "research_focus": "pricing"}'
```

### Visual Audit
```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=@browser:recipes/visual-audit.yaml \
  context='{"base_url": "https://mysite.com", "pages": ["/", "/pricing", "/about"]}'
```

### Form Automation
```bash
amplifier tool invoke recipes operation=execute \
  recipe_path=@browser:recipes/form-automation.yaml \
  context='{"form_url": "https://example.com/contact", "form_data": {"name": "John", "email": "john@test.com"}}'
```

## Use Cases

| Scenario | Agent | Example |
|----------|-------|---------|
| **Research** | web-researcher | "What are the rate limits for the GitHub API?" |
| **Documentation** | visual-documenter | "Screenshot the error page I'm seeing" |
| **Testing** | browser-operator | "Verify the login flow works on staging" |
| **Data Entry** | browser-operator | "Fill out this support ticket form" |
| **Monitoring** | web-researcher | "Check if competitor updated their pricing" |
| **QA Evidence** | visual-documenter | "Document the checkout flow for review" |

## How It Works

This bundle uses [agent-browser](https://agent-browser.dev) under the hood—a headless browser CLI designed specifically for AI agents. Key features:

- **Ref-based selection**: Elements are identified by accessibility tree refs (`@e1`, `@e2`) rather than fragile CSS selectors
- **AI-first design**: Optimized for LLM interaction patterns
- **Session management**: Cookies and auth state persist within sessions

## Composing with Other Bundles

The browser behavior can be included in your own bundles:

```yaml
# your-bundle.yaml
includes:
  - bundle: git+https://github.com/anthropics/amplifier-bundle-browser#subdirectory=behaviors/browser.yaml
```

This adds just the browser agents without the full foundation dependency.

## Requirements

- [agent-browser](https://agent-browser.dev) (`npm install -g agent-browser`)
- Node.js 18+
- Amplifier with foundation bundle (or equivalent providing bash tool)

## Troubleshooting

### "agent-browser: command not found"
```bash
npm install -g agent-browser
```

### "Element not found"
The page may have changed since the snapshot. Re-run `snapshot -ic` to get fresh refs.

### "Timeout" errors
Some pages are slow. The agent will retry, but you may need to wait for dynamic content.

### Sessions not persisting
Use the `--session` flag for authenticated workflows:
```bash
agent-browser --session myapp open "https://app.example.com"
```

## Contributing

Contributions welcome! Ideas for improvement:

- [ ] Additional specialized agents (e.g., `form-tester`, `accessibility-auditor`)
- [ ] More recipes for common workflows
- [ ] Better error handling patterns
- [ ] Viewport/responsive testing support

## License

MIT

---

Built for the [Amplifier](https://github.com/microsoft/amplifier) ecosystem.
