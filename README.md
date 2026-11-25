# Medikuak

Citas médicas de la familia: visitas, pruebas, medicación, tareas e informes, organizados por persona y episodio. Se usa desde el móvil y funciona sin conexión.

- **Web pública:** `index.html` (HTML + CSS + JS, sin servidor) en GitHub Pages.
- **Datos privados:** una hoja de Google Sheets llamada «Medikuak», creada por la propia app en el Drive de quien la crea. Tiene una pestaña por tabla: Familia, Redes, Medicos, Episodios, Visitas, Pruebas, Medicacion, Tareas e Informes.
- **Acceso:** cada persona entra con su cuenta de Google. Solo puede entrar quien tenga la hoja compartida: Editor para cambiar cosas, Lector solo para ver.
- **Permiso mínimo:** la app pide `drive.file`, así que solo puede abrir la hoja que ella crea o la que el usuario elige con el selector de Google Drive. No ve el resto del Drive.
- **Sin conexión:** copia en el móvil (IndexedDB). Cada registro guarda «Actualizado» y gana el cambio más reciente. Los borrados se marcan y se limpian a los 30 días. Sincroniza al abrir, al volver a la app, al recuperar la conexión y cada 20 s.

## Archivos

| Archivo | Qué es |
| --- | --- |
| `index.html` | Toda la app |
| `config.js` | ID de cliente de Google y clave del selector de Drive (nada secreto) |
| `manifest.webmanifest`, `icon-*.png` | Para añadirla a la pantalla de inicio |
| `vendor/qrcode.js` | Generador de códigos QR ([qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator), MIT), guardado aquí para no cargar código de terceros |

## Puesta en marcha (una vez)

Medikuak tiene su propio proyecto de Google Cloud, **medikuak** (número 163541765712), separado del de la lista de la compra. Es gratis y no necesita facturación.

1. **GitHub Pages.** En el repositorio: Settings → Pages → *Deploy from a branch* → rama `main`, carpeta `/ (root)`. La app queda en `https://bsanchezlarrouy.github.io/medikuak/`.
2. **APIs activadas** en el proyecto: Google Sheets API, Google Picker API y Google Drive API (esta última solo para que un dispositivo nuevo encuentre solo la hoja).
3. **Google Auth Platform** (pantalla de permiso): app «Medikuak», audiencia *External*, en modo prueba.
   - *Test users:* el correo de cada cuenta que vaya a entrar.
   - *Data access:* `openid`, `userinfo.email`, `userinfo.profile` y `drive.file`.
4. **Cliente OAuth «Medikuak»** (tipo *Web application*):
   - *Orígenes de JavaScript autorizados:* `https://bsanchezlarrouy.github.io`
   - *URI de redirección autorizados:* `https://bsanchezlarrouy.github.io/medikuak/` (con la barra final)
5. **Clave de API «Medikuak Picker»** para el selector de Google Drive:
   - *Restricciones de aplicación:* Sitios web → `https://bsanchezlarrouy.github.io/*` (sin `/medikuak/`: el navegador solo envía el dominio al selector)
   - *Restricciones de API:* solo Google Picker API.
6. El ID de cliente y la clave van en `config.js`. Ninguno es secreto: la clave solo funciona desde esta web y solo para el selector.

Si alguien nuevo tiene que entrar, añade su correo en *Test users* y comparte con él la hoja.

## Primer uso

1. En tu móvil, abre la app → **Entrar con Google** → **Crear la hoja de la familia** → pon nombre al móvil.
2. Abre la hoja (Más → Abrir en Google Sheets) → **Compartir** → añade el correo de la otra persona como **Editor**.
3. En su móvil: Más → **Conectar otro móvil** en el tuyo, y que escanee el QR. Entra con su cuenta y, cuando la app lo pida, elige la hoja «Medikuak» en su Drive (solo la primera vez).
4. En cada móvil: menú del navegador → **Añadir a pantalla de inicio**.

## Personas y episodios privados

- Desde la ficha de una persona o de un episodio: **Marcar como privado**. Si la persona es privada, también lo son todos sus episodios, citas, pruebas, medicación, tareas e informes.
- El PIN (de 4 a 8 números) se crea en Más y es el mismo en todos los dispositivos. Se guarda en la pestaña Ajustes como huella PBKDF2, nunca el PIN en claro.
- En cada dispositivo, «Ocultar lo privado en este dispositivo» está activado por defecto. En vuestros móviles se puede desactivar metiendo el PIN; en la tablet familiar, déjalo activado.
- «Ver lo privado» lo muestra durante 5 minutos o hasta salir de la app.
- **Límite:** el PIN oculta lo privado en la app, no en la hoja. Quien abra la hoja en Google Sheets con una cuenta que tenga acceso puede verlo. Por eso conviene compartir la hoja con la cuenta de la tablet (haudaguretablet@gmail.com) como **Lector**.

## Notas

- La app solo se conecta a servicios de Google. Todo su código está en este repositorio; lo único que carga de fuera es el selector de Google Drive, que es de Google.

- El enlace del QR lleva el id de la hoja después de `#`, así que el navegador no lo envía a GitHub. El enlace no da acceso por sí solo.
- **Cerrar sesión** (en Más) revoca el permiso de Google y borra la copia de ese móvil. La hoja no se toca.
- Cada sesión de Google dura una hora. Al caducar, la app intenta renovarla sola; si no puede, el aviso de arriba dice «toca para entrar».
- Las columnas «(id)» de la hoja enlazan con otras pestañas. Se puede leer y corregir texto en la hoja, pero no cambies los ids ni la columna «Logo (no tocar)».
- Los informes se guardan como enlace de Google Drive. Cada persona los abre con sus propios permisos de Drive.
