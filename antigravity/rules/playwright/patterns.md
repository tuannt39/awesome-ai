> This file extends [common/patterns.md](../common/patterns.md) with Playwright browser-automation specific content.

# Playwright Automation Patterns

General-purpose browser automation is written per-task, not from canned scripts. These patterns encode the required workflow.

## The Four Core Rules

Apply in order for every automation task:

1. **Detect dev servers first** - run detection before writing any localhost automation code.
2. **Write scripts to `/tmp`** - never write test files into the skill directory or the user's project.
3. **Visible browser by default** - `headless: false` unless the user explicitly asks for headless.
4. **Parameterize URLs** - a `TARGET_URL` constant at the top of every script.

## 1. Detect Before Code

Always resolve the target before writing automation for localhost:

```bash
cd $SKILL_DIR && node -e "require('./lib/helpers').detectDevServers().then(s => console.log(JSON.stringify(s)))"
```

- One server found: use it, inform the user.
- Multiple servers: ask which to test.
- No servers: ask for a URL or offer to start the dev server.
- Non-standard ports: `detectDevServers([customPort])`.

## 2. Parameterize the URL

```javascript
// /tmp/playwright-test-page.js
const { chromium } = require('playwright');

const TARGET_URL = 'http://localhost:3001'; // detected or user-provided

(async () => {
  const browser = await chromium.launch({ headless: false });
  const page = await browser.newPage();
  try {
    await page.goto(TARGET_URL);
    console.log('Page loaded:', await page.title());
    await page.screenshot({ path: '/tmp/screenshot.png', fullPage: true });
  } finally {
    await browser.close();
  }
})();
```

## 3. Execute Through the Universal Executor

Run scripts from the skill directory so module resolution is correct and Playwright auto-installs if missing:

```bash
cd $SKILL_DIR && node run.js /tmp/playwright-test-page.js
```

- **Files** (`/tmp/playwright-test-*.js`) for complex or reusable flows.
- **Inline** for quick one-offs (screenshot, title check, element existence).
- The executor wraps bare code in an async IIFE with try/catch and cleans temp files on the next run.

## 4. Prefer Helpers

Use `lib/helpers.js` instead of re-implementing common actions:

- `detectDevServers()` - required first step for localhost.
- `safeClick` / `safeType` - retry-backed interactions.
- `takeScreenshot` - timestamped captures.
- `authenticate` - login flows.
- `handleCookieBanner` - dismiss common consent banners.
- `extractTableData` - structured table extraction.
- `createContext` - context with env-driven custom headers.

## 5. Wait Deterministically

Prefer state-based waits over fixed timeouts:

- `page.waitForURL('**/dashboard')`
- `page.waitForSelector('.success-message')`
- `page.waitForLoadState('networkidle')`

Avoid `page.waitForTimeout(...)` except as a last resort for animation settling.

## 6. Custom HTTP Headers

Mark automated traffic or request LLM-optimized responses via environment variables:

```bash
PW_HEADER_NAME=X-Automated-By PW_HEADER_VALUE=playwright-skill \
  node run.js /tmp/my-script.js
```

Multiple headers use `PW_EXTRA_HEADERS` (JSON). Headers apply automatically through `helpers.createContext()` or the injected `getContextOptionsWithHeaders()`.

## Progressive Disclosure

Keep scripts minimal. Load `API_REFERENCE.md` only when a task needs advanced features: network interception, session reuse, visual regression, mobile emulation, performance capture, or CI integration.
