Para crear esta infraestructura de agentes globales bajo el estándar `.agents`, seguiremos una estructura modular. Este sistema permitirá que tus "skills" (Diseño, Código, Scraping) operen de forma coordinada sobre el proyecto de la agencia de autos.

---

## 1. Estructura de la Habilidad Agente Global: `.agents`

Primero, configuramos el entorno de trabajo del agente para que reconozca sus capacidades y recursos.

### Archivo: `SKILL.md`
Este archivo define la lógica de operación de los agentes.

```markdown
# Global Agent Skill: .agents
## Propósito
Automatizar el desarrollo de una App de Agencia de Carros utilizando Flutter y Firebase.

## Skills Disponibles
- **Skill de Diseño**: Generación de UI/UX siguiendo patrones Material Design 3.
- **Skill de Código**: Implementación de lógica Dart y Clean Architecture.
- **Skill de Scraping**: Extracción de datos de vehículos para poblar la base de datos.

## Flujo de Trabajo
1. Verificación de entorno (Flutter/Firebase CLI).
2. Configuración de Firebase (Auth/Firestore).
3. Generación de estructura de carpetas.
4. Implementación de CRUD y UI.
```

### Estructura de Carpetas del Agente
* `scripts/`: Scripts de automatización (bash/python) para tareas repetitivas.
* `ejemplos/`: Snippets de código de referencia.
* `resources/`: Activos, logos y esquemas de base de datos.

---

## 2. Prerrequisitos y Configuración de Entorno

Antes de programar, debemos asegurar que el "motor" esté encendido. Ejecuta estos comandos en tu terminal de VS Code:

### Verificación e Instalación
```bash
# 1. Verificar Flutter
flutter --version

# 2. Instalar Firebase CLI (si no lo tienes)
curl -sL https://firebase.tools | upgrade=true bash

# 3. Login en Firebase
firebase login

# 4. Activar FlutterFire CLI (Indispensable para conectar con Firestore)
dart pub global activate flutterfire_cli
```

---

## 3. Creación del Proyecto: "proyectoagencia"

Ejecuta los siguientes comandos para inicializar la estructura base:

```bash
flutter create proyectoagencia
cd proyectoagencia
# Configurar Firebase automáticamente
flutterfire configure
```

### Configuración de `pubspec.yaml`
Añade las dependencias necesarias para Firestore y el manejo de estado:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^3.0.0
  cloud_firestore: ^5.0.0
  cupertino_icons: ^1.0.6
```

---

## 4. Desarrollo del Proyecto (Dart/Flutter)

Implementaremos una arquitectura sencilla pero funcional dividida en modelos, servicios y pantallas.

### Estructura de Archivos
* `lib/models/carro_model.dart`
* `lib/services/firebase_service.dart`
* `lib/screens/home_screen.dart`
* `lib/screens/crud_screen.dart`

---

### A. Modelo de Datos (`lib/models/carro_model.dart`)
```dart
class Carro {
  String id;
  String marca;
  String modelo;
  double precio;

  Carro({required this.id, required this.marca, required this.modelo, required this.precio});

  Map<String, dynamic> toMap() {
    return {'marca': marca, 'modelo': modelo, 'precio': precio};
  }

  factory Carro.fromFirestore(Map<String, dynamic> data, String id) {
    return Carro(
      id: id,
      marca: data['marca'] ?? '',
      modelo: data['modelo'] ?? '',
      precio: (data['precio'] ?? 0).toDouble(),
    );
  }
}
```

### B. Servicio Firestore (`lib/services/firebase_service.dart`)
Este es el corazón del CRUD manejado por el **Skill de Código**.

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/carro_model.dart';

class FirebaseService {
  final CollectionReference carrosCollection = FirebaseFirestore.instance.collection('carros');

  // CREATE
  Future<void> addCarro(String marca, String modelo, double precio) {
    return carrosCollection.add({'marca': marca, 'modelo': modelo, 'precio': precio});
  }

  // READ (Stream para tiempo real)
  Stream<List<Carro>> getCarros() {
    return carrosCollection.snapshots().map((snapshot) {
      return snapshot.docs.map((doc) => Carro.fromFirestore(doc.data() as Map<String, dynamic>, doc.id)).toList();
    });
  }

  // UPDATE
  Future<void> updateCarro(String id, String nuevaMarca, String nuevoModelo, double nuevoPrecio) {
    return carrosCollection.doc(id).update({'marca': nuevaMarca, 'modelo': nuevoModelo, 'precio': nuevoPrecio});
  }

  // DELETE
  Future<void> deleteCarro(String id) {
    return carrosCollection.doc(id).delete();
  }
}
```

### C. Interfaz de Usuario UI (`lib/screens/home_screen.dart`)
Diseño limpio gestionado por el **Skill de Diseño**.

```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';
import '../models/carro_model.dart';

class HomeScreen extends StatelessWidget {
  final FirebaseService _service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      app_bar: AppBar(title: Text('Agencia de Carros'), backgroundColor: Colors.blueGrey),
      body: StreamBuilder<List<Carro>>(
        stream: _service.getCarros(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          final carros = snapshot.data!;
          return ListView.builder(
            itemCount: carros.length,
            itemBuilder: (context, index) {
              return ListTile(
                title: Text('${carros[index].marca} ${carros[index].modelo}'),
                subtitle: Text('\$${carros[index].precio}'),
                trailing: Row(
                  mainAxisSize: MainAxisSize.min,
                  children: [
                    IconButton(icon: Icon(Icons.edit), onPressed: () => _mostrarDialogo(context, carro: carros[index])),
                    IconButton(icon: Icon(Icons.delete, color: Colors.red), onPressed: () => _service.deleteCarro(carros[index].id)),
                  ],
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => _mostrarDialogo(context),
      ),
    );
  }

  void _mostrarDialogo(BuildContext context, {Carro? carro}) {
    // Aquí iría el formulario para Crear/Editar (Skill de Diseño implementado)
  }
}
```

---

## 5. Verificación de Herramientas (Checklist Final)

Para asegurar que todo el parque de agentes está funcional, realiza las siguientes pruebas en tu terminal:

1.  **Analizador de código**: `flutter analyze` (Debe salir limpio).
2.  **Prueba de Conexión**: Abre `Firebase Console` > `Firestore` y verifica que al añadir un carro desde la app, aparezca el documento en la nube.
3.  **Ejecución**: 
    * Presiona `F5` en VS Code o utiliza el **IDE Antigravity** para compilar en el simulador.

> **Nota del Agente:** Recuerda que para el **Skill de Scraping**, deberás añadir scripts en la carpeta `/scripts` que utilicen paquetes como `beautiful_soup` o `http` para obtener precios actualizados del mercado y alimentar tu base de datos en Firestore.
