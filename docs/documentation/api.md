# API Reference

Documentación completa de la API de Boris AI.

## Cliente Principal

### BorisAI

```python
class BorisAI:
    def __init__(self, config=None):
        """Inicializa el cliente de Boris AI"""
        pass
```

#### Parámetros

- **config** (dict, opcional): Configuración personalizada

#### Métodos

### generate()

Genera texto basado en un prompt.

```python
def generate(self, prompt, max_tokens=2048, temperature=0.7):
    """Genera texto"""
    pass
```

**Parámetros:**
- `prompt` (str): Texto de entrada
- `max_tokens` (int): Máximo número de tokens a generar
- `temperature` (float): Creatividad del modelo (0.0-1.0)

**Retorna:** `GenerateResponse`

### analyze_sentiment()

Analiza el sentimiento de un texto.

```python
def analyze_sentiment(self, text):
    """Analiza sentimiento"""
    pass
```

**Parámetros:**
- `text` (str): Texto a analizar

**Retorna:** `SentimentResponse`

### classify()

Clasifica texto en categorías.

```python
def classify(self, text, categories):
    """Clasifica texto"""
    pass
```

**Parámetros:**
- `text` (str): Texto a clasificar
- `categories` (list): Lista de categorías posibles

**Retorna:** `ClassificationResponse`

### translate()

Traduce texto entre idiomas.

```python
def translate(self, text, source_lang, target_lang):
    """Traduce texto"""
    pass
```

**Parámetros:**
- `text` (str): Texto a traducir
- `source_lang` (str): Código del idioma origen
- `target_lang` (str): Código del idioma destino

**Retorna:** `TranslationResponse`

## Objetos de Respuesta

### GenerateResponse

```python
class GenerateResponse:
    def __init__(self, text, tokens_used, model):
        self.text = text
        self.tokens_used = tokens_used
        self.model = model
```

### SentimentResponse

```python
class SentimentResponse:
    def __init__(self, label, score, confidence):
        self.label = label  # 'positive', 'negative', 'neutral'
        self.score = score  # -1.0 a 1.0
        self.confidence = confidence  # 0.0 a 1.0
```

### ClassificationResponse

```python
class ClassificationResponse:
    def __init__(self, category, confidence, scores):
        self.category = category
        self.confidence = confidence
        self.scores = scores  # Dict con scores por categoría
```

### TranslationResponse

```python
class TranslationResponse:
    def __init__(self, text, source_lang, target_lang, confidence):
        self.text = text
        self.source_lang = source_lang
        self.target_lang = target_lang
        self.confidence = confidence
```

## Manejo de Errores

```python
from boris_ai.exceptions import BorisAIError, APIError, ValidationError

try:
    response = client.generate("Hola mundo")
except APIError as e:
    print(f"Error de API: {e}")
except ValidationError as e:
    print(f"Error de validación: {e}")
except BorisAIError as e:
    print(f"Error general: {e}")
```

## Límites y Cuotas

- **Máximo tokens por request**: 4096
- **Requests por minuto**: 60
- **Tamaño máximo de texto**: 1MB

## Ejemplos Complejos

Ver [Ejemplos](examples.md) para casos de uso avanzados.
