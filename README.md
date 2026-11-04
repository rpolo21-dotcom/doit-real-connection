# Doit — Real Connections

Aplicación web progresiva para organizar encuentros, crear comunidades y conversar de forma privada. Interfaz en español y almacenamiento multiusuario en Supabase.

## Producción
- Aplicación: https://doit-real-connections.vercel.app (enlace de la versión anterior; el dominio definitivo se confirmará tras conectar el proyecto Git con Vercel).
- Supabase: proyecto `pvtwpvlrvlezsxnvublz`, región São Paulo.
- Repositorio: https://github.com/rpolo21-dotcom/doit-real-connection

## Funciones
- Registro e inicio de sesión por correo y contraseña con Supabase Auth.
- Perfiles, eventos, grupos, membresías y confirmaciones de asistencia en PostgreSQL.
- Mensajes directos entre dos cuentas. RLS limita la lectura/escritura de los mensajes a los miembros de cada conversación.
- Entrega en tiempo real con canales privados de Supabase Realtime.
- Notificaciones Web Push opcionales por dispositivo, con permiso explícito del usuario.
- PWA instalable, service worker e iconos creados a partir del logo facilitado para Doit.

## Variables de entorno (Vercel)
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
Las notificaciones requieren HTTPS, navegador compatible, permiso explícito y una suscripción activa. En iPhone/iPad puede ser necesario instalar la PWA en la pantalla de inicio. Las notificaciones de mensaje usan texto genérico para no mostrar contenido privado en la pantalla bloqueada.

## Privacidad y seguridad
- Las tablas expuestas tienen RLS habilitado.
- Los permisos de perfil no exponen la dirección de correo en el directorio de usuarios.
- Los mensajes no tienen cifrado de extremo a extremo: el administrador de la base de datos puede acceder al contenido almacenado.
- Esta primera versión no incorpora todavía bloqueo, denuncia, moderación de cuentas, llamadas ni recuperación de cuenta personalizada. No usar el chat para contraseñas, dirección, ubicación en tiempo real, documentos o datos financieros.

## Desarrollo y pruebas
Requiere Node.js 22 o superior.
```bash
npm install
npm test
```
El script de prueba verifica sintaxis de JavaScript de navegador y funciones serverless. La prueba real de chat y push requiere dos cuentas verificadas en dispositivos/navegadores distintos y permiso de notificaciones.

## GitHub y Vercel
La rama `main` de este repositorio es la fuente de código. Al conectar el repositorio con Vercel, cada push puede generar un despliegue. Mantener las variables privadas solo en Vercel.
