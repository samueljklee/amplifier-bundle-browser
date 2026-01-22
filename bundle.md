---
bundle:
  name: browser
  version: 0.1.0
  description: Browser automation capabilities via agent-browser CLI

# Include foundation for bash tool, then our behavior for the agent
includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main
  - bundle: browser:behaviors/browser.yaml
---

# Browser Bundle

Web browser automation for AI agents using [agent-browser](https://agent-browser.dev).

## Capabilities

- Navigate to URLs and interact with web pages
- Fill forms and click buttons using natural language
- Extract structured data from pages
- Take screenshots for verification
- Handle multi-step workflows

## Usage

Delegate browser tasks to the `browser-operator` agent:

```
task(agent="browser:browser-operator", 
     instruction="Go to example.com and extract the main heading")
```

## Requirements

Install agent-browser:
```bash
npm install -g agent-browser
```

@browser:context/browser-instructions.md
