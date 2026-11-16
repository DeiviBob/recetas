# Mapa de proyectos

Todo vive en **un solo repositorio** (GitHub, **público**, base-plataforma-20). Estados:
`pendiente` → `decidido` (tecnología, arquitectura y plan elegidos) →
`en construcción` → `listo`.

| # | Proyecto | Qué es | Estado | Tecnología | Depende de |
|---|---|---|---|---|---|
| 0 | **base-plataforma** | Decisiones comunes | Fase desarrollo: **decidido** · Fase producción: **decidido** (2026-11-15) | Docker Engine + Compose · GitHub (repositorio público) · contrato en OpenAPI 3.1 vía TypeSpec · Cloudflare (dominio `.com`, DNS, túnel, Access) · nginx 1.30 · correo por SMTP: Brevo + Resend de respaldo · CI → `ghcr.io` + `desplegar` · restic 0.19 → disco externo + Backblaze B2 · disco cifrado con TPM · Hostinger KVM 1 en São Paulo cuando se cumpla un criterio (base-plataforma-01 a 20) | Análisis validado ✅ |
| 1 | **contrato** | API entre backend y apps | **decidido** (2026-11-14) | TypeSpec 1.18 → OpenAPI 3.1 · oasdiff · Redocly CLI · Prism · Schemathesis 4.x (contrato-01 a 14) | 0 (fase desarrollo ✅) |
| 2 | **backend** | Módulos: Identidad, Perfil, Recetas, Colecciones, Favoritos, Medios, Respaldo, Notificaciones | **decidido** (2026-11-14) | Go 1.27 + `net/http` + oapi-codegen 2.8 · PostgreSQL 19 (pgx, sqlc, goose) · River · fotos en disco con libvips → WebP · búsqueda en PostgreSQL · respaldos con age · CORS para la web (backend-01 a 20) | 0 ✅, 1 ✅ |
| 3 | **app-web** | Navegador | **decidido** (2026-11-14) | Flutter 3.50 (Dart 3.14) + Riverpod 3 · `/apps/comun` + `/apps/web` · go_router 18 · swagger_parser + retrofit + dio · gen-l10n · Wasm con respaldo JS · nginx 1.30 en su contenedor (app-web-01 a 10; app-web-07 reemplazada por app-web-10) | 1 ✅, 2 ✅ |
| 4 | **app-apple** | iPhone, iPad y Mac | **decidido** (2026-11-15) | Flutter 3.50 (Dart 3.14) + Riverpod 3 · `/apps/comun` + `/apps/nativo` + `/apps/apple` (destinos iOS y macOS) · Material 3 con toques de Apple · flutter_secure_storage 10.3 · image_picker + flutter_image_compress 2.5 · Xcode 27 · firma gratuita · CI en Linux + script en la Mac (app-apple-01 a 09) | 1 ✅, 2 ✅ |
| 5 | **app-android** | Celulares, tablets y plegables | **decidido** (2026-11-15) | Flutter 3.50 (Dart 3.14) + Riverpod 3 · `/apps/comun` + `/apps/nativo` + `/apps/android` · Material 3 (`material_ui`), Expressive a medida que se publique · flutter_secure_storage 10.3 (Keystore) · image_picker + flutter_image_compress 2.5 · desde Android 7.0 (API 24), compila para Android 17 (API 37) · APK firmado: ADB en los aparatos propios, descarga directa para conocidos, cuenta gratuita de Google (20 aparatos) · actualización desde la app · CI en Linux que compila el APK + script en la notebook (app-android-01 a 10) | 1 ✅, 2 ✅ |
| 6 | **vigia** | Vigilancia fuera de casa: salud de la API y la web, respaldos, prueba de restauración y discos; avisos por Telegram y correo (P-20) | **decidido** (2026-11-15) | Go 1.27 en Docker · Google Cloud e2-micro gratis (EE. UU.) · nginx + Let's Encrypt en `vigia.<dominio>` · SQLite · Telegram + SMTP (Brevo → Resend) · restic → B2 (vigia-01 a 04) | 0 (fase producción ✅), 1 ✅ |
| — | receta-desde-foto | Futuro | — | — | 2 |

**Abrir la plataforma a la primera persona** requiere: la fase producción de
base-plataforma en marcha (etapas 1 a 8), el backend y la app web listos y
el **vigía** listo (P-20). Las apps de Apple y Android no son condición. La
fase producción no frena al contrato ni al backend.

**Escala (P-19)**: < 100 personas el primer año; la **meta de diseño es
10.000 personas** sin rediseñar. A esa escala la notebook no alcanza y las
fotos (~350 GB estimados) no entran en el disco del VPS elegido (50 GB:
≈ 1.000–1.400 personas): habrá que mudarlas o agrandar el disco, y revisar
el presupuesto.

## Costo mensual acumulado

| Concepto | Hoy | Cuando se active |
|---|---|---|
| Notebook con Linux | USD 0 | — |
| Herramientas del contrato (TypeSpec, oasdiff, Redocly CLI, Prism, Schemathesis) | USD 0 | USD 0 |
| Backend (Go, PostgreSQL, River, libvips, age, goose, sqlc; fotos en el disco propio) | USD 0 | USD 0 |
| App web (Flutter, Riverpod, go_router, swagger_parser, retrofit, dio, nginx) | USD 0 | USD 0 |
| App Apple (Flutter, Xcode 27, firma gratuita de Apple) | USD 0 | USD 0 |
| App Android (Flutter, SDK de Android, cuenta gratuita de distribución limitada de Google) | USD 0 | USD 0 |
| Dominio (`.com` en Cloudflare) | USD 0 (se compra apenas se elija el nombre) | ≈ 0,93 (≈ USD 11,17/año) |
| Túnel de Cloudflare, Cloudflare Access, nginx, `ghcr.io`, Dependabot | USD 0 | USD 0 |
| Correo (Brevo + Resend, planes gratis) | USD 0 | USD 0 (con 10.000 personas, plan pago desde ≈ USD 9) |
| Respaldos (restic + disco externo + Backblaze B2) | USD 0 | USD 0 dentro de los 10 GB gratis; ≈ 2,4 con 10.000 personas |
| Vigía (Google Cloud e2-micro gratis, alerta de gasto de USD 1) | — | USD 0 |
| VPS (Hostinger KVM 1, São Paulo; cuando se cumpla un criterio) | — | USD 6,49 (24 meses prepagos ≈ USD 156); renueva a USD 11,99 |
| **Total** | **USD 0** | ≈ USD 0,93 con el dominio · ≈ USD 7,4 en el VPS · ≈ USD 12,9 al renovar (tope para el arranque: USD 20) |

**Compras únicas** (no mensuales): un disco externo para los respaldos; una
UPS chica para el router (opcional). **Cuenta paga de Apple** (USD 99/año
≈ USD 8,25/mes): **no por ahora**; se revisa cuando 5 personas con iPhone o
Mac la pidan (base-plataforma-18). Con ella, el total en el VPS renovado
pasaría a ≈ USD 21,2: por encima del tope. **Cuenta completa de Google para
Android** (USD 25 una sola vez): solo si se superan 15 aparatos autorizados
en la gratuita (base-plataforma-19). Al crecer hacia 10.000 personas (P-19),
las fotos podrían pasar a un almacenamiento externo (≈ USD 2,5–5/mes
estimados) o a un disco más grande, y el correo a un plan pago; el
presupuesto se revisa entonces.

---

## base-plataforma

### Fase desarrollo — decidido (2026-11-14)

- Alojamiento: notebook con Linux para desarrollar y para las primeras
  personas; VPS después (P-17).
- Dominio y DNS: Cloudflare; se compra en la fase producción
  (base-plataforma-01).
- HTTPS: Let's Encrypt; en el VPS, tráfico directo; CDN de fotos → backend
  (backend-06: por ahora no); cómo se publica la notebook → fase producción
  (base-plataforma-02; decidido: túnel, base-plataforma-08).
- Entorno de ejecución: Docker Engine + Compose (base-plataforma-03).
- Dos entornos en la notebook: desarrollo y producción (base-plataforma-04).
- Repositorio: GitHub, estructura y convenciones (base-plataforma-05).
- Formato del contrato: OpenAPI 3.1 vía TypeSpec (base-plataforma-06).

### Arquitectura (fase desarrollo)

```
Notebook con Linux (Docker Engine + Compose)
├─ entorno "desarrollo"  → usa el código de la carpeta de trabajo, datos de
│                          prueba que se pueden borrar, bandeja de correo de
│                          prueba, puertos propios
└─ entorno "produccion"  → solo versiones publicadas (con etiqueta), datos
                           reales, claves propias, otros puertos; no se abre
                           a internet hasta la fase producción

Repositorio GitHub (público; imágenes de ghcr.io privadas)
├─ /contrato    TypeSpec ──compila──► OpenAPI 3.1 (versionado) ──► cada app
│               genera su cliente
├─ /plataforma  definición de los dos entornos: base común + ajustes por entorno
├─ /backend, /apps/comun, /apps/nativo, /apps/web, /apps/apple, /apps/android,
│  /vigia       (vacías hasta su proyecto; /apps es un espacio de trabajo
│               de Dart, app-web-02; /apps/nativo lo comparten Apple y
│               Android, app-android-02)
├─ /docs        copia de ANALISIS, DECISIONES y MAPA
├─ CLAUDE.md    reglas M1–M5, capas, contrato y convenciones
└─ CI (GitHub Actions): en cada pull request compila y valida el contrato y
                avisa si rompe compatibilidad
```

Reglas del armado:
- Una sola definición de servicios con dos capas de ajustes (desarrollo y
  producción): lo que se prueba es lo que corre.
- Producción nunca depende de la carpeta de trabajo.
- Secretos: un archivo por entorno, fuera del repositorio; en el repositorio
  solo un ejemplo sin valores.
- Cada tecnología que decida otro proyecto (base de datos, fotos, búsqueda)
  se suma como un servicio más, sin cambiar la estructura.

### Plan (fase desarrollo)

| # | Etapa | Terminado cuando… | Estado |
|---|---|---|---|
| 1 | **Repositorio** en GitHub con la estructura de carpetas, `main` protegida y el archivo de ejemplo de secretos | Un push directo a `main` se rechaza; un pull request de prueba muestra los chequeos y solo se puede mezclar con todo en verde | pendiente |
| 2 | **Docs y reglas para Claude Code**: `/docs` con los tres archivos y `CLAUDE.md` con M1–M5, capas, reglas del contrato y convenciones | Abriendo Claude Code en el repositorio y preguntando "¿qué módulos hay y qué reglas los separan?", responde lo mismo que ANALISIS §4 | pendiente |
| 3 | **Docker en la notebook**, instalado desde los repositorios oficiales y con arranque automático | Después de reiniciar, Docker está funcionando sin intervención y Compose informa versión 5.x | pendiente |
| 4 | **Dos entornos** en `/plataforma`, con un servicio de muestra y la bandeja de correo de prueba en desarrollo | Los dos levantan a la vez sin choques; borrar todo desarrollo (datos incluidos) no cambia producción; producción rechaza una versión sin etiqueta; un correo de prueba aparece en la bandeja | pendiente |
| 5 | **Esqueleto del contrato**: TypeSpec en `/contrato` con una operación de muestra ("salud") y el formato común de errores | Un comando genera un OpenAPI 3.1 que pasa el validador; el archivo generado queda versionado | pendiente |
| 6 | **CI del contrato**: en cada pull request compila, valida y compara con la última versión publicada | Un PR que agrega un campo opcional pasa; uno que borra un campo sin subir la versión mayor falla con un mensaje claro; uno que cambia el TypeSpec sin regenerar el OpenAPI falla | pendiente |

Las etapas 5 y 6 usan lo decidido en el proyecto contrato: formato de
errores (contrato-02) y herramientas (contrato-12). Las etapas 1 y 2 del
contrato las completan.

### Fase producción — decidido (2026-11-15)

#### Decisiones

| ID | Tema | Decisión |
|---|---|---|
| base-plataforma-07 | Dominio | `.com` en Cloudflare (≈ USD 11,17/año), se compra apenas se elija el nombre; paquete `com.<nombre>.recetas` (y `.dev`) |
| base-plataforma-08 | Publicación de la notebook | Túnel de Cloudflare, sin puertos abiertos; HTTPS termina en Cloudflare (excepción temporal a base-plataforma-02) |
| base-plataforma-09 | Proxy de entrada | nginx 1.30; en el VPS, certificados con su módulo ACME |
| base-plataforma-10 | Correo | Solo SMTP; textos del backend; Brevo principal + Resend de respaldo; SPF, DKIM y DMARC |
| base-plataforma-11 | Despliegue | CI → `ghcr.io` al etiquetar; `desplegar` a mano con respaldo, salud y vuelta atrás; migraciones compatibles |
| base-plataforma-12 | Respaldos | restic 0.19, diario, 30 días: disco externo en casa + copia cifrada en Backblaze B2; prueba mensual |
| base-plataforma-13 | Monitoreo | Señales HTTP (formato de Healthchecks.io) al proyecto vigía (P-20) |
| base-plataforma-14 | Mantenimiento | Parches solos, Dependabot y día mensual, firewall cerrado, sin puertos publicados, SSH con llave + Access, dos pasos, sin suspensión, batería como UPS |
| base-plataforma-15 | Disco de la notebook | Cifrado con desbloqueo por TPM 2.0 y arranque seguro |
| base-plataforma-16 | Cuándo pasar al VPS | Caída > 8 h/mes o > 24 h seguidas · velocidad · > 50 personas o desconocidos · necesidad del usuario |
| base-plataforma-17 | VPS | Hostinger KVM 1 en São Paulo (verificar precio y región al contratar) |
| base-plataforma-18 | Cuenta paga de Apple | No por ahora; se revisa con 5 pedidos |
| base-plataforma-19 | Android | Descargas en `app.<dominio>/descargas/android/`; cuenta gratuita de Google (20 aparatos) apenas existan dominio y clave; completa con más de 15 |
| base-plataforma-20 | Repositorio público | GitHub Free no protege ramas en privados: el repositorio pasa a público; commits con la dirección privada de GitHub; protección contra secretos; imágenes privadas |

#### Arquitectura (en la notebook)

```
Internet ─HTTPS─► Cloudflare: DNS · túnel · Access (SSH remoto)
                     │ túnel cifrado (conexión de salida, ningún puerto abierto)
Notebook Linux: disco cifrado con TPM 2.0 · arranque seguro · sin suspensión ·
                firewall cerrado
└─ Docker Compose, entorno "produccion" (solo imágenes con etiqueta, desde ghcr.io)
   ├─ cloudflared ── red interna de Docker (ningún contenedor publica puertos)
   ├─ entrada: nginx 1.30
   │    app.<dominio>                     → web
   │    app.<dominio>/descargas/android/  → carpeta "descargas" (APK + ultima.json)
   │    api.<dominio>                     → backend (topes 25/50 MB, CORS de backend-20)
   ├─ web: nginx con la imagen app-web-vX.Y.Z (app-web-10)
   ├─ backend: imagen backend-vX.Y.Z ──SMTP──► Brevo (principal) → Resend (respaldo)
   ├─ postgres 19
   └─ volúmenes: datos · fotos · temporales · descargas

Tareas programadas en la notebook (fuera de Docker)
   salud cada 5 min (por la dirección pública) ─┐
   respaldo diario: base + fotos + secretos     │
     + descargas + clave de Android             ├─► señales HTTP ─► vigía (fuera de casa)
     → restic → disco externo → copia a B2      │     └─► Telegram y correo
   prueba de restauración mensual               │
   control de discos diario (70 %)  ───────────┘

GitHub: CI → al etiquetar, arma y sube imágenes a ghcr.io · Dependabot abre PR
desplegar <proyecto> <versión>: respaldo → bajar imagen → levantar → salud →
                                si falla, vuelve a la anterior y avisa
```

**En el VPS** (cuando se cumpla un criterio de base-plataforma-16): la misma
definición, sin `cloudflared`; nginx saca los certificados de Let's Encrypt
con su módulo ACME y solo quedan abiertos los puertos 80 y 443; tráfico
directo (base-plataforma-02). Los respaldos salen del VPS a B2 y la
notebook de la casa copia todos los días de B2 al disco externo.

Reglas del armado (fase producción):
- Ningún contenedor publica puertos hacia afuera; todo entra por el proxy
  de entrada.
- Contenedores sin permisos de administrador y, donde se pueda, en solo
  lectura; ninguno con acceso al control de Docker.
- Los servicios externos (correo, vigía, registro de imágenes, destino de
  respaldos) se cambian por configuración, sin tocar código.
- Las contraseñas críticas (repositorio de restic, clave de Android,
  secretos) viven también en un gestor de contraseñas fuera de la notebook.

#### Plan (fase producción)

| # | Etapa | Terminado cuando… | Necesita antes | Estado |
|---|---|---|---|---|
| 1 | **Dominio y cuentas** | El dominio está comprado y responde con el DNS de Cloudflare, con DNSSEC activo. Verificación en dos pasos activa en GitHub, Cloudflare, Backblaze, Brevo, Resend y Google. Las contraseñas críticas están en el gestor. `com.<nombre>.recetas` figura en el repositorio | — | pendiente |
| 2 | **Notebook como servidor** | Después de reiniciar vuelve todo sin tocar nada (el disco se abre con TPM). Cerrar la tapa no la suspende. Un escaneo desde afuera no encuentra ningún puerto abierto. SSH con contraseña se rechaza. Sin cargador 10 minutos sigue andando; con menos del 10 % de batería se apaga ordenada. Al volver la luz arranca sola (si el BIOS lo permite; si no, queda anotado) | base-plataforma 3 (desarrollo) | pendiente |
| 3 | **Publicación** | `app.<dominio>` carga y `api.<dominio>/v1/salud` responde 200 por HTTPS. La cookie `__Host-` funciona entre la web y la API. Un pedido con credenciales desde otro origen se rechaza. Las fotos traen permiso CORS solo para `app.`. Un pedido de 60 MB recibe 413 y una parte de 50 MB pasa. Un `ultima.json` de prueba se baja de `app.<dominio>/descargas/android/` sin sesión y sin caché. SSH desde afuera por Access pide entrar. Ningún contenedor publica puertos | backend 1; app-web 1 | pendiente |
| 4 | **Correo** | El correo de verificación llega a la bandeja de entrada de Gmail y de Outlook, no a spam. Sus encabezados muestran SPF, DKIM y DMARC aprobados. Desactivando Brevo, el mismo correo sale por Resend | backend 2 | pendiente |
| 5 | **Despliegue** | Una etiqueta arma y sube la imagen a `ghcr.io`. `desplegar backend vX` la deja corriendo. Una versión de prueba que falla la salud vuelve sola a la anterior y avisa. Producción rechaza imágenes sin etiqueta. La CI falla con libvips anterior a 8.18.3. Dependabot abre su primer PR | etapa 3 | pendiente |
| 6 | **Respaldos** | Hay copia diaria en el disco y en B2, y quedan solo 30 días. Borrar desde la notebook no elimina de inmediato lo que hay en B2. Restauración completa en otra máquina: la plataforma anda con los datos de prueba y la clave de Android restaurada firma con la misma huella. La prueba mensual automática pasa | etapa 5 | pendiente |
| 7 | **Vigilancia** | Con el vigía en marcha: apagar el backend da aviso en menos de 10 minutos; cortar internet en casa, también; saltear un respaldo da aviso en 26 h; un disco de prueba arriba del 70 % da aviso; el resumen diario llega todos los días | **vigía etapas 1–5** | pendiente |
| 8 | **Cuenta de Google y apertura** | La cuenta gratuita de Google tiene registrado `com.<nombre>.recetas` con un APK firmado con la clave de producción, y los aparatos propios están autorizados. Lista de apertura completa: backend 8, app-web 6, etapas 1 a 7 de esta fase y vigía listo → **entra la primera persona** | etapas 1–7; backend 8; app-web 6; app-android 1 (script de firma) | pendiente |
| — | **Mudanza al VPS** (cuando se cumpla un criterio) | Hostinger KVM 1 en São Paulo contratado. Plataforma restaurada desde el respaldo. El DNS apunta al VPS con Let's Encrypt; el túnel apagado; solo los puertos 80 y 443 abiertos. Respaldos del VPS a B2 y de B2 al disco de la casa. El vigía no registra cortes durante el cambio | etapa 8 | pendiente |

---

## vigia — decidido (2026-11-15)

Proyecto nuevo (P-20). Vigila la plataforma **desde fuera del lugar donde
corre** y avisa por Telegram y correo. Abrir a la primera persona espera a
que esté listo. Se decidió en la misma conversación que la fase producción,
como excepción a "un proyecto por conversación".

### Decisiones

| ID | Tema | Decisión |
|---|---|---|
| vigia-01 | Dónde y en qué lenguaje | Go 1.27 en Docker sobre una e2-micro gratis de Google Cloud (EE. UU.), sin IP fija, alerta de gasto de USD 1; `vigia.<dominio>` con nginx + Let's Encrypt, fuera de Cloudflare |
| vigia-02 | Estado | Controles en `/vigia/controles.yaml` **sin claves** (las claves van al archivo de secretos del vigía, base-plataforma-20); estado e historial (30 días) en SQLite; copia diaria a B2 |
| vigia-03 | Avisos | Telegram + correo (Brevo → Resend, credenciales propias); aviso al instante, recordatorio cada 2 h, "se recuperó", resumen diario 8:30 |
| vigia-04 | Arquitectura, señales y despliegue | Dominio puro + casos de uso + puertos y adaptadores; `POST /s/<clave>` y `/s/<clave>/falla` (formato Healthchecks.io); `vigia-vX.Y.Z` en `ghcr.io` + `desplegar` |

### Arquitectura

```
Google Cloud e2-micro (región gratis de EE. UU.) · disco estándar de 30 GB · alerta de gasto de USD 1
└─ Docker Compose
   ├─ nginx + Let's Encrypt     vigia.<dominio> (en Cloudflare solo DNS, sin proxy)
   └─ vigia (Go, imagen vigia-vX.Y.Z desde ghcr.io)
      ├─ recibe señales   POST /s/<clave>          "anduvo"
      │                   POST /s/<clave>/falla    "falló" + texto corto
      ├─ revisa cada 5 min  api.<dominio>/v1/salud y app.<dominio>
      ├─ decide          ¿venció la señal? ¿avisar o recordar? ¿se recuperó?
      ├─ avisa           Telegram (bot propio) + correo SMTP: Brevo → Resend
      ├─ resumen diario  8:30 (Asunción): estado y horas de caída del mes
      └─ estado          SQLite (volumen) → copia diaria con restic a B2

/vigia
├─ controles.yaml   la lista de controles, sin claves (sumar uno = un pull request)
├─ dominio/         vencimiento, margen, recordatorio y recuperación (puro)
├─ casosdeuso/      recibir señal · revisar controles · mandar resumen
├─ puertos/         reloj · almacén · revisión HTTP · avisos
└─ adaptadores/     sqlite · http · telegram · smtp
```

Reglas propias:
- El dominio no importa SQLite ni HTTP; la CI lo verifica.
- Cada control tiene una clave al azar en su dirección; una clave
  desconocida responde 404. Las claves viven en el archivo de secretos del
  vigía, nunca en el repositorio (es público).
- No guarda datos de personas ni secretos de la plataforma.
- El servidor lleva el paquete de mantenimiento de base-plataforma-14: solo
  80 y 443 abiertos, SSH con llave, parches automáticos.
- Sin IP fija (se cobra): si la IP cambia (solo al apagar el servidor), se
  actualiza el DNS.

### Plan

| # | Etapa | Terminado cuando… | Necesita antes | Estado |
|---|---|---|---|---|
| 1 | **Esqueleto y reglas** | `/vigia` con el dominio y sus pruebas: una señal a tiempo no avisa; una vencida avisa una sola vez; a las 2 h recuerda; al volver avisa "se recuperó"; una falla explícita avisa al instante. La CI **falla** si el dominio importa SQLite o HTTP | base-plataforma 1 (repositorio) | pendiente |
| 2 | **Servidor en Google Cloud** | e2-micro en una región gratis con disco estándar y alerta de gasto de USD 1. `https://vigia.<dominio>/salud` responde 200. Un escaneo desde afuera ve solo 80 y 443. La primera factura dice USD 0 | producción etapa 1 (dominio) | pendiente |
| 3 | **Señales y revisiones** | Los controles se cargan de `controles.yaml`. Una señal con clave válida se registra y una inválida da 404. Con la API de desarrollo apagada, la revisión lo detecta. Al reiniciar no se pierde el estado | etapa 2 | pendiente |
| 4 | **Avisos** | Ante una caída simulada llegan Telegram **y** correo en menos de 10 min, el recordatorio a las 2 h y el "se recuperó" al volver. El resumen llega a las 8:30. Desactivando Brevo, el correo sale por Resend | etapa 3 | pendiente |
| 5 | **Conexión con la plataforma y cierre** | Los scripts de la notebook mandan sus señales. Se cumplen los criterios de la etapa 7 de producción. El estado se restaura desde B2. `vigia-v1.0.0` corre desplegado con `desplegar` | producción etapas 3 y 6 | pendiente |

---

## contrato — decidido (2026-11-14)

### Decisiones

| ID | Tema | Decisión |
|---|---|---|
| contrato-01 | Versionado | Mayor en la dirección (`/v1/`); 90 días de convivencia con `Deprecation`/`Sunset`; aviso "actualizá la app" (`App-Version`) |
| contrato-02 | Errores | Problem Details (RFC 9457) + `codigo`, `errores[]`, `idPedido`; privada ajena → 404 |
| contrato-03 | Conflictos | `ETag` + `If-Match` → 412 / 428; `If-None-Match` → 304; reemplazo completo; perfil: gana el último |
| contrato-04 | Idempotencia | Favoritos PUT/DELETE; `Idempotency-Key` en todo lo que crea (24 h) |
| contrato-05 | Paginación | Cursor opaco: `limite`, `cursor` → `{ elementos, siguiente }` (máx. 100) |
| contrato-06 | Cambios desde | Una consulta por módulo (Perfil, Recetas, Colecciones, Favoritos); lápidas 90 días; 410 |
| contrato-07 | Subidas | Foto en un pedido (≤ 25 MB); importar en partes (≤ 50 MB c/u); exportar en segundo plano con enlace de 24 h |
| contrato-08 | Fotos | Direcciones firmadas que vencen a las 6 h |
| contrato-09 | Sesión | Token opaco: `Bearer` en apps nativas, cookie segura en la web |
| contrato-10 | Nombres y formatos | Español camelCase; RFC 3339 UTC; UUIDv7; cantidades decimales o `null`; redondeo P-18 |
| contrato-11 | Clientes | Cada app genera el suyo al compilar; no se guarda; cliente de prueba en la CI |
| contrato-12 | Herramientas | oasdiff · Redocly CLI · Prism · Schemathesis 4.x |
| contrato-13 | Casos del escalado | `/contrato/casos/escalado.json`, compartido por backend y apps |
| contrato-14 | Formatos de foto | La subida acepta JPEG, PNG y WebP; error `foto.formato_no_soportado` (422) (de backend-07) |

### Arquitectura

```
/contrato
├─ tspconfig.yaml        TypeSpec 1.18 → OpenAPI 3.1
├─ src/
│  ├─ main.tsp           servicio, versión v1 y las dos formas de sesión
│  ├─ comun/             convenciones escritas una vez y reusadas
│  │   errores.tsp       Problem Details + códigos propios + tabla HTTP
│  │   paginacion.tsp    cursor opaco: elementos + siguiente
│  │   concurrencia.tsp  ETag / If-Match → 412 / 428; If-None-Match → 304
│  │   idempotencia.tsp  Idempotency-Key → 400 / 409 / 422
│  │   cambios.tsp       "…/cambios?desde=" con lápidas y 410
│  │   tipos.tsp         Id (UUIDv7), FechaHora (RFC 3339), Cantidad, Idioma
│  │   versionado.tsp    App-Version, Deprecation / Sunset
│  └─ modulos/           un archivo por módulo con operaciones (refleja M1)
│      identidad · perfil · recetas · colecciones · favoritos · medios · respaldo
├─ openapi/v1/openapi.yaml   generado y versionado; nunca se edita a mano
├─ casos/escalado.json       casos de prueba de R8/R9 (backend y 3 apps)
├─ ejemplos/                 ejemplos por operación (Prism y documentación)
└─ reglas/redocly.yaml       reglas de estilo propias
```

Notificaciones no tiene archivo porque no tiene operaciones públicas.

**En cada pull request que toca `/contrato`, la CI:**
1. Compila TypeSpec y falla si el OpenAPI generado no coincide con el
   guardado.
2. Corre Redocly con las reglas propias.
3. Corre oasdiff contra la última etiqueta `contrato-v1.*` y falla si algo
   rompe sin subir la versión mayor.
4. Genera un cliente de prueba con openapi-generator y falla si no lo puede
   generar.
5. Revisa el formato de `casos/escalado.json`.

**Al publicar una etiqueta**: el OpenAPI queda disponible para las apps;
Prism corre como servicio del entorno desarrollo (en `/plataforma`);
Schemathesis queda listo para la CI del backend.

### Plan

Las etapas 1 y 2 van después de las etapas 5 y 6 de base-plataforma.

| # | Etapa | Terminado cuando… | Estado |
|---|---|---|---|
| 1 | **Convenciones comunes** en TypeSpec | Una operación de muestra usa cada plantilla, y el OpenAPI generado muestra: errores en `problem+json`, una lista con `cursor` y `siguiente`, un guardado con `If-Match` y las respuestas 412 y 428, una creación con `Idempotency-Key` y las dos formas de sesión. Pasa el validador | pendiente |
| 2 | **Reglas de estilo y CI completa** | En pull requests de prueba, cada uno de estos casos falla con un mensaje claro: un campo en inglés o con tilde; una lista sin paginación; una creación sin `Idempotency-Key`; un campo borrado sin subir la versión mayor; un OpenAPI que el generador de prueba no entiende. Un pull request correcto pasa | pendiente |
| 3 | **Identidad y Perfil** (CU01–CU06, más los cambios de Perfil) | Cada caso de uso tiene su operación con ejemplos. Un chequeo automático confirma que solo registrarse, entrar, pedir código y recuperar acceso van sin sesión, y que `correo` aparece únicamente en los datos de la propia cuenta (V9) | pendiente |
| 4 | **Recetas y Medios** (CU07–CU15, CU17, CU18, más los cambios de Recetas) y casos del escalado | Cada caso de uso tiene su operación con ejemplos. `casos/escalado.json` tiene al menos 15 casos (cantidades vacías, factor menor y mayor que 1, redondeo). El ejemplo de una receta privada ajena responde 404. Las fotos traen direcciones firmadas con `vencenEn`. La subida de fotos declara JPEG, PNG y WebP y el error `foto.formato_no_soportado` (contrato-14) | pendiente |
| 5 | **Colecciones y Favoritos** (CU16, CU21–CU23, más sus cambios) | Cada caso de uso tiene su operación con ejemplos. Hay ejemplos de "no disponible" (K4, K6, F3). Marcar un favorito es "poner" y desmarcarlo, "quitar" | pendiente |
| 6 | **Respaldo** (CU19, CU20) | Los dos flujos están completos con ejemplos: exportar y descargar; subir en partes, importar y ver el resumen (E9). Una regla de estilo verifica que la contraseña viaje solo en el contenido y que ninguna parte supere los 50 MB | pendiente |
| 7 | **Servidor de prueba, verificación y publicación de v1.0.0** | Con el entorno desarrollo levantado, Prism responde todas las operaciones con sus ejemplos. Schemathesis corre contra Prism sin errores. La documentación navegable se abre. Existe la etiqueta `contrato-v1.0.0` y la CI compara contra ella | pendiente |

---

## backend — decidido (2026-11-14)

### Decisiones

| ID | Tema | Decisión |
|---|---|---|
| backend-01 | Estilos por módulo | DDD táctico (Identidad, Recetas, Colecciones) · script de transacción (Perfil, Favoritos, Medios, Notificaciones) · núcleo funcional (Respaldo); reglas comunes; sin event sourcing |
| backend-02 | Eventos | Outbox con repartidor en el mismo proceso; catálogo común de eventos; receptores que toleran repetidos |
| backend-03 | Visibilidad entre módulos | El dueño decide, los demás preguntan en lote; "no disponible" al leer (cambia la redacción de M3) |
| backend-04 | Lenguaje y framework | Go 1.27 + `net/http` + oapi-codegen 2.8 |
| backend-05 | Base de datos | PostgreSQL 19 + pgx + sqlc; un esquema y un usuario por módulo, más `infra` |
| backend-06 | Fotos: dónde y cómo | Disco propio, servidas por el backend con firma; sin CDN por ahora |
| backend-07 | Fotos: formatos | Entran JPEG, PNG y WebP; se guardan en WebP 400 y 1600 px, sin metadatos |
| backend-08 | Búsqueda | Dentro de PostgreSQL: unaccent + pg_trgm + texto completo |
| backend-09 | Cifrado de respaldos | age 1.3 dentro de un sobre propio; contraseña ≥ 12; importar en dos pasadas; un trabajo a la vez |
| backend-10 | Idempotencia | Entrada HTTP común, esquema `infra`, 24 h |
| backend-11 | Cambios y lápidas | Tabla por módulo en la misma transacción; cursor con contador por persona; lápidas 90 días |
| backend-12 | Trabajos y outbox | River en PostgreSQL; los respaldos corren aparte, en memoria |
| backend-13 | Subida en partes | Partes en disco, con suma de verificación; 24 h; tope 5 GB |
| backend-14 | Secretos en registros | Tipo "secreto" + prueba automática |
| backend-15 | Sesión | Token de 256 bits, se guarda su huella; 30 días (web) / 90 (nativas) sin uso; cookie `__Host-` |
| backend-16 | Contraseñas y códigos | Argon2id (OWASP); códigos de 6 dígitos, 10 min, 5 intentos; límites C6 en la base |
| backend-17 | Migraciones | goose 3.29, por esquema, al arrancar con bloqueo; compatibles con la versión anterior (base-plataforma-11) |
| backend-18 | Pruebas de arquitectura | Prueba propia del grafo de paquetes + linter; `internal/` por módulo |
| backend-19 | Verificación contra el contrato | Schemathesis contra el backend real en la CI; `escalado.json`; código generado no se guarda |
| backend-20 | CORS para la web | Fotos con permiso CORS simple solo para el origen de la web; credenciales solo desde ese origen; configurable por entorno (de app-web-01) |

Desde la fase producción, el adaptador de correo de Notificaciones es SMTP
con un servidor principal (Brevo) y uno de respaldo (Resend), configurables
(base-plataforma-10).

### Arquitectura

**Estilo por módulo y dependencias** (las consultas van siempre hacia abajo;
los eventos van por el catálogo común, así que no crean ciclos):

```
Consultas (por interfaz pública)
  Respaldo ─────► Recetas, Colecciones, Favoritos, Medios     núcleo funcional
  Favoritos ────► Recetas, Colecciones                        script
  Colecciones ──► Recetas, Perfil                             DDD táctico
  Recetas ──────► Perfil (nombre visible), Medios (guardar y firmar)  DDD táctico
  Identidad ────► Notificaciones                              DDD táctico
  Perfil · Medios · Notificaciones → no dependen de nadie     script

Eventos (outbox con River)
  CuentaCreada                  Identidad   → Perfil
  CuentaBorrada                 Identidad   → todos (borran o ceden, C4)
  RecetaBorrada                 Recetas     → Medios, Colecciones, Favoritos
  VisibilidadCambiada           Recetas     → Colecciones, Favoritos
  ColeccionBorrada /
  VisibilidadColeccionCambiada  Colecciones → Favoritos
```

**Estructura de `/backend`:**

```
/backend
├─ cmd/servidor        arranque: configuración, base, migraciones, HTTP y trabajos
├─ api/                código generado por oapi-codegen desde el OpenAPI
│                      (se genera al compilar; no se guarda ni se edita)
├─ compartido/         tipos comunes (Id, FechaHora, Cantidad) y catálogo de
│                      eventos, sin lógica
├─ infra/              entrada HTTP común: sesión, errores problem+json, idPedido,
│                      App-Version, origen y CORS (backend-20), idempotencia
│                      (esquema "infra"); River; registros con secretos
│                      ocultos; reloj
├─ modulos/
│  └─ <modulo>/        identidad, perfil, recetas, colecciones, favoritos,
│     │                medios, respaldo, notificaciones
│     ├─ interfaz      lo ÚNICO que otro módulo puede usar
│     └─ internal/     Go impide que otro módulo importe esto (M2)
│        ├─ dominio/        agregados, o tipos + funciones puras (backend-01)
│        ├─ casosdeuso/     una carpeta por caso de uso
│        ├─ consultas/      lecturas aparte de las escrituras
│        ├─ puertos/
│        ├─ adaptadores/    http, postgres (sqlc), disco, age, correo…
│        └─ migraciones/    goose, sobre su propio esquema
└─ pruebas/            arquitectura (M1–M5), contrato (Schemathesis), carga
```

**Servicios que se suman a `/plataforma`** (base-plataforma-03):
- `backend`: una imagen y un proceso (API + trabajos).
- `postgres` 19: un esquema y un usuario por módulo, más `infra`.
- Volúmenes: datos de la base, fotos y respaldos temporales (partes y
  exportaciones).
- Memoria estimada: PostgreSQL unos cientos de MB y el backend menos de
  150 MB, más ~256 MB mientras corre un respaldo. Entra holgado en 4 GB.

### Plan

Cada etapa del backend va detrás de la etapa del contrato que define sus
operaciones. El orden sigue el sugerido: Identidad + Notificaciones →
Perfil → Recetas → Medios → Colecciones → Favoritos → Respaldo.

| # | Etapa | Terminado cuando… | Necesita antes | Estado |
|---|---|---|---|---|
| 1 | **Esqueleto y plataforma interna**: módulo Go, infra común, PostgreSQL 19 en los dos entornos, goose por esquema, River, registros, pruebas de arquitectura y CI | `/v1/salud` responde 200 en desarrollo. Un error de prueba sale como `problem+json` con `idPedido`. La CI **falla** en PRs de prueba que: (a) hacen que un dominio importe pgx, (b) importan el `internal/` de otro módulo, (c) crean un ciclo entre módulos. Una prueba confirma que el usuario de un módulo **no puede leer** el esquema de otro | base-plataforma 1–4; contrato 1 | pendiente |
| 2 | **Identidad + Notificaciones** (CU01–CU04) | El correo de verificación aparece en la bandeja de prueba. Se entra con contraseña y con código. El 6.º intento en 15 min devuelve 429. "Cerrar todas" deja afuera a otro dispositivo en el pedido siguiente. Una prueba recorre la base y no encuentra ningún token, código ni contraseña en claro. La cookie sale con `__Host-`, `HttpOnly`, `Secure` y `SameSite=Strict`. Un pedido desde otro origen se rechaza; uno con credenciales desde el origen de la web del entorno se acepta (backend-20) | contrato 3 | pendiente |
| 3 | **Perfil y "cambios desde"** (CU05) | Una preferencia guardada en un dispositivo llega a otro por `cambios?desde=`. Lo borrado aparece como lápida. Un cursor de más de 90 días recibe 410. Dos guardados al mismo tiempo dejan el último (contrato-03) | contrato 3 | pendiente |
| 4 | **Recetas** (CU07–CU15, CU17) | Romper R1 o R2 devuelve 422 con su código. Un `If-Match` viejo devuelve 412 con la versión actual; sin `If-Match`, 428; sin cambios, 304. Una receta privada ajena devuelve 404. Repetir la `Idempotency-Key` crea una sola receta. Pasan todos los casos de `escalado.json`. "azucar" encuentra "Azúcar" por nombre, tag e ingrediente. Una receta que pasa a privada **no aparece** en la búsqueda pública siguiente | contrato 4 | pendiente |
| 5 | **Medios** (CU18) | Un HEIC recibe `foto.formato_no_soportado`. Un JPEG con GPS queda guardado **sin metadatos**. Se generan los dos tamaños WebP. Una dirección vencida se rechaza. La 11.ª foto devuelve 422 (R10). Una imagen con demasiados megapíxeles se rechaza sin agotar la memoria. Al borrar la receta, los archivos desaparecen del disco, con reintento si falla el primer intento. Una foto pedida desde el origen de la web trae el permiso CORS; desde otro origen, no (backend-20) | contrato 4 | pendiente |
| 6 | **Colecciones + Favoritos** (CU16, CU21–CU23) | Las privadas del dueño se ven "no disponible" para los demás (K4). Marcar dos veces deja un solo favorito (F2). Al borrar una receta, el favorito queda "no disponible" y aparece en "cambios desde". Una colección cuya receta pasó a privada **no** responde 304. Publicar con recetas privadas (K5) se puede repetir sin daño | contrato 5 | pendiente |
| 7 | **Respaldo** (CU19, CU20) | Se exporta, se baja y se importa en la misma cuenta (agrega y actualiza) y en otra (ids nuevos, privadas, referencias actualizadas). Con **un byte cambiado** o el archivo cortado, no se importa nada. Un respaldo de 1 GB se sube en partes, se corta y se retoma. La prueba de "contraseña en los registros" pasa. Si el backend se reinicia durante una exportación, el trabajo queda "falló". `age -d` con la contraseña sola **no** abre el archivo | contrato 6 | pendiente |
| 8 | **Borrado de cuenta y cierre** (CU06, C4, C5) | Después de borrar una cuenta, una prueba que recorre todos los esquemas no encuentra nada de ella, salvo lo cedido al autor anónimo. Schemathesis contra el backend real termina sin errores. Con datos de **10.000 personas simuladas**, abrir una receta y buscar tardan menos de 1 s en la notebook (en 95 de cada 100 pedidos). La etiqueta `backend-v1.0.0` corre en el entorno producción | contrato 7 (v1.0.0) | pendiente |

---

## app-web — decidido (2026-11-14)

### Decisiones

| ID | Tema | Decisión |
|---|---|---|
| app-web-01 | Tecnología | Flutter 3.50 (Dart 3.14) + Riverpod 3; SPA, sin SSR; Flutter queda como candidata para Apple y Android |
| app-web-02 | Organización del código | `/apps` espacio de trabajo de Dart: `/apps/comun` compartido + un proyecto por app |
| app-web-03 | Cliente generado | swagger_parser 1.44 + retrofit 4.9 + dio 5.9; se genera al compilar; cookie con `withCredentials` |
| app-web-04 | Rutas | go_router 18 |
| app-web-05 | Textos | gen-l10n (ES/EN); idioma del Perfil; errores con texto propio por `codigo` |
| app-web-06 | Compilación | Wasm con respaldo en JavaScript |
| app-web-07 | Servidor de archivos | ~~Caddy 2.11 en el contenedor de la web~~ · *Reemplazada por app-web-10* |
| app-web-08 | Diseño adaptable | Dos paneles desde 840 px; uno por debajo |
| app-web-09 | Prueba de riesgo | ≤ 3 s la primera carga y ≤ 1 s las siguientes (red a 10 Mbps); si no se cumple, se vuelve a SvelteKit |
| app-web-10 | Servidor de archivos | nginx 1.30 en el contenedor de la web, con imagen propia `app-web-vX.Y.Z` (de base-plataforma-09) |

### Arquitectura

```
/apps                   espacio de trabajo de Dart (app-web-02)
├─ comun/               paquete compartido por web, Apple y Android
│  ├─ escalado/         R8/R9: función pura + /contrato/casos/escalado.json
│  ├─ <funcionalidad>/  cuenta, perfil, recetas, colecciones, favoritos,
│  │  │                 fotos, respaldo, sincronizacion
│  │  ├─ dominio/       datos que muestra la app y puertos (sin reglas de negocio)
│  │  ├─ casosdeuso/    orquestan: guardar con If-Match, favorito al instante…
│  │  ├─ datos/         adaptadores: usan el cliente generado y traducen
│  │  │                 304 / 412 / 428 / problem+json a errores propios
│  │  └─ presentacion/  providers de Riverpod + pantallas adaptables (1 o 2 paneles)
│  ├─ api/              cliente generado (swagger_parser): se genera al compilar,
│  │                    no se guarda ni se edita
│  ├─ textos/           ES/EN (gen-l10n)
│  └─ diseno/           tema según color y modo del Perfil; anchos de pantalla
└─ web/                 proyecto Flutter solo para la web
   ├─ arranque          configuración por entorno (dirección de la API) y
   │                    adaptadores del navegador
   ├─ rutas/            go_router; sin sesión → "entrar"
   ├─ plataforma/       adaptadores del navegador: sesión con cookie (la web
   │                    nunca ve el token) · elegir foto (aviso HEIC en Chrome
   │                    y Firefox) · respaldo en partes de 50 MB · descarga de
   │                    la exportación · sin conexión · App-Version y "hay una
   │                    versión nueva"
   └─ contenedor        compila con Flutter (Wasm + JS) y sirve con nginx
                        (app-web-10)
```

**Reglas internas:**
- El dominio y los casos de uso de `comun` no importan Flutter, Riverpod,
  dio ni el cliente generado; una prueba en la CI lo verifica (como
  backend-18).
- Las pantallas usan casos de uso a través de providers; nunca llaman al
  cliente.
- Sin reglas de negocio salvo el escalado (R9): las validaciones en pantalla
  son solo ayuda; manda el 422 del backend, mostrado según su `codigo`.
- La sesión es un puerto con dos adaptadores: cookie en la web
  (contrato-09) y almacén seguro con `Bearer` en las apps nativas.
- Cada acción que crea algo genera su `Idempotency-Key` y la reusa si se
  reintenta (contrato-04).
- Cada receta y colección guarda su `ETag` en memoria; un 412 abre
  "conservar la mía / quedarme con la otra" (contrato-03).
- Si las fotos de una receta vencieron (`vencenEn`, 6 h), la app la vuelve a
  pedir **sin** `If-None-Match`: un 304 dejaría las direcciones viejas
  (contrato-08).
- "Cambios desde": el cursor de cada módulo vive en memoria mientras la
  pestaña está abierta (no hay caché, P-03); un 410 recarga todo
  (contrato-06).
- Cada pedido manda `App-Version` y `Accept-Language` (contrato-01,
  contrato-10).

**CI de la web** (en cada pull request que toca `/apps` o `/contrato`):
genera el cliente desde el OpenAPI, analiza el código, corre las pruebas
(`escalado.json`, casos de uso con puertos falsos, pantallas en ES/EN y en
1 y 2 paneles) y la prueba de arquitectura, y compila la web.

**Servicio que se suma a `/plataforma`**: `web`. En desarrollo, Flutter con
recarga en caliente; en producción, solo la imagen etiquetada
(`app-web-vX.Y.Z`) con nginx sirviendo los archivos (app-web-10), detrás
del nginx de entrada. La web y la API van en orígenes distintos del mismo
sitio en los dos entornos, como en la fase producción (`app.<dominio>` y
`api.<dominio>`).

### Plan

| # | Etapa | Terminado cuando… | Necesita antes | Estado |
|---|---|---|---|---|
| 1 | **Esqueleto y prueba de riesgo**: `/apps` con `comun` y `web`, cliente generado al compilar, contenedor en los dos entornos, entrar y salir | Un PR de prueba **falla** si guarda el cliente generado o si un caso de uso de `comun` importa Flutter, Riverpod o dio. En desarrollo, con la web y la API en orígenes distintos, se entra con contraseña en Chrome, Firefox y Safari, y el token no aparece en ningún almacenamiento del navegador. Con la red limitada a 10 Mbps, la primera carga tarda ≤ 3 s y las siguientes ≤ 1 s (app-web-09); si no se cumple, se vuelve a SvelteKit antes de seguir | base-plataforma 1–4; contrato 1; backend 1–2 | pendiente |
| 2 | **Cuenta y perfil** (CU01–CU05) | El correo de verificación aparece en la bandeja de prueba y su enlace deja la cuenta lista. El 6.º intento muestra "demasiados intentos" en el idioma elegido. Un cambio de idioma, color o modo oscuro en un navegador se ve en otro al refrescar. "Cerrar todas" manda al otro navegador a "entrar" en su pedido siguiente. Sin red aparece el aviso y "reintentar" recupera. Con una `App-Version` menor que la mínima aparece "hay una versión nueva" y recargar la trae | backend 2–3 | pendiente |
| 3 | **Recetas, fotos y escalado** (CU07–CU15, CU17, CU18) | Pasan todos los casos de `escalado.json` y escalar no hace pedidos. Editando la misma receta en dos navegadores, el segundo elige "conservar la mía" o "quedarme con la otra". Un doble clic en "guardar" crea una sola receta. Un HEIC en Chrome o Firefox muestra el aviso y no se sube. Las fotos se ven (backend-20) y una pestaña abierta más de 6 h las sigue mostrando. Aparecen los avisos de ciclos (R6), de receta usada (R7) y de publicar con privadas (V6). "azucar" encuentra "Azúcar". Un panel en celular y dos desde 840 px | backend 4–5 | pendiente |
| 4 | **Colecciones y favoritos** (CU16, CU21–CU23) | Marcar un favorito se ve al instante y, si el backend falla, se desmarca con un aviso. Lo que no se puede ver aparece "no disponible" (K4, K6, F3). Se reordena una colección y se publica con recetas privadas (K5). Refrescar usa "cambios desde" (4 pedidos en paralelo); con un 410 se recarga todo | backend 6 | pendiente |
| 5 | **Respaldo y borrado de cuenta** (CU06, CU19, CU20) | Exportar pide la contraseña dos veces (12 caracteres o más) con el aviso de que no se recupera, y el archivo se baja con su enlace. Un respaldo de 1 GB se sube en partes de 50 MB; si se corta la red, sigue sin repetir lo ya subido; al terminar se ve el resumen (E9). La contraseña no queda en el navegador (almacenamiento, historial ni direcciones). Antes de borrar la cuenta se ofrecen el respaldo y el autor anónimo, y después la app vuelve a "entrar" | backend 7–8 | pendiente |
| 6 | **Cierre y publicación** | La CI recorre los flujos principales en Chrome y Firefox contra el backend real; Safari se prueba a mano en la Mac. Los formularios se usan con teclado y lector de pantalla. Con caché, una receta abre en menos de 1 s. La etiqueta `app-web-v1.0.0` corre en el entorno producción | contrato 7; backend 8 | pendiente |

---

## app-apple — decidido (2026-11-15)

### Decisiones

| ID | Tema | Decisión |
|---|---|---|
| app-apple-01 | Tecnología | Flutter 3.50 (Dart 3.14) + Riverpod 3 con `/apps/comun`; `/apps/apple` suma solo lo propio de Apple (confirma app-web-01) |
| app-apple-02 | Distribución y firma | Firma gratuita, solo en los aparatos propios (reinstalar cada 7 días en iPhone/iPad); los demás usan la web; cuenta paga: no por ahora, se revisa con 5 pedidos (base-plataforma-18) |
| app-apple-03 | Mac | App propia de macOS (destino macOS de Flutter), no la de iPad en la Mac |
| app-apple-04 | Versiones y variantes | Xcode 27 (Swift 6.4) en Mac con chip Apple; desde iOS 15 / macOS 12, se prueba en iOS 27 / macOS 27; iPad en el simulador; variantes desarrollo y producción |
| app-apple-05 | Aspecto | Material 3 con toques de Apple; Liquid Glass cuando Flutter lo publique |
| app-apple-06 | Sesión | Bearer con flutter_secure_storage 10.3; en la Mac, llavero de inicio de sesión mientras haya firma gratuita |
| app-apple-07 | Fotos y HEIC | image_picker + flutter_image_compress 2.5: HEIC a JPEG sin metadatos en el aparato, máx. 3200 px y 25 MB |
| app-apple-08 | Compilación y CI | CI en Linux (pruebas de Dart, sin compilar para Apple) + script en la Mac que compila, firma e instala |
| app-apple-09 | Prueba de riesgo | ≤ 2 s al abrir, 200 recetas sin cuadros lentos, HEIC de 48 MP sin ubicación, VoiceOver, teclado y dictado, y tu visto bueno; si no, Swift/SwiftUI |

Desde app-android-02, la sesión, las fotos, el respaldo, la conexión y la
versión se escriben en `/apps/nativo`, compartido con Android; las
decisiones de Apple no cambian.

### Arquitectura

```
/apps                    espacio de trabajo de Dart (app-web-02)
├─ comun/                igual que en la web: cliente generado, casos de uso,
│                        escalado, estado, textos, diseño y pantallas.
│                        Solo suma la variante de diseño con toques de Apple
├─ nativo/               adaptadores compartidos con Android (app-android-02):
│                        sesión Bearer, fotos, respaldo, conexión y versión
│                        (detalle en app-android)
├─ web/
└─ apple/                proyecto Flutter con destinos iOS (iPhone y iPad) y macOS
   ├─ arranque           variante "desarrollo" (API de la notebook por la red de
   │                     la casa) o "producción" (solo HTTPS a api.<dominio>);
   │                     conecta los adaptadores de nativo y los propios
   ├─ rutas/             go_router 18, como la web, sin barra de direcciones
   ├─ plataforma/        lo que solo existe en Apple:
   │  ├─ llavero         en la Mac, nativo/sesion usa el llavero de inicio de
   │  │                  sesión (app-apple-06)
   │  ├─ fotos           en la Mac, solo elegir archivo (no hay cámara)
   │  └─ version         aviso "hay una versión nueva: instalala desde la Mac"
   ├─ mac/               menú nativo con atajos (⌘N, ⌘F, ⌘R) y tamaño mínimo
   │                     de ventana
   ├─ ios/ · macos/      proyectos de Xcode que genera Flutter: permisos de fotos,
   │                     cámara y red; en la Mac, sandbox (la app solo toca sus
   │                     archivos y los que elige la persona)
   ├─ test/              pruebas de los adaptadores con dobles
   └─ herramientas/      script de la Mac: genera el cliente, compila, firma e
                         instala en iPhone, simulador de iPad y Mac
```

**Reglas propias de Apple** (las reglas internas de `comun`, descritas en
app-web, valen igual):
- La sesión viaja en `Authorization: Bearer` y, al entrar, la app avisa que
  es nativa (contrato-09). Un 401 borra el token del llavero y lleva a
  "entrar". Salir y borrar la cuenta vacían el llavero.
- Al volver a primer plano (desde otra app), se refresca con "cambios desde"
  (Y2).
- Las fotos se convierten y pierden sus metadatos **dentro del aparato**: la
  ubicación nunca sale del iPhone. Se achican a 3200 px del lado largo (el
  doble del tamaño más grande que guarda el backend).
- El respaldo se sube con la app en primer plano y la pantalla encendida. Si
  la app pasa a segundo plano, la subida se pausa; al volver, pregunta qué
  partes llegaron y sigue (contrato-07). El archivo nunca se carga entero en
  memoria. La contraseña no se guarda en el llavero, en registros ni en
  archivos.
- La variante desarrollo tiene la excepción de red local de Apple (para
  hablar con la notebook sin HTTPS); la de producción solo acepta HTTPS. En
  el aparato son dos apps, dentro del límite de la firma gratuita.
- Etiquetas `app-apple-vX.Y.Z`.

**CI de Apple** (en Linux, en cada pull request que toca `/apps` o
`/contrato`): genera el cliente, analiza el código, corre las pruebas de
`comun`, de `/apps/nativo` y de `/apps/apple` (adaptadores con dobles) y la
prueba de arquitectura. No compila para Apple: eso lo hace el script en la
Mac (app-apple-08).

**No suma servicios a `/plataforma`**: la app se instala en los aparatos.

### Plan

Cada etapa va detrás de la etapa de la web que construye las piezas de
`comun` que usa. Los adaptadores compartidos con Android (sesión, fotos,
respaldo, conexión y versión) se escriben en `/apps/nativo` desde la etapa 1
(app-android-02).

| # | Etapa | Terminado cuando… | Necesita antes | Estado |
|---|---|---|---|---|
| 1 | **Esqueleto y prueba de riesgo**: `/apps/apple` con destinos iOS y macOS, dos variantes, sesión en el llavero, script de la Mac y CI | Un PR de prueba **falla** si guarda el cliente generado o si `/apps/apple` llama al cliente sin pasar por un caso de uso. En el iPhone, el simulador de iPad y la Mac se entra contra la notebook; al cerrar y abrir la app no hay que volver a entrar, y el token no aparece en ningún archivo de la app. **Prueba de riesgo** (app-apple-09, compilación final, datos de prueba): abre en ≤ 2 s; 200 recetas se desplazan sin cuadros lentos; un HEIC de 48 MP queda en un JPEG ≤ 25 MB sin ubicación; VoiceOver lee los botones principales; teclado y dictado en español andan; y vos decís "se siente bien". La app de Mac sigue abriendo 8 días después de instalada. Si algo falla, se pasa a Swift/SwiftUI antes de seguir | base-plataforma 1–4; contrato 1; backend 1–2; app-web 1 | pendiente |
| 2 | **Cuenta y perfil** (CU01–CU05) | El correo de verificación llega a la bandeja de prueba. El 6.º intento muestra "demasiados intentos" en el idioma elegido. Un cambio de idioma, color o modo en el iPhone se ve en la Mac al volver a la app. "Cerrar todas" manda al otro aparato a "entrar" en su pedido siguiente. En modo avión aparece el aviso y "reintentar" recupera. Con una `App-Version` menor que la mínima aparece "hay una versión nueva: instalala desde la Mac" | backend 2–3; app-web 2 | pendiente |
| 3 | **Recetas, fotos y escalado** (CU07–CU15, CU17, CU18) | Pasan los casos de `escalado.json` y escalar no hace pedidos. La misma receta editada en el iPhone y en la Mac ofrece "conservar la mía" o "quedarme con la otra". Un doble toque en "guardar" crea una sola receta. Se suben un HEIC de la fototeca, uno de la cámara y uno elegido en el Finder, y ninguno sale del aparato con ubicación. Con la app abierta más de 6 h, las fotos se siguen viendo. Aparecen los avisos R6, R7 y V6. "azucar" encuentra "Azúcar". Un panel en el iPhone y dos en el iPad horizontal y en la Mac desde 840 px | backend 4–5; app-web 3 | pendiente |
| 4 | **Colecciones y favoritos** (CU16, CU21–CU23) | Un favorito se ve al instante y, si el backend falla, se desmarca con un aviso. Lo que no se puede ver aparece como "no disponible" (K4, K6, F3). Se reordena una colección con el dedo y con el mouse, y se publica con recetas privadas (K5). Volver a la app refresca con "cambios desde" (4 pedidos en paralelo); un 410 recarga todo | backend 6; app-web 4 | pendiente |
| 5 | **Respaldo y borrado de cuenta** (CU06, CU19, CU20) | Exportar pide la contraseña dos veces (12 caracteres o más) con el aviso de que no se puede recuperar, y el archivo se guarda en Archivos o donde se elija en la Mac. Un respaldo de 1 GB sube en partes de 50 MB sin que la memoria de la app crezca más de 100 MB; si la app pasa a segundo plano o se corta la red, sigue sin repetir partes y al final muestra el resumen (E9). La contraseña no queda en el llavero, en registros ni en archivos. Al borrar la cuenta se ofrecen el respaldo y el autor anónimo; después la app vuelve a "entrar" y el llavero queda vacío | backend 7–8; app-web 5 | pendiente |
| 6 | **Mac completa y cierre** | El menú de la Mac tiene ⌘N (receta nueva), ⌘F (buscar) y ⌘R (refrescar), y la ventana no se achica por debajo del mínimo. Con la letra más grande del sistema no se corta ningún texto, y VoiceOver recorre los flujos principales. La variante producción entra por HTTPS a `api.<dominio>` y rechaza HTTP. Un recorrido completo pasa en el iPhone, el simulador de iPad y la Mac. `app-apple-v1.0.0` se instala con el script | contrato 7; backend 8; app-web 6; **base-plataforma producción, etapa 3** | pendiente |

La etapa 6 es la única que depende de la fase producción (HTTPS en el
dominio propio, etapa 3); las etapas 1 a 5 no la necesitan.

---

## app-android — decidido (2026-11-15)

### Decisiones

| ID | Tema | Decisión |
|---|---|---|
| app-android-01 | Tecnología | Flutter 3.50 (Dart 3.14) + Riverpod 3 con `/apps/comun` y `/apps/nativo`; `/apps/android` suma solo lo propio de Android (confirma app-web-01) |
| app-android-02 | Código nativo compartido | Paquete `/apps/nativo` para Apple y Android: sesión, fotos, respaldo, conexión y versión |
| app-android-03 | Distribución | APK firmado: ADB en los aparatos propios; descarga directa para conocidos al abrir la plataforma; cuenta gratuita de distribución limitada de Google (hasta 20 aparatos), abierta apenas existan el dominio y la clave; la completa (USD 25 una vez) si se superan 15 aparatos (base-plataforma-19) |
| app-android-04 | Firma, compilación y CI | Clave de producción solo en la notebook + copia cifrada en los respaldos de restic; la CI en Linux compila el APK de desarrollo; un script en la notebook firma, verifica e instala |
| app-android-05 | Actualizaciones | Botón en la app: baja el APK de `app.<dominio>/descargas/android/` (servido por nginx), verifica la huella y abre el instalador de Android; aviso opcional con `ultima.json` |
| app-android-06 | Versiones, variantes y aparatos | Desde Android 7.0 (API 24), compila para Android 17 (API 37); variantes desarrollo y producción; celular y tablet físicos, plegable en el emulador |
| app-android-07 | Aspecto | Material 3 (`material_ui`) con el color y el modo del Perfil; Expressive a medida que se publique |
| app-android-08 | Tablets y plegables | Dos paneles desde 840 px; giro y tamaño libres; respeta la bisagra |
| app-android-09 | Sesión, fotos, respaldo y permisos | Keystore sin respaldo automático de Android; selector de fotos del sistema + HEIC a JPEG sin metadatos; respaldo en primer plano; solo dos permisos |
| app-android-10 | Prueba de riesgo | ≤ 2 s al abrir, 200 recetas sin cuadros lentos, HEIC sin ubicación, TalkBack, dictado, girar sin perder nada y tu visto bueno; si no, Kotlin + Compose |

### Arquitectura

```
/apps                     espacio de trabajo de Dart (app-web-02)
├─ comun/                 igual que en la web y Apple: cliente generado, casos de
│                         uso, escalado, estado, textos, diseño y pantallas.
│                         Suma la regla de la bisagra en diseno/ (app-android-08)
├─ nativo/                adaptadores compartidos por Apple y Android (app-android-02)
│  ├─ sesion              token Bearer en el almacén seguro (flutter_secure_storage
│  │                      10.3: Keychain en Apple, Keystore en Android); un 401
│  │                      borra el token y lleva a "entrar"
│  ├─ fotos               elegir o sacar una foto (image_picker) → HEIC a JPEG sin
│  │                      metadatos (flutter_image_compress 2.5) → máx. 3200 px y
│  │                      25 MB
│  ├─ respaldo            elegir el archivo y guardar la exportación con las
│  │                      ventanas del sistema; subir en partes de 50 MB leyendo
│  │                      del disco; pausar en segundo plano y retomar
│  │                      preguntando qué partes llegaron
│  ├─ conexion            "sin conexión" a partir del error de red, y reintentar
│  └─ version             App-Version del paquete; puerto "conseguir la versión
│                         nueva" (cada app pone su adaptador)
├─ web/
├─ apple/                 solo lo exclusivo de Apple (ver app-apple)
└─ android/               proyecto Flutter con destino Android (celulares,
   │                      tablets y plegables)
   ├─ arranque            variante "desarrollo" (API de la notebook por la red de
   │                      la casa, con HTTP permitido solo hacia esa dirección) o
   │                      "producción" (solo HTTPS a api.<dominio>); conecta los
   │                      adaptadores de nativo y los propios
   ├─ rutas/              go_router 18, como Apple; botón y gesto "atrás" de Android
   ├─ plataforma/
   │  └─ actualizar       lee ultima.json, baja el APK, verifica su huella SHA-256
   │                      y abre el instalador de Android (candidato: ota_update
   │                      7.1); es el adaptador de nativo/version en Android
   ├─ android/            proyecto de Gradle que genera Flutter: dos identificadores
   │                      (desarrollo y producción), solo los permisos de internet
   │                      e instalar paquetes, respaldo automático de Android
   │                      desactivado, red de desarrollo, firma leída de un
   │                      archivo fuera del repositorio
   ├─ test/               pruebas de los adaptadores con dobles
   └─ herramientas/       script de la notebook: genera el cliente, compila, firma
                          con la clave de producción, verifica con apksigner,
                          instala por ADB y prepara el APK y ultima.json
```

**Reglas propias de Android** (las reglas internas de `comun`, descritas en
app-web, valen igual):
- La sesión viaja en `Authorization: Bearer` y, al entrar, la app avisa que
  es nativa (contrato-09); dura 90 días sin uso (backend-15). Un 401 borra el
  token y lleva a "entrar". Salir y borrar la cuenta lo borran. El respaldo
  automático de Android está desactivado para la app: el token no viaja a
  otro aparato.
- Al volver a primer plano (desde otra app), se refresca con "cambios desde"
  (Y2).
- Las fotos se eligen con el selector del sistema (sin permiso de fotos; ya
  quita la ubicación) o se sacan con la cámara del sistema, y se convierten y
  pierden sus metadatos **dentro del aparato**. En Android 7 u 8, si aparece
  una imagen que no se puede convertir, la app avisa y no la sube.
- El respaldo se sube con la app en primer plano y la pantalla encendida; en
  segundo plano se pausa y al volver retoma (contrato-07). El archivo nunca
  se carga entero en memoria y la contraseña no se guarda.
- Gira y cambia de tamaño libremente (Android 17, pantallas de 600 dp o
  más); girar, plegar o dividir la pantalla no pierde lo escrito, porque el
  estado vive en Riverpod. En un plegable a medio abrir, nada cruza el
  pliegue.
- Solo dos permisos: internet e instalar paquetes. La CI rechaza cualquier
  otro.
- La variante desarrollo permite HTTP solo hacia la notebook; la de
  producción, solo HTTPS. En el aparato son dos apps.
- "Descargar e instalar" verifica la huella SHA-256 antes de abrir el
  instalador; Android, además, exige la misma firma.
- La clave de producción nunca entra al repositorio ni a la CI. Etiquetas
  `app-android-vX.Y.Z`, con número de versión de Android siempre creciente.

**CI de Android** (en Linux, en cada pull request que toca `/apps` o
`/contrato`): genera el cliente, analiza el código, corre las pruebas de
`comun`, de `/apps/nativo` y de `/apps/android` (adaptadores con dobles), la
prueba de arquitectura y la de permisos, y **compila el APK de desarrollo**
(firmado con la clave de depuración). Nunca usa la clave de producción.

**No suma servicios a `/plataforma`**: la app se instala en los aparatos.
La fase producción publica el APK y su ficha en
`app.<dominio>/descargas/android/` (base-plataforma-19).

### Plan

Cada etapa va detrás de la etapa de la web que construye las piezas de
`comun` y de la de Apple que construye las de `/apps/nativo`.

| # | Etapa | Terminado cuando… | Necesita antes | Estado |
|---|---|---|---|---|
| 1 | **Esqueleto y prueba de riesgo**: `/apps/android` con dos variantes, adaptadores de `/apps/nativo`, script de la notebook y CI que compila el APK | Un PR de prueba **falla** si guarda el cliente generado, si `/apps/android` llama al cliente sin pasar por un caso de uso o si agrega un permiso fuera de la lista. La CI compila el APK de desarrollo. En el celular y la tablet (instalados por ADB) se entra contra la notebook; al cerrar y abrir la app no hay que volver a entrar; el token no aparece en ningún archivo legible de la app, y el respaldo automático de Android figura desactivado. **Prueba de riesgo** (app-android-10, compilación final, datos de prueba): abre en ≤ 2 s; 200 recetas se desplazan sin cuadros lentos; un HEIC de 48 MP queda en un JPEG ≤ 25 MB sin ubicación; TalkBack lee los botones principales; teclado y dictado en español andan; girar la tablet y usar pantalla dividida no pierden lo escrito; y vos decís "se siente bien". Si algo falla, se pasa a Kotlin + Compose antes de seguir | base-plataforma 1–4; contrato 1; backend 1–2; app-web 1; app-apple 1 | pendiente |
| 2 | **Cuenta, perfil y actualización** (CU01–CU05) | El correo de verificación llega a la bandeja de prueba. El 6.º intento muestra "demasiados intentos" en el idioma elegido. Un cambio de idioma, color o modo en el celular se ve en la tablet al volver a la app. "Cerrar todas" manda al otro aparato a "entrar" en su pedido siguiente. En modo avión aparece el aviso y "reintentar" recupera. Con una `App-Version` menor que la mínima aparece "Hay una versión nueva"; "Descargar e instalar" (con una ficha de prueba servida por la notebook) instala la versión nueva encima, sin perder la sesión, y un APK con la huella cambiada se rechaza antes de abrir el instalador | backend 2–3; app-web 2; app-apple 2 | pendiente |
| 3 | **Recetas, fotos y escalado** (CU07–CU15, CU17, CU18) | Pasan los casos de `escalado.json` y escalar no hace pedidos. La misma receta editada en el celular y en la tablet ofrece "conservar la mía" o "quedarme con la otra". Un doble toque en "guardar" crea una sola receta. Se suben un HEIC desde el selector y una foto de la cámara, y ninguna sale del aparato con ubicación. Con la app abierta más de 6 h, las fotos se siguen viendo. Aparecen los avisos R6, R7 y V6. "azucar" encuentra "Azúcar". Un panel en el celular y dos en la tablet horizontal; en el emulador de plegable a medio abrir, nada cruza el pliegue; girar en medio de una edición no pierde lo escrito | backend 4–5; app-web 3; app-apple 3 | pendiente |
| 4 | **Colecciones y favoritos** (CU16, CU21–CU23) | Un favorito se ve al instante y, si el backend falla, se desmarca con un aviso. Lo que no se puede ver aparece como "no disponible" (K4, K6, F3). Se reordena una colección con el dedo y se publica con recetas privadas (K5). Volver a la app refresca con "cambios desde" (4 pedidos en paralelo); un 410 recarga todo | backend 6; app-web 4; app-apple 4 | pendiente |
| 5 | **Respaldo y borrado de cuenta** (CU06, CU19, CU20) | Exportar pide la contraseña dos veces (12 caracteres o más) con el aviso de que no se puede recuperar, y el archivo se guarda donde se elija. Un respaldo de 1 GB sube en partes de 50 MB sin que la memoria de la app crezca más de 100 MB; si la app pasa a segundo plano o se corta la red, sigue sin repetir partes y al final muestra el resumen (E9). La contraseña no queda en el almacén seguro, en registros ni en archivos. Al borrar la cuenta se ofrecen el respaldo y el autor anónimo; después la app vuelve a "entrar" y el almacén seguro queda vacío | backend 7–8; app-web 5; app-apple 5 | pendiente |
| 6 | **Cierre y publicación** | Con la letra más grande del sistema no se corta ningún texto, y TalkBack recorre los flujos principales. La variante producción entra por HTTPS a `api.<dominio>` y rechaza HTTP. Un recorrido completo pasa en el celular, la tablet y el emulador de plegable. `app-android-v1.0.0`, firmada con la clave de producción y verificada con apksigner, se baja de `app.<dominio>/descargas/android/` y se instala en el aparato de una persona conocida, y una 1.0.1 de prueba llega con el botón. La clave restaurada desde la copia cifrada firma con la misma huella. Si la verificación de Google ya rige en la región, el paquete está registrado en la cuenta gratuita y ese aparato, autorizado | contrato 7; backend 8; app-web 6; **base-plataforma producción, etapas 3 y 8** | pendiente |

La etapa 6 es la única que depende de la fase producción (HTTPS y descarga
en el dominio propio, etapa 3; cuenta de Google, etapa 8); las etapas 1 a 5
no la necesitan.
