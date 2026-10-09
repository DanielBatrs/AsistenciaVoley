# Bitácora de entrenos en Netlify

## Qué hay en la carpeta

| Archivo | Para qué sirve |
| --- | --- |
| `index.html` | La página (calendario, contador y recordatorios). |
| `bitacora.json` | Datos iniciales. Si el servidor aún no tiene nada, la página los carga de aquí (misma carpeta). Puedes pegar asistencias viejas antes de subir. |
| `netlify/functions/data.mjs` | Guarda y lee la bitácora en Netlify Blobs. |
| `netlify/functions/reminders.mjs` | Cada 5 minutos revisa tus recordatorios y envía el correo. |
| `netlify.toml`, `package.json` | Configuración de Netlify. |

## Por qué la bitácora no se escribe en un archivo de la carpeta

Netlify sirve la carpeta como archivos de solo lectura: la página no puede reescribir `bitacora.json` después de publicada. Por eso los datos viven en Netlify Blobs (el almacenamiento propio de Netlify, incluido en tu sitio) y `bitacora.json` solo sirve como punto de partida.

## Pasos

1. Crea una cuenta gratis en https://resend.com y genera una API key. Con el remitente de prueba `onboarding@resend.dev` Resend solo entrega al correo con el que te registraste, y eso es justo lo que necesitas.
2. Sube esta carpeta a Netlify. Debe desplegarse con funciones, así que usa Git (GitHub) o la CLI (`npm i -g netlify-cli`, luego `netlify deploy --prod`). Arrastrar la carpeta en "Netlify Drop" no despliega las funciones.
3. En Netlify, ve a Site configuration > Environment variables y crea:

| Variable | Valor |
| --- | --- |
| `APP_TOKEN` | Una clave que inventes (la pedirá la página la primera vez). |
| `RESEND_API_KEY` | La API key de Resend. |
| `REMINDER_EMAIL` | El correo donde quieres recibir los recordatorios. |
| `TIMEZONE` | Opcional. Por defecto `America/El_Salvador`. |
| `DEFAULT_TIME` | Opcional. Hora de aviso para recordatorios sin hora. Por defecto `07:00`. |
| `FROM_EMAIL` | Opcional. Remitente. Por defecto `onboarding@resend.dev`. |

4. Vuelve a desplegar para que tome las variables.
5. Abre tu sitio, escribe la clave una vez y listo.

## Cómo funcionan los avisos

- Un recordatorio con hora llega al correo a esa hora (hora de El Salvador). Si no tiene hora, llega a las 07:00.
- Si ya lo marcaste como hecho antes de esa hora, no se envía.
- Cada recordatorio se envía una sola vez por día. Si el envío falla, se reintenta cada 5 minutos durante 3 horas.
- Las funciones programadas de Netlify solo corren en el deploy publicado, no en vistas previas.
