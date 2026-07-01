# Music App - Proyecto de Reproducción y Gestión de Audio 🎵

Documentación técnica y especificación de requerimientos para el desarrollo de la aplicación móvil de música personalizada (Flutter/Dart).

---

## 1. Módulo de Autenticación y Gestión de Usuarios

Este módulo implementa el flujo de registro y acceso seguro de usuarios, adaptando la lógica del sistema de biblioteca previamente desarrollado en Kotlin.

* **Métodos de Autenticación**:
  * Registro y login tradicional mediante correo electrónico y contraseña.
  * Autenticación de terceros mediante **Google Sign-In**.
* **Campos del Registro**:
  * Nombre y Apellido.
  * **Nombre de Usuario Único (Username)**: El usuario define su propio identificador. Durante el ingreso del texto en el formulario de registro, el sistema ejecutará una consulta y validación en tiempo real para verificar la disponibilidad y asegurar que sea único en la base de datos.
* **Gestión de Perfil**:
  * Carga y actualización de foto de perfil (restringido únicamente a la imagen de avatar del usuario).
  * Panel de actualización de datos personales (cambio de correo, restablecimiento de contraseña y edición de perfil).

---

## 2. Buscador y Módulo de Descarga (YouTube Integration)

Módulo encargado de la obtención y procesamiento de archivos multimedia utilizando la plataforma YouTube como origen de contenido personal.

* **Búsqueda en Tiempo Real**:
  * Motor de búsqueda integrado en la interfaz que realiza consultas directas y actualizadas de contenido multimedia.
* **Extractor de Audio local (MP3)**:
  * Funcionalidad para procesar y descargar vídeos (contenido propio del usuario) extrayendo únicamente la pista de audio en formato **MP3 de alta calidad**.
  * Asociación automática de los metadatos y la miniatura del vídeo como imagen de carátula/álbum del archivo descargado.

---

## 3. Motor de Audio y Biblioteca Musical

Desarrollo del reproductor multimedia local y herramientas de organización de la biblioteca.

* **Calidad de Audio**:
  * Optimización del decodificador de audio para soportar reproducción de alta fidelidad (alta tasa de bits).
* **Controles del Reproductor (UX/UI)**:
  * Controles estándar: Play, Pausa, Siguiente (Next) y Anterior (Prev).
  * **Interactive Seek (Deslizamiento)**: Barra de progreso del tiempo de reproducción interactiva, permitiendo al usuario avanzar o retroceder el segundo exacto deslizando el dedo de forma fluida.
* **Organización del Contenido**:
  * Sistema de creación y edición de **Playlists** (Listas de reproducción).
  * Carpeta/Lista inteligente de **Favoritos** para acceso rápido.

---

## 4. Diseño y UX/UI (Pendiente de definición)

* La paleta de colores oficial, los patrones visuales y el manual de estilo general se definirán y adaptarán en etapas posteriores del desarrollo.
