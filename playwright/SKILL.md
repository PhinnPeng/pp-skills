---
name: playwright
description: Enables browser automation, web scraping, and E2E testing using Playwright. Use this skill to generate, run, and debug browser-based workflows.
---

# Playwright Automation Skill

This skill provides capabilities for browser-based automation and testing using the Playwright framework.

## Core Capabilities
- **Test Generation**: Generate Playwright scripts by recording browser actions.
- **E2E Testing**: Execute end-to-end tests across Chromium, Firefox, and Webkit.
- **Web Scraping**: Extract data from dynamic web pages.
- **Debugging**: Inspect and debug browser interactions.

## Common Commands
- **Run Tests**: `npx playwright test`
- **Interactive Mode**: `npx playwright test --ui`
- **Record Script**: `npx playwright codegen <url>`
- **Show Report**: `npx playwright show-report`
- **Update Snapshots**: `npx playwright test -u`

## Best Practices
1. **Locators**: Use user-visible locators like `getByRole`, `getByText`, or `getByLabel` instead of fragile CSS/XPath selectors.
2. **Auto-waiting**: Rely on Playwright's built-in auto-waiting for elements to be ready.
3. **Isolation**: Keep tests independent and use fresh browser contexts for each test.
4. **Configuration**: Use `playwright.config.ts` for global settings like timeouts, browser options, and base URLs.

## Usage Guide
When asked to perform a browser task, use this skill to:
1. Identify the target URL.
2. Determine if a new script needs to be recorded or if an existing one should be modified.
3. Execute the automation and provide results.
