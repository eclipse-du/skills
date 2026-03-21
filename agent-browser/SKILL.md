---
name: agent-browser
description: Fast, persistent browser automation with session continuity across sequential agent commands.
---

# Browser Automation with agent-browser

`agent-browser` is a fast, Rust-based headless browser automation CLI designed for AI agents. It uses Chrome/Chromium via the Chrome DevTools Protocol (CDP).

## Installation

You can install `agent-browser` using one of the following methods, depending on your environment:

```bash
# Using npm
npm i -g agent-browser

# Using brew (macOS)
brew install agent-browser

# Using cargo
cargo install agent-browser
```

After installing the CLI package, you need to run the following command to download the required Chrome browser binaries:
```bash
agent-browser install
```

To upgrade to the latest version at any time:
```bash
agent-browser upgrade
```

## Features and Capabilities

This skill provides persistent browser automation with session continuity across sequential agent commands.

- Supports three browser modes: headless Chromium, real Chrome with profile support, and cloud-hosted remote browsers with proxy configuration
- Includes 15+ command categories covering navigation, page inspection, interactions, data extraction, cookie management, and JavaScript execution
- Offers cloud session management, local server tunneling via Cloudflare, and parallel subagent execution through remote sessions
- Built-in Python integration for setting variables, accessing the browser object, and running scripts within the automation context

## Quick Start

You can load this skill by using:
```bash
npx skills add https://github.com/vercel-labs/agent-browser --skill agent-browser
```

*(For a full command list and usage options, refer to the agent-browser documentation or run `agent-browser --help` once installed)*
