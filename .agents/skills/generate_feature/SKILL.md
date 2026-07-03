---
name: generate_feature
description: Genera la estructura de carpetas y código base (boilerplate) para una nueva característica (feature) siguiendo Clean Architecture y Riverpod en Flutter.
---

# Habilidad: Generador de Características (Clean Architecture)

Esta habilidad permite al agente de IA generar de forma automatizada y coherente toda la estructura de directorios y los archivos boilerplate iniciales para una nueva funcionalidad del proyecto, asegurando el cumplimiento de las guías de Clean Architecture y estilo de código.

## Cuándo Activar esta Habilidad
* Se debe activar cuando el usuario solicita crear una nueva funcionalidad o módulo (ej. "crea la pantalla de autenticación", "genera la feature de favoritos", "inicializa la sección del reproductor").

## Pasos para la Ejecución

### Paso 1: Confirmar y Validar el Nombre
1. Pregunta al usuario el nombre del feature (si no lo especificó).
2. Asegura que el nombre sea en inglés, corto, técnico y en formato `snake_case` (ej. `auth`, `player`, `media_download`, `playlists`).

### Paso 2: Crear el Árbol de Directorios
Crea la siguiente estructura de carpetas bajo `lib/features/[feature_name]/`:

* `lib/features/[feature_name]/domain/entities/`
* `lib/features/[feature_name]/domain/repositories/`
* `lib/features/[feature_name]/domain/usecases/`
* `lib/features/[feature_name]/data/models/`
* `lib/features/[feature_name]/data/datasources/`
* `lib/features/[feature_name]/data/repositories/`
* `lib/features/[feature_name]/presentation/providers/`
* `lib/features/[feature_name]/presentation/screens/`
* `lib/features/[feature_name]/presentation/widgets/`

### Paso 3: Generar Archivos Iniciales (Boilerplate)

Genera los archivos básicos para conectar las capas. Utiliza nombres de archivos cortos en snake_case y clases en PascalCase.

#### 1. Entidad del Dominio (Domain Entity)
`lib/features/[feature_name]/domain/entities/[feature_name]_entity.dart`
```dart
class [FeatureName]Entity {
  final String id;
  // TODO: Agregar campos de negocio propios de [feature_name]

  const [FeatureName]Entity({
    required this.id,
  });
}
```

#### 2. Interfaz de Repositorio (Domain Repository Interface)
`lib/features/[feature_name]/domain/repositories/[feature_name]_repository.dart`
```dart
import '../entities/[feature_name]_entity.dart';

abstract class [FeatureName]Repository {
  Future<List<[FeatureName]Entity>> getData();
}
```

#### 3. Modelo de Datos (Data Model / DTO)
`lib/features/[feature_name]/data/models/[feature_name]_model.dart`
```dart
import '../../domain/entities/[feature_name]_entity.dart';

class [FeatureName]Model extends [FeatureName]Entity {
  const [FeatureName]Model({
    required super.id,
  });

  factory [FeatureName]Model.fromJson(Map<String, dynamic> json) {
    return [FeatureName]Model(
      id: json['id'] as String,
    );
  }

  Map<String, dynamic> toJson() {
    return {
      'id': id,
    };
  }
}
```

#### 4. Fuente de Datos (Data Source)
`lib/features/[feature_name]/data/datasources/[feature_name]_local_datasource.dart`
```dart
import '../models/[feature_name]_model.dart';

abstract class [FeatureName]LocalDataSource {
  Future<List<[FeatureName]Model>> getCachedData();
}

class [FeatureName]LocalDataSourceImpl implements [FeatureName]LocalDataSource {
  // Hive u otra base local aquí
  @override
  Future<List<[FeatureName]Model>> getCachedData() async {
    // TODO: Implementar lógica de acceso a datos locales
    return [];
  }
}
```

#### 5. Implementación del Repositorio (Data Repository Implementation)
`lib/features/[feature_name]/data/repositories/[feature_name]_repository_impl.dart`
```dart
import '../../domain/repositories/[feature_name]_repository.dart';
import '../../domain/entities/[feature_name]_entity.dart';
import '../datasources/[feature_name]_local_datasource.dart';

class [FeatureName]RepositoryImpl implements [FeatureName]Repository {
  final [FeatureName]LocalDataSource localDataSource;

  [FeatureName]RepositoryImpl({required this.localDataSource});

  @override
  Future<List<[FeatureName]Entity>> getData() async {
    return await localDataSource.getCachedData();
  }
}
```

#### 6. Proveedor de Estado (Presentation Provider - Riverpod)
`lib/features/[feature_name]/presentation/providers/[feature_name]_provider.dart`
```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../../domain/entities/[feature_name]_entity.dart';

class [FeatureName]Notifier extends StateNotifier<AsyncValue<List<[FeatureName]Entity>>> {
  [FeatureName]Notifier() : super(const AsyncValue.loading()) {
    _init();
  }

  Future<void> _init() async {
    try {
      // TODO: Cargar datos iniciales
      state = const AsyncValue.data([]);
    } catch (e, st) {
      state = AsyncValue.error(e, st);
    }
  }
}

final [featureName]Provider = StateNotifierProvider<[FeatureName]Notifier, AsyncValue<List<[FeatureName]Entity>>>((ref) {
  return [FeatureName]Notifier();
});
```

#### 7. Pantalla del Módulo (Presentation Screen Widget)
`lib/features/[feature_name]/presentation/screens/[feature_name]_screen.dart`
```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../providers/[feature_name]_provider.dart';

class [FeatureName]Screen extends ConsumerWidget {
  const [FeatureName]Screen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final state = ref.watch([featureName]Provider);

    return Scaffold(
      appBar: AppBar(
        title: const Text('[FeatureName]'),
      ),
      body: state.when(
        data: (items) => const Center(child: Text('[FeatureName] Loaded')),
        error: (err, stack) => Center(child: Text('Error: $err')),
        loading: () => const Center(child: CircularProgressIndicator()),
      ),
    );
  }
}
```

### Paso 4: Reportar la Finalización al Usuario
Una vez creadas las carpetas y archivos, genera un listado en formato markdown con los enlaces a los archivos para que el usuario pueda explorarlos y editarlos directamente.
Ejemplo: "Se ha inicializado con éxito el feature `[feature_name]`. Archivos creados: [lib/features/...](file:///lib/features/...)"
