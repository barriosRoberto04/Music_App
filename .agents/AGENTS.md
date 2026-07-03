# AGENTS.md - Reglas Globales y Contexto del Proyecto `music_app`

Este archivo define el contexto del proyecto, la pila tecnológica y las reglas de comportamiento que todos los agentes de codificación de IA (Antigravity, Claude Code, Cursor, Copilotos) deben seguir estrictamente.

---

## 1. Visión y Objetivos del Proyecto
* **Nombre del Proyecto**: `music_app`
* **Propósito**: Reproductor de música y podcasts móvil de alta fidelidad, de uso personal (por ahora), con diseño premium interactivo inspirado en los mejores estándares visuales (reproductor expansivo, seek bar fluida, transiciones dinámicas).
* **Funcionalidades Clave**:
  1. Autenticación con Firebase Auth (Google Sign-In + Email/Contraseña).
  2. Búsqueda y streaming en tiempo real de pistas multimedia.
  3. Descarga física de audios en formato MP3 de alta calidad con metadatos y carátula integrados.
  4. Respaldo y sincronización automática en la nube del usuario utilizando la carpeta oculta `appDataFolder` de **Google Drive API** con su misma sesión de Google.
  5. Base de datos local ultra-rápida con **Hive** para guardar configuraciones, favoritos, playlists y archivos locales descargados offline.

---

## 2. Stack Tecnológico Principal
* **Frontend/Mobile**: Flutter & Dart.
* **Estado e Inyección de Dependencias**: `flutter_riverpod` (Riverpod) como gestor de estado exclusivo.
* **Base de Datos Local**: Hive (`songs_box`, `playlists_box`, `favorites_box`, `settings_box`).
* **Backend & Servicios en la Nube**:
  * Firebase Auth (Autenticación).
  * Cloud Firestore (Validación de nombres de usuario únicos y sincronización ligera).
  * Google Drive API (Almacenamiento en la nube personal del usuario).
* **Servicios de Backend / Scripts Auxiliares**: Python (para scripts de descarga de YouTube y extracción de audios MP3).

---

## 3. Arquitectura del Proyecto (Clean Architecture)
El proyecto se organiza bajo una estructura **Feature-First** (por funcionalidad) utilizando tres capas bien desacopladas en cada feature:

```
lib/features/[feature_name]/
├── data/                  # Fuentes de datos (locales/remotas), modelos y repositorios (DTOs)
│   ├── datasources/
│   ├── models/
│   └── repositories/
├── domain/                # Lógica pura de negocio. Cero frameworks.
│   ├── entities/
│   ├── repositories/      # Interfaces de repositorios
│   └── usecases/
└── presentation/          # Vistas (widgets) y gestión de estados
    ├── providers/         # Providers de Riverpod (StateNotifier o AsyncNotifier)
    ├── screens/
    └── widgets/
```

---

## 4. Roles de IA Asignados (Pair-Programming)
Al interactuar con el código de este repositorio, puedes asumir o solicitar el apoyo de los siguientes perfiles técnicos:

1. **Lead Flutter Architect**: Responsable de mantener la pureza de las capas de Clean Architecture y la óptima gestión del estado global mediante Riverpod.
2. **UI/UX Specialist**: Encargado de construir interfaces fluidas y visualmente deslumbrantes. Diseña la vista del reproductor con animaciones de transición, controles interactivos tipo slide y soporte para temas.
3. **Backend & Cloud Specialist**: Experto en la integración de Firebase API, autenticación de Google y consumo seguro de la API de Google Drive.
4. **QA & Security Auditor**: Diseña pruebas unitarias aisladas con `mocktail` y garantiza que la información sensitiva (tokens de OAuth, claves del dispositivo) se guarde de forma segura en `flutter_secure_storage`.

---

## 5. Directrices de Seguridad y Comportamiento Innegociables
* **Cero Secretos**: Nunca guardes llaves de API, credenciales de Firebase, o secretos de OAuth directamente en el código. Utiliza variables de entorno o `--dart-define`.
* **Seguridad en Credenciales**: Los tokens de acceso de Google Sign-in y Firebase deben almacenarse localmente con `flutter_secure_storage`, nunca con `shared_preferences` común.
* **Control de Permisos**: Antes de ejecutar cualquier comando del sistema potencialmente peligroso o realizar cambios estructurales profundos, debes pedir permiso explícito al usuario.
* **Validación Rigurosa**: Implementa validación en todos los formularios y respuestas de APIs externas. Las entradas de datos deben estar sanitizadas.
* **Código Limpio y DRY**: No dupliques lógica de negocio. Si una función es de utilidad compartida, extráela a un módulo `core/utils/` o `core/network/`.

*Nota: Para detalles específicos sobre nomenclatura y guías de codificación, consulta la regla personalizada en [.agent/rules/code-style.md](file:///c:/Users/rbarr/Desktop/music_app/.agent/rules/code-style.md).*
