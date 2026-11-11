# Pesux

Control de gastos personales — "Donde está cada peso."

Aplicación de una sola página (`index.html`), sin build ni dependencias de
servidor. Usa Firebase para autenticación (correo/contraseña) y Firestore
como base de datos, con un panel privado por usuario.

## Desplegar

Sube `index.html` tal cual a cualquier hosting estático (el que ya usas,
Netlify, Vercel, GitHub Pages, etc.). No requiere build ni configuración
adicional en el servidor.

## Configuración de Firebase

El proyecto ya está conectado a un proyecto real de Firebase
(`balanceclaro`). El objeto `firebaseConfig` dentro de `index.html` no es
secreto — es seguro que esté en el código del cliente. La protección real
vive en las reglas de seguridad de Firestore.

### Reglas de seguridad (Firestore → pestaña Reglas)

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
      match /records/{month} {
        allow read, write: if request.auth != null && request.auth.uid == uid;
      }
    }
  }
}
```

Esto garantiza que cada usuario solo puede leer/escribir su propio panel.

## Estructura de datos

- `users/{uid}` — subcategorías, favoritos, configuración (meta de ahorro, etc.)
- `users/{uid}/records/{YYYY-MM}` — un documento por mes con los gastos de ese mes
