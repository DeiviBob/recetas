# CLAUDE.md — Plataforma de recetas

Plataforma de recetas: un backend modular (Go) y tres apps (web, Apple,
Android, en Flutter) que hablan con él solo a través de un contrato
(OpenAPI 3.1 escrito en TypeSpec). Todo corre en Docker Compose, primero en
una notebook con Linux y después en un VPS.

## Fuente de verdad

- Las decisiones viven en `/docs`: `ANALISIS.md` (reglas y arquitectura),
  `DECISIONES.md` (cada decisión con su ID y su porqué) y `MAPA.md`
  (proyectos, etapas y "Terminado cuando…").
- No los cargues enteros: leé solo la parte de la etapa en curso y las
  decisiones que nombra.
- **No tomes decisiones nuevas.** Si algo no está decidido (una tecnología,
  una regla, un nombre) o choca con lo decidido, pará y preguntá. Las
  decisiones se cambian primero en el proyecto de Claude y después en `/docs`.
- No agregues dependencias, servicios ni herramientas que no estén en
  DECISIONES. Si hacen falta, proponelas y esperá.

## Cómo trabajamos

- **Una etapa de MAPA por vez**, en su propia rama (`etapa/<proyecto>-<n>`).
- **Primero el plan, sin código**: qué archivos, qué decisiones aplicás (por
  ID) y cómo probás cada punto del "Terminado cuando…". Esperá el ok.
- Una etapa está terminada solo cuando se cumple su "Terminado cuando…",
  incluidos los casos que tienen que **fallar** (probalos con un PR de prueba).
- Todo entra a `main` por pull request con la CI en verde; `main` siempre
  funciona.
- Commits con el proyecto: `feat(backend): …`, `fix(contrato): …`.
- Versiones con etiqueta por proyecto: `contrato-vX.Y.Z`, `backend-vX.Y.Z`,
  `app-web-vX.Y.Z`, `app-apple-vX.Y.Z`, `app-android-vX.Y.Z`, `vigia-vX.Y.Z`.
  Subir la mayor = rompe compatibilidad.
- Explicame en español, claro, cada concepto técnico nuevo en una línea.

## Estructura del repositorio

```
/docs          copia de ANALISIS, DECISIONES y MAPA
/contrato      TypeSpec → OpenAPI 3.1 (openapi/v1/openapi.yaml, generado)
/backend       Go: un solo programa con ocho módulos
/apps          espacio de trabajo de Dart
  /comun       compartido por las tres apps
  /nativo      adaptadores compartidos por Apple y Android
  /web /apple /android
/plataforma    Docker Compose: base común + ajustes de desarrollo y producción
/vigia         vigilancia de la plataforma (Go)
```

## Reglas del backend (no negociables)

Es **un solo backend modular, no microservicios**. Módulos: Identidad, Perfil,
Recetas, Colecciones, Favoritos, Medios, Respaldo, Notificaciones.

- **M1**: cada módulo es dueño exclusivo de sus datos: un esquema y un
  usuario de PostgreSQL por módulo, más `infra`.
- **M2**: un módulo usa a otro solo por su `interfaz`; lo demás va en su
  `internal/`.
- **M3**: para avisar que algo pasó, publica un evento del catálogo común
  (outbox con River). La visibilidad de cada cosa la decide su módulo dueño.
- **M4**: sin dependencias circulares. Las consultas van siempre hacia abajo:
  Respaldo → Recetas, Colecciones, Favoritos, Medios; Favoritos → Recetas,
  Colecciones; Colecciones → Recetas, Perfil; Recetas → Perfil, Medios;
  Identidad → Notificaciones.
- **M5**: cualquier módulo se tiene que poder sacar a un servicio aparte
  cambiando solo cómo se comunica.
- **Capas dentro de cada módulo**: dominio puro → casos de uso (una carpeta
  por caso) → puertos → adaptadores. **La tecnología (pgx, SQL, HTTP,
  libvips, age, SMTP) vive solo en adaptadores.** El dominio no la importa.
- Sin transacciones entre módulos: lo que cruza módulos va por pasos que se
  pueden repetir sin daño.
- La prueba de arquitectura (backend-18) tiene que frenar cualquier violación.

## Reglas del contrato

- El OpenAPI se genera desde TypeSpec y **nunca se edita a mano**.
- Los clientes y el código de servidor generados **no se guardan** en el
  repositorio: se generan al compilar.
- Versión mayor en la dirección (`/v1/`). Un cambio que rompe crea una
  versión nueva; oasdiff lo frena en la CI.
- Campos en español, sin tildes ni ñ, camelCase; errores en `problem+json`
  con `codigo` en español; fechas RFC 3339 en UTC; ids UUIDv7 del backend.
- Paginación por cursor; `ETag`/`If-Match` (412/428); `Idempotency-Key` en
  todo lo que crea; una privada ajena responde 404.

## Reglas de las apps

- Solo hablan con el backend por el contrato. **Sin reglas de negocio**,
  salvo el escalado (R8/R9), probado con `/contrato/casos/escalado.json`.
- Las pantallas usan casos de uso a través de Riverpod; nunca llaman al
  cliente generado.
- El dominio y los casos de uso de `comun` no importan Flutter, Riverpod, dio
  ni el cliente generado.

## Seguridad y privacidad

- Todo pedido autenticado (salvo registrarse, entrar, pedir código y
  recuperar el acceso). Todo dato tiene dueño. Privado por defecto.
- **Nunca se expone el correo** de una persona a otra. Las públicas solo las
  ven personas con cuenta.
- La contraseña de un respaldo **nunca se guarda** ni aparece en registros,
  direcciones o encabezados.
- Contraseñas, códigos y tokens se guardan solo como huella.
- Las fotos se guardan sin metadatos (ubicación incluida).
- **Secretos nunca en el repositorio ni en el chat**: un archivo por entorno,
  fuera del repositorio; en el repositorio, solo un ejemplo sin valores. La
  clave de firma de Android nunca entra al repositorio ni a la CI.
- **El repositorio es público** (base-plataforma-20, P-21). Nada de la
  seguridad puede depender de que el código sea secreto. Nunca subas datos
  reales, correos, claves, tokens ni las claves de las señales del vigía
  (`controles.yaml` va sin claves), tampoco en ejemplos ni en pruebas. Los
  commits van con la dirección privada de GitHub (`…@users.noreply.github.com`).
  Las imágenes de `ghcr.io` son privadas.

## Plataforma

- Docker Engine + Compose. Una sola definición con dos capas de ajustes:
  desarrollo y producción. Lo que se prueba es lo que corre.
- Producción solo corre imágenes con etiqueta y nunca depende de la carpeta
  de trabajo.
- En producción ningún contenedor publica puertos hacia afuera; todo entra
  por el nginx de entrada.
- Contenedores sin permisos de administrador; ninguno con acceso al control
  de Docker.

## Pendientes conocidos (no los decidas vos)

- El programa de la bandeja de correo de prueba del entorno desarrollo.
- El nombre del dominio (mientras tanto: `<dominio>` y el paquete
  `com.<nombre>.recetas`).
