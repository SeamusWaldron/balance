# Balance Game - Puppeteer Test Suite

This directory contains automated browser testing scripts for the Balance game using Puppeteer.

## Quick Start

### Prerequisites
```bash
npm install puppeteer
```

### Run Tests
```bash
# Basic observation with screenshots
node test-balance-game.js

# Detailed with debug panel extraction
node test-balance-detailed.js

# Canvas pixel analysis
node test-balance-canvas.js

# Comprehensive report
node test-final-report.js
```

## Test Scripts

| Script | Purpose | Output |
|--------|---------|--------|
| `test-balance-game.js` | Basic screenshot capture | 5 PNG screenshots |
| `test-balance-detailed.js` | Extract debug DOM values | Debug metrics + 5 PNG |
| `test-balance-canvas.js` | Analyze canvas pixels | Color analysis + 5 PNG |
| `test-final-report.js` | Comprehensive testing | Full report + 5 PNG |

## Key Findings

- **Balance Score:** Remains constant at 0.00
- **Visual Changes:** Zero pixel variance across 30 seconds
- **Drift:** No movement detected
- **Timer:** Shows 0.0s (not advancing)
- **Conclusion:** Game displays static content in idle state

## Documentation

- `TEST-REPORT.md` - Detailed test findings and analysis
- `TESTING-SUMMARY.md` - Executive summary and recommendations
- `README.md` - This file

## Screenshots

All screenshots are 600x600 canvas captures:
- `frame-at-0s.png` - Initial state
- `frame-at-10s.png` - After 10 seconds (identical)
- `frame-at-20s.png` - After 20 seconds (identical)
- `frame-at-30s.png` - After 30 seconds (identical)
- `debug-panel-visible.png` - Debug panel enabled

## Test Execution

Each test takes approximately 35-40 seconds to run (30 second observation + overhead):
```bash
time node test-final-report.js
# Output: real 0m40s, user 0m5s, sys 0m2s
```

## Browser Automation Best Practices Used

✓ Proper async/await patterns
✓ Viewport configuration for consistency
✓ Timeout management
✓ Resource cleanup (browser.close())
✓ Error handling with failure screenshots
✓ Promise-based timing instead of deprecated waitForTimeout()

## Findings

The game renders correctly but appears to be in a static state. See TEST-REPORT.md for detailed analysis and recommendations for further investigation.

---
Created: 2026-01-25
Status: Complete
