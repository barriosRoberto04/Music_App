# Arquitectura de Datos y Plan de Integración de Hive

Este documento detalla el diseño de almacenamiento local, los modelos de datos y la integración de **Hive** como base de datos local embebida para la aplicación `music_app`.

---

## 1. ¿Cómo funciona Hive en nuestro proyecto?

**Hive** es una base de datos NoSQL basada en un modelo de **Clave-Valor** rápida y ligera escrita en Dart. 

### Conceptos Clave:
* **Boxes (Cajas)**: Son el equivalente a las tablas en bases de datos relacionales o colecciones en MongoDB. Todo dato en Hive se guarda dentro de una "caja".
* **Adapters (Adaptadores)**: Son clases autogeneradas o escritas a mano que le enseñan a Hive cómo convertir un objeto de Dart personalizado (como nuestra clase `Song` o `Playlist`) a un formato binario para guardarlo en el disco del celular, y viceversa.

---

## 2. Plan de Cajas (Boxes) Requeridas

Para gestionar la biblioteca musical de forma offline y mantener el consumo de recursos al mínimo, utilizaremos las siguientes cajas:

### A. `songs_box` (Caja de Canciones Descargadas)
Guarda la ficha técnica y la ruta física de cada canción descargada en el dispositivo.
* **Clave (Key)**: `String` (El ID del vídeo de YouTube, garantizando unicidad).
* **Valor**: Objeto de tipo `LocalSong`.

### B. `playlists_box` (Caja de Listas de Reproducción)
Guarda las listas de reproducción personalizadas creadas por el usuario.
* **Clave (Key)**: `String` (ID de la playlist generado aleatoriamente).
* **Valor**: Objeto de tipo `LocalPlaylist`.

### C. `favorites_box` (Caja de Favoritos)
Almacena una lista de los IDs de las canciones marcadas como favoritas por el usuario.
* **Clave (Key)**: `String` ("favorites_list").
* **Valor**: `List<String>` (Lista con los IDs de las canciones marcadas como favoritas).

### D. `settings_box` (Configuración Local)
Guarda las preferencias del sistema y el estado de la sesión para evitar llamadas constantes a Firebase.
* **Claves comunes**:
  * `"theme_mode"`: `String` (`'dark'` o `'light'`).
  * `"user_session"`: `Map` (Nombre de usuario, nombre, apellido y ruta local de la foto de perfil en caché).

---

## 3. Especificación de Modelos de Datos (Dart)

Definiremos los modelos con soporte nativo para ser guardados en Hive:

### Modelo: `LocalSong`
Representa un archivo de música almacenado físicamente en el dispositivo.

```dart
class LocalSong {
  final String id;                // ID único (ej. ID de YouTube)
  final String title;             // Título de la canción
  final String artist;            // Nombre del artista
  final String album;             // Nombre del álbum (opcional)
  final String localFilePath;     // Ruta absoluta del archivo .mp3 en el celular
  final String localCoverPath;    // Ruta absoluta de la carátula (.jpg/.png) en el celular
  final int durationInSeconds;    // Duración de la canción
  final DateTime downloadedAt;    // Fecha de descarga (para ordenar por "Recientes")

  LocalSong({
    required this.id,
    required this.title,
    required this.artist,
    required this.album,
    required this.localFilePath,
    required this.localCoverPath,
    required this.durationInSeconds,
    required this.downloadedAt,
  });
}
```

### Modelo: `LocalPlaylist`
Representa una lista de reproducción creada por el usuario.
*(Utiliza una relación por IDs para evitar duplicar archivos pesados en memoria).*

```dart
class LocalPlaylist {
  final String id;                // ID único de la playlist
  final String name;              // Nombre de la playlist (ej. "Canciones para entrenar")
  final List<String> songIds;     // Lista de IDs de canciones (hacen referencia a la songs_box)
  final DateTime createdAt;       // Fecha de creación

  LocalPlaylist({
    required this.id,
    required this.name,
    required this.songIds,
    required this.createdAt,
  });
}
```

---

## 4. Flujo de Trabajo para la Inicialización de Hive

Cuando inicies la programación de la app en Flutter, el flujo de inicialización en tu archivo `lib/main.dart` deberá seguir los siguientes pasos:

1. **Inicializar Hive** con soporte para Flutter en la raíz del proyecto:
   ```dart
   await Hive.initFlutter();
   ```
2. **Registrar los Adaptadores** para que Hive entienda nuestras clases personalizadas:
   ```dart
   Hive.registerAdapter(LocalSongAdapter());
   Hive.registerAdapter(LocalPlaylistAdapter());
   ```
3. **Abrir las Cajas (Boxes)** para que queden listas para lectura/escritura:
   ```dart
   await Hive.openBox<LocalSong>('songs_box');
   await Hive.openBox<LocalPlaylist>('playlists_box');
   await Hive.openBox<List<String>>('favorites_box');
   await Hive.openBox('settings_box');
   ```

---

## 5. Ventajas de esta estructura
* **Eficiencia extrema**: Al dar click en una Playlist, el sistema lee los IDs de las canciones en milisegundos y busca sus fichas técnicas en la `songs_box` instantáneamente.
* **Sincronización fácil**: Si en el futuro quieres sincronizar tus favoritos en la nube (Firebase), solo necesitas leer la lista de IDs de la caja `favorites_box` y subirla a tu cuenta.
