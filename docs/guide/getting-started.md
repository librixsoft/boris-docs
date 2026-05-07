# Primeros Pasos

Comienza a usar Boris AI con estos ejemplos básicos.

## Hola Mundo

Tu primer programa con Boris AI:

```python
from boris_ai import BorisAI

# Inicializar el cliente
client = BorisAI()

# Generar texto
response = client.generate("Hola, ¿cómo estás?")
print(response.text)
```

## Análisis de Sentimiento

Analiza el sentimiento de un texto:

```python
sentiment = client.analyze_sentiment("Me encanta este producto!")
print(f"Sentimiento: {sentiment.label}")
print(f"Confianza: {sentiment.score}")
```

## Clasificación de Texto

Clasifica texto en categorías predefinidas:

```python
categories = ["positivo", "negativo", "neutro"]
result = client.classify("El servicio fue excelente", categories)
print(f"Categoría: {result.category}")
```

## Traducción

Traduce texto entre idiomas:

```python
translation = client.translate(
    text="Hello, how are you?",
    source_lang="en",
    target_lang="es"
)
print(f"Traducción: {translation.text}")
```

## Procesamiento por Lotes

Procesa múltiples textos eficientemente:

```python
texts = [
    "Texto 1 para procesar",
    "Texto 2 para procesar",
    "Texto 3 para procesar"
]

results = client.batch_process(texts)
for i, result in enumerate(results):
    print(f"Texto {i+1}: {result}")
```

!!! tip "Mejores Prácticas"
    - Usa procesamiento por lotes para múltiples textos
    - Maneja excepciones apropiadamente
    - Cachea resultados cuando sea posible

## Siguiente Paso

Explora la [API Reference](../documentation/api.md) para ver todas las funcionalidades disponibles.
