# Ejemplos

Casos de uso prácticos y ejemplos avanzados de Boris AI.

## Chatbot Simple

```python
from boris_ai import BorisAI

class SimpleChatbot:
    def __init__(self):
        self.client = BorisAI()
        self.context = []
    
    def chat(self, message):
        self.context.append(f"Usuario: {message}")
        
        prompt = "\\n".join(self.context[-5:]) + "\\nAsistente:"
        response = self.client.generate(prompt)
        
        self.context.append(f"Asistente: {response.text}")
        return response.text

# Uso
bot = SimpleChatbot()
while True:
    user_input = input("Tú: ")
    if user_input.lower() == 'salir':
        break
    response = bot.chat(user_input)
    print(f"Bot: {response}")
```

## Análisis de Sentimiento

```python
from boris_ai import BorisAI

def analyze_sentiment(text):
    client = BorisAI()
    sentiment = client.analyze_sentiment(text)
    return {
        'text': text,
        'sentiment': sentiment.label,
        'score': sentiment.score,
        'confidence': sentiment.confidence
    }

# Uso
result = analyze_sentiment("Me encanta este producto!")
print(f"Sentimiento: {result['sentiment']}")
print(f"Confianza: {result['confidence']}")
```

## Generación de Texto

```python
from boris_ai import BorisAI

def generate_text(prompt, max_tokens=100):
    client = BorisAI()
    response = client.generate(prompt, max_tokens=max_tokens)
    return response.text

# Uso
text = generate_text("Escribe un poema sobre la tecnología")
print(text)
```

## Clasificación Simple

```python
from boris_ai import BorisAI

def classify_text(text, categories):
    client = BorisAI()
    result = client.classify(text, categories)
    return {
        'category': result.category,
        'confidence': result.confidence
    }

# Uso
categories = ['positivo', 'negativo', 'neutro']
result = classify_text("El servicio fue excelente", categories)
print(f"Categoría: {result['category']}")
```
