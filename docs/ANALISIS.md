# Análisis — Plataforma de recetas (v3.9)

> Documento **sin tecnología**: describe *qué* hace el sistema y *qué reglas*
> cumple, no *con qué* se construye. Lo marcado **[VALIDAR]** se cierra antes
> de elegir tecnologías.
>
> **Cambios de v2 a v3**: proyecto desde cero (sin migrar `recipe_app`);
> exportación **cifrada con contraseña**; importación solo de archivos de la
> plataforma; login por **correo** con **dominio propio**; búsqueda por
> ingrediente; recetas públicas solo para personas con cuenta, con buscador;
> copia de recetas públicas; datos de contexto para elegir tecnología y
> despliegue (§8). **v3.1**: colecciones privadas o públicas en el MVP;
> respaldo ofrecido antes de borrar la cuenta. **v3.2**: alojamiento inicial
> en una **notebook con Linux** (para desarrollar y para las primeras
> personas) y VPS después (D31); personas en Paraguay, Argentina y Uruguay;
> disponibilidad y riesgos de la notebook como servidor (§7, §9).
> **v3.3** (al decidir el contrato): **redondeo** del escalado (R8, D32);
> las fotos se bajan con una **dirección firmada que vence** (§6, regla 1);
> **"cambios desde" por módulo**, con registro de cambios en cada módulo
> (§4.1, §6); respaldos que se suben **en partes** (§4.1).
> **v3.4** (al decidir el backend): **meta de diseño de 10.000 personas** sin
> rediseño, con el tope de USD 20/mes para el arranque (D33; §7, §8, §9);
> **M3 precisado**: cada módulo decide la visibilidad de lo suyo y los demás
> le preguntan (§4.1, backend-03); las fotos se guardan **sin metadatos de la
> cámara** (R10, backend-07); la subida de fotos declara los formatos que
> acepta (§6, contrato-14).
> **v3.5** (al decidir la app web): las apps pueden **compartir código
> común** en un paquete compartido, sin dejar de ser proyectos separados
> (§4.3, §9, app-web-02).
> **v3.6** (al decidir la app Apple): la app Apple se instala con **firma
> gratuita solo en los aparatos propios**; las demás personas usan la web, y
> la cuenta paga de Apple se decide en la fase producción (§8, app-apple-02);
> en la Mac va una **app propia de macOS** (§2, app-apple-03).
> **v3.7** (al decidir la app Android): la app Android se distribuye como
> **APK firmado**: en los aparatos propios se instala desde la notebook y en
> los de **personas conocidas, por descarga directa**, sin pagar; desde 2027,
> con la **cuenta gratuita de distribución limitada de Google** (§8,
> app-android-03); Apple y Android **comparten los adaptadores del aparato**
> en un segundo paquete (§4.3, app-android-02); tablets y **plegables** con
> el pliegue respetado (§2, app-android-08); dos riesgos nuevos: perder la
> clave de firma de Android y la verificación de desarrolladores de Google
> (§9).
> **v3.8** (al decidir la fase producción de la base común): los respaldos
> del sistema van a un **disco externo en casa** y a una **copia cifrada
> fuera de casa**, con restauración probada todos los meses (§7,
> base-plataforma-12); la vigilancia es un **proyecto propio, el "vigía"**,
> que corre fuera del lugar donde corre la plataforma (§7, §8, D34); mientras
> sea la notebook, el **HTTPS termina en Cloudflare** (§7,
> base-plataforma-08); **criterios medibles** para pasar al VPS (§8,
> base-plataforma-16); la cuenta paga de Apple **no se paga por ahora** (§8,
> base-plataforma-18); la cuenta gratuita de Google admite **20 aparatos**,
> no 50 (§8); cinco riesgos nuevos (§9). El vigía corre en otro proveedor,
> fuera de la cuenta que publica la plataforma (vigia-01).
> **v3.9** (al arrancar la construcción): el repositorio es **público**,
> porque GitHub Free no protege ramas en repositorios privados; por eso
> **E4 deja de pedir que el formato del respaldo no sea público**: la
> protección del respaldo es la contraseña (E2) y la detección de cambios
> (E3) (D35, P-21); un riesgo nuevo: código y documentos a la vista (§9).

---

## 1. Visión

Un **backend** con las reglas y los datos de las recetas, que expone sus
capacidades mediante un **contrato** estable. Tres apps —web, Apple y
Android— se conectan a ese contrato. Una persona entra con su cuenta desde
cualquier dispositivo y ve lo mismo: sus recetas, sus favoritos y las
recetas públicas de otras personas.

**Objetivos**

1. Sumar o rehacer una app no obliga a tocar el backend.
2. Cambiar una tecnología del backend (base de datos, fotos, correo,
   framework) toca **una sola pieza** y no las reglas de negocio.
3. Las reglas del negocio viven en **un solo lugar**: el backend.
4. Los módulos del backend están tan separados que, si hace falta, uno se
   puede convertir en un servicio aparte sin rediseñarlo.

**No-objetivos (por ahora)**

Red social (comentarios, seguidores, "me gusta" públicos), monetización,
publicación en tiendas de apps, planificador de menús, lista de compras,
nutrición, migración desde `recipe_app` (se empieza de cero).

---

## 2. Actores y clientes

| Actor / cliente | Qué hace |
|---|---|
| **Persona con cuenta** | Crea y gestiona sus recetas y colecciones, decide si son públicas o privadas, busca recetas y colecciones públicas, marca favoritos, copia recetas públicas, exporta e importa. |
| **App web** | Primera app (D14). |
| **App Apple** | iPhone, iPad y Mac (en la Mac, app propia de macOS, app-apple-03). Segunda. |
| **App Android** | Celulares, tablets y plegables (respetando el pliegue, app-android-08). Tercera. |

Sin cuenta no se ve nada (D20). Las tres apps tienen **las mismas
funcionalidades**; cambia el diseño según el aparato (por ejemplo, dos
paneles en tablet, iPad, Mac y web). "Tres proyectos" no obliga a usar tres
tecnologías distintas: se decide por app.

---

## 3. Modelo del dominio

### 3.1 Entidades

```
Cuenta              id, correo (verificado), credencial, estado, creadaEn
Perfil              cuenta, nombre visible, preferencias (idioma, color, modo)
Sesión              cuenta, dispositivo, creadaEn, últimaActividad, revocada

Receta (raíz)       id, dueño, visibilidad (privada | pública),
                    basadaEn? (receta y autor originales, si es copia),
                    nombre, porcionesBase, creadaEn, actualizadaEn, versión
 ├─ Ingrediente[]   orden, nombre, cantidad?, unidad?, nota?
 ├─ Paso[]          orden, texto
 ├─ Foto[]          orden, referencia al archivo, descripción?  (máx. 10)
 ├─ Observación[]   texto
 ├─ Tag[]           texto normalizado
 └─ Referencias     (derivado del texto)

Colección           id, dueño, visibilidad (privada | pública), nombre,
                    descripción?, recetas[] (ordenadas), creadaEn, versión
Favorito            cuenta, receta o colección, marcadoEn
Respaldo            archivo cifrado con recetas, fotos y favoritos (§3.2 E)
Autor anónimo       dueño especial de recetas públicas cedidas al borrar
                    una cuenta (C4)
```

### 3.2 Reglas de negocio

**Recetas**

| # | Regla |
|---|---|
| R1 | Nombre no vacío y `porcionesBase ≥ 1`. |
| R2 | `cantidad` es un número `> 0` o vacía ("c/n", "a gusto"). Nunca texto. |
| R3 | Pasos e ingredientes con orden explícito y estable. |
| R4 | Referencias como `[[idReceta\|Texto visible]]` en pasos, notas de ingredientes u observaciones. Se **derivan** del texto. Se puede referenciar una receta propia o una pública de otra persona. |
| R5 | Una receta no se referencia a sí misma. |
| R6 | Se permiten ciclos (A usa B y B usa A); se muestra un aviso al guardar (D2). |
| R7 | Borrar una receta que otras usan: se muestra quién la usa y se pide confirmación; las referencias quedan como "receta no disponible" (D3). |
| R8 | **Escalado**: `cantidadMostrada = cantidad × (porcionesElegidas / porcionesBase)`. Cantidades vacías no cambian; el texto de los pasos no se escala; no modifica la receta guardada. **Redondeo (D32)**: se calcula con precisión completa y se muestra con **hasta 2 decimales**, redondeando la mitad hacia arriba y sin ceros al final (1,50 → 1,5; 0,333 → 0,33). |
| R9 | El backend ofrece la receta a N porciones; las apps recalculan en pantalla con la misma regla y casos de prueba compartidos, para que sea instantáneo (D4). |
| R10 | Hasta **10 fotos** por receta; se guardan redimensionadas y **sin metadatos de la cámara** (ubicación, aparato), para no revelar dónde se sacó la foto (backend-07). |
| R11 | Las fotos se identifican por una referencia propia, nunca por una ruta del dispositivo. |

**Búsqueda**

| # | Regla |
|---|---|
| B1 | Encuentra por **nombre, tag o ingrediente**, sin distinguir mayúsculas ni tildes (D11). |
| B2 | Dos alcances: "mío" y "público". Cada uno busca recetas y colecciones. En públicas nunca aparece nada privado. |

**Visibilidad**

| # | Regla |
|---|---|
| V1 | Toda receta nueva es **privada**. |
| V2 | Solo el dueño edita, borra o cambia la visibilidad. |
| V3 | Una privada la ve solo su dueño. Una pública la ve **cualquier persona con cuenta** (D20). Sin cuenta no se ve nada. |
| V4 | Las fotos tienen la misma visibilidad que su receta. |
| V5 | Una referencia a algo que la persona no puede ver (privada de otro, borrada) se muestra como "receta no disponible". |
| V6 | Al publicar una receta que usa recetas privadas propias: aviso + opción de publicarlas también (D21). |
| V7 | Hay un **buscador de recetas públicas** (D22). |
| V8 | Una receta pública se puede **copiar** a "mis recetas": la copia es privada, independiente, y muestra "basada en: <receta> de <autor>" (D23). Si el original se borra, la copia sigue intacta. |
| V9 | De un autor se muestra su nombre visible, nunca su correo. |
| V10 | Sin moderación ni denuncias en el MVP (D24). |

**Colecciones (K)**

| # | Regla |
|---|---|
| K1 | Una colección tiene nombre no vacío y una lista **ordenada** de recetas. Una receta puede estar en varias colecciones. |
| K2 | Toda colección nueva es **privada**. Solo el dueño la edita, la borra o cambia su visibilidad. |
| K3 | Puede incluir recetas propias (privadas o públicas) y recetas **públicas** de otras personas. |
| K4 | Una colección pública la ve cualquier persona con cuenta, pero **dentro solo ve las recetas que puede ver**: las privadas del dueño aparecen como "no disponible" para los demás. |
| K5 | Al publicar una colección que tiene recetas privadas propias: aviso + opción de publicarlas también (igual que V6). |
| K6 | Si una receta de la colección se borra o pasa a privada (de otro), queda como "no disponible" hasta que el dueño la quite (igual que F3). |
| K7 | Las colecciones públicas aparecen en el buscador de públicas y se pueden marcar como **favoritas**. |
| K8 | Borrar una colección **no borra** sus recetas. |

**Favoritos**

| # | Regla |
|---|---|
| F1 | Se puede marcar como favorita una receta o colección propia, o una pública de otro. |
| F2 | Marcar dos veces no duplica. |
| F3 | Si una favorita deja de estar disponible, queda en la lista como "no disponible" hasta que la persona la quite (D25). |
| F4 | Los favoritos son privados. |

**Sincronización**

| # | Regla |
|---|---|
| Y1 | Todo vive en el backend; cualquier dispositivo con sesión ve el mismo estado. |
| Y2 | Los cambios de otro dispositivo se ven **al abrir o refrescar** la pantalla (D26). La actualización en vivo queda para el futuro. |
| Y3 | Si dos dispositivos editan la misma receta a la vez, el segundo recibe un **conflicto** y elige qué conservar (D7). |
| Y4 | Las preferencias (idioma, color, modo oscuro, nombre) se sincronizan. |

**Exportar e importar (E)**

| # | Regla |
|---|---|
| E1 | Exportar genera **un archivo de respaldo** con las recetas propias elegidas (o todas), sus fotos, las colecciones propias y la lista de favoritos. |
| E2 | Al exportar, la persona elige una **contraseña**. El archivo queda **cifrado** con ella: sin la contraseña no se puede leer ni importar. Si la olvida, no hay forma de recuperarlo (se avisa antes). |
| E3 | El cifrado también **detecta cualquier modificación**: un archivo alterado no se importa. |
| E4 | El formato interno no está pensado para que la persona lo lea ni lo edite a mano; puede ser público (el código de la plataforma es público). La protección del respaldo es la contraseña (E2) y la detección de cambios (E3) (D35). |
| E5 | Se exige una contraseña mínima razonable (longitud) y confirmarla dos veces. |
| E6 | Importar acepta **solo respaldos de la plataforma** y pide la contraseña. |
| E7 | Importar **nunca borra**. En la **misma cuenta**: agrega las recetas que no están y actualiza las que tienen el mismo id. En **otra cuenta**: crea recetas nuevas (ids nuevos), privadas, con las referencias internas del respaldo actualizadas a los ids nuevos. |
| E8 | Al importar, los favoritos se restauran solo si esas recetas siguen disponibles; si no, quedan como "no disponible". |
| E9 | Al terminar, se muestra un resumen: creadas, actualizadas, fotos, favoritos y advertencias. |
| E10 | El respaldo lleva una versión de formato, para poder leer respaldos viejos cuando el formato cambie. |
| E11 | A futuro, si se abre a más personas: agregar una descarga legible de los datos propios (exigencia de algunas leyes de datos personales). |

**Cuentas (C)**

| # | Regla |
|---|---|
| C1 | Inicio de sesión por **correo + contraseña** y por **código enviado al correo** (D10). El correo se verifica al crear la cuenta. |
| C2 | Recuperar acceso: código o enlace al correo para crear una contraseña nueva. |
| C3 | Una persona ve sus sesiones abiertas y puede cerrar una o todas. |
| C4 | **Borrar cuenta**: antes se ofrece **dejar sus recetas y colecciones públicas publicadas bajo un "autor anónimo"**, para que no se pierdan para quienes las usan (D12/D27). Si no acepta, se borran. Todo lo demás (privadas, fotos, favoritos, perfil, sesiones) se borra siempre. |
| C5 | Antes de borrar también se ofrece **descargar el respaldo** cifrado (D29). |
| C6 | Límites para evitar abusos: intentos de inicio de sesión y envíos de código por correo limitados por tiempo. |

### 3.3 Glosario

*Receta, dueño, visibilidad (pública / privada), copia ("basada en"),
porciones base, porciones elegidas, factor de escala, ingrediente "c/n",
referencia, receta usada / usada en, favorito, sesión, dispositivo,
respaldo cifrado, autor anónimo.*

---

## 4. Arquitectura del backend (sin tecnología)

### 4.1 Un backend, módulos con fronteras estrictas

| Módulo | Responsabilidad | Datos propios |
|---|---|---|
| **Identidad** | Cuentas, verificación de correo, inicio de sesión (contraseña y código), recuperación, sesiones, borrado de cuenta | Cuentas, credenciales, sesiones, códigos |
| **Perfil** | Nombre visible y preferencias sincronizadas | Perfiles |
| **Recetas** | Crear/editar/borrar, visibilidad, copias, referencias, escalado, búsqueda (R, B, V) | Recetas, tags, índice de búsqueda |
| **Colecciones** | Crear/editar/borrar, ordenar recetas, visibilidad (K) | Colecciones |
| **Favoritos** | Marcar, desmarcar, listar (F) | Favoritos |
| **Medios** | Recibir, redimensionar, servir y borrar fotos según visibilidad | Archivos e información de fotos |
| **Respaldo** | Exportar e importar respaldos cifrados (E) | Trabajos de importación/exportación en curso y subidas en partes |
| **Notificaciones** | Enviar correos (verificación, códigos, recuperación) | Registro de envíos |

Perfil, Recetas, Colecciones y Favoritos guardan además su **registro de
cambios** (incluidos los borrados), que usa "cambios desde" (§6).

Reglas entre módulos:

| # | Regla |
|---|---|
| M1 | Cada módulo es **dueño exclusivo de sus datos**. |
| M2 | Un módulo usa a otro **solo por su interfaz pública**. |
| M3 | Para avisar que algo pasó, publica un **evento interno**, que se entrega aunque el backend se reinicie (backend-02): *RecetaBorrada* → Medios borra las fotos; Favoritos y Colecciones anotan el cambio en su registro de cambios. *VisibilidadCambiada* → Favoritos y Colecciones anotan el cambio. *CuentaBorrada* → todos borran o ceden (C4) lo de esa persona. **La visibilidad de cada cosa la decide su módulo dueño**: cuando un módulo muestra algo de otro (Favoritos y Colecciones muestran recetas; Recetas pide a Medios las direcciones de sus fotos), el dueño decide qué se puede ver, y lo que no se puede ver aparece como "no disponible" (backend-03). |
| M4 | Sin dependencias circulares entre módulos. |
| M5 | Respetando M1–M4, cualquier módulo se puede sacar a un servicio aparte cambiando solo cómo se comunica. |

### 4.2 Capas dentro de cada módulo

```
  Entrada (HTTP, tareas programadas)      ← adaptadores de entrada
        │
  Casos de uso                            ← orquesta, sin tecnología
        │
  Dominio (entidades y reglas)            ← puro
        ▲
  Puertos (interfaces: "guardar receta", "guardar archivo",
  "enviar correo", "cifrar respaldo", "buscar texto")
        ▲
  Adaptadores de salida (base de datos, almacenamiento de fotos,
  servicio de correo, motor de búsqueda)  ← lo único que conoce la tecnología
```

| Si se cambia… | Se toca solo… |
|---|---|
| La base de datos | El adaptador de persistencia de cada módulo |
| Dónde se guardan las fotos | El adaptador de almacenamiento de Medios |
| El servicio de correo | El adaptador de Notificaciones |
| El motor de búsqueda | El adaptador de búsqueda de Recetas |
| El framework web | Los adaptadores de entrada |
| El lenguaje del backend entero | Se reescribe, pero el **contrato** y sus **pruebas** garantizan que las apps no se enteran |

### 4.3 Apps

Cada app es un proyecto con su propia arquitectura interna (Clean
Architecture por defecto). Ninguna tiene reglas de negocio propias salvo el
escalado (R9), validado con los casos de prueba compartidos.

Las apps pueden **compartir código común** en paquetes compartidos, sin
dejar de ser proyectos separados, cada uno con su versión: uno para las tres
apps (pantallas, casos de uso de la app, acceso al contrato y escalado;
app-web-02) y otro solo para las apps nativas de Apple y Android, con los
adaptadores del aparato (sesión guardada en el aparato, fotos, archivos de
respaldo, conexión y versión; app-android-02). Lo compartido tampoco tiene
reglas de negocio, salvo el escalado.

---

## 5. Casos de uso (MVP)

| ID | Caso de uso |
|---|---|
| CU01 | Crear cuenta y verificar correo |
| CU02 | Iniciar sesión con contraseña o con código por correo |
| CU03 | Recuperar acceso |
| CU04 | Ver sesiones y cerrar una o todas |
| CU05 | Editar perfil y preferencias |
| CU06 | Borrar cuenta (C4, C5) |
| CU07 | Crear receta (privada) |
| CU08 | Editar receta propia (con conflicto si hay edición simultánea) |
| CU09 | Borrar receta propia (R7) |
| CU10 | Ver receta (propia o pública) con "usa" y "usada en" |
| CU11 | Ver receta escalada a N porciones |
| CU12 | Buscar en mis recetas (nombre, tag, ingrediente) |
| CU13 | Buscar recetas públicas |
| CU14 | Publicar / despublicar receta (V6) |
| CU15 | Copiar receta pública a mis recetas |
| CU16 | Marcar / desmarcar favorito y ver mis favoritos |
| CU17 | Insertar referencia a otra receta (propia o pública) |
| CU18 | Gestionar fotos de una receta (hasta 10) |
| CU19 | Exportar respaldo cifrado |
| CU20 | Importar respaldo |
| CU21 | Crear, editar, ordenar y borrar colecciones |
| CU22 | Publicar / despublicar colección (K5) |
| CU23 | Ver colección (propia o pública) |

---

## 6. Contrato conceptual

**Recursos**: Cuenta/Sesión, Perfil, Receta, Colección, Foto, Favorito, Respaldo.

**Operaciones principales**

- Cuenta y sesión: registrarse, verificar correo, iniciar sesión (contraseña
  o código), pedir código, recuperar acceso, listar y cerrar sesiones,
  borrar cuenta.
- Perfil: obtener y actualizar.
- Recetas: buscar mías, buscar públicas, obtener (opcional a N porciones),
  crear, modificar, borrar, publicar/despublicar, copiar, "usada en".
- Fotos: subir (en los formatos comunes que declara el contrato,
  contrato-14), obtener en tamaños, describir/ordenar, quitar.
- Colecciones: listar mías, buscar públicas, obtener, crear, modificar
  (nombre, descripción, recetas y su orden), borrar, publicar/despublicar.
- Favoritos: marcar, desmarcar, listar (recetas y colecciones).
- Respaldo: exportar (con contraseña), importar (con contraseña y resumen).
  El archivo para importar se sube **en partes**, para poder retomar si se
  corta y no chocar con límites de tamaño por pedido.
- **Cambios desde**: "qué cambió desde la última vez que consulté", para
  refrescar rápido (Y2). **Una consulta por módulo** (Perfil, Recetas,
  Colecciones, Favoritos), que incluye lo borrado.

**Reglas del contrato**

1. Todo pedido autenticado, salvo registrarse, iniciar sesión, pedir código
   y recuperar acceso. **Fotos**: la dirección de cada foto se obtiene con
   un pedido autenticado y viene **firmada**; la imagen se baja con esa
   firma, que **vence a las pocas horas** (contrato-08).
2. El backend sabe quién es la persona por su sesión; una app nunca envía
   "soy el usuario X".
3. Versionado: un cambio que rompe crea una versión nueva.
4. Errores uniformes: código, mensaje legible, detalle por campo.
5. Identificadores únicos y estables.
6. Cada receta y colección lleva una versión para detectar ediciones simultáneas (Y3).
7. Idempotencia en crear, importar y marcar favoritos.
8. La contraseña de un respaldo nunca se guarda en el backend.

Las convenciones concretas del contrato (versionado, errores, conflictos,
idempotencia, paginación, cambios, subidas, fotos, sesión, nombres y
formatos, clientes, herramientas y formatos de fotos) están en
DECISIONES.md, de **contrato-01** a **contrato-14**.

---

## 7. Requisitos no funcionales

| Tema | Decisión / propuesta |
|---|---|
| **Conexión** | Requiere internet (D8). Sin conexión: aviso claro y reintentar. |
| **Seguridad** | Todo por HTTPS en el **dominio propio**; contraseñas nunca en claro; sesiones que expiran y se revocan; fotos privadas solo para su dueño (direcciones firmadas que vencen, contrato-08); límites de intentos (C6); respaldos cifrados (E2). Mientras la plataforma corra en la notebook, el HTTPS termina en el servicio que la publica (túnel), que ve el tráfico descifrado: excepción temporal aceptada, que termina al pasar al VPS (base-plataforma-08). |
| **Privacidad** | Privado por defecto; favoritos y correos nunca visibles para otros; fotos guardadas sin metadatos de la cámara, como la ubicación (R10). |
| **Rendimiento (prioridad del backend)** | Respuestas rápidas: abrir una receta < 1 s, buscar < 1 s, marcar favorito al instante (la app lo muestra y confirma después). |
| **Datos (prioridad: escalabilidad)** | El almacenamiento de datos tiene que poder crecer sin rediseño hasta la meta de diseño de 10.000 personas (D33), aunque el volumen inicial sea chico. |
| **Fotos (prioridad: entrega rápida)** | Redimensionadas al subir, varios tamaños (miniatura y pantalla), sin metadatos de la cámara, servidas de forma que carguen rápido. |
| **Volumen** | < 100 personas el primer año; **meta de diseño: 10.000 personas con cuenta** sin rediseñar (D33); cientos a pocos miles de recetas por persona; hasta 10 fotos por receta. |
| **Disponibilidad** | Mientras la plataforma corra en la notebook (D31), depende de la luz, de internet y de que la notebook esté prendida; se acepta para el grupo inicial. Se pasa a un VPS cuando se cumple alguno de los criterios medibles de §8. |
| **Respaldo del sistema** | Diario, guardando 30 días, en **dos lugares**: un disco externo en casa (restauración rápida) y una copia **cifrada fuera** del lugar donde corre la plataforma (fuera de la casa mientras sea la notebook). La restauración se prueba todos los meses (base-plataforma-12). |
| **Observabilidad** | Registros con identificador por pedido. Avisos si el backend cae, si un respaldo o la prueba de restauración no corren y si un disco pasa el 70 %, desde **fuera** del lugar donde corre la plataforma, con un proyecto propio: el **vigía** (D34). |
| **Idiomas** | Contenido en el idioma en que se cargó; interfaz ES/EN. |

---

## 8. Contexto para decidir tecnología y despliegue

| Tema | Respuesta |
|---|---|
| Alojamiento | **Primero una notebook con Linux** en casa, para desarrollar y para atender a las primeras personas, con desarrollo y producción separados. **Después un servidor propio en la nube (VPS)**, administrado por el usuario, cuando la notebook quede chica (D31). Se pasa al VPS si se cumple **alguno** de estos criterios: más de 8 h de caída en un mes o un corte de más de 24 h; la subida de internet de la casa no alcanza para los tiempos de §7; se va a invitar a más de 50 personas o a personas desconocidas; o la notebook hace falta para otra cosa (base-plataforma-16). |
| Ubicación de las personas | **Paraguay, Argentina y Uruguay** → servidor cercano (São Paulo) cuando haya VPS. |
| Dominio | **Propio, ~USD 11/año**; se compra apenas se elija el nombre, antes de abrir a la primera persona. |
| Correo | Necesario (verificación, códigos, recuperación). Requiere un servicio de envío con el dominio propio, con un servicio principal y otro de respaldo, intercambiables por configuración (base-plataforma-10). En desarrollo, los correos quedan en una bandeja de prueba local. |
| Presupuesto | Hasta **USD 20/mes** en total para el arranque (servidor, fotos, respaldos, correo, dominio). Al crecer hacia la meta de 10.000 personas se revisa (D33). |
| Tiendas | **No** se publica por ahora. iPhone, iPad y Mac con **firma gratuita de desarrollo**, solo en los aparatos propios (en iPhone y iPad se reinstala cada 7 días); las demás personas usan la web (en el iPhone, se puede agregar a la pantalla de inicio). La cuenta paga de Apple (~USD 99/año) **no se paga por ahora**: se revisa cuando 5 personas con iPhone o Mac la pidan (base-plataforma-18). **Android**: APK firmado con una clave propia; en los aparatos propios se instala desde la notebook y en los de **personas conocidas, por descarga directa** desde el dominio propio, sin pagar. Desde 2027 (verificación de desarrolladores de Google) se usa la **cuenta gratuita de distribución limitada** de Google (hoy, hasta 20 aparatos) y, solo si se superan 15 aparatos autorizados, la completa (USD 25 una vez) (app-android-03, base-plataforma-19). |
| Aparatos de prueba | Mac, iPhone, celular Android y tablet Android. El iPad se prueba en el simulador y los plegables, en el emulador de Android. |
| Usuarios | < 100 el primer año (amigos y conocidos); **meta de diseño: 10.000** (D33). |
| Tiempo disponible | Más de 10 h por semana. |
| Repositorio | **Uno solo con todo** (backend, apps, contrato), **público** (P-21, base-plataforma-20). |
| Orden de construcción | Base común (fase desarrollo) → contrato → backend → **web → Apple → Android**. La fase producción de la base común y el **vigía** (D34) se completan antes de abrir a la primera persona. |
| Criterio para elegir tecnología | **Cada parte con la tecnología que mejor sirva a su necesidad**: backend → rapidez; datos → escalabilidad; fotos → entrega rápida. Se proponen opciones y se descartan según conveniencia (costo, mantenimiento, coherencia). Distintas tecnologías entre partes están permitidas si se justifican. |
| Experiencia previa | Flutter + Riverpod + Clean Architecture (app anterior). Trabaja con Claude Code. |

---

## 9. Riesgos

| Riesgo | Mitigación |
|---|---|
| Módulos que se mezclan con el tiempo | Reglas M1–M5 verificadas con pruebas de arquitectura. |
| Fugas de privacidad | Pruebas específicas por regla V1–V5 y F4. |
| Olvido de la contraseña de un respaldo | Aviso claro antes de exportar; confirmación doble. |
| Administrar el servidor (notebook y después VPS: actualizaciones, seguridad, caídas) | Plan de mantenimiento en base-plataforma (fase producción, base-plataforma-14); respaldos diarios probados (restauración automática todos los meses). |
| Notebook como servidor: cortes de luz o de internet, suspensión, robo o pérdida, mezcla con el trabajo de desarrollo | Desarrollo y producción separados; respaldos en disco externo y fuera de la casa; vigía; disco cifrado que vuelve solo después de un corte; batería como UPS; sin suspensión; pasar a VPS con criterios medibles. |
| Crecer hacia 10.000 personas: disco de fotos, tamaño del servidor, presupuesto | Meta de diseño desde el inicio (D33); las fotos detrás de un puerto de almacenamiento (backend-06), así que mudarlas a un almacenamiento externo es cambiar un adaptador; aviso del disco al 70 %; revisar el presupuesto al crecer. |
| Correos que no llegan (spam) | Servicio de envío con dominio propio configurado correctamente (SPF, DKIM y DMARC), con un servicio de respaldo (base-plataforma-10). |
| Contenido público inapropiado | Solo personas con cuenta; moderación a futuro. |
| Tres apps desparejas | Contrato único + lista de funcionalidades común + casos de prueba compartidos + código común compartido entre las apps (app-web-02, app-apple-01, app-android-01) + adaptadores nativos compartidos entre Apple y Android (app-android-02). |
| Muchas tecnologías distintas para mantener | Cada tecnología nueva se justifica (§8). |
| Perder la clave de firma de Android (sin ella no hay más actualizaciones ni registro en Google) | Clave fuera del repositorio, copia cifrada fuera de la casa y una restauración probada (app-android-04, base-plataforma-12). |
| Verificación de desarrolladores de Google (llega a la región en 2027) | Cuenta gratuita de distribución limitada abierta apenas existan el dominio y la clave de producción; los aparatos propios se instalan desde la notebook, que no la necesita; la web queda como alternativa (app-android-03, base-plataforma-19). |
| Una sola cuenta concentra el dominio, el DNS y la publicación | Respaldos y vigía fuera de esa cuenta (disco en casa, otro proveedor de respaldos y otro proveedor para el vigía, vigia-01); verificación en dos pasos en todas las cuentas; el dominio se puede transferir. |
| Mientras sea la notebook, quien publica la plataforma ve el tráfico descifrado | Excepción temporal y aceptada (base-plataforma-08); termina al pasar al VPS; los respaldos viajan cifrados. |
| Perder la contraseña del repositorio de respaldos (sin ella, los respaldos no se abren) | Gestor de contraseñas fuera de la notebook; prueba de restauración mensual que la usa. |
| Un vigía propio que falle en silencio | Resumen diario por Telegram y correo: si no llega, algo anda mal (D34, vigia-03). |
| Código y documentos públicos: un secreto, una clave o un dato personal subido por error queda a la vista | Secretos solo en archivos por entorno fuera del repositorio; protección contra secretos de GitHub activada; commits con la dirección privada de GitHub; claves de las señales del vigía fuera del repositorio; nada de la seguridad depende de que el código sea secreto (base-plataforma-20). |
| Planes gratis que cambian o se cortan (correo, respaldos, registro de imágenes) | Todo intercambiable por configuración (correo por SMTP con servicio de respaldo, señales HTTP estándar, respaldos con formato propio de la herramienta). |

---

## 10. A futuro

- **Crear receta desde una foto** (sacar una foto a una receta de un libro
  y que se arme sola: nombre, ingredientes, pasos, foto del plato), con
  revisión antes de guardar. El caso de uso "crear receta" ya lo soporta.
- Actualización en vivo entre dispositivos.
- Caché sin conexión.
- Iniciar sesión con Apple / Google.
- Descarga legible de los datos (E11).
- Compartir una receta privada con personas puntuales.
- Moderación de recetas públicas.
- Publicación en tiendas.
- Lista de compras, planificador, nutrición.

---

## 11. Decisiones

| ID | Tema | Estado |
|---|---|---|
| D1 | Usuarios | ✅ Varias personas con cuenta |
| D2 | Ciclos de referencias | ✅ Permitir y avisar |
| D3 | Borrar receta usada por otras | ✅ Avisar y confirmar |
| D4 | Escalado | ✅ Backend + misma regla en apps |
| D5 | Preferencias | ✅ Sincronizadas |
| D6 | Arquitectura | ✅ Backend único modular |
| D7 | Ediciones simultáneas | ✅ Conflicto explícito |
| D8 | Conexión | ✅ Requiere internet |
| D9 | Costo | ✅ Hasta USD 20/mes para el arranque; se revisa al crecer (P-19) |
| D10 | Inicio de sesión | ✅ Correo + contraseña y código por correo |
| D11 | Búsqueda por ingrediente | ✅ Sí |
| D12/D27 | Borrar cuenta | ✅ Ofrecer dejar las públicas bajo "autor anónimo"; si no, se borran |
| D14 | Orden de apps | ✅ Web → Apple → Android |
| D15 | Colecciones | ✅ En el MVP, privadas o públicas (K1–K8) |
| D16 | Apps | ✅ Web, Apple, Android |
| D17–D19 | Públicas/privadas, favoritos, sincronización | ✅ |
| D20 | Quién ve públicas | ✅ Solo personas con cuenta |
| D21 | Publicar con referencias privadas | ✅ Avisar y ofrecer publicarlas |
| D22 | Buscador de públicas | ✅ Sí |
| D23 | Copiar receta pública | ✅ Sí, privada, con "basada en" |
| D24 | Moderación | ✅ Fuera del MVP |
| D25 | Favorito no disponible | ✅ Queda marcado |
| D26 | Sincronización | ✅ Al abrir o refrescar |
| D28 | Exportar/importar | ✅ Respaldo cifrado con contraseña; importa solo respaldos propios |
| D29 | Ofrecer respaldo antes de borrar cuenta | ✅ Sí |
| D30 | Dominio | ✅ Propio, ~USD 11/año |
| D31 | Alojamiento inicial | ✅ Notebook con Linux (desarrollo y primeras personas); VPS después (P-17) |
| D32 | Redondeo del escalado | ✅ Hasta 2 decimales, la mitad hacia arriba, sin ceros al final (P-18) |
| D33 | Escala | ✅ Meta de diseño de 10.000 personas con cuenta; USD 20/mes para el arranque (P-19) |
| D34 | Vigilancia | ✅ Proyecto propio, el "vigía", que corre fuera del lugar donde corre la plataforma (P-20) |
| D35 | Formato del respaldo y código público | ✅ El repositorio es público; E4 ya no exige que el formato del respaldo sea secreto: protegen la contraseña (E2) y la detección de cambios (E3) (P-21) |

**Criterio para pasar a tecnología**: ✅ cumplido. La tecnología se decide
**por proyecto** (MAPA.md): base común (fase desarrollo ✅) → contrato ✅ →
backend ✅ → web ✅ → Apple ✅ → Android ✅ → base común (fase producción ✅)
→ vigía ✅. El vigía se construye antes de abrir a la primera persona.
