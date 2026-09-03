# Registro Académico

Registro de notas para secundaria (MEP, Costa Rica). Una sola página web, sin instalar nada:
exámenes, tareas, trabajo cotidiano y asistencia con pesos configurables por sección,
calendario de días lectivos, acta de calificaciones lista para imprimir e inicio de sesión
con Google para que todo quede guardado en la nube.

- **Cada rubro tiene su fórmula.** Escala (`obtenido ÷ base × peso`), varias pruebas
  (`Σ puntos ÷ Σ bases × peso`) o puntos directos.
- **La asistencia se calcula sola.** Se digita la cantidad de días asistidos y se divide entre
  los días lectivos que salen del calendario del periodo.
- **Días no lectivos.** Feriados, reuniones, actos cívicos: se marcan en el calendario y el
  divisor de la asistencia se ajusta solo.
- **Todo es editable**: nombres de secciones, institución, rubros, pesos, cantidad de exámenes,
  nota mínima de aprobación y etiquetas.
- **Ficha por estudiante.** Desglose rubro por rubro, cuánto aporta cada uno y —lo más útil—
  **cuántos puntos necesita en los rubros pendientes** para alcanzar la nota mínima.
- **Boleta individual imprimible** para reuniones con encargados.
- **Condición y adecuación.** Estudiantes retirados quedan fuera del promedio; las adecuaciones
  (acceso, no significativa, significativa) se marcan en la rejilla y en el acta.
- **Filtros rápidos**: aprobados, en riesgo, reprobados, sin datos, retirados.
- **Configuración inicial guiada**: cada docente crea sus propias secciones desde cero.

> **Privacidad**: este repositorio **no contiene ningún dato de estudiantes**. Las listas y las
> notas viven en su propia base de datos de Firebase, protegidas por las reglas de
> `firestore.rules`, que solo permiten a cada docente leer y escribir su propio registro.

---

## Puesta en marcha (una sola vez)

### 1. Crear el proyecto en Firebase

1. Entre a <https://console.firebase.google.com> con su cuenta de Google → **Crear un proyecto**.
2. **Compilación → Authentication → Comenzar** → pestaña **Sign-in method** → **Google** →
   activar → elegir correo de soporte → **Guardar**.
3. **Compilación → Firestore Database → Crear base de datos** → modo de producción.
4. **Firestore Database → Reglas**: borre lo que haya y pegue el contenido de
   [`firestore.rules`](firestore.rules) → **Publicar**.
5. Engranaje ⚙ **Configuración del proyecto** → pestaña **General** → bajar hasta
   **Sus apps** → icono **`</>`** (Web) → poner un apodo → **Registrar app** →
   copiar el bloque `firebaseConfig`.

> ⚠️ **No confunda con la pestaña «Cuentas de servicio».** Ahí Firebase muestra un código con
> `firebase-admin` y un `serviceAccountKey.json`: eso es para servidores con Node.js y esa
> llave es **privada**. Si se sube a un repositorio público, cualquiera puede leer y borrar
> toda la base de datos. Para esta app no se necesita.
>
> La configuración correcta es la de la pestaña **General**, y se reconoce porque empieza con
> `apiKey` y `authDomain`.

### 2. Pegar la configuración

Abra `index.html` en un editor de texto, busque `const FIREBASE_CONFIG` (cerca del final del
archivo) y pegue sus valores:

```js
const FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "su-proyecto.firebaseapp.com",
  projectId: "su-proyecto",
  storageBucket: "su-proyecto.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

Estos valores **no son secretos**: van dentro de cualquier página web que use Firebase, y por eso
pueden estar en un repositorio público. Lo que protege los datos son las reglas del paso 1.4.

### 3. Publicar con GitHub Pages

En este repositorio: **Settings → Pages → Build and deployment → Source: Deploy from a branch**
→ rama `main`, carpeta `/ (root)` → **Save**.

En un par de minutos la app queda en:

```
https://SU-USUARIO.github.io/registro-academico/
```

### 4. Autorizar el dominio en Firebase ← *el paso que más se olvida*

Sin esto, el botón de Google da el error `auth/unauthorized-domain`.

Firebase → **Authentication → Settings → Dominios autorizados → Agregar dominio** →
escriba `SU-USUARIO.github.io` → **Agregar**.

### 5. Empezar a usarla

Abra la dirección de GitHub Pages → **Continuar con Google**. La primera vez aparece una
configuración guiada de tres pasos (su nombre, el periodo lectivo y sus secciones).

Si ya tiene un respaldo, use en su lugar **Respaldo y datos → Restaurar desde archivo**.

Eso sube todo a su cuenta. De ahí en adelante no hay que volver a hacerlo: entra desde
cualquier computadora, tablet o celular y encuentra el registro igual.

---

## Uso diario

- Se digita y **se guarda solo**. En la barra lateral, abajo, aparece su nombre con un punto
  verde y la hora del último guardado.
- **Sin internet también funciona**: los cambios se guardan en el navegador y suben cuando
  vuelve la conexión.
- Si abre la app en dos dispositivos, los cambios del otro aparecen solos — nunca mientras
  usted está digitando.
- La primera vez que inicia sesión en una computadora donde ya había notas digitadas, la app
  compara las dos versiones y le deja elegir cuál conservar. **Nunca borra nada sin preguntar.**
- Aun con la nube, conviene bajar un respaldo `.json` de vez en cuando desde
  **Respaldo y datos**.

### Atajos

| Tecla | Acción |
|---|---|
| `Tab`, `↑`, `↓`, `Enter` | Moverse entre celdas |
| `/` | Ir al buscador |
| `Ctrl + Z` | Deshacer |
| `Ctrl + P` | Imprimir el acta |

Para ver la ficha de un estudiante, pase el ratón por su fila y pulse el icono de ficha junto
al nombre.

---

## Proveedores de inicio de sesión

La pantalla de acceso muestra los tres: **Google, Microsoft y Facebook**. Cada uno debe estar
activado en **Firebase → Authentication → Sign-in method**:

| Proveedor | Qué hay que hacer |
|---|---|
| **Google** | Activarlo en Firebase y elegir un correo de soporte. Dos clics, nada más. |
| **Microsoft** | Además de activarlo, registrar una aplicación en [Azure Portal](https://portal.azure.com) (App registrations) y pegar en Firebase el *Application ID* y el *secret*. |
| **Facebook** | Además de activarlo, crear una app en [Meta for Developers](https://developers.facebook.com), agregar el producto *Facebook Login* y pegar en Firebase el *App ID* y el *App Secret*. |

Mientras un proveedor no esté activado, su botón aparece pero al pulsarlo avisa
«Ese proveedor no está activado en Firebase». Para esconder alguno, en `index.html`:

```js
const PROVEEDORES = { google: true, microsoft: false, facebook: false };
```

---

## Actualizar la app

Edite `index.html`, guarde y suba el cambio:

```bash
git add -A && git commit -m "Ajustes" && git push
```

GitHub Pages vuelve a publicar en un par de minutos.

---

## Sin cuenta

El mismo `index.html` abierto con doble clic sigue funcionando sin iniciar sesión, guardando
en ese navegador. El inicio de sesión con Google **no** funciona así: los proveedores exigen
una dirección `https://`, por eso hace falta GitHub Pages.
