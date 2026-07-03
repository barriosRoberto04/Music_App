# Code Style & Naming Conventions for `music_app`

This document defines the strict formatting, naming conventions, and structural rules for Dart/Flutter and Python files in the `music_app` codebase. All AI agents and developers must strictly adhere to these rules.

---

## 1. Naming Conventions (Technical English)

All code identifiers (classes, variables, methods, files, assets) must use precise, technical English terms. Avoid ambiguous, generic, or Spanish terms.

### A. File and Folder Naming
* **Dart/Flutter Files**: Must use `snake_case` (all lowercase, words separated by underscores).
  * *Good*: `audio_player_notifier.dart`, `song_repository_impl.dart`, `favorites_screen.dart`
  * *Bad*: `AudioPlayerNotifier.dart`, `song-repository.dart`, `favoritos_page.dart`
* **Python Files**: Must use `snake_case`.
  * *Good*: `youtube_extractor.py`, `metadata_parser.py`

### B. Classes and Type Declarations (Dart & Python)
* **Classes, Mixins, Enums, Extensions**: Must use `PascalCase` (Capitalized camelCase).
  * *Good*: `class MusicPlayerNotifier {}`, `enum PlaybackState {}`
* **Widgets (Flutter)**: Must use `PascalCase`. Specify widget type at the end of the name (e.g., `Screen`, `Widget`, `Button`, `Dialog`).
  * *Good*: `class PlayerControlsWidget {}`, `class LibraryScreen {}`

### C. Variables and Functions (Dart & Python)
* **Dart Variables, Methods, Parameters**: Must use `camelCase`.
  * *Good*: `final String songId;`, `void playNextSong() {}`
* **Python Functions and Local Variables**: Must use `snake_case` (PEP 8 standard).
  * *Good*: `def download_audio_from_youtube(video_url: str):`, `audio_path = "/tmp"`

---

## 2. Flutter & Dart Coding Best Practices

### A. Widget Optimization
* **Always use `const`** constructors for widgets that do not depend on dynamic values. This avoids unnecessary rebuilds and improves UI performance.
* **Avoid monolithic widgets**. Break screens down into small, specialized widgets in a `widgets/` directory under the same feature folder.
* **Memory Management**: Always dispose of `StreamControllers`, `TextEditingControllers`, `ScrollControllers`, and audio controllers (`AudioPlayer` instance) inside the `dispose()` method of State/Stateful widgets, or manage their lifecycle via Riverpod.

### B. Clean Architecture Implementation
* **Domain Layer Isolation**: The domain layer (entities, usecases, repository interfaces) must NOT import `package:flutter/...` or any external package that is not pure Dart (e.g., Hive, Firebase).
* **Data Transfer Objects (DTOs)**: Model classes in the data layer (e.g., `LocalSongModel`, `YoutubeSongDto`) are responsible for serialization (to/from JSON, Hive adapters). They must extend or map to domain entities (e.g., `SongEntity`). Usecases should only return domain entities.

### C. Riverpod State Management
* **State Immutability**: All states managed by Riverpod providers must be immutable. Use `@immutable` decorators or packages like `freezed` if complex.
* **Separation of UI and Logic**: Do not write business logic inside the UI. UI widgets should only call methods on providers or notifier classes.
  * *Good*: `ref.read(playerProvider.notifier).next()`
  * *Bad*: `ref.read(playerProvider).songsList.removeAt(0); setState(() {});`

---

## 3. Python Coding Best Practices (Scripts)

* **PEP 8 Compliance**: Strictly follow PEP 8 standards. Use `black` or `autopep8` formatting.
* **Type Hinting**: Use type hints for all function arguments and return types.
  * *Good*: `def parse_metadata(file_path: str) -> dict[str, str]:`
* **Error Handling**: Always wrap system-level processes (like executing `yt-dlp` or extracting ffmpeg audio) in `try-except` blocks and log descriptive error outputs.
