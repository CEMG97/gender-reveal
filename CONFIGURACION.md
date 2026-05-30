# 💙💗 Revelación de Género — Guía de configuración

Tu sitio ya funciona en **Modo demostración** sin configurar nada (los votos se
guardan solo en tu navegador). Para que **toda la familia comparta los votos EN VIVO**,
conecta Firebase gratis siguiendo estos pasos.

---

## 1. Crear un proyecto Firebase (gratis)

1. Entra a <https://console.firebase.google.com>
2. **Agregar proyecto** → ponle un nombre (ej. `revelacion-bebe`) → completa el asistente.
   Puedes desactivar Google Analytics.

## 2. Activar Firestore

1. Menú izquierdo: **Compilación → Firestore Database → Crear base de datos**.
2. Elige **modo producción** y la ubicación más cercana a tu familia.
3. Ve a la pestaña **Reglas** y pega **exactamente** el contenido de `firestore.rules`
   (también está abajo). Pulsa **Publicar**.

## 3. Crear los documentos iniciales

En **Firestore → Datos**, crea a mano la colección `reveal` con dos documentos:

| Colección | ID del documento | Campo | Tipo | Valor |
|-----------|------------------|-------|------|-------|
| `reveal`  | `state`          | `revealed` | boolean | `false` |
| `reveal`  | `secret`         | `gender`   | string  | `"boy"` o `"girl"` ← **tu secreto** |

> ⚠️ El documento `reveal/secret` guarda el género real. Gracias a las reglas,
> **nadie puede leerlo** hasta que pongas `reveal/state.revealed = true`.
> La colección `votes` se crea sola con el primer voto.

## 4. Pegar tus credenciales

1. Consola → ⚙️ **Configuración del proyecto → Tus apps →** icono Web `</>`.
2. Registra una app web y copia el objeto `firebaseConfig`.
3. En `index.html`, busca `const FIREBASE_CONFIG = {...}` (arriba del todo en el script)
   y reemplaza los valores `"TU_..."` por los tuyos.

> ✅ Estas claves **son públicas a propósito** y es seguro publicarlas en GitHub.
> Lo que protege tus datos son las **reglas** de Firestore, no las claves.

## 5. Publicar en GitHub Pages

1. Crea un repositorio y sube `index.html` (las otras dos archivos son solo para ti).
2. Repo → **Settings → Pages → Branch: `main` / root → Save**.
3. Comparte el enlace `https://TU_USUARIO.github.io/TU_REPO/` con la familia. 🎈

## 6. El día del evento — revelar

El género real **nunca** viaja al navegador de la familia hasta que tú lo dispares.
Dos formas:

- **Opción A (recomendada):** abre el sitio con tu enlace secreto de anfitrión:
  `https://TU_USUARIO.github.io/TU_REPO/?host=miclave-2026`
  (cambia `miclave-2026` por el valor de `HOST_SECRET_KEY` en `index.html`).
  Aparecerá un panel con el botón **💥 Revelar ahora**. Al pulsarlo, todos verán
  la animación y el género al instante.

- **Opción B:** cambia tú mismo `reveal/state.revealed` a `true` desde la consola
  de Firestore. El efecto es el mismo.

> La cuenta regresiva **nunca** revela sola al llegar a cero. Solo se revela cuando
> tú lo dispares. 🎉

---

## Reglas de seguridad (pegar en Firestore → Reglas)

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    match /votes/{voteId} {
      allow read: if true;
      allow create: if request.resource.data.keys().hasOnly(['name', 'team', 'ts'])
                    && request.resource.data.name is string
                    && request.resource.data.name.size() > 0
                    && request.resource.data.name.size() <= 60
                    && request.resource.data.team in ['boy', 'girl'];
      allow update, delete: if false;
    }

    match /reveal/state {
      allow read: if true;
      allow update: if request.resource.data.diff(resource.data).affectedKeys().hasOnly(['revealed'])
                    && request.resource.data.revealed == true;
      allow create, delete: if false;
    }

    match /reveal/secret {
      allow read: if get(/databases/$(database)/documents/reveal/state).data.revealed == true;
      allow write: if false;
    }

    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```
