## 1. Problemas de seguridad identificados y solución aplicada

El archivo `requirements.txt` original presentaba varios riesgos de seguridad y mantenimiento:

**Falta de verificación de integridad**  
  Al no incluir hashes, el instalador (`pip`) no podía garantizar que los paquetes descargados no hubieran sido manipulados, ya sea en el repositorio o durante el tránsito.

**Dependencias transitivas no controladas**  
  Únicamente se listaban las dependencias principales. Las sub-dependencias quedaban fuera de control y podían cambiar de versión en cualquier momento, provocando errores inesperados o introduciendo vulnerabilidades sin previo aviso.

**Inconsistencia entre entornos**  
  Diferentes desarrolladores (o entornos) podían acabar utilizando versiones ligeramente distintas de las dependencias, generando comportamientos no reproducibles.

### Solución aplicada

Se ha implementado una estrategia combinada de **Version Pinning** (fijación de versiones exactas) y **Hashing**.

**Version Pinning vs. Hashing**  
  El *pinning* garantiza que siempre se instala la misma versión de un paquete (por ejemplo, `Django==6.0`), mientras que el *hashing* asegura que el contenido descargado de esa versión es exactamente el esperado mediante la validación de su huella digital (`sha256`).

**Lock Files vs. Input Files**  
  Se ha separado el flujo de trabajo en dos tipos de archivos:
  - `requirements.in`: archivo de entrada donde se definen las dependencias deseadas.
  - `requirements.txt`: archivo de bloqueo generado automáticamente, que incluye versiones exactas y hashes, y es el que se utiliza en producción para garantizar instalaciones deterministas y seguras.

---

## 2. Procedimiento de actualización en producción

Para mantener el sistema actualizado sin comprometer la seguridad ni la estabilidad, se recomienda seguir el siguiente flujo:

**Revisión periódica y controlada**  
  No actualizar dependencias de forma automática. Es necesario revisar qué versiones nuevas existen y qué vulnerabilidades o fallos corrigen.

**Uso de un entorno de *staging***  
  Antes de desplegar cambios en producción, las nuevas versiones deben probarse en un entorno idéntico para detectar posibles *breaking changes*.

**Compilación controlada de dependencias**  
  El proceso recomendado es:
  1. Modificar el archivo `requirements.in`.
  2. Generar el nuevo `requirements.txt` con hashes usando `pip-compile`.
  3. Ejecutar los tests automáticos antes del despliegue.

---

## 3. Escenarios que se intentan prevenir

Con esta nueva configuración de dependencias, la aplicación queda protegida frente a los siguientes escenarios:

- **Ataques de sustitución de paquetes**  
  Si un atacante compromete una librería en PyPI, el hash no coincidirá y la instalación fallará.

- **Errores por cambios incompatibles**  
  Se evita que una actualización automática de una sub-dependencia rompa la compatibilidad del código existente.

- **Deriva de configuración**  
  Se garantiza que los entornos de desarrollo, pruebas y producción sean exactamente iguales, reduciendo errores difíciles de reproducir.
