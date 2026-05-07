# Instalación

Esta guía te ayudará a instalar Boris AI en tu sistema.

## Requisitos del Sistema

- **Python**: 3.8 o superior
- **Sistema Operativo**: Windows 10+, macOS 10.14+, Ubuntu 18.04+
- **Memoria RAM**: Mínimo 4GB, recomendado 8GB
- **Espacio en Disco**: 1GB de espacio libre

## Instalación con pip

La forma más sencilla de instalar Boris AI es usando pip:

```bash
pip install boris-ai
```

!!! tip "Entorno Virtual"
    Se recomienda usar un entorno virtual para evitar conflictos con otros paquetes:
    ```bash
    python -m venv boris-env
    source boris-env/bin/activate  # En Windows: boris-env\Scripts\activate
    pip install boris-ai
    ```

## Verificación de Instalación

Para verificar que la instalación fue exitosa:

```python
import boris_ai
print(boris_ai.__version__)
```

!!! success "Instalación Exitosa"
    Si ves el número de versión sin errores, ¡la instalación fue exitosa!

## Problemas Comunes

### Error: "Python no encontrado"

Asegúrate de tener Python 3.8+ instalado y en el PATH del sistema.

### Error: "Permiso denegado"

En macOS/Linux, usa `pip install --user boris-ai` o ejecuta con sudo.

### Error: "Versión incompatible"

Actualiza pip: `pip install --upgrade pip`

## Siguiente Paso

Una vez instalado, continúa con la [Configuración](configuration.md).
