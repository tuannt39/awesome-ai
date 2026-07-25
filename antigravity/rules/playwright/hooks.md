> This file extends [common/hooks.md](../common/hooks.md) with Playwright browser-automation specific hook recommendations.

# Playwright Hooks

Prefer project-local tooling. Do not wire hooks to remote one-off package execution.

## PreToolUse Hooks

### Enforce /tmp for Test Scripts

Block writes of Playwright test scripts outside `/tmp`, reading from tool input rather than a file that may not exist yet:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "command": "node -e \"let d='';process.stdin.on('data',c=>d+=c);process.stdin.on('end',()=>{const i=JSON.parse(d);const p=i.tool_input?.path||i.tool_input?.file_path||'';if(/playwright-test-.*\\.js$/.test(p)&&!p.startsWith('/tmp/')){console.error('[Hook] BLOCKED: Playwright test scripts must be written to /tmp');process.exit(2)}console.log(d)})\"",
        "description": "Keep Playwright test scripts confined to /tmp"
      }
    ]
  }
}
```

## PostToolUse Hooks

### Verify Playwright Is Installed

Run once after edits to the skill so the executor never fails mid-run:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "node -e \"try{require.resolve('playwright');process.exit(0)}catch(e){console.error('[Hook] Playwright not installed - run: npm run setup');process.exit(0)}\"",
        "description": "Warn if Playwright is missing after edits"
      }
    ]
  }
}
```

### Lint Automation Scripts

Lint edited automation scripts with the project's local linter:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "npx --no-install eslint --fix \"$FILE_PATH\" 2>/dev/null || true",
        "description": "Lint edited Playwright scripts when a local ESLint exists"
      }
    ]
  }
}
```

## Stop Hooks

### Clean Up Screenshots and Temp Artifacts

Sweep transient artifacts at session end (OS also cleans `/tmp`, but this keeps runs tidy):

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "rm -f /tmp/playwright-test-*.js 2>/dev/null; true",
        "description": "Remove temporary Playwright test scripts at session end"
      }
    ]
  }
}
```

## Ordering

Recommended order:
1. enforce /tmp (PreToolUse guard)
2. verify Playwright installed
3. lint scripts
4. cleanup at session end
