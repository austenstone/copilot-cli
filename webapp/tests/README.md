# Color Palette Generator - Test Suite

Comprehensive test suite for the Color Palette Generator web application.

## Running Tests

### Browser Tests (Recommended)

Open `test.html` in a web browser:

```bash
# From the webapp/tests directory
open test.html  # macOS
xdg-open test.html  # Linux
start test.html  # Windows
```

Or use a local server:

```bash
# From the webapp directory
python -m http.server 8000
# Then navigate to http://localhost:8000/tests/test.html
```

The tests will run automatically when the page loads. You can also click the "Run All Tests" button to re-run them.

## Test Coverage

### 1. Color Generation (3 tests)
- ✓ Validates hex color format generation
- ✓ Ensures randomness in color generation
- ✓ Validates color name generation

### 2. Palette Generation (4 tests)
- ✓ Confirms 5 colors are generated
- ✓ Validates palette structure (hex + name)
- ✓ Checks DOM element creation
- ✓ Verifies hex code display formatting

### 3. Color Locking (3 tests)
- ✓ Tests lock toggle functionality
- ✓ Validates unlock functionality
- ✓ Ensures locked colors persist across regenerations

### 4. Palette Storage (4 tests)
- ✓ Tests saving to localStorage
- ✓ Validates saved palette structure
- ✓ Tests loading saved palettes
- ✓ Verifies clear functionality

### 5. UI Interactions (4 tests)
- ✓ Copy indicator visibility
- ✓ Saved palettes display/hide logic
- ✓ Lock icon state changes
- ✓ UI element updates

### 6. Hex Color Validation (2 tests)
- ✓ Validates hex color range (0x000000 to 0xFFFFFF)
- ✓ Ensures proper padding of hex values

## Test Framework

The test suite uses a custom lightweight test framework that includes:

- **Test Runner**: Organizes and executes test suites
- **Assertions**: Comprehensive assertion library
- **Async Support**: Handles asynchronous operations
- **Visual Feedback**: Real-time test results with pass/fail indicators
- **Summary Statistics**: Overview of test execution

## Assertions Available

```javascript
assert.equals(actual, expected, message)
assert.true(value, message)
assert.false(value, message)
assert.exists(value, message)
assert.isType(value, type, message)
assert.matches(value, regex, message)
assert.greaterThan(actual, expected, message)
assert.arrayLength(array, length, message)
assert.includes(container, value, message)
```

## Test Structure

Each test suite follows this pattern:

```javascript
runner.suite('Suite Name', ({ test }) => {
    test('test description', async () => {
        // Test implementation
        const appWindow = await loadApp();
        // Assertions
        assert.equals(actual, expected);
    });
});
```

## Key Features Tested

1. **Core Functionality**
   - Random color generation
   - Palette initialization
   - Color locking mechanism

2. **Persistence**
   - LocalStorage integration
   - Save/load operations
   - Clear functionality

3. **User Interface**
   - DOM manipulation
   - Event handling
   - Visual feedback

4. **Data Validation**
   - Hex color format
   - Color range validation
   - Palette structure

## Expected Results

All tests should pass when the Color Palette Generator is functioning correctly. The test summary will show:

- Total tests: 20
- All tests should pass (green)
- Duration: typically under 500ms

## Troubleshooting

### Tests Not Running

1. Ensure you're accessing `test.html` via a web server or `file://` protocol
2. Check browser console for any errors
3. Verify `../index.html` path is correct relative to test file

### Test Failures

If tests fail, check:

1. The main `index.html` file has all required functions
2. localStorage is enabled in your browser
3. The app initializes correctly in an iframe

### Cross-Browser Testing

This test suite has been designed to work in:
- Chrome/Edge (latest)
- Firefox (latest)
- Safari (latest)

## Continuous Integration

To integrate these tests into a CI/CD pipeline, consider using:

- **Playwright**: For headless browser testing
- **Puppeteer**: For Chrome-based automated testing
- **Selenium**: For multi-browser testing

Example Playwright test runner:

```javascript
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  await page.goto('file://path/to/tests/test.html');
  await page.waitForTimeout(2000);
  const failed = await page.locator('#failed-tests').textContent();
  await browser.close();
  process.exit(parseInt(failed) === 0 ? 0 : 1);
})();
```

## Future Enhancements

Potential additions to the test suite:

- [ ] Clipboard functionality tests
- [ ] Keyboard shortcut tests (SPACE key)
- [ ] Animation timing tests
- [ ] Accessibility tests
- [ ] Performance benchmarks
- [ ] Visual regression tests
- [ ] Edge case handling (invalid colors, storage limits)

## Contributing

When adding new features to the Color Palette Generator:

1. Add corresponding tests to this suite
2. Ensure all existing tests still pass
3. Update this README with new test coverage
4. Follow the existing test structure and naming conventions
