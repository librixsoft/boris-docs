# Configuración

Configura Boris AI según tus necesidades específicas.

## Archivo de Configuración

Boris AI utiliza un archivo `config.yaml` para la configuración principal:

```yaml
# config.yaml
boris_ai:
  modelo: "default"
  idioma: "es"
  max_tokens: 2048
  temperatura: 0.7
  
api:
  host: "localhost"
  puerto: 8000
  ssl: false
  
logging:
  nivel: "INFO"
  archivo: "boris.log"
```

## Variables de Entorno

También puedes configurar Boris AI usando variables de entorno:

```bash
export BORIS_AI_MODEL="advanced"
export BORIS_API_KEY="tu-api-key"
export BORIS_LOG_LEVEL="DEBUG"
```

## Configuración Avanzada

### Modelos Disponibles

- **default**: Modelo estándar para uso general
- **advanced**: Modelo con mayor capacidad
- **fast**: Modelo optimizado para velocidad
- **custom**: Modelo personalizado

### Opciones de Rendimiento

```yaml
rendimiento:
  hilos: 4
  memoria_cache: "2GB"
  batch_size: 32
```

## Validación de Configuración

Usa el comando de validación:

```bash
boris-cli validate-config
```

!!! warning "Configuración Inválida"
    Si la configuración es inválida, Boris AI mostrará errores específicos para corregirlos.

## Siguiente Paso

Una vez configurado, revisa los [Primeros Pasos](getting-started.md).
