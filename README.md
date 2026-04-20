# Minería de Repositorios: Análisis de SBOMs y Vulnerabilidades

Este repositorio contiene un conjunto de herramientas y notebooks diseñados para automatizar la recolección, generación de listas de materiales de software (SBOM) y el escaneo de vulnerabilidades de múltiples repositorios de una organización en GitHub (por defecto `openclaw`).

## 📋 Resumen del Proyecto

El objetivo principal de este proyecto es proveer un análisis exhaustivo y cuantitativo sobre las dependencias que utilizan los proyectos de una organización, así como detectar posibles vulnerabilidades (CVEs) introducidas a través de paquetes de terceros (NPM, PyPI, etc.). 

El flujo de trabajo automatizado realiza lo siguiente:
1. **Descubrimiento y Descarga:** Consulta la API de GitHub para identificar repositorios activos y descarga su código fuente en un directorio local.
2. **Generación de SBOM:** Utiliza [Syft](https://github.com/anchore/syft) (ejecutado mediante Docker) para analizar el código fuente y extraer un inventario detallado de todas las dependencias.
3. **Escaneo de Vulnerabilidades:** Pasa los SBOMs generados por [Grype](https://github.com/anchore/grype) (ejecutado mediante Docker) para contrastar los paquetes con bases de datos de vulnerabilidades conocidas.
4. **Análisis Cuantitativo y Visualización:** Procesa los resultados en formato JSON mediante Pandas, generando gráficos detallados utilizando Matplotlib y Seaborn.

## 🚀 Paso a Paso (Cómo reproducir el análisis)

### Prerrequisitos

* **Python 3.8+**
* **Docker:** El demonio de Docker debe estar en ejecución, ya que Syft y Grype se ejecutan como contenedores efímeros para evitar instalar herramientas adicionales en el sistema anfitrión.
* **Jupyter Notebook** (para abrir y ejecutar los análisis visuales).

Instala las dependencias de Python necesarias:
```bash
pip install requests pandas matplotlib seaborn jupyter
```

### Ejecución del Análisis

El repositorio se divide en dos notebooks principales:

#### 1. Extracción y Escaneo (`analisis_completo.ipynb`)
Este notebook realiza el trabajo pesado. Solo necesitas abrirlo y ejecutar las celdas en orden. 
El notebook se encargará de:
* Crear las carpetas de caché `data/repos/`, `data/sboms/` y `data/vulnerabilidades/`.
* Descargar el código fuente de los repositorios de la organización.
* Ejecutar los contenedores Docker de Syft para generar los archivos `.json` en `data/sboms/`.
* Ejecutar los contenedores Docker de Grype para generar los reportes de vulnerabilidades `.json` en `data/vulnerabilidades/`.

#### 2. Visualización de Resultados (`visualizacion_resultados.ipynb`)
Una vez completado el primer notebook (o si ya dispones de los datos en caché dentro de la carpeta `data/`), puedes usar este segundo notebook.
* Al ejecutarlo, cargará los archivos JSON y mostrará los gráficos de manera interactiva.
* Proporcionará un resumen de qué ecosistemas (lenguajes/gestores de paquetes) son los más utilizados.
* Destacará qué repositorios tienen la mayor cantidad de vulnerabilidades, categorizadas por su nivel de gravedad (Critical, High, Medium, Low).
* Mostrará un "Top 10" de las dependencias que introducen la mayor cantidad de riesgos.

## 📂 Estructura de Directorios

* `analisis_completo.ipynb`: Notebook para orquestar la descarga, Syft y Grype.
* `visualizacion_resultados.ipynb`: Notebook de visualización gráfica con Pandas y Seaborn.
* `.gitignore`: Configurado para ignorar la carpeta `data/` y no subir repositorios pesados ni reportes masivos a control de versiones.
* `data/`: *(Generada dinámicamente)*
  * `repos/`: Contiene el código fuente descargado en archivos comprimidos y carpetas.
  * `sboms/`: Archivos JSON con el resultado de Syft por cada repositorio.
  * `vulnerabilidades/`: Archivos JSON con el resultado de Grype por cada repositorio.
