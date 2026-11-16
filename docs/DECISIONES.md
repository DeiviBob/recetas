# Decisiones

Cada decisión con el porqué. No se borran: si una cambia, se agrega otra
que la reemplaza y la vieja se marca "Reemplazada por D-XX".
Prefijos: `P-` plataforma; `<proyecto>-` decisiones de un proyecto
(por ejemplo `backend-01`, `app-apple-01`).

---

## P-01 — Usuarios (D1) · *Reemplazada por P-06*
- **Fecha**: 2026-11-13
- **Decisión**: arranca con una persona, con identidad, permisos y dueño de
  cada dato desde el día uno.
- **Por qué**: sumar personas y compartir después sin rediseñar ni migrar.

## P-02 — Arquitectura (D6) · *Reemplazada por P-05*
- **Fecha**: 2026-11-13
- **Decisión**: microservicios desde el inicio, con Entrada única, datos
  exclusivos por servicio y canal de eventos.
- **Alternativa descartada**: núcleo modular con separación progresiva
  (menos costo y complejidad).
- **Por qué**: preferencia explícita de separar el núcleo y poder sumar
  clientes y servicios de forma independiente.
- **Consecuencias**: consistencia eventual, más piezas que desplegar y
  vigilar; se mitiga compartiendo alojamiento al inicio.

## P-03 — Conexión (D8)
- **Fecha**: 2026-11-13
- **Decisión**: los clientes requieren conexión; el núcleo es la única
  fuente de verdad.
- **Consecuencias**: sin internet no se ven recetas; un caché de lectura
  queda como mejora futura.

## P-04 — Presupuesto (D9) · *Precisada por P-19*
- **Fecha**: 2026-11-13
- **Decisión**: hasta ~USD 20/mes en total.

## P-05 — Arquitectura: backend único y modular (D6)
- **Fecha**: 2026-11-14
- **Decisión**: un solo backend con módulos de fronteras estrictas
  (Identidad, Perfil, Recetas, Favoritos, Medios, Intercambio), cada uno
  con dominio, casos de uso, puertos y adaptadores, y eventos internos entre
  módulos. Reemplaza a P-02.
- **Alternativa descartada**: microservicios desde el inicio.
- **Por qué**: menos costo, despliegue y complejidad para el tamaño actual;
  igual permite cambiar tecnologías tocando solo adaptadores y separar un
  módulo como servicio en el futuro (reglas M1–M5).
- **Nota (2026-11-14)**: la lista vigente de módulos es la de ANALISIS §4.1
  (ocho: Identidad, Perfil, Recetas, Colecciones, Favoritos, Medios,
  Respaldo, Notificaciones). "Intercambio" pasó a llamarse "Respaldo" y
  Colecciones se sumó con P-15. La decisión no cambia.

## P-06 — Usuarios, visibilidad, favoritos y sincronización (D1, D17–D19)
- **Fecha**: 2026-11-14
- **Decisión**: varias personas con cuenta desde el inicio; recetas
  privadas por defecto y opcionalmente públicas; favoritos por persona;
  recetas, favoritos y preferencias sincronizados entre dispositivos a
  través del backend. Reemplaza a P-01.
- **Por qué**: usar la misma cuenta en todos los aparatos y poder compartir
  recetas públicamente.

## P-07 — Proyectos de app (D16, D14)
- **Fecha**: 2026-11-14
- **Decisión**: tres proyectos de app: Apple (iPhone, iPad, Mac), Android
  (celulares y tablets) y web. La tecnología de cada uno se decide por
  separado; pueden compartirla o no.

## P-08 — Desde cero, sin migración (2026-11-14)
- **Decisión**: el proyecto empieza de cero; no se migran datos de `recipe_app`.

## P-09 — Respaldo cifrado (D28) (2026-11-14)
- **Decisión**: exportar genera un respaldo cifrado con una contraseña que
  elige la persona; el cifrado también detecta modificaciones; el formato
  interno no es legible. Importar acepta solo respaldos de la plataforma:
  en la misma cuenta agrega/actualiza, en otra cuenta crea copias privadas
  con ids nuevos. Nunca borra.
- **Alternativas descartadas**: clave solo de la plataforma (el respaldo
  sería inútil si la plataforma deja de existir); solo misma cuenta.
- **Por qué**: evitar modificaciones, proteger la privacidad si el archivo
  se filtra y ocultar el formato interno.
- **Pendiente a futuro**: descarga legible si se abre a más personas (E11).
- **Nota (2026-11-16)**: con el repositorio público deja de ser posible
  "ocultar el formato interno": ese porqué se cae (P-21). La protección sigue
  siendo la contraseña y la detección de cambios. La decisión no cambia.

## P-10 — Inicio de sesión y dominio (D10, D30) (2026-11-14)
- **Decisión**: correo + contraseña y código por correo; dominio propio
  (~USD 11/año).
- **Alternativas consideradas**: Google/Apple (Apple requiere cuenta paga
  de desarrollador); subdominio gratuito (los correos suelen ir a spam).
- **Por qué**: login sin depender de terceros; el dominio propio hace que
  los correos lleguen y permite HTTPS.

## P-11 — Recetas públicas, búsqueda y favoritos (D11, D20–D26) (2026-11-14)
- **Decisión**: públicas visibles solo para personas con cuenta; buscador
  de públicas; búsqueda por nombre, tag e ingrediente; copia de públicas
  con "basada en"; favorito no disponible queda marcado; cambios entre
  dispositivos al abrir o refrescar; sin moderación en el MVP; al publicar
  una receta con referencias privadas se avisa y se ofrece publicarlas.

## P-12 — Reglas de recetas (D2, D3, D4, D7) (2026-11-14)
- **Decisión**: ciclos permitidos con aviso; borrar receta usada pide
  confirmación; escalado en backend y apps con la misma regla; conflicto
  explícito en ediciones simultáneas.

## P-13 — Borrado de cuenta (D12, D27) (2026-11-14)
- **Decisión**: antes de borrar se ofrece dejar las recetas públicas
  publicadas bajo un "autor anónimo"; si no se acepta, se borran. Todo lo
  demás se borra siempre.

## P-14 — Despliegue y contexto (2026-11-14) · *Alojamiento inicial precisado por P-17*
- **Decisión**: VPS administrado por el usuario; un solo repositorio;
  respaldos diarios con 30 días; no se publica en tiendas por ahora; hasta
  10 fotos por receta; orden web → Apple → Android.
- **Criterio de tecnología**: cada parte con la tecnología que mejor sirva
  a su necesidad (backend: rapidez; datos: escalabilidad; fotos: entrega
  rápida), descartando según costo, mantenimiento y coherencia.
- **Nota (2026-11-15)**: la firma de desarrollo de Apple es la **gratuita**,
  solo en los aparatos propios; la cuenta paga se decide en la fase
  producción (app-apple-02). La decisión no cambia.
- **Nota (2026-11-15)**: Android se distribuye como **APK firmado**: en los
  aparatos propios se instala por ADB desde la notebook; a personas
  conocidas, por descarga directa; desde 2027, con la cuenta gratuita de
  distribución limitada de Google (app-android-03). La decisión no cambia.
- **Nota (2026-11-15)**: los respaldos diarios de 30 días van a un **disco
  externo en casa** y a una **copia cifrada en Backblaze B2**
  (base-plataforma-12); la cuenta paga de Apple no se paga por ahora
  (base-plataforma-18). La decisión no cambia.

## P-15 — Colecciones (D15) (2026-11-14)
- **Decisión**: colecciones en el MVP, privadas por defecto y publicables;
  pueden incluir recetas propias y públicas de otros; se buscan y se marcan
  como favoritas; las recetas que no se pueden ver aparecen como "no
  disponible"; borrar una colección no borra sus recetas. Módulo propio.
- **Por qué**: organizar recetas por tema y compartir grupos de recetas.

## P-16 — Respaldo antes de borrar cuenta (D29) (2026-11-14)
- **Decisión**: antes de borrar la cuenta se ofrece descargar el respaldo
  cifrado, además de dejar lo público bajo "autor anónimo" (P-13).

**Análisis validado: criterio para pasar a tecnología cumplido.**

---

## P-17 — Alojamiento en dos fases (D31) (2026-11-14)
- **Decisión**: la plataforma arranca en una **notebook con Linux**, que se
  usa para desarrollar y, más adelante, para atender a las primeras
  personas. El **VPS** se contrata cuando la notebook quede chica
  (disponibilidad, recursos o cantidad de personas). base-plataforma se
  decide en dos fases: **desarrollo** (ahora) y **producción** (antes de que
  entre la primera persona). Precisa el alojamiento de P-14; el resto de
  P-14 sigue vigente.
- **Alternativas consideradas**: contratar el VPS ya (desde USD 6,49/mes con
  pago adelantado, o USD 12/mes mes a mes); usar la notebook solo para
  desarrollar.
- **Por qué**: no pagar un servidor mientras nadie la use; obliga a que todo
  sea portable (de la notebook al VPS sin rehacer nada).
- **Consecuencias**: mientras corra en la notebook, la disponibilidad
  depende de la luz, de internet y de que esté prendida; para publicarla
  hace falta un túnel (se decide en la fase producción); desarrollo y
  producción conviven en la misma máquina (ver base-plataforma-04).
- **Preselección del VPS (no es una decisión)**: Hostinger KVM 1 en São
  Paulo (1 núcleo, 4 GB de RAM, 50 GB de disco; USD 6,49/mes pagando 24
  meses por adelantado, renueva a USD 11,99). Evaluadas también: Amazon
  Lightsail 2 GB en São Paulo (USD 12/mes, mes a mes) y Oracle Cloud gratis
  (riesgo de no conseguir o perder la máquina). Región São Paulo porque las
  personas están en Paraguay, Argentina y Uruguay. Revisar precios al pasar
  al VPS.
- **Nota (2026-11-15)**: cuándo pasar quedó fijado con cuatro criterios
  medibles (base-plataforma-16) y la preselección quedó confirmada como
  decisión (base-plataforma-17): Hostinger KVM 1 en São Paulo; el precio y
  la región se verifican al contratar. La notebook se publica con un túnel
  de Cloudflare (base-plataforma-08). La decisión no cambia.

## P-18 — Redondeo del escalado (D32) (2026-11-14)
- **Decisión**: la cantidad escalada se calcula con precisión completa y se
  muestra con **hasta 2 decimales**, redondeando la mitad hacia arriba y sin
  ceros al final (1,50 → 1,5; 0,333 → 0,33). Se agrega a R8. Los ejemplos
  forman parte de los casos de prueba compartidos de R9 (contrato-13).
- **Por qué**: R8 daba la fórmula pero no el redondeo, y R9 exige que el
  backend y las tres apps muestren lo mismo.
- **Afecta a**: backend y las tres apps. No rompe nada (no hay nada
  construido).

## P-19 — Escala: meta de diseño de 10.000 personas (D33) (2026-11-14)
- **Decisión**: la plataforma se diseña para llegar a **10.000 personas con
  cuenta** (no conectadas al mismo tiempo) **sin rediseñar**: crecer es
  agrandar el servidor o el almacenamiento. El tope de USD 20/mes vale para
  el **arranque**; al crecer hacia la meta se revisa el presupuesto. Precisa
  P-04. El volumen inicial (< 100 personas el primer año) no cambia.
- **Alternativas consideradas**: 10.000 personas dentro de USD 20/mes
  (exigía una regla nueva: un límite de espacio de fotos por persona);
  tomarlo como aspiración sin cambiar nada.
- **Por qué**: elegir desde ya tecnologías que escalen, sin pagar por
  adelantado.
- **Consecuencias**: lo que más crece son las fotos (estimación con
  supuestos: ~350 GB a 10.000 personas, con ~100 recetas y ~1,5 fotos por
  receta); a esa escala la notebook no alcanza y el VPS preseleccionado
  (50 GB de disco) tampoco, salvo mudando las fotos (backend-06).
- **Afecta a**: ANALISIS §7, §8, §9 y §11; backend (base de datos, fotos,
  búsqueda); fase producción de base-plataforma. No rompe el contrato.

## P-20 — Vigilancia propia: proyecto "vigía" (D34) (2026-11-15)
- **Decisión**: el monitoreo de la plataforma (backend caído, respaldo que
  no corrió, prueba de restauración que falló, discos arriba del 70 %) lo
  hace un **proyecto propio, el "vigía"**, que corre **fuera del lugar
  donde corre la plataforma** y avisa por Telegram y por correo. **Sin
  servicio intermedio**: abrir a la primera persona espera a que el vigía
  esté listo. Su tecnología y dónde corre (candidato: Cloudflare Workers,
  plan gratis: 100.000 pedidos por día y 5 tareas programadas por cuenta)
  se deciden en su propia conversación, como un proyecto más de MAPA.
- **Alternativas descartadas**: Healthchecks.io (gratis, 20 controles, sin
  nada que construir); UptimeRobot (gratis, 50 monitores cada 5 min) +
  Healthchecks.io; Better Stack (gratis, 10 controles, "para proyectos
  personales"); vigía propio arrancando con Healthchecks.io mientras tanto.
- **Por qué**: preferencia por el control propio, sin depender del plan
  gratis de un tercero.
- **Consecuencias**: un proyecto más para construir y mantener; si corre en
  Workers, probablemente un lenguaje más (ANALISIS §9: hay que justificarlo
  en su conversación); si corre en Cloudflare, comparte punto de falla con
  el túnel; necesita un resumen diario para detectar que falló en silencio.
  Lo vigilado le habla con **señales HTTP** en el formato de Healthchecks.io
  (base-plataforma-13), así queda desacoplado y con un plan B.
- **Afecta a**: MAPA (proyecto nuevo y condición para abrir), ANALISIS §7,
  §8, §9 y §11, fase producción de base-plataforma (etapa 7). No toca el
  contrato, el backend ni las apps. No rompe nada.
- **Cambiarlo después**: fácil (se cambian las direcciones de las señales).
- **Nota (2026-11-15)**: el vigía quedó decidido en vigia-01 a vigia-04:
  corre en **Google Cloud (e2-micro gratis, EE. UU.) con Go en Docker**, fuera
  de Cloudflare, así que no suma un lenguaje ni comparte punto de falla con
  el túnel. Se decidió en la misma conversación que la fase producción, como
  excepción a "un proyecto por conversación", a pedido del usuario. La
  decisión no cambia.

## P-21 — Repositorio público y formato del respaldo (D35) (2026-11-16)
- **Decisión**: el repositorio pasa a ser **público** (base-plataforma-20) y
  **E4 cambia**: el formato interno del respaldo ya no tiene que ser secreto;
  no está pensado para leerse ni editarse a mano, pero puede ser público. La
  protección del respaldo es la contraseña de la persona (E2) y la detección
  de cualquier cambio (E3). La etiqueta fija de backend-09 queda en el
  código, a la vista.
- **Alternativas descartadas**: repositorio privado con GitHub Pro (USD
  4/mes, no cambiaba nada del análisis; total al renovar el VPS ≈ USD 16,9);
  privado y gratis con protección solo local (gancho de git y una regla en
  Claude Code; GitHub no impide mezclar con la CI en rojo); con el código
  público, pasar la etiqueta a un secreto de producción (el respaldo no se
  abriría con la contraseña sola, pero si ese secreto se pierde los respaldos
  viejos no se importan, lo que P-09 quería evitar).
- **Por qué**: elección del usuario: USD 0 y `main` protegida en GitHub. La
  seguridad del respaldo nunca dependió de esconder el formato.
- **Consecuencias**: quien tenga la contraseña y el código puede abrir o
  armar un respaldo; la importación igual valida cada regla como si los
  datos fueran nuevos (backend-09). Código, documentos e historial quedan a
  la vista: nada de la seguridad puede depender de que el código sea
  secreto. Vuelve a la etapa 1 del análisis solo para E4.
- **Afecta a**: ANALISIS E4, §8, §9 y §11 (v3.9); P-09 y backend-09 (notas);
  base-plataforma-05 (reemplazada en la visibilidad); vigia-02 y vigia-04
  (claves fuera del repositorio); `CLAUDE.md`. No toca el contrato ni las
  apps. No rompe nada: no hay nada construido.
- **Cambiarlo después**: fácil (un repositorio se puede volver privado; el
  historial que ya fue público no se puede "despublicar").

---

## base-plataforma — fase desarrollo

## base-plataforma-01 — Dominio y DNS (2026-11-14)
- **Decisión**: dominio registrado en **Cloudflare Registrar**, con el DNS en
  Cloudflare. Se compra en la **fase producción**.
- **Costo**: ≈ USD 11,2/año para un .com (precio al costo, igual al
  renovar) ≈ USD 0,93/mes.
- **Alternativa descartada**: Porkbun (≈ USD 11–12/año, permite llevar el
  DNS a otro lado).
- **Por qué**: lo más barato a largo plazo; DNS, reenvío gratis del correo
  que llega al dominio y una futura CDN en un solo panel.
- **Consecuencias**: mientras el dominio esté registrado en Cloudflare, el
  DNS tiene que estar ahí. Hasta comprarlo, otra persona puede registrar el
  nombre.
- **Cambiarlo después**: media (transferir un dominio lleva días).
- **Nota (2026-11-15)**: terminación `.com`, ≈ USD 11,17/año desde la suba
  del precio mayorista del 1 de noviembre de 2026; se compra apenas se elija
  el nombre (base-plataforma-07). La decisión no cambia.

## base-plataforma-02 — HTTPS y tráfico (2026-11-14)
- **Decisión**: certificados gratuitos de **Let's Encrypt**, renovados solos
  por el proxy inverso (se elige en la fase producción). En el **VPS** el
  tráfico va **directo**, sin pasar por el proxy de Cloudflare. Si conviene
  una CDN para las fotos lo decide el backend (módulo Medios). Cómo se
  publica la notebook en internet (probablemente un túnel de Cloudflare) se
  decide en la fase producción.
- **Alternativa descartada**: todo el tráfico por Cloudflare desde el día uno.
- **Por qué**: menos piezas; Cloudflare no ve el tráfico descifrado.
- **Consecuencias**: la dirección IP del VPS queda visible.
- **Nota (2026-11-14)**: la CDN de fotos quedó decidida en backend-06: por
  ahora no.
- **Nota (2026-11-15)**: mientras producción corra en la notebook, se
  publica con un **túnel de Cloudflare** y el HTTPS termina en Cloudflare:
  es una **excepción temporal y aceptada** al porqué de esta decisión
  (base-plataforma-08). En el VPS vale lo de arriba; los certificados los
  saca nginx con su módulo ACME (base-plataforma-09). La decisión no cambia.

## base-plataforma-03 — Entorno de ejecución (2026-11-14)
- **Decisión**: **Docker Engine + Docker Compose** (versiones vigentes en
  noviembre de 2026: Engine 29.9, Compose 5.6). La misma definición de
  servicios corre en la notebook y, después, en el VPS.
- **Alternativas descartadas**: Podman 6 + Quadlet (más seguro, menos
  documentado, cambios que rompen en la versión 6); instalar todo directo en
  Linux (la notebook y el VPS terminarían configurados distinto).
- **Por qué**: lo más documentado; Claude Code lo maneja bien; pasar al VPS
  es copiar la definición y los datos.
- **Consecuencias**: el servicio de Docker corre con permisos de
  administrador; se mitiga en el mantenimiento (fase producción). Cada
  tecnología que elija el backend (base de datos, fotos, búsqueda) se suma
  como un servicio más.
- **Cambiarlo después**: fácil (Podman lee casi los mismos archivos).
- **Nota (2026-11-15)**: la mitigación quedó en base-plataforma-14
  (contenedores sin permisos de administrador, ninguno con acceso al control
  de Docker, ninguno publica puertos hacia afuera). La decisión no cambia.

## base-plataforma-04 — Dos entornos en la notebook (2026-11-14)
- **Decisión**: **desarrollo** y **producción** separados en la misma
  notebook, con datos, claves, redes, puertos y versiones propios.
  Producción solo corre versiones publicadas (con etiqueta) y nunca depende
  de la carpeta de trabajo. Desarrollo incluye una bandeja de correo de
  prueba (los correos no salen a internet).
- **Alternativa descartada**: un solo entorno.
- **Por qué**: que un experimento no rompa ni borre datos reales.
- **Nota (2026-11-15)**: cómo producción toma una versión con etiqueta quedó
  en base-plataforma-11 (`ghcr.io` + `desplegar`). La decisión no cambia.

## base-plataforma-05 — Repositorio (2026-11-14) · *Visibilidad reemplazada por base-plataforma-20*
- **Decisión**: **GitHub**, un repositorio privado único con esta
  estructura: `/docs` (copia de ANALISIS, DECISIONES y MAPA), `/contrato`,
  `/backend`, `/apps/web`, `/apps/apple`, `/apps/android`, `/plataforma` y
  `CLAUDE.md` (reglas para Claude Code).
- **Convenciones**:
  - `main` siempre funciona y está protegida; cada tarea va en una rama corta
    que entra por *pull request* con la CI en verde.
  - Commits con el proyecto: `feat(backend): …`, `fix(contrato): …`.
  - Versiones mayor.menor.parche con etiqueta por proyecto
    (`contrato-v1.0.0`); subir la mayor = rompe compatibilidad.
  - La CI corre solo lo que tocó cada cambio (por carpeta).
  - Secretos nunca en el repo: solo un archivo de ejemplo sin valores.
  - La fuente de verdad de los documentos sigue siendo el proyecto de
    Claude; `/docs` se actualiza junto con él.
- **Alternativa descartada**: GitLab (400 min/mes de CI; hasta 5 personas en
  lo privado).
- **Por qué**: 2.000 min/mes de CI gratis en repositorios privados y la mejor
  integración con Claude Code.
- **Consecuencias**: las builds de Apple conviene hacerlas en la Mac (en
  GitHub, los minutos de macOS cuestan unas 10 veces más).
- **Cambiarlo después**: fácil (git se muda con su historial; se reescriben
  las automatizaciones).
- **Nota (2026-11-14)**: se suma `/apps/comun`, un paquete compartido por
  las apps; `/apps` pasa a ser un espacio de trabajo de Dart (app-web-02).
  Un cambio en `/apps/comun` o en `/contrato` corre también la CI de las
  apps. La decisión no cambia.
- **Nota (2026-11-15)**: `/apps/apple` es un proyecto Flutter con destinos
  iOS y macOS. Su CI corre en Linux (pruebas de Dart, sin compilar para
  Apple) y las compilaciones de Apple se hacen en la Mac con un script
  (app-apple-08). La decisión no cambia.
- **Nota (2026-11-15)**: se suma `/apps/nativo`, un paquete con los
  adaptadores que comparten Apple y Android (app-android-02); un cambio ahí
  corre las CI de las dos. `/apps/android` es un proyecto Flutter; su CI
  corre en Linux y, a diferencia de Apple, compila el APK de desarrollo. La
  clave de firma de producción de Android nunca entra al repositorio
  (app-android-04). La decisión no cambia.
- **Nota (2026-11-15)**: al crear una etiqueta, la CI arma la imagen y la
  sube a `ghcr.io` (base-plataforma-11); Dependabot abre pull requests con
  las actualizaciones (base-plataforma-14). La decisión no cambia.
- **Nota (2026-11-15)**: se suma `/vigia`, el proyecto de vigilancia (P-20,
  vigia-04), con su propia CI y etiquetas `vigia-vX.Y.Z`. La decisión no
  cambia.

## base-plataforma-06 — Formato del contrato (2026-11-14)
- **Decisión**: **OpenAPI 3.1**, escrito en **TypeSpec** (versión 1.x), que
  genera el OpenAPI. El OpenAPI generado se versiona en el repositorio y es
  lo que usan los generadores de clientes de cada app.
- **Alternativas descartadas**: OpenAPI escrito a mano (largo y fácil de
  equivocar); Protobuf + Connect (no es REST: conflictos, idempotencia y
  subida de archivos habría que resolverlos a mano); OpenAPI 3.2 (los
  generadores todavía no la soportan bien; pasar de 3.1 a 3.2 no rompe).
- **Por qué**: más corto y legible, con chequeo de errores, sin perder el
  estándar que entienden todas las herramientas.
- **Cambiarlo después**: fácil (se deja TypeSpec y se sigue con el OpenAPI
  generado).

---

## base-plataforma — fase producción

Versiones y precios vigentes al decidir (noviembre de 2026): `.com` en
Cloudflare ≈ USD 11,17/año (mayorista de Verisign USD 10,97 desde el 1 de
noviembre de 2026, más USD 0,20 de ICANN); cloudflared 2026.10.0; nginx
1.30.6 (estable) con nginx-acme 0.4.x; Brevo gratis (300 correos por día);
Resend gratis (3.000 por mes, 100 por día, 1 dominio); `ghcr.io` gratis para
imágenes privadas (GitHub avisa con un mes si cambia); restic 0.19.1;
Backblaze B2 USD 6,95 por TB al mes, 10 GB gratis y descargas gratis hasta 3
veces lo guardado; Hostinger KVM 1 en São Paulo USD 6,49/mes pagando 24
meses, renueva a USD 11,99; Apple Developer Program USD 99/año; cuenta
completa de Google USD 25 una vez; cuenta gratuita de distribución limitada
de Google: hasta 20 aparatos. Evaluados y descartados: Tailscale Funnel,
Caddy 2.11.7, Traefik 3.7.16, Amazon SES, Mailjet, Cloudflare R2, Kopia
0.23.1, Healthchecks.io, UptimeRobot, Better Stack, Hostinger KVM 2, Vultr
São Paulo.

## base-plataforma-07 — Dominio: terminación y nombres derivados (2026-11-15)
- **Decisión**: terminación **`.com`**, en Cloudflare (base-plataforma-01).
  El nombre lo elige el usuario y el dominio se compra **apenas lo elija**.
  Nombre del paquete de Android: `com.<nombre>.recetas` (producción) y
  `com.<nombre>.recetas.dev` (desarrollo); Apple usa el mismo patrón. Si el
  nombre lleva guion, en el paquete va guion bajo.
- **Costo**: ≈ USD 11,17/año ≈ USD 0,93/mes, igual al renovar.
- **Alternativas descartadas**: `.app` (USD 8,20 el primer año, USD 14,20 al
  renovar); `.net` (USD 11,86).
- **Por qué**: la terminación más reconocible y ya presupuestada; comprar
  temprano fija el paquete de Android, evita que otro se quede con el
  nombre y suma antigüedad al dominio, que ayuda contra el spam.
- **Pendiente**: el nombre.
- **Cambiarlo después**: difícil (cambian el correo, los enlaces y el
  paquete de Android, que queda fijo al registrarlo en Google).

## base-plataforma-08 — Publicación de la notebook (2026-11-15)
- **Decisión**: mientras producción corra en la notebook, se publica con un
  **túnel de Cloudflare** (`cloudflared`): una conexión de salida desde la
  notebook, sin abrir ningún puerto del router. HTTPS: entre el navegador y
  Cloudflare, con el certificado gratuito de Cloudflare; entre Cloudflare y
  la notebook, por dentro del túnel. Termina al pasar al VPS.
- **Excepción aceptada**: Cloudflare ve el tráfico descifrado (la contraseña
  al entrar, las recetas y la contraseña del respaldo, que viaja en el
  contenido; el archivo de respaldo sigue cifrado con age). Contradice
  **temporalmente** el porqué de base-plataforma-02.
- **Alternativas descartadas**: abrir el puerto 443 del router con DNS
  dinámico y Let's Encrypt (cifrado de punta a punta, pero necesita IP
  pública —no anda con CGNAT— y expone la IP de la casa); un VPS de paso
  (relé) con túnel propio (≈ USD 4–7/mes: conviene adelantar el VPS);
  Tailscale Funnel (solo publica nombres `*.ts.net`: rompe `app.<dominio>` +
  `api.<dominio>` y la cookie de la web).
- **Por qué**: anda aunque el proveedor de internet no dé IP pública,
  cuesta USD 0, esconde la IP de la casa, frena ataques de tráfico masivo y
  da HTTPS automático.
- **Consecuencias**: tope de 100 MB por pedido, ya resuelto con la subida en
  partes de 50 MB (contrato-07); los términos de Cloudflare limitan servir
  video y archivos grandes por su CDN, y las fotos chicas de menos de 100
  personas son un riesgo bajo; la IP real de quien pide llega en un
  encabezado de Cloudflare, que el proxy de entrada tiene que leer.
- **Afecta a**: base-plataforma-02 (nota). No toca el contrato.
- **Cambiarlo después**: fácil (se cambia el DNS y se apaga el túnel).

## base-plataforma-09 — Proxy de entrada: nginx (2026-11-15)
- **Decisión**: **nginx 1.30** (rama estable) como proxy de entrada, en su
  propio contenedor de `/plataforma`, igual en desarrollo y en producción.
  Reparte `app.<dominio>` (web), `app.<dominio>/descargas/android/`
  (base-plataforma-19) y `api.<dominio>` (backend); pone los topes de tamaño
  (25 MB para fotos, 50 MB por parte, contrato-07), los encabezados de
  seguridad y los registros de acceso, y toma la IP real del encabezado de
  Cloudflare. En la notebook recibe lo que llega por el túnel; en el VPS
  recibe directo y saca los certificados de Let's Encrypt con el módulo
  oficial `ngx_http_acme_module` (0.4.x), sin certbot.
- **Alternativas descartadas**: Caddy 2.11 (la recomendación: la misma
  tecnología que ya servía la web y la configuración más corta); Traefik 3.7
  (descubre los contenedores solo, pero necesita acceso al control de
  Docker, y cada versión pierde soporte a los 6 meses); no poner proxy
  mientras sea la notebook (la notebook y el VPS quedarían armados
  distinto).
- **Por qué**: elección del usuario: el servidor web más difundido y
  documentado, muy rápido; con su módulo ACME ya no necesita una pieza
  aparte para los certificados.
- **Consecuencias**: configuración más larga que Caddy; nginx publicó
  parches de seguridad casi todos los meses de 2026, así que entra en el
  día de mantenimiento mensual (base-plataforma-14); para no mantener dos
  servidores web, la web también pasa a nginx (app-web-10).
- **Afecta a**: app-web (app-web-07 reemplazada por app-web-10). No rompe
  nada.
- **Cambiarlo después**: fácil (es un archivo de configuración).

## base-plataforma-10 — Correo de envío (2026-11-15)
- **Decisión**: los correos de Notificaciones salen **solo por SMTP**, con un
  único adaptador SMTP (el mismo que usa la bandeja de prueba del entorno
  desarrollo). Los **textos los arma el backend** (ES/EN); no se usan
  plantillas, paneles ni avisos automáticos de ningún servicio. El servicio
  es **pura configuración** (servidor, usuario, clave y remitente en los
  secretos de producción). **Principal: Brevo**, plan gratis (300 correos
  por día). **Respaldo: Resend**, plan gratis (3.000 por mes, 100 por día):
  si el principal rechaza o no responde, el adaptador reintenta por el
  respaldo. En el DNS de Cloudflare, **SPF, DKIM y DMARC** de los dos
  servicios; DMARC empieza en "solo informar" y después se endurece.
  Remitente `avisos@<dominio>`; las respuestas llegan al correo personal
  con el reenvío gratis de Cloudflare.
- **Alternativas descartadas**: Amazon SES (USD 0,10 cada 1.000, escala a
  10.000 personas, pero sin plan gratis para cuentas nuevas desde el 21 de
  julio de 2026 y con un modo de prueba del que hay que pedir salir); solo
  Brevo; Mailjet (6.000 por mes, 200 por día) + Resend; solo Resend (tope
  duro de 100 por día); el envío de correo de Cloudflare (beta, necesita el
  plan pago de Workers).
- **Por qué**: pedido del usuario: gratis y que cambiar sea lo más fácil
  posible. Cambiar de servicio es cargar sus registros en el DNS y cambiar
  cuatro líneas de configuración (≈ 15 minutos, sin código); el respaldo
  evita que nadie se quede sin su código para entrar si un plan gratis
  cambia.
- **Consecuencias**: dos cuentas; Brevo envía desde IP compartidas y su plan
  gratis puede sumar su marca (se verifica en la etapa 4); con 10.000
  personas habrá que pasar a un plan pago (Brevo desde USD 9/mes) y revisar
  el presupuesto (P-19).
- **Afecta a**: backend (adaptador de Notificaciones: SMTP con servidor
  principal y de respaldo). No toca el contrato.
- **Cambiarlo después**: fácil.

## base-plataforma-11 — Despliegue (2026-11-15)
- **Decisión**: al crear una etiqueta (`backend-vX.Y.Z`, `app-web-vX.Y.Z`),
  la **CI arma la imagen y la sube a `ghcr.io`** (privado). En el servidor,
  el usuario corre **`desplegar <proyecto> <versión>`**, que: (1) hace un
  respaldo de la base; (2) baja la imagen con esa etiqueta; (3) la levanta;
  (4) espera a que `/v1/salud` responda; (5) si no responde, vuelve sola a
  la versión anterior y avisa. Las migraciones (goose, backend-17) tienen
  que ser **compatibles con la versión anterior del backend**: primero se
  agrega y recién en una versión siguiente se borra; si alguna vez no se
  puede, queda el respaldo del paso 1.
- **Alternativas descartadas**: que el servidor arme las imágenes desde la
  etiqueta (sin registro, pero la imagen puede diferir de la probada y en
  un VPS de 1 núcleo compilar Flutter es lento); que la CI despliegue sola
  (GitHub necesitaría una llave para entrar al servidor).
- **Por qué**: corre exactamente la imagen que probó la CI; el servidor no
  compila; el usuario elige cuándo.
- **Consecuencias**: depende de que `ghcr.io` siga gratis para imágenes
  privadas (aviso de un mes si cambia). El APK de Android no pasa por acá
  (base-plataforma-19).
- **Cambiarlo después**: fácil (cambiar de registro es una línea).
- **Nota (2026-11-16)**: con el repositorio público, las imágenes de
  `ghcr.io` siguen **privadas** (base-plataforma-20). La decisión no cambia.

## base-plataforma-12 — Respaldos (2026-11-15)
- **Decisión**: **restic 0.19** (cifra antes de que nada salga de la
  máquina y guarda cada archivo una sola vez). Diario, se guardan **30
  días**.
  - **Qué**: la base completa (copia lógica de todos los esquemas), el
    volumen de fotos, el archivo de secretos de producción, la carpeta de
    descargas y la clave de firma de Android (app-android-04). No entran los
    temporales (partes de subida y exportaciones de 24 h).
  - **Dónde**: un **disco externo en casa** (restauración rápida) y una
    **copia cifrada en Backblaze B2** (fuera de casa), que guarda unos días
    las versiones borradas: ni con la notebook en la mano se pueden borrar
    los respaldos de golpe.
  - **Prueba**: todos los meses, sola: baja el último respaldo, levanta una
    base descartable, cuenta cuentas y recetas y revisa fotos al azar; antes
    de abrir, una restauración completa a mano en otra máquina; la mudanza
    al VPS es una restauración.
  - **Contraseñas**: la del repositorio de restic y la de la clave de
    Android van a un gestor de contraseñas fuera de la notebook.
  - **En el VPS**: el VPS respalda a B2 y la notebook de la casa copia todos
    los días de B2 al disco externo.
- **Costo**: USD 0 hoy (10 GB gratis en B2); ≈ USD 2,4/mes con 10.000
  personas (~350 GB); compra única de un disco externo.
- **Alternativas descartadas**: restic + B2 sin disco en casa; restic +
  Cloudflare R2 (todo en una cuenta; ≈ USD 5,1/mes a 10.000 personas); Kopia
  + B2 (versión 0.x, con una falla reciente que podía perder datos); dos
  discos que rotan cada semana con una persona de confianza (la copia de
  afuera atrasa hasta 7 días y depende de acordarse); un equipo con disco
  en la casa de una persona de confianza (un aparato más para mantener);
  solo un disco en casa (no cumplía ANALISIS §7).
- **Por qué**: pedido del usuario de tener los respaldos en casa, sumado a
  la regla 3-2-1 que pide §7 (una copia fuera); los respaldos quedan fuera
  de la cuenta de Cloudflare; restic es maduro y está en Go.
- **Afecta a**: ANALISIS §7 y §9 (redacción). No rompe nada.
- **Cambiarlo después**: fácil (restic copia un repositorio entero a otro
  destino).

## base-plataforma-13 — Monitoreo: señales al vigía (2026-11-15)
- **Decisión**: la vigilancia la hace el proyecto **vigía** (P-20). Esta fase
  fija **qué se vigila y cómo se avisa**: tareas programadas de la notebook
  (fuera de Docker) mandan **señales HTTP**, una dirección por control; la
  variante `/falla`, con un texto corto, indica que algo falló. Formato
  compatible con Healthchecks.io (plan B gratis). Controles:

  | Control | Cada cuánto | Aviso si… |
  |---|---|---|
  | Salud de la API y de la web, por la dirección pública | 5 min | falla o no llega señal en 10 min |
  | Respaldo diario a disco y copia a B2 (por separado) | 1 día | no llega señal en 26 h o terminó con error |
  | Prueba de restauración | 1 mes | falló o no corrió |
  | Disco de fotos y disco externo de respaldos | 1 día | pasan el 70 % |

  Avisos por Telegram y correo; resumen diario del vigía.
- **Alternativas descartadas**: ver P-20.
- **Por qué**: si la notebook se apaga o se corta internet, las señales dejan
  de llegar y el vigía avisa igual; las señales desacoplan lo vigilado de
  quien vigila.
- **Consecuencias**: hasta que el vigía esté listo, no se abre a la primera
  persona.
- **Cambiarlo después**: fácil.

## base-plataforma-14 — Mantenimiento base (2026-11-15)
- **Decisión**:
  - **Linux**: parches de seguridad automáticos todas las noches; si hace
    falta reiniciar, reinicia sola a las 4 de la mañana y Docker vuelve a
    levantar todo.
  - **Imágenes y librerías**: **Dependabot** (gratis en GitHub) abre pull
    requests con versiones nuevas de nginx, PostgreSQL, Go, las librerías de
    Go y Dart y las acciones de la CI; un **día de mantenimiento mensual**
    mezcla, etiqueta y despliega. La CI falla si libvips es anterior a
    8.18.3.
  - **PostgreSQL mayor**: una vez por año, planificado, con respaldo y
    restauración probada en la versión nueva.
  - **Firewall**: todo cerrado hacia adentro; con el túnel no se abre
    ningún puerto. En producción **ningún contenedor publica puertos** hacia
    afuera (Docker se saltea el firewall común): el túnel entra por la red
    interna de Docker.
  - **Contenedores**: usuarios sin permisos de administrador, solo lectura
    donde se pueda, ninguno con acceso al control de Docker.
  - **Acceso**: SSH solo con llave, sin contraseña y sin entrar como
    administrador, desde la red de la casa; desde afuera, por el mismo
    túnel con **Cloudflare Access** (gratis hasta 50 usuarios).
  - **Cuentas**: verificación en dos pasos en GitHub, Cloudflare, Backblaze,
    Brevo, Resend, Google y Apple; gestor de contraseñas para las críticas
    (Bitwarden gratis si no hay otro).
  - **Notebook**: sin suspensión ni hibernación, cerrar la tapa no hace
    nada; registros de contenedores con tope de tamaño; hora sincronizada
    (las firmas de fotos y las sesiones dependen de ella).
  - **Cortes cortos**: la batería hace de UPS; con menos del 10 % se apaga
    ordenada. Una UPS chica para el router queda **opcional** (compra
    única).
- **Alternativa**: ajustes a cada punto (el usuario aprobó el paquete tal
  cual).
- **Por qué**: lo que más ataca a un servidor son fallas conocidas; cierra
  lo que base-plataforma-03 pedía mitigar.
- **Costo**: USD 0/mes.
- **Cambiarlo después**: fácil.

## base-plataforma-15 — Cifrado del disco de la notebook (2026-11-15)
- **Decisión**: disco cifrado con **desbloqueo automático por el chip TPM
  2.0** de la notebook y **arranque seguro** activado. Después de un corte de
  luz vuelve sola; si alguien saca el disco, no lo puede leer. En el BIOS se
  verifica que haya TPM 2.0 y se activa, si existe, "encender al volver la
  luz".
- **Alternativas descartadas**: contraseña al arrancar con desbloqueo remoto
  por SSH (fuerte, pero la plataforma queda caída hasta desbloquearla, y no
  se puede desde fuera de la casa); contraseña escrita en el teclado (la más
  fuerte, hay que estar frente a la notebook); sin cifrado (rompe la
  mitigación de ANALISIS §9).
- **Por qué**: equilibra la disponibilidad (vuelve sola) con la protección
  si se roban el disco.
- **Consecuencias**: si se llevan la notebook entera, el disco se abre al
  prenderla y la protección pasa a ser el inicio de sesión de Linux; las
  contraseñas de las personas y los tokens ya se guardan como huellas
  (backend-15, backend-16) y los respaldos van cifrados.
- **Cambiarlo después**: fácil (se le agrega una contraseña al arranque).

## base-plataforma-16 — Cuándo pasar al VPS (P-17) (2026-11-15)
- **Decisión**: se pasa al VPS si se cumple **alguno** de estos criterios:
  1. **Disponibilidad**: más de 8 h de caída en un mes, o un corte de más de
     24 h (lo mide el vigía).
  2. **Velocidad**: las fotos o la web no cumplen los tiempos de ANALISIS §7
     porque la subida de internet de la casa no alcanza.
  3. **Personas**: se va a invitar a más de 50 personas o a personas
     desconocidas.
  4. **Usuario**: la notebook hace falta para otra cosa, o hay mudanza o
     viajes largos.
  La mudanza es una restauración desde el respaldo; después se cambia el
  DNS al VPS (tráfico directo, base-plataforma-02) y se apaga el túnel.
- **Alternativas descartadas**: producción directo en el VPS al abrir (cambia
  P-17 y suma ≈ USD 6,5/mes desde el primer día); una fecha fija.
- **Por qué**: medible y sin pagar un servidor antes de necesitarlo.
- **Cambiarlo después**: fácil.

## base-plataforma-17 — VPS elegido (P-17) (2026-11-15)
- **Decisión**: **Hostinger KVM 1 en São Paulo** (1 núcleo, 4 GB de RAM, 50
  GB de disco, 4 TB de tráfico): USD 6,49/mes pagando 24 meses por
  adelantado (≈ USD 156), renueva a USD 11,99. Se contrata recién cuando se
  cumple un criterio de base-plataforma-16; ese día se verifican el precio y
  que São Paulo siga disponible para VPS (en 2026 hubo información
  contradictoria al respecto).
- **Alternativas descartadas**: Hostinger KVM 2 en São Paulo (2 núcleos, 8
  GB, 100 GB; USD 8,79 → 14,99); Vultr São Paulo 2 GB (USD 15/mes, sin
  compromiso, con la mitad de RAM). En Brasil no hay Hetzner ni Contabo.
- **Por qué**: la opción más barata con los 4 GB que estimó el backend; deja
  el mayor margen en el presupuesto.
- **Consecuencias**: con 50 GB entran unas 1.000–1.400 personas (~35 MB de
  fotos por persona); al acercarse al 70 % se mudan las fotos a un
  almacenamiento externo (adaptador, backend-06) o se agranda el plan, con
  el presupuesto revisado (P-19). 1 núcleo compartido entre la API, las
  fotos y los respaldos.
- **Cambiarlo después**: fácil (Docker + respaldo = mudarse a otro VPS en
  una tarde).

## base-plataforma-18 — Cuenta paga de Apple (app-apple-02) (2026-11-15)
- **Decisión**: **no se paga por ahora**. Las personas con iPhone, iPad o
  Mac usan la web en Safari (se puede agregar a la pantalla de inicio). Se
  revisa cuando **5 personas con iPhone o Mac pidan la app** y la web no les
  alcance.
- **Alternativas descartadas**: pagar al abrir a la primera persona (USD
  99/año ≈ 8,25/mes; con el VPS renovado el total pasa a ≈ USD 21,2, sobre
  el tope); pagar y subir el tope a ≈ USD 22 (cambia P-04 y P-19 y obliga a
  volver a la etapa 1).
- **Por qué**: respeta el tope en todos los escenarios y la web ya tiene
  todas las funciones.
- **Consecuencias**: el usuario sigue reinstalando cada 7 días en sus
  aparatos; los demás no tienen la app nativa. Con la cuenta paga, TestFlight
  admite hasta 10.000 personas, cada versión vence a los 90 días y la
  primera versión para personas de afuera pasa una revisión liviana.
- **Cambiarlo después**: fácil (se paga el día que se cumpla el disparador).

## base-plataforma-19 — Android: publicación, paquete y cuenta de Google (app-android-03, app-android-05) (2026-11-15)
- **Decisión**:
  - **Publicación**: `https://app.<dominio>/descargas/android/` con
    `ultima.json` (versión, número de versión de Android y huella SHA-256) y
    `recetas-<versión>.apk`, en una **carpeta propia** que sirve el nginx de
    entrada, aparte de la imagen de la web. El script de la notebook
    (app-android-04) firma, verifica y deja ahí los dos archivos.
    `ultima.json` sin caché; cada APK, con la versión en el nombre, con
    caché. La descarga **no pide sesión** (como los archivos de la web: el
    APK no tiene datos ni secretos; la regla "todo pedido autenticado" es
    del contrato). La carpeta entra en los respaldos.
  - **Paquete**: `com.<nombre>.recetas` y `com.<nombre>.recetas.dev`
    (base-plataforma-07), fijo el día que se compra el dominio.
  - **Cuenta de Google**: la **gratuita de distribución limitada (hasta 20
    aparatos)**, abierta **apenas existan el dominio y la clave de
    producción**, sin esperar a 2027; ese día se registra el paquete con un
    APK firmado con la clave de producción y se autorizan los aparatos
    propios. La **completa** (USD 25 una vez, con documento) recién cuando
    se superen **15 aparatos autorizados**.
- **Alternativas descartadas**: la cuenta completa desde el arranque (sin
  autorizar aparato por aparato, pero USD 25 y documento quizás sin
  necesidad); publicar el APK en GitHub (el repositorio es privado) o dentro
  de la imagen de la web (ata las versiones de Android a las de la web).
- **Por qué**: USD 0 mientras alcance, coherente con app-android-03; la
  fecha de 2027 no se conoce, así que conviene registrar temprano.
- **Consecuencias**: cada aparato se autoriza con un código o QR que su
  dueño confirma; el enlace vence a los 7 días y quitar un aparato no se
  deshace.
- **Cambiarlo después**: fácil (se pasa de la limitada a la completa, no al
  revés).

## base-plataforma-20 — Repositorio público (2026-11-16)
- **Decisión**: el repositorio de GitHub es **público**, con `main` protegida
  (PR obligatorio, chequeos obligatorios y al día, sin push directo, sin
  forzar ni borrar, también para administradores). Reemplaza solo la
  visibilidad de base-plataforma-05; el resto de base-plataforma-05 sigue
  vigente. Medidas que vienen con lo público:
  - commits con la **dirección privada de GitHub**
    (`…@users.noreply.github.com`) y la opción que bloquea los push que
    muestran el correo;
  - **protección contra secretos de GitHub** activada (frena un push que trae
    una clave);
  - las **imágenes de `ghcr.io` siguen privadas**;
  - las **claves de las señales del vigía no van en `controles.yaml`**: van
    al archivo de secretos del vigía (vigia-02, vigia-04);
  - nunca datos reales, correos ni secretos en el repositorio, ni en
    ejemplos ni en pruebas.
- **Alternativas descartadas**: privado con GitHub Pro (USD 4/mes); privado y
  gratis con protección solo local (ver P-21).
- **Por qué**: GitHub Free no deja proteger ramas en repositorios privados
  (responde "Upgrade to GitHub Pro"); elección del usuario por USD 0.
- **Consecuencias**: la arquitectura y los documentos quedan a la vista; los
  minutos de GitHub Actions dejan de ser un límite; cambia E4 (P-21). Surgió
  en la etapa 1 de la fase desarrollo, que sigue con su "Terminado cuando…"
  sin cambios.
- **Costo**: USD 0.
- **Cambiarlo después**: fácil para el futuro; lo que ya fue público no se
  puede despublicar.

---

## contrato

Versiones vigentes al decidir (noviembre de 2026): TypeSpec 1.18,
openapi-generator 7.2x, Schemathesis 4.x; oasdiff, Redocly CLI y Prism en
su última versión estable. Todo cuesta USD 0.

## contrato-01 — Versionado (2026-11-14)
- **Decisión**: la versión **mayor va en la dirección** (`/v1/…`). Lo que no
  rompe (un campo opcional, una operación nueva) sube la menor y queda en la
  misma mayor. Si algo rompe, nace `/v2/` y la anterior sigue andando
  **90 días**, avisando con los encabezados estándar `Deprecation`
  (RFC 9745) y `Sunset` (RFC 8594). Cada app manda su versión en el
  encabezado `App-Version`; si es más vieja que la mínima aceptada, recibe
  el error `app.desactualizada` y muestra "actualizá la app". Las etiquetas
  siguen siendo `contrato-vX.Y.Z` (base-plataforma-05).
- **Alternativas descartadas**: versión por fecha en un encabezado (el
  backend necesita capas de traducción entre versiones); sin versión en la
  dirección (choca con "un cambio que rompe crea versión nueva").
- **Por qué**: se ve en cada pedido, la entienden todos los generadores y
  proxies, y dos mayores conviven sin trucos. Como no hay tiendas, el aviso
  de actualización es la forma de retirar versiones viejas de las apps.
- **Consecuencias**: mientras conviven dos mayores, el backend atiende dos
  juegos de direcciones.
- **Cambiarlo después**: media (cambiar de esquema ya es romper).

## contrato-02 — Errores (2026-11-14)
- **Decisión**: formato estándar **Problem Details** (RFC 9457,
  `application/problem+json`) con campos propios: `codigo` estable (por
  ejemplo `receta.nombre_vacio`), `errores[]` por campo (`campo`, `codigo`,
  `mensaje`) e `idPedido`. El mensaje viene en el idioma de
  `Accept-Language` (es/en), y cada app puede mostrar su propio texto según
  el código.
- **Tabla común de códigos**: 400 pedido mal armado · 401 sin sesión o
  vencida · 403 la ves pero no podés hacer eso (por ejemplo, editar una
  pública ajena) · **404 no existe o no la podés ver** (una privada ajena
  responde 404 y no 403, para no revelar que existe; V3) · 409 pedido
  idempotente todavía en proceso · 410 cursor de cambios vencido · 412
  conflicto de edición · 413 archivo muy grande · 422 regla de negocio rota
  (R1, R2…) o clave de idempotencia reusada · 428 falta `If-Match` · 429
  demasiados intentos (C6).
- **Alternativa descartada**: formato propio (`{ error: { … } }`).
- **Por qué**: estándar del IETF; muchos frameworks web lo traen hecho; los
  generadores lo leen como un tipo más; la idempotencia ya responde así.
- **Consecuencias**: cuatro nombres estándar en inglés (`type`, `title`,
  `status`, `detail`) conviven con los propios en español.
- **Cambiarlo después**: difícil (lo leen las tres apps).

## contrato-03 — Conflictos de edición (Y3) (2026-11-14)
- **Decisión**: al leer, el backend manda la versión en el encabezado
  `ETag` (y también en el contenido, solo para leer). Al guardar, la app la
  devuelve en `If-Match`. Si otro dispositivo guardó antes → **412** con la
  versión actual adentro, y la app ofrece "conservar la mía" o "quedarme
  con la otra". Guardar sin `If-Match` → **428**. Al refrescar, la app manda
  `If-None-Match` y recibe **304** si no cambió nada (recetas, colecciones y
  listas). Guardar es **reemplazar entera** la receta o colección; publicar,
  copiar y el resto son operaciones aparte. **Perfil y preferencias: gana el
  último que guarda**, sin conflicto.
- **Alternativa descartada**: versión en el contenido con respuesta 409.
- **Por qué**: estándar (RFC 9110) y trae de yapa el 304 para refrescar
  rápido (Y2). Las preferencias son cambios chicos que se rehacen en un
  segundo.
- **Consecuencias**: las apps manejan dos encabezados.
- **Cambiarlo después**: difícil (rompe).

## contrato-04 — Idempotencia (2026-11-14)
- **Decisión**: marcar y desmarcar favoritos son "poner" y "quitar"
  (PUT/DELETE), que no duplican por sí solas (F2). Todo lo que crea algo
  (receta, colección, copia, foto, exportación, importación, subida en
  partes) lleva el encabezado `Idempotency-Key`, un id al azar que la app
  genera por acción; el backend recuerda la respuesta **24 h**. Sin clave
  → 400; misma clave con otro contenido → 422; misma clave mientras el
  primero se procesa → 409. Los ids siempre los genera el backend.
- **Alternativas descartadas**: ids generados por la app (UUIDv7) y crear
  con PUT; `Idempotency-Key` en todo, favoritos incluidos.
- **Por qué**: un solo mecanismo para todo lo que crea. Pasar más adelante a
  ids de la app (para un modo sin conexión) es agregar operaciones, no
  rompe.
- **Consecuencias**: el backend guarda las claves con vencimiento (dónde:
  backend-10). Se apoya en el borrador IETF
  `draft-ietf-httpapi-idempotency-key-header` (versión 08, octubre de 2026,
  todavía no es RFC), que no cambió en lo esencial desde 2022.
- **Cambiarlo después**: fácil.

## contrato-05 — Paginación (2026-11-14)
- **Decisión**: **cursor opaco**. La app pide `?limite=` (por defecto 50,
  máximo 100) y `?cursor=`; la respuesta es `{ elementos, siguiente }`, y sin
  `siguiente` no hay más páginas. El orden es estable. Se escribe con la
  paginación que trae TypeSpec (`@list`, `@pageItems`, `@continuationToken`).
- **Alternativa descartada**: número de página con total.
- **Por qué**: no saltea ni repite nada si alguien crea o borra mientras se
  pasan las páginas, y sigue siendo rápido aunque crezcan los datos
  (datos → escalabilidad).
- **Consecuencias**: no hay "página 7 de 12" ni total exacto.
- **Cambiarlo después**: difícil (rompe).

## contrato-06 — "Cambios desde" (Y2) (2026-11-14)
- **Decisión**: **una consulta por módulo**: Perfil, Recetas, Colecciones y
  Favoritos tienen cada uno su `…/cambios?desde=<cursor>`. Devuelve qué se
  creó, cambió o borró (id, tipo y versión) y un cursor nuevo, paginado
  como en contrato-05. Los borrados quedan como "lápidas" (una marca de que
  algo se borró) durante **90 días**; con un cursor más viejo, la app recibe
  410 y vuelve a cargar todo.
- **Alternativas descartadas**: una sola consulta para todo (necesitaba un
  módulo nuevo, "Sincronización", y cambiaba ANALISIS §4.1); sin consulta,
  solo 304 por pantalla (sacaba "cambios desde" de §6).
- **Por qué**: respeta M1–M5 sin tocar el análisis: cada módulo es dueño de
  sus cambios y, si se separa como servicio, su consulta se va con él. Deja
  listo el futuro modo sin conexión.
- **Consecuencias**: un refresco completo son hasta 4 pedidos en paralelo;
  cada módulo guarda su registro de cambios y sus lápidas (ANALISIS §4.1;
  cómo: backend-11).
- **Cambiarlo después**: fácil (sumar una consulta única no rompe).

## contrato-07 — Subida de fotos y respaldos (2026-11-14)
- **Decisión**:
  - **Foto**: un solo pedido con la imagen (hasta **25 MB**; si pesa más,
    413) y `Idempotency-Key`. La respuesta trae la foto ya redimensionada,
    con sus tamaños.
  - **Importar un respaldo**: una **sesión de subida en partes**, definida
    en el contrato: se crea la sesión con el tamaño total → se mandan partes
    numeradas de hasta **50 MB** → se puede preguntar qué partes llegaron,
    para retomar → se cierra → se pide la importación con la sesión y la
    contraseña → corre en segundo plano → resumen (E9).
  - **Exportar**: corre en segundo plano; al terminar hay un enlace de
    descarga válido **24 h**, que permite retomar la descarga. El archivo se
    borra a las 24 h.
  - La contraseña viaja **solo en el contenido** del pedido (nunca en
    direcciones ni encabezados), no se guarda y no queda en los registros.
    Si el backend se reinicia en medio de un trabajo, el trabajo falla y se
    vuelve a pedir.
- **Alternativas descartadas**: todo en partes (3 pedidos por foto);
  respaldos con el protocolo tus 1.0 (queda fuera del OpenAPI, una librería
  más en cada app; el estándar del IETF que lo va a reemplazar sigue en
  borrador).
- **Por qué**: lo simple para el caso de todos los días y por partes solo
  donde hace falta. Un respaldo con fotos puede pasar 1 GB, y Cloudflare
  corta los pedidos de más de 100 MB en el plan gratuito (el túnel es lo
  probable en la fase producción); las descargas no tienen ese límite. Todo
  queda en el OpenAPI, así que los clientes se generan solos.
- **Consecuencias**: dos mecanismos; el de partes lo implementa el backend
  (módulo Respaldo; backend-12, backend-13 y backend-14).
- **Cambiarlo después**: media (sumar otro método no rompe).

## contrato-08 — Entrega de fotos (2026-11-14)
- **Decisión**: dentro de la receta (que se pide con sesión), cada foto
  trae una dirección por tamaño (miniatura y pantalla) con una **firma que
  vence a las 6 h** (`vencenEn`). La web y las apps cargan la imagen directo
  con esa dirección. **Matiza la regla 1 de ANALISIS §6**: la dirección se
  obtiene con un pedido autenticado; la imagen se baja con la firma.
- **Alternativas descartadas**: fotos con la sesión (complica la web y una
  CDN futura, y la caché funciona peor); mixto, con firma solo para las
  públicas (dos caminos para mantener).
- **Por qué**: rápido, con caché del navegador y listo para una CDN. La
  dirección es opaca, así que el backend puede cambiar dónde guarda las
  fotos sin que las apps se enteren.
- **Consecuencias**: quien consiga un enlace ve esa foto hasta que vence (un
  margen de horas sobre V3/V4). El vencimiento se configura en el backend.
  Afecta al backend (módulo Medios), a las tres apps y a la regla 1 de §6;
  no rompe nada, porque no hay nada construido.
- **Cambiarlo después**: fácil.

## contrato-09 — Cómo viaja la sesión (2026-11-14)
- **Decisión**: **token opaco**, un texto al azar que el backend busca en su
  lista de sesiones; cerrar una sesión es borrarlo (C3, al instante). Las
  apps de Apple y Android lo mandan en `Authorization: Bearer`. La **web**
  lo recibe en una **cookie segura** (`HttpOnly`, `Secure`,
  `SameSite=Strict`) dentro del mismo dominio, con chequeo de origen contra
  pedidos falsificados desde otro sitio. El contrato define las dos formas,
  y al iniciar sesión la app indica si es la web o una app nativa. La
  duración y la renovación de la sesión las decide el backend (módulo
  Identidad; backend-15).
- **Alternativas descartadas**: token en encabezado también en la web
  (queda expuesto a scripts, riesgo de XSS); token firmado (JWT) de vida
  corta con renovación (cerrar una sesión deja de ser inmediato, y son más
  piezas sin ganancia real a menos de 100 personas).
- **Por qué**: lo más seguro para la web y revocación inmediata.
- **Consecuencias**: dos formas de autenticarse en el contrato. La web y el
  backend tienen que estar en el mismo sitio (por ejemplo `app.<dominio>` y
  `api.<dominio>`), lo que se confirma en la fase producción.
- **Cambiarlo después**: media.
- **Nota (2026-11-15)**: confirmado en la fase producción: `app.<dominio>` y
  `api.<dominio>` detrás del mismo proxy de entrada (base-plataforma-09). La
  decisión no cambia.

## contrato-10 — Nombres y formatos (2026-11-14)
- **Decisión**:
  - **Nombres**: campos en español, sin tildes ni ñ, con mayúscula en cada
    palabra nueva (`porcionesBase`, `actualizadaEn`); direcciones en
    minúscula y plural (`/v1/recetas/{id}/fotos`); códigos de error en
    español (`receta.nombre_vacio`). Los encabezados son los estándar de
    HTTP; el único propio (`App-Version`) sigue su misma costumbre en
    inglés.
  - **Fechas**: RFC 3339 en UTC (`2026-11-14T15:20:00Z`); cada app las
    muestra en la zona horaria de la persona.
  - **Ids**: UUIDv7 generados por el backend, como texto. No revelan
    cantidades y se ordenan por fecha.
  - **Cantidades (R2)**: número decimal mayor que 0 con hasta 3 decimales, o
    `null` para "c/n" o "a gusto". Nunca texto.
  - **Redondeo del escalado**: P-18 (D32).
  - **Idioma**: la app manda `Accept-Language` (es o en) para los mensajes;
    el contenido de las recetas no se traduce.
  - **Texto**: UTF-8; el backend normaliza los tags (minúsculas y sin
    tildes).
- **Alternativa descartada**: el mismo paquete con nombres en inglés.
- **Por qué**: el mismo idioma que el glosario del análisis, en documentos y
  en código.
- **Cambiarlo después**: difícil (cambiar nombres rompe).

## contrato-11 — Clientes de las apps (2026-11-14)
- **Decisión**: cada app genera su cliente desde el OpenAPI publicado, cada
  vez que se compila, con el generador de su ecosistema, que se elige en el
  proyecto de cada app. El código generado **no se guarda** en el
  repositorio ni se edita a mano. La CI del contrato genera además un
  cliente de prueba con openapi-generator (7.2x), para avisar temprano si
  algo del OpenAPI no lo entienden los generadores.
- **Alternativas descartadas**: un solo generador para todas (adelanta una
  decisión que corresponde a cada app); cliente generado guardado en el
  repositorio (archivos grandes, conflictos al mezclar, riesgo de edición a
  mano).
- **Por qué**: el mejor generador para cada lenguaje y ningún cliente
  desactualizado.
- **Consecuencias**: el generador se elige tres veces; para compilar una app
  hace falta su generador instalado.
- **Cambiarlo después**: fácil.

## contrato-12 — Herramientas del contrato (2026-11-14)
- **Decisión**: kit completo, todo gratuito, de código abierto y en
  contenedores:
  - **oasdiff**: compara con la última etiqueta publicada y frena cualquier
    cambio que rompa sin versión mayor nueva.
  - **Redocly CLI**: valida el OpenAPI, aplica las reglas de estilo propias y
    arma la documentación navegable.
  - **Prism**: servidor de prueba que responde con los ejemplos del
    contrato; es un servicio del entorno desarrollo.
  - **Schemathesis 4.x**: genera miles de pedidos a partir del contrato y
    verifica que el backend responda lo que promete; lo corre la CI del
    backend (backend-19).
- **Alternativas descartadas**: kit mínimo (solo oasdiff y Redocly, sin
  verificación automática del backend); Spectral en lugar de Redocly (más
  configuración y sin documentación navegable).
- **Por qué**: cubre desde el diseño hasta la verificación y completa la
  etapa 6 de base-plataforma.
- **Consecuencias**: cuatro herramientas (Node, Go y Python), cada una en su
  contenedor. El soporte de OpenAPI 3.1 en oasdiff todavía figura como beta.
- **Costo**: USD 0 (entra en los 2.000 min/mes gratis de CI).
- **Cambiarlo después**: fácil (cada herramienta se reemplaza sola).

## contrato-13 — Casos de prueba compartidos del escalado (2026-11-14)
- **Decisión**: los casos de prueba de R8/R9 viven en
  `/contrato/casos/escalado.json` y los usan el backend y las tres apps.
- **Alternativa descartada**: una copia de los casos en cada proyecto (se
  desincronizan).
- **Por qué**: una sola fuente, para que las cuatro implementaciones del
  escalado den exactamente lo mismo.
- **Cambiarlo después**: fácil.

## contrato-14 — Formatos de foto en la subida (2026-11-14)
- **Decisión**: la operación de subir foto declara los formatos aceptados
  (**JPEG, PNG y WebP**) y un error propio, `foto.formato_no_soportado`
  (422). Surge de backend-07.
- **Alternativa descartada**: no declarar formatos y aceptar cualquier
  imagen (el backend tendría que leer HEIC y AVIF; ver backend-07).
- **Por qué**: que cada app sepa qué mandar y convierta HEIC a JPEG antes de
  subir.
- **Afecta a**: backend (Medios), app-apple y app-android (convierten HEIC),
  app-web (aviso claro si en Chrome o Firefox se elige un HEIC). No rompe
  nada: no hay nada construido, y sumar un formato más adelante es un
  cambio menor.
- **Cambiarlo después**: fácil.

---

## backend

Versiones vigentes al decidir (noviembre de 2026): Go 1.27; oapi-codegen
2.8; PostgreSQL 19 (versión final prevista para el 19 de noviembre de 2026;
si se atrasa, se arranca con 18 y se pasa después sin tocar código); pgx
5.10; sqlc; goose 3.29; River 0.x; govips 2.18 con libvips 8.18.3 o
posterior; age 1.3. Todo cuesta USD 0.

## backend-01 — Estilos de arquitectura por módulo (2026-11-14)
- **Decisión**: dentro del marco (P-05, ANALISIS §4.2) se usan **tres
  estilos**, uno principal por módulo:
  - **DDD táctico** (las reglas viven en agregados que nunca quedan
    inválidos): Identidad, Recetas y Colecciones.
  - **Script de transacción** (cada caso de uso es una secuencia directa:
    leer, validar, guardar): Perfil, Favoritos, Medios y Notificaciones.
  - **Núcleo funcional** (las reglas son funciones puras): Respaldo.
  - **Reglas para todos**: funciones puras para los cálculos (escalado,
    referencias, normalización, visibilidad, vencimientos y límites); una
    carpeta por caso de uso; lecturas por su propio puerto, aparte de las
    escrituras (CQRS liviano); reloj y azar por puerto; **sin transacciones
    entre módulos** (lo que cruza módulos —K5, importar, borrar cuenta— va
    por pasos que se pueden repetir sin daño); pruebas de arquitectura en la
    CI (backend-18); sin event sourcing.
- **Alternativas descartadas**: DDD táctico en todos (más código sin
  ganancia en módulos simples); script de transacción en todos (las reglas
  de Recetas e Identidad quedan repartidas y repetidas); event sourcing
  (nadie necesita la historia, curva alta y choca con "se borra siempre",
  C4); rebanadas verticales puras (cada rebanada habla directo con la base
  y saltea los puertos; solo se toma la idea de una carpeta por caso de
  uso); CQRS completo (dos almacenamientos sincronizados sin necesidad).
- **Por qué**: cada módulo con la forma justa según cuántas reglas tiene.
  El centro de Respaldo es transformar datos (armar el respaldo, validar el
  formato, planificar la importación, armar el resumen), y eso se prueba
  sin base ni archivos.
- **Cambiarlo después**: fácil, módulo por módulo.

## backend-02 — Eventos entre módulos (2026-11-14)
- **Decisión**: **outbox**: el evento se guarda en la misma operación que el
  cambio y un repartidor en el mismo proceso lo entrega después,
  reintentando si falla; quien lo recibe tolera repetidos (si el mismo
  evento llega dos veces, el resultado es el mismo). Los eventos se definen
  en un **catálogo común sin lógica**, así que escucharlos no crea
  dependencias entre módulos (M4). Se implementa con River (backend-12).
- **Alternativas descartadas**: en memoria dentro del mismo pedido (si el
  backend se cae justo después de guardar, el evento se pierde: fotos que
  quedan para siempre o favoritos de una cuenta borrada); servicio de
  mensajería aparte (un contenedor más, sin ganancia con un solo backend,
  ni siquiera a 10.000 personas).
- **Por qué**: no se pierde nada si algo falla o se reinicia; separar un
  módulo (M5) es cambiar solo el transporte.
- **Consecuencias**: los otros módulos se enteran con alrededor de un
  segundo de demora.
- **Cambiarlo después**: fácil.

## backend-03 — Visibilidad entre módulos (2026-11-14)
- **Decisión**: **cada módulo decide la visibilidad de lo suyo y los demás le
  preguntan, en lote, por su interfaz**. Favoritos y Colecciones calculan
  "no disponible" al leer. Recetas pide a Medios la firma de las fotos solo
  para quien puede verlas (contrato-08). Con *RecetaBorrada* y
  *VisibilidadCambiada*, Favoritos y Colecciones **anotan el cambio** en su
  registro (para "cambios desde") en vez de guardar una marca.
- **Alternativa descartada**: copias locales de la visibilidad actualizadas
  por eventos (M3 tal como estaba): la regla se repetía en tres o cuatro
  lugares y había segundos de desfase.
- **Por qué**: V3 vive en un solo lugar y "no disponible" está siempre al
  día.
- **Consecuencias**: Medios ya no escucha *VisibilidadCambiada*. Mapa de
  dependencias (las consultas van siempre hacia abajo): Respaldo → Recetas,
  Colecciones, Favoritos, Medios; Favoritos → Recetas, Colecciones;
  Colecciones → Recetas, Perfil; Recetas → Perfil (nombre visible, V9),
  Medios (guardar y firmar); Identidad → Notificaciones; Perfil, Medios y
  Notificaciones no dependen de nadie. La entrada HTTP común usa a
  Identidad para saber quién hace cada pedido.
- **Afecta a**: ANALISIS §4.1 (redacción de M3). No rompe nada: no hay nada
  construido.
- **Cambiarlo después**: media.

## backend-04 — Lenguaje y framework (2026-11-14)
- **Decisión**: **Go 1.27**, con la biblioteca estándar **`net/http`** y
  **oapi-codegen 2.8**, que genera los tipos y la interfaz del servidor
  desde el OpenAPI 3.1. Plan B si su soporte de 3.1 se queda corto: la
  variante v3 de oapi-codegen mantenida por DoorDash (soporta 3.0, 3.1 y
  3.2).
- **Alternativas descartadas**: TypeScript + Node.js 26 LTS + Fastify 5
  (curva baja y mismo lenguaje que la web, pero más CPU por pedido,
  fronteras que solo cuida una herramienta, muchas dependencias de npm y
  una migración a Fastify 6 en camino); C# + .NET 10 LTS (u 11) + ASP.NET
  Core (muy rápido y parecido a Dart, pero más memoria y un ecosistema
  pensado para escribir el código primero y generar el OpenAPI después).
  También descartados: Dart (ecosistema de servidor chico), Kotlin con
  Spring o Ktor (JVM pesada para 1 núcleo y 4 GB), Rust (curva muy alta),
  Python (más lento).
- **Por qué**: lo más liviano y con menos mantenimiento para un VPS de 1
  núcleo (criterio: rapidez); el compilador prohíbe ciclos entre paquetes y
  la carpeta `internal/` impide usar lo privado de otro módulo (M2 y M4);
  HTTP, JSON y cifrado vienen en la biblioteca estándar.
- **Consecuencias**: lenguaje nuevo para aprender; libvips se usa con cgo
  (el contenedor se arma con un compilador de C); el soporte de 3.1 en
  oapi-codegen es reciente.
- **Costo**: USD 0.
- **Cambiarlo después**: difícil (es reescribir el backend); el contrato y
  Schemathesis protegen a las apps (ANALISIS §4.2).

## backend-05 — Base de datos (2026-11-14)
- **Decisión**: **PostgreSQL 19** con **pgx** (el conector) y **sqlc** (SQL
  escrito a mano que se convierte en código Go con tipos). **Un esquema y un
  usuario por módulo**: la base rechaza que un módulo lea tablas de otro
  (M1). Un esquema `infra` para lo común (idempotencia y trabajos).
- **Alternativas descartadas**: SQLite + Litestream (un solo escritor a la
  vez por archivo y crece solo en una máquina: techo para 10.000 personas);
  MongoDB (pesado para 1 núcleo; su ventaja de repartirse entre muchas
  máquinas no se usa a esta escala; licencia que no es de código abierto).
- **Por qué**: crece sin rediseño hasta 10.000 personas (datos →
  escalabilidad); transacciones sólidas para outbox y registro de cambios;
  búsqueda sin tildes incorporada.
- **Consecuencias**: un servicio más para administrar (actualizaciones
  mayores, respaldos).
- **Costo**: USD 0.
- **Cambiarlo después**: media (se reescriben los adaptadores de
  persistencia de cada módulo).

## backend-06 — Fotos: almacenamiento y entrega (2026-11-14)
- **Decisión**: las fotos viven en un **volumen del propio servidor** (la
  notebook y, después, el VPS). Medios verifica la firma (contrato-08) y
  entrega el archivo, con caché larga en el navegador. **Sin CDN por
  ahora.** No se suma ningún otro adaptador: solo el puerto de
  almacenamiento que el marco ya exige.
- **Alternativas descartadas**: Cloudflare R2 con dirección prefirmada (los
  datos quedan en Norteamérica, la dirección prefirmada no pasa por la CDN
  y es una cuenta más; ≈ USD 5/mes a 10.000 personas); Backblaze B2 (también
  Norteamérica y proveedor nuevo; ≈ USD 2,5/mes a 10.000 personas).
- **Por qué**: lo más cerca de las personas (São Paulo, o la casa mientras
  sea la notebook), USD 0 y ninguna pieza nueva (criterio: entrega rápida).
  Una CDN casi no acertaría la caché, porque cada foto la miran pocas
  personas y las firmas complican cachear.
- **Consecuencias**: el límite es el disco (el VPS preseleccionado trae 50
  GB; ~350 GB estimados a 10.000 personas): antes de acercarse a esa escala
  hay que mudar las fotos o agrandar el disco. Las fotos entran en el
  respaldo diario. Mientras sea la notebook, salen por la subida de
  internet de la casa. Cierra lo que base-plataforma-02 dejó al backend
  sobre la CDN.
- **Costo**: USD 0.
- **Cambiarlo después**: fácil (un adaptador nuevo y copiar los archivos;
  la dirección es opaca).
- **Nota (2026-11-14)**: las respuestas de fotos llevan permiso CORS para el
  origen de la web (backend-20).
- **Nota (2026-11-15)**: con el VPS elegido (50 GB) entran unas 1.000–1.400
  personas; el aviso del disco al 70 % lo da el vigía (base-plataforma-13,
  base-plataforma-17). La decisión no cambia.

## backend-07 — Fotos: formatos y tamaños (2026-11-14)
- **Decisión**: se aceptan **JPEG, PNG y WebP**; las apps de Apple y
  Android convierten HEIC a JPEG antes de subir. Se guardan en **WebP**, en
  dos tamaños: **miniatura de 400 px y pantalla de 1600 px** del lado más
  largo. Se giran según cómo se sacaron y después se **borran todos los
  metadatos** (incluida la ubicación GPS). El formato se verifica por el
  contenido y no por el nombre, y hay un límite de megapíxeles. libvips
  8.18.3 o posterior.
- **Alternativas descartadas**: aceptar HEIC y AVIF y convertir en el
  servidor (suma libheif, que tuvo fallas graves este año, y más CPU en 1
  núcleo); guardar en AVIF (algo menos de disco, pero codificar es bastante
  más lento y la subida tardaría segundos).
- **Por qué**: la menor superficie de ataque y codificación rápida
  (contrato-07 devuelve la foto ya redimensionada); publicar una receta no
  revela dónde vive la persona.
- **Afecta a**: contrato (contrato-14), app-apple y app-android (convierten
  HEIC), app-web (aviso si se elige un HEIC en Chrome o Firefox), ANALISIS
  R10. No rompe nada.
- **Cambiarlo después**: fácil.

## backend-08 — Búsqueda (2026-11-14)
- **Decisión**: **dentro de PostgreSQL**, con unaccent (quita tildes),
  pg_trgm (encuentra pedazos de palabra y tolera errores chicos) y la
  búsqueda de texto completo. Recetas y Colecciones tienen cada uno su
  índice en su esquema y su propia búsqueda (B1, B2), y la visibilidad se
  filtra en la misma consulta.
- **Alternativas descartadas**: Meilisearch (es una copia que se actualiza
  después: una receta recién pasada a privada podría aparecer unos segundos
  en lo público; un servicio más; versiones casi semanales); ParadeDB
  pg_search (imagen de PostgreSQL no oficial, actualizaciones atadas a la
  extensión, licencia AGPL).
- **Por qué**: ninguna pieza nueva y privacidad al instante (B2).
- **Consecuencias**: orden por relevancia más básico que un motor dedicado.
- **Costo**: USD 0.
- **Cambiarlo después**: fácil (el motor vive en el adaptador de búsqueda de
  cada módulo).

## backend-09 — Cifrado de respaldos (2026-11-14)
- **Decisión**: biblioteca **age 1.3** (formato estándar y revisado: bloques
  de 64 KiB autenticados que detectan cualquier cambio y el corte del
  archivo), **dentro de un sobre propio**: el archivo empieza con una marca
  y la versión de formato (E10), y la clave que recibe age se arma con la
  contraseña de la persona más una etiqueta fija de la plataforma, así no
  se abre con la herramienta común de age y la contraseña sola (E4).
  Además:
  - contraseña de **12 caracteres como mínimo**, ingresada dos veces (E5);
  - importar en **dos pasadas**: la primera descifra y verifica todo sin
    guardar nada; la segunda aplica (un archivo alterado no deja una
    importación a medias, E3); nunca queda nada descifrado en el disco;
  - **un solo trabajo de respaldo a la vez** (derivar la clave usa ~256 MB
    de memoria).
- **Alternativas descartadas**: lo mismo más una firma con una clave de la
  plataforma (protege también contra el dueño, pero es una clave para
  guardar para siempre; si se pierde, los respaldos viejos no se importan,
  justo lo que P-09 quiso evitar); construcción propia con Argon2id y
  XChaCha20-Poly1305 (alto riesgo de errores de diseño difíciles de ver).
- **Por qué**: cumple E2–E6 y E10 frente a cualquiera sin la contraseña, con
  una biblioteca revisada y sin claves que se puedan perder.
- **Consecuencias**: quien tenga la contraseña y el código del proyecto
  (privado) podría rearmar un respaldo; igual, la importación valida cada
  regla como si los datos fueran nuevos. Si algún día se abre la plataforma
  a personas desconocidas, reconsiderar la firma.
- **Costo**: USD 0.
- **Cambiarlo después**: fácil (la versión de formato permite leer los
  respaldos viejos y escribir los nuevos).
- **Nota (2026-11-16)**: con el repositorio público (P-21), la etiqueta fija
  queda a la vista: el archivo se puede abrir con la contraseña y el código
  público, y E4 ya no pide que el formato sea secreto. El mecanismo no cambia
  (age, sobre propio, versión de formato, dos pasadas). La decisión no cambia.

## backend-10 — Claves de idempotencia (contrato-04) (2026-11-14)
- **Decisión**: viven en la **entrada HTTP común**, en el esquema `infra`
  (infraestructura sin datos de negocio, así que M1 no cambia). La clave se
  reserva "en proceso" al llegar (repetida mientras tanto → 409), se guarda
  la respuesta al terminar y vence a las 24 h. Van separadas por cuenta;
  una huella del contenido detecta la clave reusada con otro contenido
  (422).
- **Alternativa descartada**: guardarla dentro de la transacción de cada
  módulo (cero duplicados incluso si el backend se cae en el peor
  milisegundo, pero el mecanismo se repite en cinco módulos).
- **Consecuencias**: si el backend se cae justo entre crear y guardar la
  respuesta, un reintento tardío podría duplicar; es muy raro.
- **Cambiarlo después**: fácil.

## backend-11 — Registro de cambios y lápidas (contrato-06) (2026-11-14)
- **Decisión**: una tabla de cambios en el esquema de cada módulo (Perfil,
  Recetas, Colecciones, Favoritos), que escribe el propio repositorio en la
  misma transacción que el cambio (no se puede olvidar). El cursor es un
  **contador por persona**, no la hora, para no saltear cambios que se
  confirman en otro orden. Lápidas por 90 días; un cursor más viejo recibe
  410. La "versión de vista" de una colección cambia también cuando cambia
  algo de adentro (una receta que se borra o pasa a privada), para que el
  304 sea correcto.
- **Alternativa descartada**: cursor por fecha (puede perder cambios que
  terminan casi al mismo tiempo).
- **Cambiarlo después**: media.

## backend-12 — Trabajos en segundo plano y outbox (2026-11-14)
- **Decisión**: **River**, una cola de trabajos en Go que vive en
  PostgreSQL (esquema `infra`). Encola en la misma transacción que el
  cambio, así que **hace de outbox** (backend-02): cada evento es un trabajo
  por destinatario, con reintentos. También corre las limpiezas periódicas:
  claves de idempotencia (24 h), lápidas (90 días), exportaciones (24 h),
  subidas abandonadas, códigos y sesiones vencidos. El usuario de cada
  módulo puede escribir **solo** en la tabla de trabajos de `infra`. Los
  trabajos de **respaldo no pasan por River**: corren en memoria en el mismo
  proceso que recibió el pedido, porque llevan la contraseña (que nunca se
  guarda); su estado vive en el esquema de Respaldo y, al arrancar, los que
  quedaron "en curso" pasan a "falló" (contrato-07).
- **Alternativa descartada**: un outbox hecho a mano (una tabla más un
  bucle): sin dependencia, pero con reintentos, programación y bloqueos
  escritos por nosotros.
- **Consecuencias**: River va por la versión 0.x (su API puede cambiar):
  queda encerrado en un adaptador de `infra`. Si algún día la API y los
  trabajos se separan en dos contenedores, los respaldos se quedan con la
  API.
- **Cambiarlo después**: fácil.

## backend-13 — Subida en partes (contrato-07) (2026-11-14)
- **Decisión**: las partes van a una carpeta propia de Respaldo en el disco
  y los datos de la sesión, a su esquema. Cada parte lleva una suma de
  verificación (detecta si se dañó en el camino). Una sesión que no se
  cierra en 24 h se borra. Tope total: 5 GB. Las exportaciones listas
  quedan en el disco, ya cifradas, durante 24 h.
- **Alternativa descartada**: guardar las partes en la base (hincha
  PostgreSQL y sus respaldos).
- **Cambiarlo después**: fácil.

## backend-14 — Secretos fuera de los registros (contrato-07) (2026-11-14)
- **Decisión**: los registros nunca incluyen el contenido de los pedidos; los
  datos sensibles usan un tipo "secreto" que se imprime como `***`. **Una
  prueba automática** exporta con una contraseña conocida y la busca en
  todos los registros: si aparece, falla.
- **Alternativa descartada**: confiar en revisarlo a mano.
- **Cambiarlo después**: fácil.

## backend-15 — Sesión (contrato-09) (2026-11-14)
- **Decisión**: token al azar de 256 bits; en la base se guarda solo su
  **huella**, así una copia robada de la base no sirve para entrar. Vence
  tras **30 días sin uso en la web y 90 en las apps nativas**, y se renueva
  sola al usarse (la última actividad se actualiza cada tanto, no en cada
  pedido). Recuperar el acceso o cambiar la contraseña cierra todas las
  sesiones. Cookie con prefijo `__Host-` (solo el dominio exacto de la API),
  `HttpOnly`, `Secure` y `SameSite=Strict`; se verifica el origen en todo lo
  que cambia algo.
- **Alternativa descartada**: sesiones cortas con renovación (más idas y
  vueltas; C3 ya permite cerrar cualquier sesión al instante).
- **Cambiarlo después**: fácil (los vencimientos se configuran).

## backend-16 — Contraseñas y códigos de Identidad (C1, C6) (2026-11-14)
- **Decisión**: contraseñas con **Argon2id**, con los parámetros mínimos de
  OWASP (19 MiB de memoria, 2 iteraciones, 1 de paralelismo). Códigos de 6
  dígitos que vencen a los 10 min, con 5 intentos, guardados como huella.
  Límites: 5 intentos de entrar cada 15 min y 3 correos de código por hora,
  por correo. Los contadores van en la base, sin servicios extra.
- **Alternativa descartada**: bcrypt (aceptable, pero más débil frente a
  placas de video).
- **Cambiarlo después**: fácil (las huellas guardan sus parámetros).

## backend-17 — Migraciones (2026-11-14)
- **Decisión**: **goose 3.29**, con una carpeta por módulo y su tabla de
  versiones dentro de su propio esquema. Se aplican al arrancar, con
  bloqueo (API Provider), para que dos arranques no choquen.
- **Alternativas descartadas**: golang-migrate y Atlas (sirven, pero manejan
  peor una historia de cambios por esquema).
- **Cambiarlo después**: fácil.
- **Nota (2026-11-15)**: para que `desplegar` pueda volver a la versión
  anterior, cada migración tiene que ser compatible con la versión anterior
  del backend (base-plataforma-11). La decisión no cambia.

## backend-18 — Pruebas de arquitectura (M1–M5) (2026-11-14)
- **Decisión**: una prueba propia en Go que lee el grafo de paquetes, más
  reglas de importación en el linter (el revisor automático de código).
  Verifica que el dominio no use tecnología, que entre módulos solo se use
  la interfaz pública y el catálogo de eventos, que no haya ciclos y que el
  SQL de cada módulo mencione solo su esquema (la base lo hace cumplir igual,
  backend-05). Cada módulo guarda sus partes privadas bajo su propia
  carpeta `internal/`, y Go impide que otro módulo las importe.
- **Alternativa descartada**: una herramienta externa de arquitectura (otra
  pieza más, con su propio formato).
- **Cambiarlo después**: fácil.

## backend-19 — Verificación contra el contrato (contrato-12, contrato-13) (2026-11-14)
- **Decisión**: la CI del backend levanta PostgreSQL y el backend en
  contenedores y corre **Schemathesis contra el backend real**. Las pruebas
  del dominio de Recetas leen `/contrato/casos/escalado.json`. El código del
  servidor que genera oapi-codegen **no se guarda**: se genera al compilar
  (el mismo criterio que contrato-11).
- **Alternativa descartada**: correr Schemathesis solo contra Prism (no
  prueba el backend).
- **Cambiarlo después**: fácil.

## backend-20 — CORS para la web (app-web-01) (2026-11-14)
- **Decisión**: Medios responde las **fotos** con un permiso CORS simple (sin
  credenciales) **solo para el origen de la web** de cada entorno. La entrada
  HTTP común acepta pedidos **con credenciales** (la cookie de sesión) solo
  desde ese mismo origen. El origen se configura por entorno: en
  producción, `app.<dominio>`. Precisa backend-06 y backend-15.
- **Alternativas descartadas**: sin CORS en las fotos (Flutter web las
  muestra con elementos `<img>` aparte, más lentos cuando hay muchas fotos en
  pantalla); pasar las fotos por el origen de la web (evita CORS, pero mezcla
  web y API en el proxy y depende de la fase producción).
- **Por qué**: Flutter web necesita los datos de la imagen para dibujarla, y
  el navegador solo se los da con permiso CORS. La firma sigue protegiendo
  cada foto (contrato-08).
- **Afecta a**: backend (Medios y entrada HTTP común), app-web y fase
  producción de base-plataforma. No toca el contrato ni rompe nada.
- **Cambiarlo después**: fácil (es configuración).

---

## app-web

Versiones vigentes al decidir (noviembre de 2026): Flutter 3.50 (11 de
noviembre de 2026) con Dart 3.14; Riverpod 3.4; go_router 18; swagger_parser
1.44 con retrofit 4.9 y dio 5.9; Caddy 2.11. Todo cuesta USD 0.

## app-web-01 — Tecnología (2026-11-14)
- **Decisión**: **Flutter 3.50** (Dart 3.14) con **Riverpod 3** para el
  estado y los datos. La web es una **SPA** (el navegador baja la app una
  vez y después solo habla con la API), sin páginas armadas en un servidor
  propio de la web (SSR).
- **Alternativas descartadas**: SvelteKit 3 en modo SPA (Svelte 5.57,
  TypeScript 7): la web más liviana y con el comportamiento normal del
  navegador, pero un lenguaje nuevo y otro código para Apple y Android;
  React 19.3 + Vite 8 + TanStack: el ecosistema más grande y deja abierto
  React Native, pero un lenguaje nuevo y más piezas para elegir y mantener.
  También descartados: Next.js y Nuxt (pensados para SSR); Angular y Vue
  (sin ventaja sobre las anteriores para este caso); Kotlin Multiplatform
  (lenguaje nuevo, sin ventaja sobre Flutter).
- **Por qué**: SPA porque la cookie de sesión es solo del dominio de la API
  (`__Host-`, backend-15) y un servidor propio de la web no la recibiría;
  como sin cuenta no se ve nada (D20), no hace falta que los buscadores
  indexen páginas. Flutter porque, descartando por mantenimiento y
  coherencia (ANALISIS §8): una persona con 23 casos de uso para tres apps
  los escribe una vez; es un lenguaje menos (§9); el escalado de las apps
  vive en un solo lugar (riesgo "apps desparejas"); reusa la experiencia
  previa con Flutter, Riverpod y Clean Architecture.
- **Consecuencias**: primera carga más pesada (alrededor de 2 MB
  comprimidos; después queda guardada en el navegador); Flutter dibuja
  sobre un lienzo, así que buscar en la página, traducir o imprimir desde
  el navegador andan mal; las fotos necesitan permiso CORS (backend-20);
  Material y Cupertino están pasando a paquetes aparte (Flutter 3.50) y
  algunos paquetes de terceros van a tardar en adaptarse; Riverpod 3 es una
  versión de transición y podría salir una 4, por eso Riverpod queda solo en
  la capa de estado y pantallas. app-apple y app-android quedan con Flutter
  como **candidata**; se confirma o se descarta en su conversación. La etapa
  1 del plan es una prueba de riesgo (app-web-09).
- **Costo**: USD 0.
- **Cambiarlo después**: difícil (reescribir la interfaz); se puede rehacer
  solo la web y dejar Flutter en Apple y Android.
- **Nota (2026-11-15)**: Flutter quedó confirmado para Apple
  (app-apple-01); para Android sigue como candidata. La decisión no cambia.
- **Nota (2026-11-15)**: Flutter quedó confirmado también para Android
  (app-android-01): las tres apps usan Flutter. La decisión no cambia.

## app-web-02 — Organización del código (2026-11-14)
- **Decisión**: `/apps` es un **espacio de trabajo de Dart** (varios paquetes
  en un mismo repositorio que se resuelven juntos) con `/apps/comun`, un
  paquete compartido (cliente generado, casos de uso de la app, escalado,
  estado, textos, diseño y pantallas), y un proyecto por app: `/apps/web`
  ahora; `/apps/apple` y `/apps/android` en sus proyectos.
- **Alternativas descartadas**: un solo proyecto Flutter con las tres
  plataformas (`/apps/flutter`: menos configuración, pero cambia
  base-plataforma-05 y une la versión y la CI de las tres); solo `/apps/web`
  por ahora (no adelanta nada, pero al llegar Apple hay que separar lo común
  y reorganizar código ya hecho).
- **Por qué**: respeta la estructura de base-plataforma-05 (solo suma
  `/apps/comun`), la CI por carpeta y las etiquetas por app
  (`app-web-vX.Y.Z`); si Apple terminara en otra tecnología, Android igual
  aprovecha lo común.
- **Afecta a**: base-plataforma-05 (nota), app-apple, app-android, ANALISIS
  §4.3 y §9. No rompe nada.
- **Cambiarlo después**: media (mover paquetes).
- **Nota (2026-11-15)**: `/apps/apple` existe como proyecto Flutter que usa
  `comun` (app-apple-01), y el diseño de `comun` suma una variante con
  toques de Apple (app-apple-05). La web no cambia. La decisión no cambia.
- **Nota (2026-11-15)**: el espacio de trabajo suma `/apps/nativo`, que
  comparten solo Apple y Android (app-android-02), y `/apps/android`, que usa
  `comun` y `nativo`. El diseño de `comun` suma la regla de la bisagra de
  los plegables (app-android-08). La web no cambia. La decisión no cambia.

## app-web-03 — Cliente generado (contrato-11) (2026-11-14)
- **Decisión**: **swagger_parser 1.44** genera el cliente desde el OpenAPI
  publicado, con **retrofit 4.9** (genera las llamadas a partir de
  anotaciones) y **dio 5.9** (el cliente HTTP). En la web, dio manda la
  cookie de sesión con `BrowserHttpClientAdapter(withCredentials: true)`. Se
  genera al compilar y no se guarda; vive en `/apps/comun`. Los 304, 412,
  428 y los errores `problem+json` los traduce una sola vez el adaptador de
  datos de `comun` a errores propios.
- **Alternativas descartadas**: Tonik 0.10 (un tipo por cada respuesta según
  su código, ideal para 304, 412 y 428, pero joven y sin comportamiento
  certificado en el navegador); openapi-generator 7.x `dart-dio` (el mismo
  generador de la CI del contrato, pero en Java, con OpenAPI 3.1 en beta y
  código más largo).
- **Por qué**: soporta OpenAPI 3.1, está probado desde 2022 y el mismo
  cliente sirve en web y en nativas.
- **Cambiarlo después**: fácil (solo lo ve el adaptador de datos).

## app-web-04 — Rutas (2026-11-14)
- **Decisión**: **go_router 18**: direcciones legibles (`/recetas/<id>`),
  botón atrás y redirección a "entrar" cuando no hay sesión.
- **Alternativas descartadas**: auto_route (rutas con tipos generadas por
  anotaciones, más funciones, pero comunitario y otro generador); el Router
  de Flutter sin paquete (mucho código propio para direcciones web y botón
  atrás).
- **Por qué**: paquete oficial del equipo de Flutter, considerado completo y
  estable: va a cambiar poco.
- **Cambiarlo después**: media (las rutas están en un solo lugar).

## app-web-05 — Textos ES/EN (2026-11-14)
- **Decisión**: **gen-l10n**, la herramienta oficial de Flutter (archivos
  ARB), en `/apps/comun`. El idioma sale de la preferencia del Perfil (Y4) y
  se manda en `Accept-Language`; los errores se muestran con texto propio
  según su `codigo` (contrato-02).
- **Alternativas descartadas**: slang (cómodo y con tipos, una dependencia
  más); easy_localization (sin tipos: un texto faltante se ve recién al usar
  la app).
- **Por qué**: oficial y con tipos: un texto faltante falla al compilar.
- **Cambiarlo después**: fácil.

## app-web-06 — Compilación para el navegador (2026-11-14)
- **Decisión**: **Wasm con respaldo en JavaScript**. Chrome y Edge (137 o
  posterior) usan Wasm (WebAssembly, más rápido y liviano); Safari y
  Firefox reciben JavaScript automáticamente.
- **Alternativas descartadas**: activar Wasm también en Safari 26.2+ y
  Firefox 147+ (más rápido, pero menos probado ahí); solo JavaScript (una
  sola versión, más pesada y lenta en Chrome y Edge).
- **Por qué**: lo que Flutter recomienda hoy; cuando Flutter active Wasm por
  defecto en Safari y Firefox, llega sin cambios.
- **Cambiarlo después**: fácil (es configuración de la compilación).

## app-web-07 — Servidor de los archivos de la web (2026-11-14) · *Reemplazada por app-web-10*
- **Decisión**: la imagen de la web compila con Flutter y entrega los
  archivos estáticos con **Caddy 2.11** dentro de su propio contenedor
  (base-plataforma-03). El proxy de entrada sigue siendo decisión de la
  fase producción.
- **Alternativas descartadas**: nginx (el más difundido y muy rápido, pero
  configuración más larga y otra pieza si el proxy termina siendo otro); que
  los sirva directamente el proxy de entrada (ata la web a una decisión
  pendiente).
- **Por qué**: liviano y de configuración corta; candidato natural para el
  proxy de producción, así quedaría una sola pieza.
- **Cambiarlo después**: fácil.

## app-web-08 — Diseño adaptable (2026-11-14)
- **Decisión**: **dos paneles (lista + detalle) desde 840 px de ancho**; por
  debajo, un solo panel, como en el celular.
- **Alternativas descartadas**: desde 600 px (la tablet vertical también con
  dos paneles, pero angostos); que cada pantalla decida (más trabajo y más
  casos para probar).
- **Por qué**: es el ancho "amplio" de Material 3: notebook, monitor y
  tablet horizontal. Vive en `comun`, así que sirve igual para iPad, Mac y
  tablets Android.
- **Cambiarlo después**: fácil.

## app-web-09 — Prueba de riesgo de Flutter web (2026-11-14)
- **Decisión**: la etapa 1 del plan mide la carga con la red limitada a 10
  Mbps, en Chrome, Firefox y Safari: **3 s o menos la primera vez y 1 s o
  menos las siguientes**. Si no se cumple, se vuelve a la opción SvelteKit
  (app-web-01 se reemplazaría) antes de construir más.
- **Alternativas descartadas**: 5 s o menos la primera vez (más tolerante,
  menos riesgo de tener que cambiar); sin umbral fijo (se decide mirando los
  números; menos objetivo).
- **Por qué**: el mayor riesgo de app-web-01 es el peso al abrir; medirlo
  temprano cuesta poco.
- **Cambiarlo después**: fácil.

## app-web-10 — Servidor de los archivos de la web: nginx (base-plataforma-09) (2026-11-15)
- **Decisión**: la imagen de la web compila con Flutter (Wasm + JS) y
  entrega los archivos estáticos con **nginx 1.30** dentro de su propio
  contenedor. Sigue siendo una imagen propia con su etiqueta
  `app-web-vX.Y.Z`, detrás del nginx de entrada. Reemplaza a app-web-07.
- **Alternativas descartadas**: dejar Caddy en la web (no toca lo decidido,
  pero quedan dos servidores web para mantener al día); que el nginx de
  entrada sirva los archivos de la web (un contenedor menos, pero la web
  pierde su imagen con versión propia, lo que app-web-07 ya había
  descartado).
- **Por qué**: al elegir nginx como proxy de entrada (base-plataforma-09),
  una sola tecnología de servidor web para aprender y actualizar (ANALISIS
  §9).
- **Consecuencias**: la configuración de la web queda algo más larga que con
  Caddy.
- **Afecta a**: app-web (contenedor y etapa 1). No rompe compatibilidad ni
  toca el contrato, el backend ni las apps nativas: no hay nada construido.
- **Costo**: USD 0.
- **Cambiarlo después**: fácil.

---

## app-apple

Versiones vigentes al decidir (noviembre de 2026): iOS 27, iPadOS 27 y
macOS 27 Golden Gate (salieron el 14 de septiembre de 2026); Xcode 27 con
Swift 6.4, que corre solo en Macs con chip Apple y pide macOS Tahoe 26.6 o
posterior; Flutter 3.50 con Dart 3.14 (desde la 3.47, preparado para
Xcode 27 y con mínimos iOS 15 y macOS 12); flutter_secure_storage 10.3;
flutter_image_compress 2.5; image_picker (oficial). Evaluado y descartado:
Swift 6.4 + SwiftUI con swift-openapi-generator 1.13. Apple Developer
Program: USD 99/año (puede variar con impuestos locales). Todo lo elegido
cuesta USD 0.

## app-apple-01 — Tecnología (2026-11-15)
- **Decisión**: **Flutter 3.50** (Dart 3.14) con Riverpod 3, reutilizando
  `/apps/comun`: cliente generado, casos de uso, escalado, estado, textos,
  diseño y pantallas. `/apps/apple` es un proyecto Flutter con destinos iOS
  (iPhone y iPad) y macOS que solo suma los adaptadores de Apple, las rutas
  (go_router 18, como la web) y el menú de la Mac. Confirma la candidata de
  app-web-01.
- **Alternativas descartadas**: Swift 6.4 + SwiftUI con
  swift-openapi-generator 1.13 (la mejor experiencia en cada aparato y
  Liquid Glass automático, pero una segunda app completa en otro lenguaje:
  cliente, casos de uso, una tercera copia del escalado, textos y los 23
  flujos de pantallas, y cada cambio del contrato hecho dos veces); Flutter
  con piezas nativas de iOS adentro (Liquid Glass en las barras con
  paquetes de la comunidad como cupertino_native: jóvenes, con vistas
  nativas más pesadas y flojos en la Mac).
- **Por qué**: mientras la app Apple sea solo para los aparatos propios
  (app-apple-02), una segunda app completa rinde poco; un solo código para
  las tres apps baja el riesgo de "apps desparejas" (§9); el proyecto queda
  con dos lenguajes en lugar de tres; aprovecha la experiencia previa con
  Flutter, Riverpod y Clean Architecture. Pasar a SwiftUI más adelante no
  toca el contrato, el backend, la web ni Android.
- **Consecuencias**: no se ve 100 % nativa, sobre todo en la Mac. Flutter
  todavía no publicó Liquid Glass (Liquid Glass es el aspecto translúcido
  de Apple; está en desarrollo en cupertino_ui, sin fecha). Compilar con el
  SDK de iOS 27 exige el ciclo de vida por escenas de Apple, que Flutter
  migra solo al compilar. La etapa 1 es una prueba de riesgo
  (app-apple-09).
- **Costo**: USD 0.
- **Cambiarlo después**: media. Pasar a SwiftUI es escribir esa app (el
  mismo trabajo que elegirla hoy); se pierde poco, porque la app Apple es
  delgada.
- **Nota (2026-11-15)**: los adaptadores de sesión, fotos, respaldo,
  conexión y versión se escriben en `/apps/nativo`, compartido con Android
  (app-android-02), desde la etapa 1. `/apps/apple` queda con lo exclusivo
  de Apple: el llavero de la Mac, las fotos solo como archivo en la Mac, el
  aviso "instalala desde la Mac", el menú y los proyectos de Xcode. La
  decisión no cambia.

## app-apple-02 — Distribución y firma (2026-11-15)
- **Decisión**: **firma gratuita** de Apple (el equipo personal de Xcode).
  La app se instala solo en los aparatos propios, desde la Mac; en iPhone y
  iPad se reinstala cada 7 días. Las demás personas usan la web en Safari.
  La **cuenta paga** (Apple Developer Program, USD 99/año ≈ USD 8,25/mes) se
  decide en la fase producción, junto con el resto del presupuesto.
- **Alternativas descartadas**: solo los aparatos propios para siempre
  (cierra la puerta sin necesidad); cuenta paga desde el arranque
  (instalaciones que duran un año en hasta 100 aparatos por tipo por año,
  pero el total pasaría a ≈ USD 15,7 y, con el VPS renovado, a ≈ USD 21,2:
  por encima del tope de USD 20).
- **Por qué**: USD 0 mientras nadie más la necesite; abrirla a otras
  personas se decide junto con abrir la plataforma.
- **Consecuencias**: con la firma gratuita entran hasta 3 aparatos por
  plataforma y 3 apps por aparato, y los perfiles vencen a los 7 días. No
  hay TestFlight ni Developer ID: en la Mac de otra persona, macOS
  bloquearía la app. Precisa ANALISIS §8 ("Tiendas") y P-14. Suma un
  pendiente a la fase producción.
- **Cambiarlo después**: fácil (pagar la cuenta y volver a firmar).
- **Nota (2026-11-15)**: la fase producción decidió **no pagar la cuenta por
  ahora**; se revisa cuando 5 personas con iPhone o Mac la pidan
  (base-plataforma-18). La decisión no cambia.

## app-apple-03 — Mac: app propia de macOS (2026-11-15)
- **Decisión**: en la Mac va una **app propia de macOS** (el destino macOS
  de Flutter, con el mismo código), con menú nativo, atajos de teclado,
  ventanas que se agrandan y ventanas de archivos de Mac.
- **Alternativas descartadas**: la app de iPad corriendo en la Mac
  ("Designed for iPad": sin trabajo extra, pero se usa como un iPad y, con
  la firma gratuita, hay que volver a firmarla cada semana); empezar con la
  de iPad y hacer la propia al final.
- **Por qué**: la Mac tiene diseño propio en el análisis (§2, dos paneles), y
  los respaldos, que son archivos grandes, se manejan mejor con las ventanas
  de archivos de Mac.
- **Consecuencias**: un destino más para compilar y probar. En la Mac no hay
  cámara: las fotos se eligen como archivo.
- **Cambiarlo después**: fácil.

## app-apple-04 — Versiones, variantes y aparatos de prueba (2026-11-15)
- **Decisión**: Xcode 27 (Swift 6.4, SDK de iOS 27 y macOS 27) en la Mac
  del usuario (chip Apple, macOS Tahoe 26.6 o posterior). La app funciona
  desde iOS/iPadOS 15 y macOS 12 (los mínimos de Flutter) y se prueba en
  iOS 27 y macOS 27, que son la referencia de aspecto. Como no hay iPad
  entre los aparatos de prueba (§8), el iPad se prueba en el simulador de
  Xcode. Hay dos variantes: **desarrollo** (habla con la notebook por la red
  de la casa, con la excepción de red local de Apple) y **producción** (solo
  HTTPS a `api.<dominio>`); en el aparato son dos apps, dentro del límite de
  la firma gratuita. Etiquetas `app-apple-vX.Y.Z`.
- **Alternativas consideradas**: mínimos iOS 27 y macOS 27 (no ganan nada con
  Flutter y dejarían afuera aparatos si algún día se abre); una sola
  variante (mezclaría la excepción de red local con producción).
- **Por qué**: Xcode 27 corre solo en Macs con chip Apple y pide Tahoe 26.6;
  los mínimos de Flutter no limitan nada.
- **Cambiarlo después**: fácil.

## app-apple-05 — Aspecto (2026-11-15)
- **Decisión**: **Material 3 con toques de Apple**. Son las mismas pantallas
  de `comun`; en Apple llevan la letra del sistema, la transición y el gesto
  "atrás" de iOS, el rebote al desplazar, diálogos y selectores adaptativos,
  y el menú nativo en la Mac. Los toques viven en una variante del diseño de
  `comun` y en `/apps/apple`. Liquid Glass se evalúa en una etapa nueva
  cuando Flutter lo publique en cupertino_ui.
- **Alternativas descartadas**: Cupertino solo en Apple (partes clave con
  dos variantes, más trabajo y más pruebas; hoy se ve como iOS 18); Material
  3 igual en todos lados (en iPhone y Mac se siente como una app de
  Android).
- **Por qué**: casi sin trabajo extra y con el comportamiento que se espera
  en Apple.
- **Consecuencias**: sin Liquid Glass; en la Mac se nota que no es nativa.
- **Cambiarlo después**: fácil a media (vive en el diseño y en adaptadores).

## app-apple-06 — Sesión en el llavero (contrato-09, backend-15) (2026-11-15)
- **Decisión**: el token `Bearer` se guarda con **flutter_secure_storage
  10.3**: en iPhone y iPad, en el llavero del sistema (Keychain, el almacén
  cifrado de Apple); en la Mac, en el **llavero de inicio de sesión**
  mientras haya firma gratuita. Al entrar, la app avisa que es nativa
  (contrato-09). Un 401 borra el token y lleva a "entrar"; salir y borrar la
  cuenta vacían el llavero.
- **Alternativa descartada**: el llavero de protección de datos también en la
  Mac (es el mismo de iOS, pero exige un perfil que con la firma gratuita
  vence cada 7 días: habría que reinstalar la app de Mac cada semana).
- **Por qué**: la app de Mac no vence y el token sigue cifrado.
- **Consecuencias**: si se pasa a la cuenta paga, la Mac cambia de llavero y
  hay que volver a entrar una vez. Si la firma cambia entre compilaciones,
  macOS puede pedir permiso para leer el llavero.
- **Cambiarlo después**: fácil.
- **Nota (2026-11-15)**: el adaptador de sesión vive en `/apps/nativo` y lo
  usa también Android, con el Keystore (app-android-02, app-android-09); la
  elección del llavero de la Mac sigue en `/apps/apple`. La decisión no
  cambia.

## app-apple-07 — Fotos y HEIC (contrato-07, contrato-14, backend-07) (2026-11-15)
- **Decisión**: la foto se elige o se saca con **image_picker** (oficial; en
  la Mac, solo elegir archivo) y se convierte con **flutter_image_compress
  2.5**, que usa el conversor nativo de Apple: HEIC a JPEG, **sin
  metadatos**, así la ubicación nunca sale del aparato. Se achica a 3200 px
  del lado largo (el doble del tamaño más grande que guarda el backend) y a
  25 MB como máximo. Cada subida lleva su `Idempotency-Key`.
- **Alternativa descartada**: un adaptador propio en Swift con Image I/O
  (sin paquetes de terceros, pero código Swift y un puente con Dart para
  mantener).
- **Por qué**: sin Swift propio y con el conversor de Apple. Quitar los
  metadatos en el aparato suma privacidad a R10 (el backend igual los
  borra, backend-07) y no cambia la regla.
- **Consecuencias**: una dependencia de la comunidad más, encerrada en el
  adaptador de fotos.
- **Cambiarlo después**: fácil.
- **Nota (2026-11-15)**: el adaptador de fotos vive en `/apps/nativo` y lo
  usa también Android, con las mismas reglas (app-android-02,
  app-android-09). La decisión no cambia.

## app-apple-08 — Compilación y CI (base-plataforma-05) (2026-11-15)
- **Decisión**: la **CI corre en Linux** en cada PR que toca `/apps` o
  `/contrato`: genera el cliente, analiza el código y corre las pruebas de
  `comun` y de `/apps/apple` (con dobles de los adaptadores) y la prueba de
  arquitectura. **No compila para Apple.** En la Mac, un **script** genera
  el cliente, compila, firma con la firma gratuita e instala en el iPhone,
  el simulador de iPad y la Mac.
- **Alternativas descartadas**: un runner propio de GitHub en la Mac (gratis
  y compila en cada PR, pero la Mac tiene que estar prendida y conectada, y
  es más configuración); los runners macOS de GitHub (los minutos cuentan
  ×10, unos 200 min reales al mes, y no pueden firmar con la firma
  gratuita).
- **Por qué**: USD 0, y la firma gratuita solo se puede aplicar en la Mac.
- **Consecuencias**: un error que solo aparece al compilar para Apple se ve
  recién al correr el script.
- **Cambiarlo después**: fácil.
- **Nota (2026-11-15)**: la CI corre también las pruebas de `/apps/nativo`
  (app-android-02). La decisión no cambia.

## app-apple-09 — Prueba de riesgo (2026-11-15)
- **Decisión**: la etapa 1 prueba Flutter en el iPhone, el simulador de iPad
  y la Mac, con la compilación final y datos de prueba. Pasa si la app abre
  en **2 s o menos**; 200 recetas se desplazan **sin cuadros lentos**; un
  HEIC de 48 MP queda en un JPEG de 25 MB o menos **sin ubicación**;
  VoiceOver lee los botones principales; el teclado y el dictado en español
  andan; y el usuario dice que **se siente bien**. Si algo falla, se pasa a
  Swift/SwiftUI (app-apple-01 se reemplazaría) antes de construir más.
- **Alternativas descartadas**: solo medidas (no capta cómo se siente);
  solo el visto bueno (sin números para comparar después).
- **Por qué**: el mayor riesgo de app-apple-01 es cómo se siente en Apple;
  medirlo temprano cuesta poco.
- **Cambiarlo después**: fácil.

---

## app-android

Versiones vigentes al decidir (noviembre de 2026): Android 17 (API 37),
estable desde el 16 de junio de 2026 (la QPR2 se espera para diciembre);
Flutter 3.50 con Dart 3.14 (mínimo Android 7.0, API 24); `material_ui`, el
paquete oficial de Material separado de Flutter (Material 3 completo;
Material 3 Expressive parcial: formas desde octubre y animaciones con
resortes desde noviembre de 2026; listas y botones flotantes en cola);
flutter_secure_storage 10.3.1; flutter_image_compress 2.5.1; image_picker
(oficial); ota_update 7.1 (candidato para bajar e instalar el APK). Evaluado
y descartado: Kotlin 2.4.20 + Jetpack Compose con Compose Material 3 1.5
(RC del 4 de noviembre de 2026, con Expressive). **Verificación de
desarrolladores de Google**: rige desde el 30 de septiembre de 2026 solo en
Brasil, Indonesia, Singapur y Tailandia, y solo para las tiendas que
participan; se extiende a todo el mundo en 2027, sin fecha. La instalación
por ADB queda exenta. Cuenta de distribución limitada: gratis, sin
documento, hasta 50 aparatos autorizados según la ayuda oficial (antes se
anunciaban 20). Cuenta completa: USD 25 una sola vez, con documento. Todo lo
elegido cuesta USD 0. *Corrección (2026-11-15): el blog oficial de Google de
junio de 2026 y su ayuda hablan de **hasta 20 aparatos**; ver la nota en
app-android-03.*

## app-android-01 — Tecnología (2026-11-15)
- **Decisión**: **Flutter 3.50** (Dart 3.14) con Riverpod 3, reutilizando
  `/apps/comun` (cliente generado, casos de uso, escalado, estado, textos,
  diseño y pantallas) y `/apps/nativo` (app-android-02). `/apps/android` es
  un proyecto Flutter con destino Android (celulares, tablets y plegables)
  que suma solo lo propio de Android: arranque y variantes, rutas
  (go_router 18, como Apple), la actualización del APK y la configuración
  del proyecto de Android. Confirma la candidata de app-web-01: las tres
  apps quedan en Flutter.
- **Alternativas descartadas**: Kotlin 2.4 + Jetpack Compose con Compose
  Material 3 1.5 (Material 3 Expressive completo, el mejor manejo de
  tablets y plegables y un APK más chico, pero una segunda app completa en
  un tercer lenguaje: cliente con otro generador, casos de uso, una tercera
  copia del escalado, textos y los 23 flujos, y cada cambio del contrato
  hecho dos veces); la app web instalada como PWA desde Chrome (sin código
  nuevo, sin firma ni verificación y con actualización sola, pero cambia
  ANALISIS §2, D16 y P-07, deja la sesión de 30 días de la web, no
  convierte HEIC y Flutter web no está medido en celulares modestos).
- **Por qué**: un solo código para las tres apps baja el riesgo de "apps
  desparejas" (§9); el proyecto sigue con dos lenguajes (Go y Dart);
  aprovecha la experiencia previa con Flutter, Riverpod y Clean
  Architecture; Android compila en Linux, así que alcanzan la notebook y la
  CI, sin Mac.
- **Consecuencias**: Material 3 Expressive llega a medida que lo publique
  `material_ui` (app-android-07); no es 100 % nativa. `comun` pasa a tener
  tres usuarios: cada cambio se prueba en las tres apps (la CI ya lo hace).
  Si Flutter tiene un problema, lo tienen las tres (riesgo ya aceptado en
  app-web-01 y app-apple-01). La etapa 1 es una prueba de riesgo
  (app-android-10).
- **Costo**: USD 0.
- **Cambiarlo después**: media. Pasar a Kotlin + Compose es escribir esa
  app; no toca el contrato, el backend, la web ni Apple.

## app-android-02 — Adaptadores compartidos por Apple y Android: `/apps/nativo` (2026-11-15)
- **Decisión**: un paquete Dart **`/apps/nativo`** dentro del espacio de
  trabajo de `/apps`, con los adaptadores que Apple y Android necesitan
  iguales:
  - **sesión**: token Bearer en el almacén seguro (flutter_secure_storage:
    Keychain en Apple, Keystore en Android);
  - **fotos**: elegir o sacar, HEIC a JPEG sin metadatos, 3200 px, 25 MB;
  - **respaldo**: elegir el archivo y guardar la exportación con las
    ventanas del sistema, subir en partes de 50 MB leyendo del disco,
    pausar y retomar;
  - **conexión**: "sin conexión" y reintentar;
  - **versión**: App-Version del paquete y un puerto "conseguir la versión
    nueva" que cada app implementa.
  La web no lo usa. `/apps/apple` y `/apps/android` quedan solo con lo
  exclusivo de cada plataforma.
- **Alternativas descartadas**: copiar los adaptadores en `/apps/android`
  (no toca Apple, pero son unos cinco adaptadores repetidos que hay que
  mantener iguales); ponerlos dentro de `/apps/comun` (sin carpeta nueva,
  pero la web arrastraría paquetes nativos que no usa).
- **Por qué**: lo que es igual se escribe y se prueba una vez; `comun` sigue
  sin tecnología del aparato; respeta la separación por carpetas de
  base-plataforma-05.
- **Afecta a**: app-apple (su carpeta `plataforma/` se achica y los
  adaptadores nacen en `/apps/nativo` desde su etapa 1; notas en
  app-apple-01, 06, 07 y 08), base-plataforma-05 y app-web-02 (notas),
  ANALISIS §4.3. No rompe nada: no hay nada construido.
- **Consecuencias**: un paquete más en el espacio de trabajo; un cambio en
  `/apps/nativo` corre las CI de Apple y de Android. Las reglas de `comun`
  valen igual: las pantallas usan casos de uso, nunca adaptadores.
- **Cambiarlo después**: fácil (mover archivos entre paquetes).

## app-android-03 — Distribución y registro en Google (2026-11-15)
- **Decisión**: la app se distribuye como **APK firmado**, sin tienda.
  - En los **aparatos propios** se instala por **ADB** (la herramienta de
    desarrollo de Android) desde la notebook, por cable o Wi-Fi: sin avisos
    y fuera del alcance de la verificación de Google.
  - Cuando se abra la plataforma (fase producción), las **personas
    conocidas** la bajan de `app.<dominio>` y la instalan directo.
  - Antes de que la verificación de desarrolladores llegue a Paraguay,
    Argentina y Uruguay (2027), se abre una **cuenta gratuita de
    distribución limitada** en Android Developer Console, se registra el
    paquete (con un APK firmado con la clave de producción) y se autoriza
    cada aparato con el código o QR que su dueño confirma.
  - La **cuenta completa** (USD 25 una sola vez, con documento) solo si
    hacen falta más aparatos de los que admite la gratuita.
- **Alternativas descartadas**: solo los aparatos propios, como Apple
  (cierra sin necesidad una puerta que en Android es gratis); la cuenta
  completa desde la apertura (sin límite ni autorizar aparato por aparato,
  pero USD 25 y documento sin necesidad mientras alcance la gratuita).
- **Por qué**: a diferencia de Apple, Android permite instalar en aparatos
  de conocidos sin pagar; la cuenta gratuita cubre el grupo inicial
  (< 100 personas, no todas con Android).
- **Consecuencias**:
  - Al instalar, la persona permite "instalar apps desconocidas" para el
    navegador y puede ver el análisis de Play Protect. La app no pide los
    permisos que disparan el bloqueo fuerte (SMS, accesibilidad, leer
    notificaciones).
  - La ayuda oficial habla hoy de hasta 50 aparatos autorizados a la vez
    (antes se anunciaban 20): si baja, se revisa. Quitar un aparato no se
    deshace y el enlace para autorizarlo vence a los 7 días. Una app
    registrada en la cuenta gratuita no se instala ni se actualiza fuera de
    los aparatos autorizados.
  - El **nombre del paquete** (el identificador de la app en Android) queda
    fijo al registrarlo y tiene que ser nuevo en Android. Conviene el
    dominio propio invertido, así que el definitivo se fija en la fase
    producción, al comprar el dominio; hasta entonces se usa uno provisorio
    (solo hay que reinstalar en los aparatos propios).
  - Suma pendientes a la fase producción (publicar el APK, fijar el nombre
    del paquete y abrir la cuenta antes de 2027). Precisa ANALISIS §8
    ("Tiendas") y P-14.
- **Costo**: USD 0 (USD 25 una sola vez si algún día hace falta la cuenta
  completa).
- **Cambiarlo después**: fácil (pasar de la cuenta limitada a la completa se
  puede; al revés, no).
- **Nota (2026-11-15)**: el blog oficial de Google (junio de 2026) y su
  ayuda hablan de **hasta 20 aparatos**, no 50: con los dos aparatos propios
  quedan 18 para conocidos. Se mantiene la cuenta gratuita; la completa se
  saca cuando se superen **15 aparatos autorizados**. La cuenta se abre
  apenas existan el dominio y la clave de producción, sin esperar a 2027; el
  paquete es `com.<nombre>.recetas` y el APK se publica en
  `app.<dominio>/descargas/android/` (base-plataforma-07,
  base-plataforma-19). Hay además un "flujo avanzado" de Android para
  instalar apps sin registrar, con varios pasos de advertencia. La decisión
  no cambia.

## app-android-04 — Firma, compilación y CI (base-plataforma-05) (2026-11-15)
- **Decisión**:
  - La **clave de producción** (un archivo *keystore* con contraseña) vive
    **solo en la notebook**, fuera del repositorio, con una **copia cifrada
    fuera de la casa** (con los respaldos externos de la fase producción o
    en un gestor de contraseñas) y una restauración probada.
  - La variante desarrollo se firma con la clave de depuración que genera
    Android, que no importa perder.
  - La **CI en Linux**, en cada PR que toca `/apps` o `/contrato`, genera el
    cliente, analiza, corre las pruebas de `comun`, `nativo` y `android`, la
    prueba de arquitectura y la de permisos, y **compila el APK de
    desarrollo**.
  - Las versiones con etiqueta (`app-android-vX.Y.Z`) las firma y verifica
    (con apksigner) un **script en la notebook**, que también instala por
    ADB y prepara el APK y su ficha para publicar.
- **Alternativas descartadas**: que la CI firme las versiones con la clave
  guardada como secreto de GitHub (más automático, pero la clave viviría
  también en GitHub); no compilar en la CI, como Apple (menos minutos, pero
  un error de compilación se vería recién en la notebook, y en Android no
  hace falta una Mac).
- **Por qué**: si la clave se pierde, no hay más actualizaciones (Android
  rechaza una firma distinta) ni se puede registrar el paquete en Google; si
  se filtra, alguien podría publicar una versión falsa. Un solo lugar con
  copia cifrada cubre los dos riesgos con lo mínimo.
- **Consecuencias**: la rotación de clave de Android (esquema v3, Android 9
  o posterior) necesita la clave vieja, así que no salva una pérdida. Si la
  clave se perdiera igual, habría que desinstalar y reinstalar como app
  nueva (la app no guarda datos en el aparato: solo hay que volver a
  entrar). Compilar el APK suma minutos de CI; entra en los 2.000 min/mes
  gratis porque solo corre si el PR toca `/apps` o `/contrato`.
- **Costo**: USD 0.
- **Cambiarlo después**: fácil (mover la firma a la CI es configuración).
- **Nota (2026-11-15)**: la copia cifrada de la clave va en los respaldos de
  restic (disco externo y Backblaze B2) y su contraseña, en el gestor de
  contraseñas (base-plataforma-12). La decisión no cambia.

## app-android-05 — Actualizaciones sin tienda (contrato-01) (2026-11-15)
- **Decisión**: la app se actualiza **desde adentro**. Con
  `app.desactualizada` (App-Version menor que la mínima, contrato-01)
  muestra "Hay una versión nueva" con un botón **"Descargar e instalar"**;
  al abrir, además, avisa sin obligar si la ficha publicada (`ultima.json`:
  versión, número de versión de Android y huella SHA-256) trae una más
  nueva. El botón baja el APK de `app.<dominio>`, **verifica la huella** y
  abre el **instalador de Android**; la persona confirma "Actualizar". La
  primera vez, Android pide permitir "instalar apps desconocidas" para
  nuestra app. Candidato: ota_update 7.1, encerrado en el adaptador de
  actualización de `/apps/android` (se confirma en la etapa 2).
- **Alternativas descartadas**: un enlace que abre el navegador para bajar
  el APK (sin permiso especial, pero más pasos y más fácil equivocarse de
  archivo); Obtainium (una app libre que vigila la página de descargas: una
  app más para cada persona).
- **Por qué**: lo más directo para personas que no conocen Android por
  dentro; la huella evita instalar un archivo dañado o cambiado, y Android
  además rechaza un APK con otra firma.
- **Consecuencias**: la app pide el permiso de instalar paquetes. Dónde y
  cómo se publican el APK y su ficha en `app.<dominio>` se decide en la
  fase producción (propuesta: una carpeta de descargas servida por Caddy,
  aparte de la imagen de la web). En desarrollo se prueba con una ficha
  servida desde la notebook.
- **Costo**: USD 0.
- **Cambiarlo después**: fácil (es un adaptador).
- **Nota (2026-11-15)**: quedó decidido en base-plataforma-19: carpeta propia
  en `app.<dominio>/descargas/android/`, servida por el **nginx** de entrada
  (no Caddy), con `ultima.json` sin caché. La decisión no cambia.

## app-android-06 — Versiones, variantes y aparatos de prueba (2026-11-15)
- **Decisión**: la app funciona **desde Android 7.0 (API 24)**, el mínimo de
  Flutter y el mismo piso de la verificación de Google, y se **compila para
  Android 17 (API 37)**, que es la referencia de aspecto y comportamiento.
  La conversión de HEIC funciona desde Android 9; los aparatos más viejos no
  sacan HEIC. Hay dos variantes, que en el aparato son **dos apps** con
  identificador distinto: **desarrollo** (habla con la notebook por la red
  de la casa, con HTTP permitido solo hacia esa dirección) y **producción**
  (solo HTTPS a `api.<dominio>`). Se prueba en el **celular y la tablet
  Android** del usuario; el **plegable**, en el emulador de Android de la
  notebook. Etiquetas `app-android-vX.Y.Z`.
- **Alternativas descartadas**: desde Android 9 (HEIC en todos los
  aparatos, pero deja afuera los que quedaron en Android 7 u 8); desde
  Android 12 (menos versiones para probar, pero deja afuera aparatos que no
  pasaron de Android 11).
- **Por qué**: el mismo criterio que app-apple-04: los mínimos de Flutter no
  limitan nada.
- **Consecuencias**: si en Android 7 u 8 aparece una imagen que no se puede
  convertir, la app avisa y no la sube. Compilar para la API 37 obliga a
  aceptar giro y tamaño libres en pantallas grandes (app-android-08).
  Precisa ANALISIS §8 ("Aparatos de prueba").
- **Cambiarlo después**: fácil.

## app-android-07 — Aspecto (2026-11-15)
- **Decisión**: **Material 3** de `material_ui` con el **color y el modo del
  Perfil** (Y4), iguales en todos los aparatos, y los comportamientos de
  Android: gesto "atrás" con vista previa, la app dibujada de borde a borde
  detrás de las barras del sistema, letra y desplazamiento de Android.
  **Material 3 Expressive** se suma a medida que `material_ui` publique sus
  piezas (hoy: formas y animaciones con resortes), en una etapa que se
  evalúa cuando estén las listas y los botones.
- **Alternativas descartadas**: Expressive ya con paquetes de la comunidad
  (m3e_core, material_3_expressive: se vería como Android 17, pero con
  paquetes jóvenes y, como las pantallas son de `comun`, cambiarían también
  la web y Apple); los colores del fondo de pantalla (Material You:
  integrado con Android, pero el color dejaría de ser el mismo en todos los
  aparatos y chocaría con Y4).
- **Por qué**: casi sin trabajo extra, coherente con app-apple-05 y sin
  depender de paquetes de terceros para el diseño.
- **Consecuencias**: hasta que llegue Expressive, se ve como Material 3 sin
  sus formas y movimientos nuevos. Como Expressive llega por `comun`,
  cuando llegue cambia también la web y Apple (Apple, con sus toques
  propios).
- **Cambiarlo después**: fácil a media (vive en el diseño de `comun`).

## app-android-08 — Tablets, plegables y ventanas (app-web-08) (2026-11-15)
- **Decisión**: la regla de `comun` (**dos paneles desde 840 px**) vale
  igual en Android. La app **gira y cambia de tamaño libremente** (Android 17
  lo exige en pantallas de 600 dp o más), funciona en pantalla dividida y
  no pierde nada al girar, plegar o cambiar el tamaño de la ventana (el
  estado vive en Riverpod, no en la pantalla). En un **plegable a medio
  abrir**, la app **respeta la bisagra**: lista de un lado y detalle del
  otro, y ningún texto ni botón cruza el pliegue. La regla de la bisagra
  vive en el diseño de `comun` y usa los pliegues que informa Flutter; en
  los aparatos sin pliegue no hace nada.
- **Alternativas descartadas**: solo la regla de 840 px (más simple, pero a
  medio abrir algo puede quedar partido por el pliegue); dos paneles desde
  600 px en todas las apps (la tablet vertical con dos paneles angostos;
  reemplaza app-web-08 y cambia la web y Apple).
- **Por qué**: Android 17 deja de permitir fijar la orientación en
  pantallas grandes, y los plegables son parte del mercado de Android.
- **Consecuencias**: el plegable se prueba en el emulador (app-android-06).
  La web y Apple no cambian. Precisa ANALISIS §2.
- **Cambiarlo después**: fácil.

## app-android-09 — Sesión, fotos, respaldo y permisos (contrato-07, contrato-09, contrato-14) (2026-11-15)
- **Decisión** (sobre los adaptadores de `/apps/nativo`, app-android-02):
  - **Sesión**: token Bearer con flutter_secure_storage 10.3, que en Android
    cifra con claves del **Keystore** (el almacén de claves del sistema). Al
    entrar, la app avisa que es nativa (contrato-09); dura 90 días sin uso
    (backend-15). Un 401 borra el token y lleva a "entrar"; salir y borrar
    la cuenta lo borran. El **respaldo automático de Android queda
    desactivado** para la app: el token no viaja a otro aparato (y la clave
    del Keystore, que no se copia, tampoco lo podría abrir allá).
  - **Fotos**: el **selector de fotos del sistema** (no pide permiso de
    fotos y ya quita la ubicación) o la cámara del sistema, con
    image_picker; después flutter_image_compress 2.5 pasa HEIC a JPEG **sin
    metadatos**, a 3200 px como máximo y 25 MB, igual que app-apple-07.
    Cada subida lleva su `Idempotency-Key`.
  - **Respaldo**: se sube con la app en **primer plano** y la pantalla
    encendida, igual que en Apple. Si la app pasa a segundo plano, la subida
    se pausa; al volver, pregunta qué partes llegaron y sigue (contrato-07).
    El archivo nunca se carga entero en memoria; la contraseña no se guarda
    en el almacén seguro, en registros ni en archivos. La exportación se
    guarda donde elija la persona, con el selector de archivos del sistema.
  - **Permisos**: solo internet e instalar paquetes (app-android-05). Ni
    SMS, ni accesibilidad, ni notificaciones, ni ubicación, ni acceso a
    todas las fotos. La CI rechaza un permiso fuera de la lista.
- **Alternativas descartadas**: seguir subiendo el respaldo en segundo plano
  con un aviso fijo, solo en Android (permite usar otra app mientras sube,
  pero suma el permiso de notificaciones y código propio de Android) o en
  Android y Apple (igual en las dos, pero cambia la regla ya decidida de
  app-apple).
- **Por qué**: el mismo comportamiento que Apple con el mismo código, y la
  menor cantidad de permisos (menos avisos al instalar y menos motivos para
  que Play Protect desconfíe).
- **Cambiarlo después**: fácil.

## app-android-10 — Prueba de riesgo (2026-11-15)
- **Decisión**: la etapa 1 prueba Flutter en el celular y la tablet, con la
  compilación final y datos de prueba. Pasa si la app abre en **2 s o
  menos**; 200 recetas se desplazan **sin cuadros lentos**; un HEIC de
  48 MP queda en un JPEG de 25 MB o menos **sin ubicación**; TalkBack (el
  lector de pantalla de Android) lee los botones principales; el teclado y
  el dictado en español andan; girar la tablet y usar pantalla dividida
  **no pierden lo escrito**; y el usuario dice que **se siente bien**. Si
  algo falla, se pasa a Kotlin + Compose (app-android-01 se reemplazaría)
  antes de construir más.
- **Alternativas descartadas**: solo medidas (no capta cómo se siente); sin
  prueba de riesgo (Android es donde Flutter está más probado, pero no se
  mediría en los aparatos propios).
- **Por qué**: el mismo criterio que app-web-09 y app-apple-09: medir
  temprano cuesta poco.
- **Cambiarlo después**: fácil.

---

## vigia

Decidido el 2026-11-15, en la misma conversación que la fase producción de
base-plataforma, como excepción a "un proyecto por conversación", a pedido
del usuario. Opciones vigentes al decidir (noviembre de 2026): Google Cloud
regala para siempre una e2-micro (2 vCPU compartidas que rinden ~0,25, 1 GB
de RAM) en us-west1, us-central1 o us-east1, con 30 GB de disco estándar y
1 GB de tráfico de salida; la IP fija y el disco SSD se cobran aparte.
Evaluados y descartados: Cloudflare Workers (plan gratis; SMTP por los
puertos 587 y 465, no por el 25), Deno Deploy (plan gratis, hasta 10 tareas
programadas; recortado dos veces en 2026) y Oracle Cloud gratis (reclama las
máquinas con menos de 20 % de procesador durante 7 días y redujo sus límites
el 18 de agosto de 2026). Todo lo elegido cuesta USD 0.

## vigia-01 — Dónde corre y en qué lenguaje (P-20) (2026-11-15)
- **Decisión**: **Go 1.27 en Docker Compose** sobre una **e2-micro gratuita de
  Google Cloud** en una región gratis de EE. UU., con disco estándar de 30 GB,
  **sin IP fija** y con una **alerta de gasto de USD 1**. Responde en
  `vigia.<dominio>` (en Cloudflare solo como DNS, sin su proxy) con nginx y
  Let's Encrypt.
- **Alternativas descartadas**: Cloudflare Workers con TypeScript (sin
  servidor que mantener, pero un lenguaje más y el mismo punto de falla que el
  túnel); Deno Deploy con TypeScript (un lenguaje más y un plan gratis que se
  viene recortando); Oracle Cloud gratis (reclama máquinas ociosas, justo lo
  que es un vigía).
- **Por qué**: el mismo lenguaje y las mismas herramientas que el backend
  (ANALISIS §9: ninguna tecnología nueva) y **fuera de Cloudflare**: si
  Cloudflare cae, el vigía sigue revisando y avisa.
- **Consecuencias**: un servidor más para mantener, con el paquete de
  base-plataforma-14 (parches solos, solo 80 y 443 abiertos, SSH con llave);
  pide tarjeta; sin IP fija, si la IP cambia (solo al apagar el servidor) se
  actualiza el DNS; la región en EE. UU. no importa para esta tarea.
- **Costo**: USD 0.
- **Cambiarlo después**: fácil (es un contenedor que se muda a cualquier
  servidor).

## vigia-02 — Estado (2026-11-15)
- **Decisión**: la **lista de controles** (nombre, cada cuánto, margen) vive en
  `/vigia/controles.yaml`, versionada en el repositorio; el **estado** (última
  señal de cada control, fallas e historial de avisos de 30 días) en
  **SQLite**, con un controlador escrito solo en Go, en un volumen. Copia
  diaria del archivo a B2 con restic. Si el estado se pierde, el vigía
  arranca de cero y se pone al día en el primer ciclo.
- **Alternativas descartadas**: PostgreSQL (pesado para 1 GB de RAM y unas
  pocas filas); memoria + archivo JSON (se puede romper si se corta al
  escribir).
- **Por qué**: sumar un control es un pull request; el estado sobrevive a los
  reinicios sin servicios extra. backend-05 descartó SQLite solo por la escala
  de 10.000 personas, que acá no aplica.
- **Cambiarlo después**: fácil (es un adaptador).
- **Nota (2026-11-16)**: con el repositorio público, `controles.yaml` lista
  los controles **sin sus claves**; las claves van al archivo de secretos del
  vigía, fuera del repositorio (base-plataforma-20). La decisión no cambia.

## vigia-03 — Avisos (2026-11-15)
- **Decisión**: **Telegram** (bot propio, por HTTPS) **y correo** por SMTP con
  el mismo esquema que base-plataforma-10 (Brevo principal, Resend de
  respaldo), con **credenciales propias del vigía**, desde `vigia@<dominio>`
  al correo personal. Reglas: primer aviso al instante; recordatorio cada 2 h
  mientras siga fallando; aviso de "se recuperó"; **resumen diario a las 8:30
  (hora de Asunción)** con el estado y las horas de caída del mes (dato del
  primer criterio de base-plataforma-16). Si el resumen no llega, el que
  falló es el vigía.
- **Alternativas descartadas**: solo Telegram (si falla o se pierde el
  celular, no hay aviso); solo correo (llega tarde y puede caer en spam).
- **Por qué**: dos caminos independientes; Telegram es inmediato y el correo
  queda como registro.
- **Costo**: USD 0 (los correos entran en los planes gratis).
- **Cambiarlo después**: fácil (adaptadores de aviso).

## vigia-04 — Arquitectura interna, señales y despliegue (base-plataforma-13) (2026-11-15)
- **Decisión**:
  - **Capas como el backend**: dominio puro (vencimiento, margen,
    recordatorio y recuperación), casos de uso ("recibir señal", "revisar
    controles", "mandar resumen"), puertos y adaptadores (reloj, SQLite,
    revisión HTTP, Telegram, SMTP). La CI falla si el dominio importa SQLite o
    HTTP.
  - **Señales**: `POST /s/<clave>` ("anduvo") y `POST /s/<clave>/falla`
    (con un texto corto), formato de Healthchecks.io; cada control tiene una
    **clave al azar** en la dirección y una clave desconocida responde 404.
  - **Revisiones**: cada 5 min a `api.<dominio>/v1/salud` y a `app.<dominio>`.
  - **Sin datos de personas ni secretos de la plataforma**: solo sabe qué
    anduvo y cuándo.
  - **Repositorio y despliegue**: `/vigia` en el mismo repositorio, etiquetas
    `vigia-vX.Y.Z`, imagen en `ghcr.io` y `desplegar` (base-plataforma-11).
- **Alternativa descartada**: un formato de señales propio (se perdería el
  plan B de Healthchecks.io).
- **Por qué**: las mismas reglas de diseño que el resto (M1–M5 aplicadas a un
  proyecto chico) y desacople total con lo vigilado.
- **Afecta a**: base-plataforma-05 (nota: se suma `/vigia`). No toca el
  contrato, el backend ni las apps.
- **Cambiarlo después**: fácil.
- **Nota (2026-11-16)**: con el repositorio público, `controles.yaml` lista
  los controles **sin sus claves**; las claves van al archivo de secretos del
  vigía, fuera del repositorio (base-plataforma-20). La decisión no cambia.
