# Installation

This guide will help you install Boris AI on your system.

## System Requirements

- **Python**: 3.8 or higher
- **Operating System**: Windows 10+, macOS 10.14+, Ubuntu 18.04+
- **RAM Memory**: Minimum 4GB, recommended 8GB
- **Disk Space**: 1GB of free space

## Installation with pip

The easiest way to install Boris AI is using pip:

```bash
pip install boris-ai
```

!!! tip "Virtual Environment"
    We recommend using a virtual environment to avoid conflicts with other packages:
    ```bash
    python -m venv boris-env
    source boris-env/bin/activate  # On Windows: boris-env\Scripts\activate
    pip install boris-ai
    ```

## Installation Verification

To verify the installation was successful:

```python
import boris_ai
print(boris_ai.__version__)
```

!!! success "Successful Installation"
    If you see the version number without errors, the installation was successful!

## Common Issues

### Error: "Python not found"

Make sure you have Python 3.8+ installed and in your system PATH.

### Error: "Permission denied"

On macOS/Linux, use `pip install --user boris-ai` or run with sudo.

### Error: "Incompatible version"

Update pip: `pip install --upgrade pip`

## Next Step

Once installed, continue with [Configuration](configuration.md).
