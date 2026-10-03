# Boris AI Documentation

Official documentation for Boris AI — an autonomous terminal-based AI development assistant for local models.

## Development

```bash
# Install dependencies
./venv/bin/pip install -r requirements.txt

# Local development server
./venv/bin/mkdocs serve

# Build for production
./venv/bin/mkdocs build

# Deploy to GitHub Pages
./venv/bin/mkdocs gh-deploy
```

## Documentation Structure

```
docs/
├── index.md                      # Project overview and architecture
├── guide/
│   ├── installation.md           # Build and run from source
│   ├── configuration.md          # settings.json reference
│   ├── tools.md                  # Agent tool reference (10 built-in tools)
│   ├── ui.md                     # Terminal UI layout and controls
│   ├── thinking.md               # Reasoning/thinking system
│   └── commands.md               # In-app commands and shortcuts
└── stylesheets/
    └── extra.css
```

## Deployed Site

https://librixsoft.github.io/boris-docs
