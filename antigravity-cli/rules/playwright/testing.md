> This file extends [common/testing.md](../common/testing.md) with Playwright browser-automation specific content.

# Playwright Testing Rules

These rules cover ad-hoc browser automation and E2E-style checks driven through Playwright. They supplement, not replace, the coverage targets in [common/testing.md](../common/testing.md).

## Test File Location

- Write automation and test scripts to `/tmp/playwright-test-*.js`.
- Never write test files into the skill directory or the user's project.
- Save screenshots to `/tmp` as well; the OS cleans them up.

## Priority Order

### 1. Flow Validation

- Exercise the real user journey (navigate, fill, submit, verify redirect).
- Verify outcomes with deterministic waits, not sleeps.

```javascript
await page.goto(`${TARGET_URL}/login`);
await page.fill('input[name="email"]', 'test@example.com');
await page.fill('input[name="password"]', 'password123');
await page.click('button[type="submit"]');
await page.waitForURL('**/dashboard');
console.log('Login successful, redirected to dashboard');
```

### 2. Responsive / Visual

- Screenshot key breakpoints: 375, 768, 1024, 1440, 1920.
- Capture full-page screenshots per viewport.
- Verify no overflow and correct layout.
- Test both themes when present.

### 3. Validation Checks

- Broken link detection via `page.request.head(href)`.
- Image load verification.
- Form validation and error states.

### 4. Accessibility

- Keyboard navigation and focus order.
- Automated accessibility spot checks.
- Color contrast on key surfaces.

## Locator Strategy

- Prefer role-, label-, and test-id-based locators.
- Avoid brittle CSS chains and index-based selectors.
- Use explicit waits: `waitForSelector('.el', { timeout: 10000 })`.

## Reliability

- Avoid flaky timeout-based assertions; prefer deterministic waits.
- Wrap automation in try/catch/finally with `browser.close()` in finally.
- Keep scripts atomic and re-runnable.
- Use `slowMo` when a human needs to watch the run.

## Browser Mode

- Default to `headless: false` for interactive debugging and demos.
- Use `headless: true` (with `--no-sandbox`) only for CI or when the user explicitly requests background execution.
