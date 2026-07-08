> This file extends [common/coding-style.md](../common/coding-style.md) with Playwright script-authoring specific content.

# Playwright Coding Style

Style rules for the custom automation scripts written to `/tmp` and executed via `run.js`.

## Script Skeleton

Every script follows the same shape:

```javascript
// /tmp/playwright-test-<name>.js
const { chromium } = require('playwright');

const TARGET_URL = 'http://localhost:3001'; // detected or user-provided

(async () => {
  const browser = await chromium.launch({ headless: false, slowMo: 100 });
  const page = await browser.newPage();
  try {
    await page.goto(TARGET_URL);
    // ... automation steps ...
    console.log('Done:', await page.title());
  } catch (error) {
    console.error('Automation error:', error.message);
  } finally {
    await browser.close();
  }
})();
```

## Conventions

- **`TARGET_URL` constant at the top** - never hardcode URLs inline mid-script.
- **Async IIFE** - wrap the flow in `(async () => { ... })()`.
- **try/catch/finally** - always close the browser in `finally`.
- **Progress logging** - use `console.log()` to trace each meaningful step.
- **`slowMo`** - add `slowMo: 100` (or `50`) when actions should be visible.
- **One concern per script** - keep scripts focused and re-runnable.

## Naming

- Files: `playwright-test-<purpose>.js` (e.g. `playwright-test-login.js`).
- Screenshots: descriptive, breakpoint-aware (`/tmp/mobile.png`, `/tmp/desktop.png`).

## Executor Compatibility

- Scripts run through `run.js`, which auto-wraps bare code and injects `getContextOptionsWithHeaders()` and `helpers` when `require()` is absent.
- When writing full scripts with `require('playwright')`, provide your own async IIFE - the executor leaves complete scripts untouched.
- Prefer `helpers.createContext(browser)` so environment-configured HTTP headers are applied automatically.

## Do Not

- Do not write scripts outside `/tmp`.
- Do not use fixed `waitForTimeout` where a state-based wait works.
- Do not hardcode credentials - inject via environment or helper arguments.
