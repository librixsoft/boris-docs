# Examples

Practical use cases and advanced examples of Boris AI.

## Simple Chatbot

```python
from boris_ai import BorisAI

class SimpleChatbot:
    def __init__(self):
        self.client = BorisAI()
        self.context = []
    
    def chat(self, message):
        self.context.append(f"User: {message}")
        
        prompt = "\n".join(self.context[-5:]) + "\nAssistant:"
        response = self.client.generate(prompt)
        
        self.context.append(f"Assistant: {response.text}")
        return response.text

# Usage
bot = SimpleChatbot()
while True:
    user_input = input("You: ")
    if user_input.lower() == 'exit':
        break
    response = bot.chat(user_input)
    print(f"Bot: {response}")
```

## Sentiment Analysis

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

# Usage
result = analyze_sentiment("I love this product!")
print(f"Sentiment: {result['sentiment']}")
print(f"Confidence: {result['confidence']}")
```

## Text Generation

```python
from boris_ai import BorisAI

def generate_text(prompt, max_tokens=100):
    client = BorisAI()
    response = client.generate(prompt, max_tokens=max_tokens)
    return response.text

# Usage
text = generate_text("Write a poem about technology")
print(text)
```

## Simple Classification

```python
from boris_ai import BorisAI

def classify_text(text, categories):
    client = BorisAI()
    result = client.classify(text, categories)
    return {
        'category': result.category,
        'confidence': result.confidence
    }

# Usage
categories = ['positive', 'negative', 'neutral']
result = classify_text("The service was excellent", categories)
print(f"Category: {result['category']}")
