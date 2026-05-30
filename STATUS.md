# 📌 STATUS — Sitio de Revelación de Género

> Documento de contexto para retomar el proyecto y hacer cambios en el futuro.
> Última actualización: **29 de mayo de 2026**.

---

## 1. Estado actual

- ✅ **El sitio está EN LÍNEA y en MODO EN VIVO** (Firebase conectado).
- ✅ La familia está **votando en tiempo real** (los votos se comparten entre todos).
- ✅ Cuenta regresiva configurada para el **7 de junio de 2026, 12:00pm (mediodía)**, hora local.
- ⏳ **Pendiente antes del evento:** poner el género real en Firestore (`reveal/secret.gender` = `boy` o `girl`). Ver sección 6.

---

## 2. Enlaces importantes

| Qué | Enlace |
|---|---|
| **Sitio público** (compartir con la familia) | https://cemg97.github.io/gender-reveal/ |
| **Enlace de anfitrión** (solo para ti, muestra el botón de revelar) | https://cemg97.github.io/gender-reveal/?host=miclave-2026 |
| **Repositorio en GitHub** | https://github.com/CEMG97/gender-reveal |
| **Consola de Firebase** | https://console.firebase.google.com/project/gender-reveal-b80b0 |

---

## 3. Dónde está todo (en tu computadora)

- **Carpeta del proyecto / repositorio git:**
  `C:\Users\miran\OneDrive\Escritorio\Projects\Gender-Reveal\gender-reveal\project`
- **Archivo principal (todo el sitio está aquí):** `index.html`
- Otros archivos: `CONFIGURACION.md` (guía original), `firestore.rules` (copia de las reglas), `screenshots/` (imágenes), este `STATUS.md`.

> El repositorio git vive **dentro de la carpeta `project`**, porque GitHub Pages sirve el `index.html` desde la raíz del repo.

---

## 4. Cómo hacer cambios y publicarlos

1. Edita `index.html` (con VS Code o el editor que uses).
2. Abre una terminal **dentro de la carpeta `project`** y ejecuta:
   ```
   git add .
   git commit -m "describe tu cambio"
   git push
   ```
3. En 1-2 minutos GitHub Pages se actualiza solo. Recarga el sitio con `Ctrl + F5` para ver la versión nueva.

---

## 5. Valores que puedes editar en `index.html`

Todos están al inicio del `<script type="module">`, en la sección **"⚙️ CONFIGURACIÓN — EDITA AQUÍ"** (alrededor de la línea 510):

| Qué cambia | Variable | Valor actual |
|---|---|---|
| Claves de Firebase | `FIREBASE_CONFIG` | proyecto `gender-reveal-b80b0` (no tocar salvo que cambies de proyecto Firebase) |
| Fecha/hora a la que llega a cero la cuenta | `REVEAL_START_DATETIME` | `"2026-06-07T12:00:00"` |
| Duración extra de la cuenta | `COUNTDOWN_HOURS` | `0` (= la cuenta termina justo en la fecha de arriba) |
| Nombre si es niño | `BOY_NAME` | `"Fabián Augusto"` |
| Nombre si es niña | `GIRL_NAME` | `"Isabella Carolina"` |
| Clave secreta de anfitrión | `HOST_SECRET_KEY` | `"miclave-2026"` |

> **Para cambiar la fecha de la cuenta regresiva:** edita `REVEAL_START_DATETIME` con el formato `"AAAA-MM-DDTHH:MM:SS"` (hora de 24h) y deja `COUNTDOWN_HOURS = 0`.

> **Nota de seguridad:** la clave de anfitrión está visible en el código público. Si quieres más privacidad, cámbiala por algo único y comparte solo a ti el nuevo enlace `?host=tu-clave`.

---

## 6. Firebase / Firestore — cómo funciona el secreto

El género **real nunca está en el código**. Vive en Firestore y las reglas impiden leerlo hasta que tú reveles.

Estructura en Firestore (colección `reveal`):

| Documento | Campo | Tipo | Valor |
|---|---|---|---|
| `reveal/state` | `revealed` | boolean | `false` (se pone en `true` al revelar) |
| `reveal/secret` | `gender` | string | `"boy"` o `"girl"` ← **tu secreto** |

- La colección `votes` se crea sola con los votos. No la toques.
- Las reglas de seguridad están en `firestore.rules` (ya pegadas en la consola).

### Poner el género real (hacer antes del evento)
1. Consola de Firebase → **Firestore → `reveal` → `secret`**.
2. Lápiz ✏️ en el campo `gender` → escribe **solo** `boy` o `girl` (minúsculas, sin comillas extra) → Guardar.

---

## 7. El día del evento — cómo revelar

1. Asegúrate de que `reveal/secret.gender` tenga el valor real (paso 6).
2. Abre **tu** enlace de anfitrión: `https://cemg97.github.io/gender-reveal/?host=miclave-2026`
3. Abajo aparece el **Panel del anfitrión** → pulsa **💥 Revelar ahora**.
4. Toda la familia que tenga el sitio abierto ve la animación y el género **en vivo**, al instante.

⚠️ **La revelación NO se puede deshacer** (queda `revealed = true` para siempre). Si necesitas reiniciarla para una prueba, cambia manualmente `reveal/state.revealed` a `false` en la consola de Firebase. La cuenta regresiva **nunca** revela sola.

---

## 8. Herramientas usadas

- **git** (control de versiones) — ya instalado.
- **GitHub CLI (`gh`)** — instalado en `C:\Program Files\GitHub CLI\gh.exe`. Sesión autenticada como **CEMG97**.
- **GitHub Pages** — sirve el sitio desde la rama `main`, carpeta raíz `/`.
- **Firebase Firestore** (plan gratuito) — base de datos en vivo de votos y revelación.
