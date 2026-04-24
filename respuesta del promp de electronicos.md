Para llevar a cabo el proyecto **“proyectoelectronicos”** bajo la metodología de agentes globales (SKILL.md), estructuraremos el entorno para que sea escalable y funcional. Utilizaremos Dart y Flutter con Firebase Firestore como persistencia.

---

## 1. Estructura de Habilidad Agente Global (.agents)

Creamos la raíz del proyecto y la documentación de habilidades para que el agente sepa cómo operar.

**Archivo: `SKILL.md`**
```markdown
# Skill: Agente Global de Electrónicos
**Propósito:** Automatización de flujos para el CRUD de inventario de cómputo.
**Capacidades:**
- `skill-diseño`: Generación de UI responsiva para Veterinaria/Electrónicos.
- `skill-codigo`: Implementación de lógica Dart y Firebase.
- `skill-scraping`: Obtención de metadatos de componentes (opcional).
```

**Estructura de Carpetas:**
* `/scripts`: Automatizaciones de despliegue.
* `/ejemplos`: Plantillas de documentos Firestore.
* `/resources`: Assets, iconos y temas.

---

## 2. Prerrequisitos y Configuración del Entorno

Si no tienes las herramientas listas, ejecuta estos comandos en tu terminal (VS Code o AntiGravity):

### Verificación e Instalación
1.  **Flutter:** `flutter --version` (Si no está, descarga el SDK de flutter.dev).
2.  **Firebase CLI:** `npm install -g firebase-tools`
3.  **FlutterFire CLI:** `dart pub global activate flutterfire_cli`
4.  **Login:** `firebase login`

### Conectividad con Console Firebase
1.  Ve a [Firebase Console](https://console.firebase.google.com/).
2.  Crea el proyecto "proyectoelectronicos".
3.  Habilita **Cloud Firestore** en modo de prueba.
4.  Desde la terminal en tu carpeta raíz:
    ```bash
    flutterfire configure
    ```

---

## 3. Configuración del Proyecto (`pubspec.yaml`)

Asegúrate de incluir las dependencias necesarias:

```yaml
name: proyectoelectronicos
description: Sistema de gestión para veterinaria y electrónica.
publish_to: 'none'
version: 1.0.0+1

environment:
  sdk: '>=3.0.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.x.x
  cloud_firestore: ^4.x.x
  cupertino_icons: ^1.0.2

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^2.0.0
```

---

## 4. Desarrollo del CRUD de Computadoras

Implementaremos la lógica en una arquitectura limpia dentro de `lib/`.

### A. Modelo de Datos (`lib/models/computer_model.dart`)
```dart
class Computer {
  String id;
  String marca;
  String modelo;
  double precio;

  Computer({required this.id, required this.marca, required this.modelo, required this.precio});

  Map<String, dynamic> toMap() => {
    "marca": marca,
    "modelo": modelo,
    "precio": precio,
  };

  factory Computer.fromFirestore(Map<String, dynamic> firestore, String id) {
    return Computer(
      id: id,
      marca: firestore['marca'],
      modelo: firestore['modelo'],
      precio: firestore['precio'].toDouble(),
    );
  }
}
```

### B. Pantalla Principal y UI (`lib/main.dart`)
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'firebase_options.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
  runApp(MaterialApp(home: ComputerList()));
}

class ComputerList extends StatelessWidget {
  final CollectionReference _computers = FirebaseFirestore.instance.collection('computadoras');

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Inventario Electrónicos"), backgroundColor: Colors.blueGrey),
      body: StreamBuilder(
        stream: _computers.snapshots(),
        builder: (context, AsyncSnapshot<QuerySnapshot> snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          return ListView(
            children: snapshot.data!.docs.map((doc) {
              return ListTile(
                title: Text(doc['marca']),
                subtitle: Text(doc['modelo']),
                trailing: IconButton(
                  icon: Icon(Icons.delete, color: Colors.red),
                  onPressed: () => _computers.doc(doc.id).delete(),
                ),
                onTap: () => _showForm(context, doc),
              );
            }).toList(),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => _showForm(context, null),
      ),
    );
  }

  // Lógica para Crear y Actualizar
  void _showForm(BuildContext context, DocumentSnapshot? doc) {
    final marcaController = TextEditingController(text: doc != null ? doc['marca'] : "");
    final modeloController = TextEditingController(text: doc != null ? doc['modelo'] : "");

    showModalBottomSheet(
      context: context,
      builder: (_) => Padding(
        padding: EdgeInsets.all(20),
        child: Column(
          children: [
            TextField(controller: marcaController, decoration: InputDecoration(labelText: 'Marca')),
            TextField(controller: modeloController, decoration: InputDecoration(labelText: 'Modelo')),
            SizedBox(height: 20),
            ElevatedButton(
              child: Text(doc == null ? "Crear" : "Actualizar"),
              onPressed: () {
                if (doc == null) {
                  _computers.add({'marca': marcaController.text, 'modelo': modeloController.text, 'precio': 0.0});
                } else {
                  _computers.doc(doc.id).update({'marca': marcaController.text, 'modelo': modeloController.text});
                }
                Navigator.pop(context);
              },
            )
          ],
        ),
      ),
    );
  }
}
```

---

## 5. Verificación de Funcionalidad

1.  **Prueba de Terminal:** Ejecuta `flutter doctor` para asegurar que el emulador de Android o dispositivo físico esté listo.
2.  **Ejecución:** Presiona `F5` en **VS Code** o usa `flutter run`.
3.  **Sincronización:** Abre **Firebase Console -> Firestore** y observa cómo se crean los documentos en tiempo real al usar el botón "+" en la app.

Este flujo de trabajo garantiza que tu **Agente Global** tenga una base sólida (SKILL.md) y que el proyecto **proyectoelectronicos** sea funcional desde el primer despliegue.
