# Installation

This guide will help you install Boris AI on your system.

## System Requirements

- **Java**: 21 or higher (OpenJDK or Oracle JDK)
- **Operating System**: Windows 10+, macOS 10.14+, Ubuntu 18.04+
- **RAM Memory**: Minimum 4GB, recommended 8GB
- **Disk Space**: 1GB of free space

## Installation

### Documentation Setup

To set up the Boris AI documentation locally:

```bash
# Clone the repository
git clone https://github.com/librixsoft/boris-docs.git
cd boris-docs

# Install documentation dependencies
./venv/bin/pip install -r requirements.txt

# Start local documentation server
./venv/bin/mkdocs serve
```

!!! tip "Access Documentation"
    Open your browser and navigate to http://127.0.0.1:8000 to view the documentation locally.

## Installation Verification

To verify the documentation setup was successful:

```bash
# Check if MkDocs is working
./venv/bin/mkdocs --version

# Build the documentation
./venv/bin/mkdocs build
```

!!! success "Successful Installation"
    If the documentation builds without errors and you can access it locally, the setup was successful!

## Common Issues

### Error: "Java not found"

Make sure you have Java 21+ installed and in your system PATH:
```bash
java -version
javac -version
```

### Error: "Permission denied"

On macOS/Linux, make sure the virtual environment has proper permissions:
```bash
chmod +x ./venv/bin/*
```

### Error: "MkDocs not found"

Install the dependencies:
```bash
./venv/bin/pip install -r requirements.txt
```

## Next Step

Once installed, continue with [Configuration](configuration.md).
