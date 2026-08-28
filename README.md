# 🏠 Casa Madridejos
### *decorando mi casita*

> Aplicación web colaborativa para que Esther y sus amigas participen juntas en la decoración y reforma del piso. Todo en tiempo real, desde el móvil, sin instalación.

---

## Índice

1. [¿Qué es?](#1-qué-es)
2. [¿Para qué sirve?](#2-para-qué-sirve)
3. [Cómo funciona — guía de uso](#3-cómo-funciona--guía-de-uso)
4. [Arquitectura técnica](#4-arquitectura-técnica)
5. [Sistema de diseño visual](#5-sistema-de-diseño-visual)
6. [Contenido por habitación](#6-contenido-por-habitación)
7. [Infraestructura y configuración](#7-infraestructura-y-configuración)
8. [Cómo desplegar](#8-cómo-desplegar)
9. [Limitaciones conocidas](#9-limitaciones-conocidas)
10. [Posibles mejoras futuras](#10-posibles-mejoras-futuras)
11. [Glosario técnico](#11-glosario-técnico)

---

## 1. ¿Qué es?

**Casa Madridejos** es una aplicación web colaborativa de una sola página (`index.html`) diseñada para que un grupo de personas pueda participar juntas en la decoración y reforma de un piso. Funciona como un tablón digital privado organizado por habitaciones.

No requiere instalación ni registro. Se abre desde el navegador del móvil como una URL y todas las aportaciones se sincronizan en tiempo real gracias a Firebase Firestore.

| Campo | Valor |
|---|---|
| **Nombre** | Casa Madridejos |
| **Subtítulo** | decorando mi casita |
| **Tipo** | SPA — archivo único `index.html` |
| **Hosting** | GitHub Pages (gratuito) |
| **Base de datos** | Firebase Firestore (plan Spark — gratuito) |
| **Usuarios** | Esther + Ali, Paula, María, Alba, Albita |
| **Dispositivo principal** | Móvil (responsive) |
| **Autenticación** | Ninguna — acceso por URL |

---

## 2. ¿Para qué sirve?

El problema que resuelve: cuando varias personas quieren colaborar en la decoración de una casa, la coordinación se hace caótica por WhatsApp — fotos perdidas, ideas dispersas, opiniones sin contexto. Casa Madridejos centraliza todo en un espacio estructurado.

### Funcionalidades principales

- 📷 **Ver y subir fotos** del espacio real de cada habitación
- 📋 **Inventario de objetos** con estado codificado por color: se queda / en duda / fuera
- 👍👎 **Votar** a favor o en contra de cada objeto del inventario
- 💬 **Comentar** directamente en cada objeto del inventario
- ➕ **Añadir objetos** nuevos al inventario (con su estado inicial)
- 🗑️ **Eliminar objetos** del inventario
- ✏️ **Editar el estado actual** y las medidas de cada habitación
- 📝 **Publicar notas, ideas, fotos de inspiración y enlaces** de productos
- 🔗 **Preview de enlaces** con nombre del producto y precio
- ♥ **Dar me gusta** a las publicaciones
- 🗺️ **Plano interactivo** del piso — tocar una habitación navega a ella
- 🔄 **Sincronización en tiempo real** — las publicaciones aparecen al instante para todas

---

## 3. Cómo funciona — guía de uso

### 3.1 Login

Al abrir la URL aparece la pantalla de bienvenida. El usuario elige su nombre entre los chips predefinidos o escribe uno libre. No hay contraseña.

Cada persona tiene un **color asignado** que aparece en su avatar, en el borde de sus publicaciones y en sus comentarios del inventario, para identificar quién ha publicado cada cosa de un vistazo.

### 3.2 Pantalla principal

Tras el login se muestra el plano del piso (SVG sobre fondo azul clarito) con botones flotantes animados sobre cada habitación. Al tocarlos se navega directamente a esa habitación. Debajo hay pestañas de navegación siempre visibles.

### 3.3 Dentro de cada habitación

Cada habitación tiene **cinco secciones** en orden descendente:

---

#### ① Fotos del espacio

Grid cuadrado **3×3**. Cualquier persona puede subir fotos desde su móvil. La previsualización siempre es cuadrada (`aspect-ratio: 1/1`). Al pulsar una foto se abre en lightbox a tamaño real, preservando la orientación original. Las fotos se comprimen automáticamente antes de guardar (máx. 900px, JPEG 75%).

---

#### ② Estado actual

Descripción textual de la habitación. Tiene un botón ✏️ que permite editar el texto en el momento. Las medidas del espacio también son editables con el mismo sistema. Ambos cambios se guardan en Firestore.

---

#### ③ Inventario — ¿qué se queda?

Lista de objetos de la habitación. Cada objeto tiene un estado visual:

| Estado | Color | Significado |
|---|---|---|
| `✓ Se queda` | 🟢 Verde | El objeto se conserva |
| `? En duda` | 🟡 Amarillo | No está decidido |
| `✕ Fuera` | 🔴 Rojo | El objeto sale |

Al tocar un objeto se despliega su ficha con:
- Descripción del objeto
- Botones 👍 **A favor** / 👎 **En contra** con contadores en tiempo real
- Historial de comentarios con avatar de color de cada persona
- Campo para añadir comentario nuevo (Enter o botón Enviar)
- Botón ✕ para eliminar el objeto

Al final del inventario hay un formulario para añadir un objeto nuevo, eligiendo primero su estado (se queda / en duda / fuera).

---

#### ④ Notas e ideas

Nota inicial de Esther con sus ideas para la habitación. Aparece en un recuadro de fondo terracota claro para diferenciarse visualmente del resto del contenido.

---

#### ⑤ Publicaciones

Feed de publicaciones en tiempo real (Firebase `onSnapshot`). Cada publicación es una tarjeta con:

- Borde izquierdo del color de la autora
- Avatar circular con iniciales + nombre + tiempo relativo
- Foto cuadrada 1:1 (si la hay) — al pulsar abre lightbox
- Texto libre
- Enlace con preview del dominio y precio (opcional)
- Botón ♥ me gusta

### 3.4 Publicar

El botón **+** fijo en la parte inferior despliega el formulario de publicación:
- Texto libre (nota, idea, opinión)
- Foto de inspiración o del espacio
- Enlace (IKEA, Zara Home, Wallapop…)
- Nombre del producto / precio

Todo es opcional excepto tener al menos un campo relleno. Al publicar, la tarjeta aparece en tiempo real para todas las participantes.

---

## 4. Arquitectura técnica

### 4.1 Estructura del proyecto

```
casa-madridejos/
└── index.html          # Toda la aplicación: HTML + CSS + JS
```

No hay servidor propio, no hay proceso de build, no hay npm, no hay bundler. Un solo archivo estático.

| Aspecto | Detalle |
|---|---|
| **Tamaño** | ~45 KB |
| **Dependencias externas** | Firebase SDK (CDN), Google Fonts |
| **JS** | Vanilla JavaScript puro |
| **CSS** | Inline en `<style>`, sin frameworks |
| **Compatibilidad** | Todos los navegadores modernos, iOS Safari, Android Chrome |

### 4.2 Patrón de arquitectura JavaScript

```
Estado global (variables JS)
    ↓
switchRoom(id)          ← cambia habitación activa
    ↓
renderRoom()            ← reescribe innerHTML del contenedor
    ↓
loadFotosGrid()         ← carga fotos desde Firestore
loadInvData()           ← carga votos y comentarios
subscribePosts()        ← activa onSnapshot para el feed
    ↓
UI actualizada          ← el usuario interactúa
    ↓
vote() / addComment()   ← escribe en Firestore
likePost() / publish()  ← escribe en Firestore
```

Cuando el usuario cambia de habitación, se cancela el listener anterior (`unsubscribe()`) y se crea uno nuevo para la habitación activa.

### 4.3 Compresión de imágenes

Antes de subir cualquier foto a Firestore se comprime en el cliente:

```javascript
function compressImg(file, maxPx = 900) {
  // 1. Leer el archivo como DataURL
  // 2. Dibujar en un <canvas> escalado a maxPx
  // 3. Exportar como JPEG al 75% de calidad
  // Resultado: ~50-150 KB por imagen (cabe en 1MB de Firestore)
}
```

### 4.4 Firebase Firestore — Colecciones

| Colección | Descripción | Campos principales |
|---|---|---|
| `fotos_casa` | Fotos del espacio por habitación | `room`, `dataUrl`, `author`, `createdAt` |
| `posts` | Publicaciones del feed | `room`, `author`, `createdAt`, `text`, `fotoData`, `linkUrl`, `prod`, `likedBy[]` |
| `inv_votes` | Votos del inventario | `room`, `itemId`, `votes: { si:[], no:[] }` |
| `inv_comments` | Comentarios del inventario | `room`, `itemId`, `author`, `text`, `ts` |
| `inv_extra` | Objetos añadidos por usuarias | `room`, `item`, `author`, `createdAt` |
| `room_data` | Estado y medidas editables | `estado`, `plano` (ID = roomId) |

### 4.5 Reglas de seguridad de Firestore

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

> ⚠️ Modo abierto — adecuado para un grupo pequeño de confianza donde la URL no se comparte públicamente. Para mayor seguridad habría que implementar Firebase Authentication.

---

## 5. Sistema de diseño visual

### 5.1 Paleta de colores

```css
--bg:      #FAF7F2  /* Fondo principal — crema cálida */
--bg2:     #F2ECE3  /* Fondo secundario — arena */
--bg3:     #E8DFD0  /* Fondo terciario — lino */
--white:   #FFFFFF
--ink:     #1A1814  /* Negro cálido — texto principal */
--ink2:    #4A4540  /* Negro suave — texto secundario */
--muted:   #9A9088  /* Gris cálido — texto terciario */
--border:  #DDD5C8  /* Borde suave */
--border2: #C8BFB0  /* Borde enfatizado */
--sage:    #4A7C59  /* Verde salvia — color principal */
--sage-d:  #2D5C3A  /* Verde oscuro — hovers */
--sage-l:  #EAF2EC  /* Verde pálido — fondos */
--clay:    #B5654A  /* Terracota — acento */
--clay-l:  #FAF0EB  /* Terracota pálido — fondos */
```

### 5.2 Colores por persona

| Persona | Color | Hex |
|---|---|---|
| **Esther** | Terracota | `#B5654A` |
| **Ali** | Verde salvia | `#4A7C59` |
| **Paula** | Púrpura | `#7B5EA7` |
| **María** | Azul | `#2563EB` |
| **Alba** | Dorado | `#B8860B` |
| **Albita** | Rosa | `#D4537E` |

El color de cada persona aparece en: chip del login, avatar en topbar, borde de publicaciones, avatar en comentarios de inventario.

### 5.3 Tipografía

| Uso | Fuente |
|---|---|
| Logo, títulos, encabezados | Lora (Google Fonts, serif) |
| Cuerpo, UI, botones | Inter (Google Fonts, sans-serif) |

### 5.4 Componentes principales

**Login**
Fondo arena con decoración geométrica (cuadrados que evocan el plano de una planta). Los chips de nombres se colorean con el color de cada persona al seleccionarse.

**Topbar**
Fondo arena, nombre en Lora, subtítulo en versalitas, avatar circular del usuario activo con su color.

**Plano interactivo**
SVG sobre fondo `#EFF6FF` (azul muy claro), habitaciones en `#DBEAFE`, paredes en `#1E40AF`. Hotspots circulares con animación CSS `@keyframes pulse`.

**Grid de fotos**
3 columnas de cuadrados perfectos (`aspect-ratio: 1/1`, `object-fit: cover`). El último hueco es siempre el botón de subir. Al pulsar, lightbox con la imagen a tamaño real.

**Inventario**
Cards con cabecera clicable (nombre + badge de estado). Cuerpo desplegable con votos, comentarios y campo de comentar. Badge de estado con fondo semántico (verde/amarillo/rojo).

**Publicaciones**
Tarjeta con `border-left: 3px solid [color de la autora]`. Foto cuadrada 1:1 si la hay. Link con preview del dominio. Me gusta con transición a terracota.

---

## 6. Contenido por habitación

### 🛋 Salón (4,20 × 5,10 m · Dos ventanas al sur)

**Estado actual:** Sofá gris, librería con guitarras, mesa de comedor negra. Buena luz al sur.

**Ideas de Esther:** Verde salvia en la pared principal. Alfombra de fibras naturales grande. Reorganizar la librería.

**Inventario:**
| Objeto | Estado |
|---|---|
| Mesa comedor negra + sillas | ✓ Se queda |
| Mesa bajita de centro | ? En duda |
| Mueble del televisor | ✕ Fuera |
| Armario junto a la ventana | ✓ Se queda |
| Guitarras | ? En duda |

---

### 🛏 Cuarto (2,61 × 3,57 m · Ventana al norte)

**Estado actual:** Cama doble, armario empotrado, escritorio en la esquina. Poca luz natural.

**Ideas de Esther:** Cambiar la posición de la cama. Más iluminación: apliques o lámparas colgantes.

**Inventario:**
| Objeto | Estado |
|---|---|
| Cama doble | ✓ Se queda |
| Armario empotrado | ✓ Se queda (fijo) |
| Escritorio | ? En duda |

---

### 🚿 Baño (1,80 × 2,40 m · Sin ventana exterior)

**Estado actual:** Azulejos azules, ducha con mampara, lavabo de pedestal y WC.

**Ideas de Esther:** Vinilos para los azulejos. Espejo con luz integrada. Plantas.

**Inventario:**
| Objeto | Estado |
|---|---|
| Mampara de ducha | ✓ Se queda |
| Espejo actual | ✕ Fuera |
| Sanitarios | ✓ Se quedan |

---

### 🍳 Cocina (2,60 × 3,10 m · Ventana sobre el fregadero)

**Estado actual:** Cocina en L, muebles blancos roto, encimera de granito negro.

**Ideas de Esther:** ¿Pintar armarios de verde oscuro? Barra americana para ganar superficie.

**Inventario:**
| Objeto | Estado |
|---|---|
| Muebles de cocina | ? En duda |
| Encimera negra | ✓ Se queda |
| Electrodomésticos | ✓ Se quedan |

---

### 🚪 Recibidor (0,90 × 3,20 m)

**Estado actual:** Pasillo estrecho ~90 cm, perchas viejas, suelo de parquet.

**Ideas de Esther:** Mueble de entrada con zapatero. Espejo grande para dar sensación de amplitud.

**Inventario:**
| Objeto | Estado |
|---|---|
| Perchas actuales | ✕ Fuera |
| Sin espejo aún | ? En duda |

---

## 7. Infraestructura y configuración

### Firebase

| Campo | Valor |
|---|---|
| **Nombre del proyecto** | Esther Trujillo |
| **Project ID** | `esther-trujillo` |
| **Auth Domain** | `esther-trujillo.firebaseapp.com` |
| **Storage Bucket** | `esther-trujillo.firebasestorage.app` |
| **Messaging Sender ID** | `477764890376` |
| **App ID** | `1:477764890376:web:4f843cc6f4c0c8b2df06bd` |
| **Plan** | Spark (gratuito) |
| **Región Firestore** | eur3 (Europa) |

### GitHub

| Campo | Valor |
|---|---|
| **Plataforma** | GitHub |
| **Visibilidad del repo** | Public (requerido para GitHub Pages gratuito) |
| **Archivos necesarios** | Solo `index.html` |
| **Rama principal** | `main` |
| **GitHub Pages** | Activado con workflow "Static HTML" |

> ⚡ Firebase es completamente independiente de GitHub. El mismo proyecto Firebase funciona con cualquier repositorio, cualquier URL y cualquier nombre de repo. Las credenciales están embebidas en el `index.html`.

---

## 8. Cómo desplegar

### Primera vez — repositorio nuevo

**1. Crear el repositorio en GitHub**
- Ir a [github.com](https://github.com) → botón verde **"New"**
- Nombre sugerido: `casa-madridejos`
- Marcar como **Public**
- Clic en **"Create repository"**

**2. Subir el archivo**
- En la página del repo → **"Add file → Upload files"**
- Arrastrar `index.html`
- Clic en **"Commit changes"**

**3. Activar GitHub Pages**
- Ir a **Settings → Pages** (menú izquierdo)
- Clic en **"Configure"** en el recuadro "Static HTML"
- Clic en **"Commit changes"** en el archivo que se abre

**4. URL pública**
```
https://[usuario].github.io/casa-madridejos
```
Disponible en 1-2 minutos.

---

### Actualizar la app (cambios futuros)

```
GitHub → index.html → icono lápiz ✏️ → editar → Commit changes
```
GitHub Pages redespliega automáticamente en ~2 minutos.

---

### Firebase — no hay que tocar nada

Firebase no necesita reconfigurarse al cambiar de repositorio. Las credenciales están dentro del `index.html` y funcionan desde cualquier dominio.

---

## 9. Limitaciones conocidas

| Limitación | Detalle |
|---|---|
| **Sin autenticación** | Cualquier persona con la URL puede acceder y publicar. Adecuado para grupo de confianza. |
| **Inventario base hardcoded** | El inventario predefinido vive en el JS. Si se actualiza el `index.html`, se resetea. Los objetos añadidos por las usuarias sí persisten en Firebase. |
| **Sin notificaciones push** | No hay alertas de nuevas publicaciones. Hay que abrir la app para ver actualizaciones. |
| **Límite de imágenes** | Firestore permite 1MB por documento. Las fotos se comprimen automáticamente pero imágenes muy grandes podrían fallar. |
| **Plan gratuito Firebase** | Límites: 50.000 lecturas/día, 20.000 escrituras/día. Para un grupo pequeño no hay riesgo. |
| **Sin modo offline** | Sin conexión a Internet la app no funciona ni carga datos previos. |

---

## 10. Posibles mejoras futuras

- [ ] Autenticación con Google para mayor seguridad
- [ ] Notificaciones push al publicar algo nuevo
- [ ] Rol administrador para Esther (fijar estados del inventario, moderar)
- [ ] Exportar resumen PDF con decisiones tomadas
- [ ] Plano del piso con imagen real del arquitecto
- [ ] Añadir más habitaciones (terraza, trastero)
- [ ] Sistema de etiquetas en publicaciones (inspiración / presupuesto / urgente)
- [ ] Historial de cambios en el inventario
- [ ] Modo oscuro

---

## 11. Glosario técnico

| Término | Definición |
|---|---|
| **SPA** | Single Page Application — aplicación de una sola página que no recarga el navegador al navegar entre secciones. |
| **Firebase** | Plataforma de Google para apps. Proporciona base de datos, autenticación, hosting y más. |
| **Firestore** | Base de datos NoSQL de Firebase, organizada en colecciones y documentos JSON, con soporte de tiempo real. |
| **onSnapshot** | Función de Firestore que escucha cambios en la base de datos y actualiza la UI automáticamente sin recargar. |
| **base64** | Formato de codificación que convierte archivos binarios (imágenes) en texto, permitiendo guardarlos en JSON/Firestore. |
| **GitHub Pages** | Servicio gratuito de GitHub para hospedar sitios web estáticos directamente desde un repositorio. |
| **CDN** | Content Delivery Network — red de servidores distribuidos que sirve librerías (como Firebase SDK) de forma rápida y global. |
| **Vanilla JS** | JavaScript puro, sin frameworks adicionales como React, Vue o Angular. |
| **Responsive** | Diseño que se adapta a cualquier tamaño de pantalla (móvil, tablet, escritorio). |
| **aspect-ratio** | Propiedad CSS que mantiene una relación fija entre ancho y alto. `1/1` produce cuadrados perfectos. |
| **Canvas API** | API del navegador para dibujar gráficos y manipular imágenes. Se usa aquí para comprimir fotos antes de subirlas. |
| **DataURL** | Representación de un archivo como URL de texto (ej: `data:image/jpeg;base64,...`). Permite incrustar imágenes en JSON. |

---

<div align="center">

**Casa Madridejos** · Documentación generada con Claude (Anthropic) · 2026

*Una app hecha con cariño para que salga un piso precioso* 🏠

</div>
