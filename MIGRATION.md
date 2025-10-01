# Migration Guide: Standalone Script to Module

This document describes the migration from the standalone `entropy.py` script to the proper Python module structure.

## What Changed

### Project Structure

**Before:**
```
high_entropy_experiments/
├── entropy.py                    # All code in one file
├── test_entropy.py              # Tests
├── common_words.yaml            # Config files
├── entropy_settings.yaml
└── redaction_patterns.yaml
```

**After:**
```
python-entrophier/
├── src/
│   └── entrophier/              # Module package
│       ├── __init__.py          # Public API
│       ├── __main__.py          # Module entry point
│       ├── cli.py               # CLI interface
│       ├── config.py            # Configuration loading
│       ├── core.py              # Core redaction logic
│       └── *.yaml               # Config files in module
├── tests/
│   └── test_entropy.py          # Tests
└── pyproject.toml               # Project metadata
```

### Import Changes

**Before:**
```python
from entropy import load_config, redact_sensitive_data
```

**After:**
```python
from entrophier import load_config, redact_sensitive_data
```

### Command-Line Changes

**Before:**
```bash
python3 entropy.py input.txt
```

**After:**
```bash
# As installed command
entrophier input.txt

# Or as module
python -m entrophier input.txt

# Or with uv in development
uv run entrophier input.txt
```

### Configuration Loading

**Before:**
Config files had to be in the same directory as `entropy.py`.

**After:**
- Default: Config files are bundled with the module in `src/entrophier/`
- Custom: Use `load_config(config_dir="/path/to/config")` or `--config-dir` CLI option

## Benefits of the Migration

1. **Proper Package Structure**: Can be installed via pip/uv
2. **Cleaner Code**: Separated into logical modules (cli, config, core)
3. **Better Testing**: Tests in dedicated directory, pytest-compatible
4. **Professional Distribution**: Can be published to PyPI
5. **Entry Points**: Provides `entrophier` command when installed
6. **Version Management**: Uses uv for modern Python dependency management
7. **Python 3.13**: Uses latest stable Python version

## Development Workflow

### Installation

```bash
# Clone repository
git clone <repository-url>
cd python-entrophier

# Install with uv (development mode)
uv sync

# Or install in editable mode with pip
pip install -e .
```

### Running Tests

```bash
# Run test script
uv run python tests/test_entropy.py

# Or with pytest
uv run pytest tests/

# With coverage
uv run pytest --cov=entrophier tests/
```

### Building and Distribution

```bash
# Build package
uv build

# This creates:
# - dist/entrophier-0.1.0.tar.gz (source distribution)
# - dist/entrophier-0.1.0-py3-none-any.whl (wheel)
```

### Using in Development

```bash
# Run CLI in development
uv run entrophier input.txt

# Or as module
uv run python -m entrophier input.txt

# Run Python scripts
uv run python your_script.py
```

## Compatibility Notes

- All functionality from the original `entropy.py` is preserved
- Configuration file formats remain unchanged (YAML)
- Default behavior and algorithms are identical
- Added `--config-dir` option for custom config locations

## Module Organization

### `__init__.py`
Exports the public API:
- `redact_sensitive_data()` - Main convenience function
- `redact_high_entropy_tokens()` - Token-based approach
- `redact_high_entropy_strings()` - Sliding window approach
- `calculate_entropy()` - Shannon entropy calculation
- `is_high_entropy_segment()` - Segment analysis
- `load_config()` - Configuration loader

### `config.py`
Configuration management:
- Loads YAML configuration files
- Validates required settings
- Manages global configuration state

### `core.py`
Core redaction logic:
- Entropy calculations
- Pattern detection
- Word preservation
- Redaction algorithms

### `cli.py`
Command-line interface:
- Argument parsing
- File I/O handling
- Output formatting

### `__main__.py`
Module entry point for `python -m entrophier`

## Next Steps

1. Update any scripts that import from `entropy` to use `entrophier`
2. Test the installation in your target environment
3. Update deployment scripts to use `entrophier` command
4. Consider publishing to PyPI for easier distribution

## Questions?

See the updated README.md for complete documentation and examples.
