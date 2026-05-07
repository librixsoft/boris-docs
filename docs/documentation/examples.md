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
        
        prompt = "\n".join(self.context[-5:]) + "\nAsistente:"
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

## Análisis de Reseñas

```python
import pandas as pd
from boris_ai import BorisAI

def analyze_reviews(csv_file):
    client = BorisAI()
    df = pd.read_csv(csv_file)
    
    results = []
    for review in df['review_text']:
        sentiment = client.analyze_sentiment(review)
        results.append({
            'review': review,
            'sentiment': sentiment.label,
            'score': sentiment.score,
            'confidence': sentiment.confidence
        })
    
    return pd.DataFrame(results)

# Uso
results = analyze_reviews('reviews.csv')
print(results.head())
```

## Generación de Contenido

```python
from boris_ai import BorisAI

def generate_blog_post(topic, word_count=500):
    client = BorisAI()
    
    prompt = f"""
    Escribe un artículo de blog sobre "{topic}".
    El artículo debe tener aproximadamente {word_count} palabras.
    Incluye una introducción, 3-4 puntos principales y una conclusión.
    Usa un tono informativo pero accesible.
    """
    
    response = client.generate(prompt, max_tokens=word_count * 2)
    return response.text

# Uso
article = generate_blog_post("Inteligencia Artificial en la Medicina")
print(article)
```

## Sistema de Traducción Automática

```python
from boris_ai import BorisAI
import os

class TranslationService:
    def __init__(self):
        self.client = BorisAI()
        self.supported_languages = {
            'es': 'Español',
            'en': 'Inglés',
            'fr': 'Francés',
            'de': 'Alemán',
            'it': 'Italiano',
            'pt': 'Portugués'
        }
    
    def translate_file(self, input_file, output_file, source_lang, target_lang):
        with open(input_file, 'r', encoding='utf-8') as f:
            content = f.read()
        
        translated = self.client.translate(content, source_lang, target_lang)
        
        with open(output_file, 'w', encoding='utf-8') as f:
            f.write(translated.text)
        
        return translated.confidence

# Uso
translator = TranslationService()
confidence = translator.translate_file(
    'document_es.txt', 
    'document_en.txt', 
    'es', 
    'en'
)
print(f"Traducción completada con confianza: {confidence}")
```

## Clasificación de Correos Electrónicos

```python
from boris_ai import BorisAI
import re

class EmailClassifier:
    def __init__(self):
        self.client = BorisAI()
        self.categories = [
            'trabajo', 'personal', 'spam', 'promociones', 
            'facturas', 'notificaciones'
        ]
    
    def preprocess_email(self, email_text):
        # Extraer asunto y cuerpo
        subject_match = re.search(r'Asunto: (.+)', email_text)
        subject = subject_match.group(1) if subject_match else ""
        
        # Limpiar texto
        cleaned = re.sub(r'[^\w\s]', '', email_text.lower())
        return f"Asunto: {subject}\n{cleaned}"
    
    def classify_email(self, email_text):
        processed = self.preprocess_email(email_text)
        result = self.client.classify(processed, self.categories)
        
        return {
            'category': result.category,
            'confidence': result.confidence,
            'all_scores': result.scores
        }

# Uso
classifier = EmailClassifier()
email = """
Asunto: Reunión importante
Hola equipo,
Quería confirmar la reunión de mañana a las 10am.
Por favor traigan sus reportes.
Saludos,
Juan
"""

classification = classifier.classify_email(email)
print(f"Categoría: {classification['category']}")
print(f"Confianza: {classification['confidence']}")
```

## Resumen de Documentos

```python
from boris_ai import BorisAI

def summarize_document(text, max_sentences=3):
    client = BorisAI()
    
    prompt = f"""
    Resume el siguiente texto en {max_sentences} oraciones clave:
    
    {text}
    
    Resumen:
    """
    
    response = client.generate(prompt, temperature=0.3)
    return response.text.strip()

# Uso
long_text = """
[Texto largo de múltiples párrafos...]
"""

summary = summarize_document(long_text)
print(f"Resumen: {summary}")
```

## Generación de Código

```python
from boris_ai import BorisAI

def generate_code(description, language="python"):
    client = BorisAI()
    
    prompt = f"""
    Genera código {language} para la siguiente descripción:
    
    {description}
    
    El código debe ser limpio, comentado y seguir buenas prácticas.
    """
    
    response = client.generate(prompt, temperature=0.2)
    return response.text

# Uso
code = generate_code("Una función que calcula el factorial de un número")
print(code)
```

!!! tip "Optimización"
    Para procesamiento de grandes volúmenes de datos, considera usar el método `batch_process()` para mejorar el rendimiento.

!!! warning "Costos"
    Algunas operaciones pueden consumir muchos tokens. Monitorea tu uso y considera implementar cacheo para resultados frecuentes.
