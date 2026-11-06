# Entrega de accesos CEAL — Netlify + Supabase

Aplicación web móvil para asignar de forma aleatoria y atómica 16 credenciales. El repositorio no contiene usuarios ni contraseñas reales.

## Arquitectura
- Netlify: hosting del frontend (React + Vite).
- Supabase: PostgreSQL, autenticación de administrador y funciones RPC.
- GitHub: repositorio privado recomendado.

## 1. Crear Supabase
1. Crea un proyecto en Supabase.
2. Abre SQL Editor y ejecuta `supabase/schema.sql`.
3. En Authentication > Users, crea el usuario administrador.
4. Copia el UUID de ese usuario y ejecuta el `INSERT` comentado al final de `schema.sql`, reemplazando el UUID.
5. En Project Settings > API, copia Project URL y la clave `anon`/publishable. Nunca uses `service_role` en el frontend.

## 2. Configurar local
1. Descomprime el proyecto.
2. Ejecuta `npm install`.
3. Copia `.env.example` a `.env`.
4. Completa `VITE_SUPABASE_URL` y `VITE_SUPABASE_ANON_KEY`.
5. Ejecuta `npm run dev`.

## 3. Publicar en Netlify
1. Sube estos archivos a un repositorio **privado** de GitHub.
2. En Netlify: Add new site > Import an existing project > GitHub.
3. Build command: `npm run build`; Publish directory: `dist`.
4. En Site configuration > Environment variables, configura:
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
5. Dispara un nuevo deploy.

## 4. Cargar las 16 credenciales
1. Entra a `https://TU-SITIO.netlify.app/admin`.
2. Inicia sesión con el usuario administrador creado en Supabase.
3. Prepara un CSV UTF-8 con encabezados `username,access_code` y exactamente 16 filas.
4. Importa el CSV. La aplicación no muestra las contraseñas en el panel.
5. Habilita la entrega. Comparte el QR/enlace de la portada.

Ejemplo de estructura CSV (no usar estos datos):
```csv
username,access_code
DEMO-01,NO-USAR-EJEMPLO
```

## Consideraciones de seguridad
- No pegues contraseñas reales en GitHub, en el código, en este chat ni en variables Vite.
- La clave anon de Supabase es pública por diseño; la seguridad depende de RLS y de las funciones SQL.
- La asignación se realiza en una función transaccional con `FOR UPDATE SKIP LOCKED`; solicitudes simultáneas no deben recibir el mismo registro.
- El enlace compartido no identifica a las personas. Una persona puede solicitar más de una credencial. Solo se garantiza que una credencial concreta no se entregue dos veces.
- El código no implementa verificación humana ni rate limit avanzado. Para una entrega presencial, supervisa que cada participante solicite una sola vez.
- La clave se muestra una sola vez al participante. Indícale que la copie antes de cerrar o recargar la página.
- Al finalizar: cierra la distribución, exporta solo el registro si lo necesitas y elimina los registros sensibles y/o el proyecto temporal de Supabase.

## Nota sobre QR
El QR se genera en el panel usando el dominio actual. Debe generarse/compartirse solo después de publicar el sitio en Netlify.
