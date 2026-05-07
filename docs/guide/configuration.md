# Configuration

Configure Boris AI according to your specific needs.

## Configuration File

Boris AI uses a `config.yaml` file for main configuration:

```yaml
# config.yaml
boris_ai:
  model: "default"
  language: "en"
  max_tokens: 2048
  temperature: 0.7
  
api:
  host: "localhost"
  port: 8000
  ssl: false
  
logging:
  level: "INFO"
  file: "boris.log"
```

## Environment Variables

You can also configure Boris AI using environment variables:

```bash
export BORIS_AI_MODEL="advanced"
export BORIS_API_KEY="your-api-key"
export BORIS_LOG_LEVEL="DEBUG"
```

## Advanced Configuration

### Available Models

- **default**: Standard model for general use
- **advanced**: Model with greater capacity
- **fast**: Model optimized for speed
- **custom**: Custom trained model

### Performance Options

```yaml
performance:
  threads: 4
  memory_cache: "2GB"
  batch_size: 32
```

## Configuration Validation

Use the validation command:

```bash
boris-cli validate-config
```

!!! warning "Invalid Configuration"
    If configuration is invalid, Boris AI will show specific errors to correct them.

## Next Step

Once configured, check [Getting Started](getting-started.md).
