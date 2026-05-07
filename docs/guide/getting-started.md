# Getting Started

Start using Boris AI with these basic examples.

## Hello World

Your first program with Boris AI:

```python
from boris_ai import BorisAI

# Initialize the client
client = BorisAI()

# Generate text
response = client.generate("Hello, how are you?")
print(response.text)
```

## Sentiment Analysis

Analyze the sentiment of a text:

```python
sentiment = client.analyze_sentiment("I love this product!")
print(f"Sentiment: {sentiment.label}")
print(f"Confidence: {sentiment.score}")
```

## Text Classification

Classify text into predefined categories:

```python
categories = ["positive", "negative", "neutral"]
result = client.classify("The service was excellent", categories)
print(f"Category: {result.category}")
```

## Translation

Translate text between languages:

```python
translation = client.translate(
    text="Hello, how are you?",
    source_lang="en",
    target_lang="es"
)
print(f"Translation: {translation.text}")
```

## Batch Processing

Process multiple texts efficiently:

```python
texts = [
    "Text 1 to process",
    "Text 2 to process",
    "Text 3 to process"
]

results = client.batch_process(texts)
for i, result in enumerate(results):
    print(f"Text {i+1}: {result}")
```

!!! tip "Best Practices"
    - Use batch processing for multiple texts
    - Handle exceptions appropriately
    - Cache results when possible

## Next Step

Explore the [API Reference](../documentation/api.md) to see all available functionalities.
