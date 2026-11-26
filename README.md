# Doit — Real Connections

Aplicación web progresiva para organizar encuentros, consultar una agenda personal, crear comunidades locales y construir conexiones con consentimiento. Interfaz en español y almacenamiento multiusuario en Supabase.

## Producción

Versión actual: **1.3.1**. Los planes personales ahora tienen una política de lectura por participantes y los enlaces de invitación resuelven únicamente el evento asociado al token.


- Aplicación: https://doit-real-connections.vercel.app (versión anterior del sitio; el despliegue actualizado se publicará al conectar este repositorio).
- Supabase: proyecto `pvtwpvlrvlezsxnvublz`, región São Paulo.
- Repositorio: https://github.com/rpolo21-dotcom/doit-real-connection

## Funciones

- Registro e inicio de sesión por correo y contraseña con Supabase Auth.
- Perfiles, eventos, grupos, membresías y confirmaciones de asistencia en PostgreSQL.
- Agenda personal con vistas diaria, semanal y mensual; incluye actividades a las que confirmaste “Voy” o “Quizás”.
- Enlaces seguros por plan: el enlace se vincula en Supabase con la actividad y con la cuenta que lo compartió. Al registrarse o iniciar sesión, la persona abre el plan, confirma asistencia y puede enviar una solicitud de amistad. La amistad solo se crea al aceptar la solicitud.
- Registro inicial ampliado: intereses, hobbies, país, ciudad y tiempo semanal que deseas dedicar a actividades.
- Descubrimiento local voluntario: puedes explorar grupos compatibles sin hacer visible tu perfil. Solo aparecen como personas recomendables quienes aceptaron participar en el descubrimiento y coinciden en ciudad y país. Los hobbies y el tiempo disponible permanecen privados. Puedes activar o desactivar tu propia visibilidad desde el perfil.
- Foto de perfil seleccionable en Perfil y configuración. Las imágenes admitidas son JPG, PNG o WebP de hasta 3 MB; solo el propietario puede subir, reemplazar o borrar archivos de su carpeta.
- Mensajes directos entre dos cuentas. RLS limita la lectura/escritura de los mensajes a los miembros de cada conversación.
- Entrega en tiempo real con canales privados de Supabase Realtime.
- Notificaciones Web Push opcionales por dispositivo, con permiso explícito del usuario.
- PWA instalable, service worker e iconos creados a partir del logo facilitado para Doit.

## Variables de entorno (Vercel)

Configurar para `production`, `preview` y `development` según corresponda:

- `SUPABASE_URL`
- `SUPABASE_PUBLISHABLE_KEY`
- `VAPID_PUBLIC_KEY`
- `VAPID_PRIVATE_KEY` (**Sensitive**, servidor solamente)
- `VAPID_SUBJECT`
- `DOIT_PUSH_GATEWAY_SECRET` (**Sensitive**, servidor solamente)

No incluir claves secretas de Supabase, `service_role`, claves privadas VAPID ni el secreto del gateway en GitHub o en código de navegador.

## Configuración de autenticación

En Supabase → Authentication → URL Configuration, definir como Site URL el dominio de producción y agregar el patrón de retorno del mismo dominio. Al publicar un nuevo dominio, actualizar ambos valores.

## Notificaciones push

Las notificaciones requieren HTTPS, un navegador compatible, permiso del usuario y una suscripción activa. En iPhone/iPad puede ser necesario instalar la PWA en la pantalla de inicio. Las notificaciones de mensaje usan texto genérico para no mostrar contenido privado en la pantalla bloqueada.

## Privacidad y seguridad

- Las tablas expuestas tienen RLS habilitado.
- Las preferencias privadas solo se pueden leer por su propietario. La tabla de descubrimiento solo devuelve personas que aceptaron aparecer y que coinciden en ciudad y país.
- Los enlaces de invitación usan tokens aleatorios resueltos por funciones autenticadas con permisos limitados; quien recibe un enlace solo puede resolver el token concreto que le compartieron.
- Las solicitudes de amistad son privadas para sus dos participantes; quien recibe la solicitud puede cambiar únicamente su estado.
- Los permisos de perfil no exponen la dirección de correo en el directorio público de usuarios.
- Los mensajes no tienen cifrado de extremo a extremo: el administrador de la base de datos puede acceder al contenido almacenado.
- Esta primera versión no incorpora todavía bloqueo, denuncia, moderación de cuentas, llamadas ni recuperación de cuenta personalizada. No usar el chat para contraseñas, dirección, ubicación en tiempo real, documentos o datos financieros. DoIT aún no incorpora bloqueo ni denuncia de usuarios; comparte solo información necesaria, elige lugares públicos e informa a una persona de confianza antes de acudir a un encuentro.

## Desarrollo y pruebas

Requiere Node.js 22 o superior.

```bash
npm install
npm test
```

El script de prueba verifica sintaxis de JavaScript, identificadores HTML únicos, controles de agenda, integraciones de invitación y presencia de las migraciones de base de datos. La prueba real de invitaciones y amistad necesita dos cuentas autenticadas; la prueba de mensajería y envío push necesita dos dispositivos/navegadores distintos y permiso de notificaciones.

## GitHub y Vercel

La rama `main` de este repositorio es la fuente de código. Al conectar el repositorio con Vercel, cada push puede generar un despliegue. Mantener las variables privadas solo en Vercel.

## Registro sin confirmación de correo

Para que un registro cree la cuenta e inicie sesión inmediatamente, en Supabase abre **Authentication → Providers → Email** y desactiva **Confirm email**. En proyectos alojados Supabase mantiene esta confirmación activada por defecto. Con esta opción desactivada, `signUp()` devuelve una sesión; si sigue activada, la app mostrará un enlace directo a ese ajuste en vez de dar por hecho que el usuario ya inició sesión.

Proyecto: https://supabase.com/dashboard/project/pvtwpvlrvlezsxnvublz/auth/providers

Desactivar esta opción significa que una persona puede registrar una dirección de correo que no controla. La app no podrá comprobar que el correo pertenece al usuario ni usarlo como prueba de identidad.

## Instalación y notificaciones

La tarjeta inicial ofrece instalar DoIT. En navegadores Chromium usa el evento `beforeinstallprompt` cuando está disponible; en iPhone/iPad muestra los pasos manuales de Safari. La notificación del navegador se solicita aparte, solo después de que el usuario pulsa el botón de activación. Para iOS, primero debe añadirse DoIT a la pantalla de inicio.

El servidor solo anuncia push como disponible cuando existen las tres variables privadas/públicas necesarias (`VAPID_PUBLIC_KEY`, `VAPID_PRIVATE_KEY` y `DOIT_PUSH_GATEWAY_SECRET`). No se solicitará permiso ni se guardará una suscripción que no pueda recibir notificaciones.

## Agenda, invitaciones y descubrimiento local (1.3.0)

Las migraciones nuevas están en `supabase/migrations/20261103000500_...sql` hasta `20261103001400_...sql`. Se aplicaron al proyecto Supabase `pvtwpvlrvlezsxnvublz`. Los datos de país/ciudad los introduce la persona; DoIT no pide GPS, dirección ni ubicación en tiempo real. Las recomendaciones se limitan a ciudad y país y requieren consentimiento explícito.

Los enlaces de planes necesitan que la persona invitada se registre o inicie sesión. Si Supabase mantiene activada la confirmación de correo, el inicio inmediato no funcionará hasta desactivar **Confirm email** como se indica arriba.


## Publicación de la versión 1.3.1 en Vercel

La fuente de DoIT 1.3.1 está empaquetada en seis fragmentos binarios dentro de `src/`. El `package.json` de arranque ejecuta un `postinstall` que reconstruye y extrae el paquete, dejando los archivos reales de la app y las funciones API en la raíz de Vercel. El proceso se verificó localmente: crea `index.html`, `app.js` (63 KB), `api/config.js`, `api/push/send.js` y `vercel.json`.

Para conectar el repositorio al proyecto Vercel existente:

1. En Vercel abre el proyecto `doit-real-connections` → **Settings → Git** y conecta `rpolo21-dotcom/doit-real-connection`, rama `main`.
2. En **Settings → Build and Development Settings**, usa Framework Preset **Other**, Root Directory `src`, Node.js **22.x**, Install Command `npm install`, y deja Build Command y Output Directory vacíos.
3. En Environment Variables conserva las seis variables indicadas arriba en Production (y Preview si se usarán previews). No copies valores secretos a GitHub ni a chats.
4. Lanza un nuevo Production Deployment desde `main`.

**Comprobación obligatoria después del despliegue:** `https://doit-real-connections.vercel.app/api/config` debe responder HTTP 200 con JSON de configuración pública; `/api/push/send` debe existir (un GET responde 405, porque solo acepta POST). La página principal en HTTP 200 no basta: también debe iniciar sesión y cargar datos de Supabase.

En Supabase, verifica además **Authentication → URL Configuration** (Site URL y Redirect URLs para el dominio de producción) y activa **Leaked Password Protection** en la sección de seguridad de contraseñas.