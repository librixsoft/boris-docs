# Referencia

Referencia completa de conceptos y configuraciones avanzadas.

## Conceptos Clave

### Tokens

Los tokens son la unidad básica que Boris AI utiliza para procesar texto:

- **1 token ≈ 4 caracteres** en español
- **100 tokens ≈ 75 palabras**
- **Límite por request**: 4096 tokens

### Modelos Disponibles

| Modelo | Descripción | Casos de Uso | Límites |
|--------|-------------|--------------|---------|
| `default` | Modelo balanceado | Uso general | 2048 tokens |
| `advanced` | Mayor capacidad | Tareas complejas | 4096 tokens |
| `fast` | Optimizado para velocidad | Respuestas rápidas | 1024 tokens |
| `custom` | Entrenado personalmente | Dominio específico | Variable |

### Parámetros de Generación

#### Temperature

Controla la creatividad del modelo:
- **0.0-0.3**: Respuestas predecibles y factuales
- **0.4-0.7**: Balance entre creatividad y coherencia
- **0.8-1.0**: Máxima creatividad (menos predecible)

#### Top P

Muestreo de núcleo:
- **0.1-0.5**: Muy conservador
- **0.6-0.9**: Balanceado
- **0.95-1.0**: Muy creativo

#### Frequency Penalty

Reduce la repetición:
- **0.0**: Sin penalización
- **0.5**: Penalización moderada
- **1.0**: Máxima penalización

## Configuración Avanzada

### Archivo de Configuración Completo

```yaml
# boris-config.yaml
boris_ai:
  modelo: "advanced"
  idioma: "es"
  max_tokens: 2048
  temperature: 0.7
  top_p: 0.9
  frequency_penalty: 0.5
  
api:
  host: "api.boris-ai.com"
  puerto: 443
  ssl: true
  timeout: 30
  retry_attempts: 3
  
cache:
  enabled: true
  ttl: 3600
  max_size: "100MB"
  
logging:
  nivel: "INFO"
  archivo: "boris.log"
  formato: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
  
monitoring:
  metrics_enabled: true
  trace_requests: true
  performance_tracking: true
```

### Variables de Entorno

| Variable | Descripción | Valor por Defecto |
|----------|-------------|-------------------|
| `BORIS_API_KEY` | Clave de API | None |
| `BORIS_MODEL` | Modelo a usar | `default` |
| `BORIS_BASE_URL` | URL base de API | `https://api.boris-ai.com` |
| `BORIS_TIMEOUT` | Timeout en segundos | `30` |
| `BORIS_LOG_LEVEL` | Nivel de log | `INFO` |
| `BORIS_CACHE_DIR` | Directorio de cache | `~/.boris/cache` |

## Patrones de Diseño

### Cliente Singleton

```python
class BorisAIClient:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.client = BorisAI()
        return cls._instance
```

### Decorador de Cache

```python
import functools
import hashlib

def cache_response(ttl=3600):
    def decorator(func):
        cache = {}
        
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            key = hashlib.md5(str(args + tuple(kwargs.items())).encode()).hexdigest()
            
            if key in cache:
                result, timestamp = cache[key]
                if time.time() - timestamp < ttl:
                    return result
            
            result = func(*args, **kwargs)
            cache[key] = (result, time.time())
            return result
        
        return wrapper
    return decorator
```

### Manejo de Errores

```python
from boris_ai.exceptions import BorisAIError
import time
from functools import wraps

def retry_on_error(max_retries=3, delay=1):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_retries):
                try:
                    return func(*args, **kwargs)
                except BorisAIError as e:
                    if attempt == max_retries - 1:
                        raise
                    time.sleep(delay * (2 ** attempt))
            return None
        return wrapper
    return decorator
```

## Métricas y Monitoreo

### Métricas Disponibles

- **Latencia**: Tiempo de respuesta
- **Throughput**: Requests por segundo
- **Error Rate**: Tasa de errores
- **Token Usage**: Consumo de tokens
- **Cache Hit Rate**: Eficiencia de cache

### Integración con Prometheus

```python
from prometheus_client import Counter, Histogram, Gauge

# Métricas
REQUEST_COUNT = Counter('boris_requests_total', 'Total requests', ['method', 'endpoint'])
REQUEST_LATENCY = Histogram('boris_request_duration_seconds', 'Request latency')
TOKEN_USAGE = Counter('boris_tokens_used_total', 'Total tokens used')
CACHE_HITS = Gauge('boris_cache_hits', 'Cache hit rate')

@REQUEST_LATENCY.time()
def make_request(prompt):
    REQUEST_COUNT.labels(method='generate', endpoint='/generate').inc()
    # ... lógica de request
```

## Optimización de Rendimiento

### Batch Processing

```python
def batch_generate(prompts, batch_size=10):
    """Procesa múltiples prompts eficientemente"""
    results = []
    for i in range(0, len(prompts), batch_size):
        batch = prompts[i:i + batch_size]
        batch_results = client.batch_process(batch)
        results.extend(batch_results)
    return results
```

### Streaming

```python
def stream_generate(prompt):
    """Genera texto en streaming"""
    for chunk in client.generate_stream(prompt):
        yield chunk.text
        # Procesar chunk inmediatamente
```

## Seguridad

### API Key Management

```python
import os
from cryptography.fernet import Fernet

def encrypt_api_key(api_key):
    key = Fernet.generate_key()
    f = Fernet(key)
    encrypted_key = f.encrypt(api_key.encode())
    return encrypted_key, key

def decrypt_api_key(encrypted_key, key):
    f = Fernet(key)
    return f.decrypt(encrypted_key).decode()
```

### Validación de Entrada

```python
import re

def validate_prompt(prompt):
    """Valida que el prompt sea seguro"""
    # Longitud máxima
    if len(prompt) > 10000:
        raise ValueError("Prompt demasiado largo")
    
    # Contenido malicioso
    if re.search(r'<script|javascript:', prompt, re.IGNORECASE):
        raise ValueError("Contenido no permitido")
    
    return True
```

## Troubleshooting

### Problemas Comunes

#### Timeouts
```python
# Aumentar timeout
client = BorisAI(timeout=60)
```

#### Rate Limiting
```python
import time
import random

def rate_limited_request(prompt):
    time.sleep(random.uniform(0.1, 0.5))  # Random delay
    return client.generate(prompt)
```

#### Memory Issues
```python
# Procesamiento en chunks
def process_large_text(text, chunk_size=1000):
    results = []
    for i in range(0, len(text), chunk_size):
        chunk = text[i:i + chunk_size]
        result = client.process(chunk)
        results.append(result)
        del chunk  # Liberar memoria
    return results
```

Esta referencia te ayudará a sacar el máximo provecho de Boris AI en tus proyectos.
