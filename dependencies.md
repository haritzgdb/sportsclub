# Seguridad en las Dependencias y Vulnerabilidades

## 1. Escaneo en GitHub: Dependabot
Para la gestión de vulnerabilidades dentro del ecosistema de GitHub, se ha activado y configurado **Dependabot**.

* **Configuración:** Se han habilitado las "Dependabot alerts" y las "Dependabot security updates" en la sección de *Security > Code security and analysis* del repositorio.
* **Resultados:** Tras el escaneo del archivo `requirements.txt` mejorado, el panel de seguridad no muestra alertas de vulnerabilidades conocidas. Esto confirma que las versiones fijadas y sus dependencias están libres de fallos reportados en este momento.
* **Funcionamiento:** Dependabot monitoriza el grafo de dependencias de forma continua. En caso de detectarse una vulnerabilidad (CVE) en el futuro, generará automáticamente un Pull Request para actualizar la librería afectada a una versión segura sin intervención manual.

## 2. Alternativas para Nube Privada
En un escenario donde el repositorio esté alojado en una infraestructura privada (fuera de GitHub), no se dispondría de las herramientas nativas de "Security". Para lograr la misma funcionalidad, se proponen las siguientes tres alternativas de análisis de composición de software:

| Herramienta | Ventajas | Desventajas | Comunidad y Respaldo |
| :--- | :--- | :--- | :--- |
| **Trivy** (Aqua Security) | Extremadamente rápido, versátil (escanea archivos, imágenes Docker y secretos) y fácil de automatizar. | El formato de salida por defecto en consola puede ser denso si hay muchos hallos técnicos. | Muy activa, es el estándar de facto para seguridad en entornos nativos de nube. |
| **Snyk CLI** | Base de datos de vulnerabilidades propia muy rica. Ofrece guías precisas de remediación. | La versión gratuita tiene un límite de escaneos mensuales. | Empresa líder en seguridad, comunidad enorme y mucha documentación. |
| **OWASP Dependency-Check** | 100% gratuita y de código abierto (Open Source). | Más lenta, ya que debe descargar bases de datos locales (NVD). Puede dar más falsos positivos. | Proyecto oficial de la fundación OWASP, el referente mundial en seguridad web. |



## 3. Elección, Instalación y Ejecución Local
Para esta práctica, se ha elegido **Trivy** por su capacidad para ejecutarse de forma independiente en cualquier entorno sin depender de servicios externos.

**Proceso realizado en local (Windows 11):**
1. **Instalación:** Se ha utilizado el gestor de paquetes de Windows: `winget install aquasecurity.trivy`.
2. **Ejecución:** Se ha lanzado un escaneo sobre el sistema de archivos del proyecto con el comando: `trivy.exe fs .`.
3. **Actualización:** La herramienta descargó automáticamente la base de datos de vulnerabilidades más reciente antes del análisis.

**Resultados obtenidos:**
El análisis confirma que las medidas de seguridad aplicadas al `requirements.txt` (pinning y hashing) son efectivas:

```text
Report Summary
┌──────────────────┬────────────┬─────────────────┬─────────┐
│      Target      │    Type    │ Vulnerabilities │ Secrets │
├──────────────────┼────────────┼─────────────────┼─────────┤
│ requirements.txt │    pip     │        0        │    -    │
└──────────────────┴────────────┴─────────────────┴─────────┘
Legend:
- '0': Clean (no security findings detected)