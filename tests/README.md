# Testing Guide for Image Archive Viewer

This directory contains the test suite for the image archive viewer application.

## Test Structure

### Automated Tests (Safe to run in CI/headless)
- `test_basic.py` - Basic package imports and fixture validation
- `test_archive_reader.py` - Archive reading functionality (ZIP/RAR handling)
- `test_logging_setup.py` - Logging configuration tests
- `test_viewer_logic.py` - Business logic tests (zoom, pan calculations)

### Manual Tests (Require display/GUI)
- `test_viewer.py` - Qt GUI widget tests (marked as skipped by default)

## Running Tests

### Run All Safe Tests
```bash
pytest
```

### Run with Coverage Report
```bash
pytest --cov-report=html
```

### Run Only Specific Test Categories
```bash
# Run only fast tests (excludes GUI)
pytest -m "not gui"

# Run only slow tests
pytest -m slow

# Run GUI tests manually (requires display)
pytest -m gui --no-cov
```

### Run Specific Test Files
```bash
# Test archive reading functionality
pytest tests/test_archive_reader.py

# Test logging setup
pytest tests/test_logging_setup.py

# Test basic functionality
pytest tests/test_basic.py
```

## Test Fixtures

Test fixtures are automatically created in `tests/fixtures/`:
- `test_archive.zip` - ZIP with sample PNG/JPG images
- `empty_archive.zip` - Empty ZIP archive
- `no_images_archive.zip` - ZIP with non-image files only

## Coverage Reports

After running tests with coverage, reports are generated in:
- `htmlcov/index.html` - Interactive HTML coverage report
- Terminal output shows coverage summary
- `coverage.xml` - XML format for CI systems

## Manual Testing

For GUI functionality that cannot be easily automated:

1. **Application Startup**
   ```bash
   show_images
   ```
   - Test file dialog opens
   - Select a ZIP/RAR archive
   - Verify fullscreen slideshow starts

2. **Keyboard Controls**
   - Arrow keys for navigation
   - +/- for zoom
   - WASD for panning
   - H for help overlay
   - O for opening new file
   - Q/Escape to quit

3. **Mouse Controls**
   - Mouse wheel for zooming
   - Click and drag for panning when zoomed
   - Click to dismiss startup overlay

4. **Error Handling**
   - Try opening corrupted archives
   - Test with archives containing no images
   - Test RAR files without `unrar` installed

## Test Development

### Adding New Tests

1. **For Pure Logic**: Add to existing test files or create new ones
2. **For Qt GUI**: Add to `test_viewer.py` with `@pytest.mark.gui`
3. **For Integration**: Create fixtures in `conftest.py`

### Test Fixtures

Use existing fixtures from `conftest.py`:
- `test_archive_zip` - Sample ZIP with images
- `empty_archive_zip` - Empty archive
- `temp_zip_archive` - Dynamically created archive
- `caplog_with_propagation` - For testing log output

### Mocking Guidelines

- Mock Qt components for non-GUI logic tests
- Use `@pytest.mark.skip` for tests requiring display
- Mock external dependencies (file system, network)
- Prefer testing logic over GUI interactions

## Continuous Integration

Safe for CI environments:
```bash
# Install test dependencies
pip install -e ".[test]"

# Run automated tests only
pytest -m "not gui" --cov=src/image_archive_viewer --cov-report=xml
```

## Troubleshooting

### Qt Display Errors
If you see "Fatal Python error: Aborted" or Qt display errors:
- Run `pytest -m "not gui"` to skip GUI tests
- Ensure you have a display available for GUI tests
- Check that Qt is properly installed

### Missing Test Dependencies
```bash
# Install missing dependencies
pip install pytest pytest-qt pytest-cov

# Or install full test suite
pip install -e ".[test]"
```

### RAR File Tests
RAR/CBR tests may fail if `unrar` is not installed:
```bash
# macOS
brew install unrar

# Ubuntu/Debian
sudo apt-get install unrar

# Or skip RAR tests
pytest -k "not rar"
```