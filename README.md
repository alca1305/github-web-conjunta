# Arigastell MH — sitio estático (HTML5 + CSS)

Web multipágina para **Arigastell MH**: información de salidas, fichas por montaña, publicaciones, contacto con formulario y ubicación. No usa frameworks ni JavaScript en el flujo principal; todo el comportamiento visual viene de **HTML semántico** y **`style.css`**.

## Estructura de archivos

| Elemento | Función |
|----------|---------|
| `index.html` | Portada: bienvenida (vídeo), próximas salidas (tarjetas), quiénes somos (imagen local). |
| `contacto.html` | Formulario de reserva, horario, mapa embebido, equipo con tarjetas circulares. |
| `publicaciones.html` | Listado de publicaciones (según el contenido actual del proyecto). |
| `montaña-*.html` | Una página por cima con texto y foto en `img/`. |
| `style.css` | Estilos globales: layout, navegación, responsive, animaciones de tarjetas, formulario, mapa. |
| `img/` | Fotografías JPG de montañas, imagen `bienvenidos a la aventura.jpg`, vídeo(s) MP4 usados en portada. |

Abrir cualquier `.html` en el navegador (doble clic o “Abrir con”) funciona con rutas relativas; no hace falta servidor para navegar el sitio en local.

## Cómo está montada la página

1. **`#contenedor`** envuelve cabecera, menú, contenido y pie con flex en columna (`min-height: 100vh`).
2. **`header.main-header`** es el banner superior compartido (título + subtítulo) con imagen de fondo y capas de degradado para legibilidad.
3. **`nav`** es **sticky** (`top: 0`) para que el menú permanezca visible al hacer scroll.
4. **`main.single-main`** centra el contenido (`max-width` ~1150px en escritorio, algo mayor por encima de 1300px) con márgenes laterales.

Las secciones del cuerpo usan la clase del body **`site-ce`**: fondo del documento con gradientes + foto de cordillera (`background-attachment: fixed` en escritorio para efecto parallax suave).

## Navegación y enlaces entre páginas

- Enlaces directos a cada ficha: **`montaña-<nombre>.html`** (por ejemplo `montaña-aneto.html`).
- **“Solicitud especial”** en cada submenú enlaza a **`contacto.html`** con fragmento **`#mountain-form`** y parámetros de consulta **`area`** y **`montana`** para orientar el mensaje (el formulario es HTML estático; los valores en la URL sirven como referencia visual si se copian o enlazan).
- **`PUBLICACIONES`** → `publicaciones.html`.
- **`CONTACTO · UBICACIÓN`** → `contacto.html`.

---

## Menús multinivel (estructura HTML + CSS)

El menú no usa JavaScript: el despliegue lo controla **`style.css`** sobre listas anidadas **`<ul>` → `<li>` → `<ul>` → …**.

### Cómo está montado en el HTML

- **Primer nivel:** `HOME`, `ÁREAS DE ACTUACIÓN` (etiqueta **`span.nav-dropdown-label`**, no es enlace: solo abre el bloque), `PUBLICACIONES`, `CONTACTO`.
- **Segundo nivel:** cordilleras (Pirineos, Picos de Europa, …) con **`span.nav-submenu-parent`** cuando el ítem no lleva a una página única, sino que abre otro submenú.
- **Niveles inferiores:** enlaces **`<a href="montaña-….html">`** a cada montaña y el enlace de **Solicitud especial**.

Así se separa lo que “solo agrupa” (**`span`**) de lo que “navega” (**`a`**).

### Cómo lo muestra el CSS

1. **Ocultar submenús por defecto:** los **`ul`** anidados llevan **`display: none`**. Los **`li`** del menú tienen **`position: relative`** para que los submenús con **`position: absolute`** se posicionen debajo o al lado del ítem padre.
2. **Mostrar al pasar el ratón:** reglas del tipo **`nav ul li:hover > ul`** — cuando el cursor está sobre el **`li`**, el **`ul` hijo directo** pasa a **`display: flex`** (columna de enlaces).
3. **Mostrar con teclado (accesibilidad):** además de **`:hover`**, se usa **`:focus-within`** en el mismo **`li`** (por ejemplo **`nav ul li:focus-within > ul`**). Si entras con **Tab** en un enlace dentro del submenú, el foco “permanece” dentro del **`li`** y la regla sigue cumpliéndose: el menú **no se cierra** al dejar de hacer hover, lo cual evita que quien navega con teclado pierda el panel.
4. **Segundo y tercer nivel a la derecha:** en escritorio, los submenús profundos usan **`left: calc(100% - 1px)`** y **`top: 0`** para pegarse al borde derecho del menú padre (efecto “acordeón lateral”). Los **`z-index`** (1000, 1001, 1002) evitan que un panel tape mal a otro.
5. **Móvil (`max-width: 768px`):** los **`ul` anidados** pasan a **`position: static`**, **`width: 100%`**, sin sombras flotantes. El menú principal **`nav > ul`** pasa a **columna**: todo se **apila** en vertical en lugar de solaparse en capas; es más usable en pantalla estrecha.

En resumen: **`:hover`** para ratón, **`:focus-within`** para foco/teclado, **`display` none/flex** para abrir y cerrar, **`position`** distinto en escritorio vs móvil.

## Home: bienvenida, vídeo e imagen “Quiénes somos”

- En **“Bienvenidos a la aventura”**, la franja visual es un **`<video>`** local (`img/…mp4`) con **`autoplay`**, **`muted`**, **`playsinline`** y **`loop`**.  
  - Sin sonido (`muted`), los navegadores permiten la reproducción automática sin interacción previa.  
  - `playsinline` evita que en iOS el vídeo salte a pantalla completa por defecto.  
  - `loop` mantiene continuidad cuando el clip termina.
- En **“Quiénes somos”**, la imagen es **`img/bienvenidos a la aventura.jpg`**, con `width`/`height` acordes al archivo para ayudar al layout.

Los estilos de ese vídeo coinciden con las imágenes de artículo (`width: 100%`, altura fija en px según breakpoint, `object-fit: cover`, bordes redondeados y sombra) mediante el selector **`.home-bienvenida article video`** en `style.css`.

## Mapa (Google Maps embebido)

En **`contacto.html`**, bloque **“Oficinas y horario”**, el mapa es un **`<iframe>`** apuntando a Google Maps en modo embed:

- Coordenadas en la URL de ejemplo: **`40.373117,-3.918837`** con **`z=15`** y **`output=embed`**.
- Atributos útiles: **`title`** (accesibilidad), **`loading="lazy"`**, **`referrerpolicy="no-referrer-when-downgrade"`**, **`allowfullscreen`**.

El contenedor usa clases como **`map-frame`** / **`map-frame-hero`** en CSS para ratio, bordes y sombra coherentes con el resto del diseño.

## Flip 3D en las tarjetas (CSS, sin librerías)

“Flip 3D” significa que hay **dos caras** (delantera y trasera) en el **mismo espacio**, como una carta: al girar el conjunto en el **eje Y** (vertical que va de arriba a abajo en pantalla), ves una cara u otra.

### Ideas clave de CSS 3D

1. **`perspective`** (en el contenedor padre, p. ej. **`.salida-item`**): define a qué “distancia” está el espectador. Sin perspectiva, la rotación se ve aplastada; con ella, el giro se percibe volumétrico.
2. **`transform-style: preserve-3d`** (en el hijo que rota, p. ej. **`.salida-flip-inner`**): indica que **los hijos** (las dos caras) siguen viviendo en **espacio 3D** cuando el padre rota. Si no se pone, el navegador puede “aplanar” y el efecto falla.
3. **Dos caras superpuestas:** ambas con **`position: absolute`** e **`inset: 0`** ocupan el mismo rectángulo (o círculo en el equipo).
4. **`backface-visibility: hidden`**: la cara que da **la espalda** a la cámara no se pinta. Así, cuando la frontal ha girado 180°, no se ve a través de ella la trasera mal superpuesta.
5. **La cara trasera ya viene “volteada”:** en **`.salida-back`** (y análogo en equipo) se aplica **`transform: rotateY(180deg)`**. En el reposo, el usuario ve la frontal (0°). Al rotar **todo el interior** 180°, la frontal mira hacia atrás (oculta) y la trasera queda de frente (visible).
6. **Animación suave:** **`transition: transform 0.62s ease`** (u otro tiempo) en el elemento que rota hace el giro gradual en lugar de un salto brusco.
7. **Disparadores:** en **Próximas salidas**, **`.salida-item:hover`** y **`.salida-item:focus-within`** aplican **`transform: rotateY(180deg)`** a **`.salida-flip-inner`**, igual que antes con el menú: ratón **y** teclado dentro de la tarjeta.

### Próximas salidas (home)

- Estructura: **`.salida-item`** → **`.salida-flip-inner`** → **`.salida-face.salida-front`** / **`.salida-face.salida-back`**.
- El reverso muestra el CTA “Ver … y reservar”.

### Equipo en contacto (círculos)

- Misma lógica 3D en **`.team-flip`** / **`.team-flip-inner`** / **`.team-flip-front`** / **`.team-flip-back`**, con **`border-radius: 50%`** para el disco.
- El giro por **`:hover`** está envuelto en una regla **`@media (hover: hover) and (pointer: fine)`** (ver siguiente sección): en **móvil táctil** no se asume un “hover” fiable; el usuario puede pulsar el enlace **“Sobre mí”** en la cara trasera sin depender de mantener el dedo “encima” como si fuera hover de escritorio.

## Formulario de contacto

Formulario HTML5 en **`contacto.html`** (`#mountain-form`): agrupación con **`<fieldset>`** / **`<legend>`**, tipos como `email`, `tel`, `date`, `textarea`, listas **`<select>`**, casillas **checkbox**/`radio` donde aplique, y botón **`type="submit"`**. Los estilos (rejilla, foco, botones) están en `style.css` bajo selectores de **`.contact-page`**, **`#mountain-form`** y afines.

## Reglas `@media` (responsive y tipo de dispositivo)

**`@media`** es la forma en CSS de decir: *“estas propiedades solo se aplican si se cumple una condición”*. El navegador evalúa el **ancho de la ventana**, la **orientación**, el **tipo de puntero**, etc., y activa o ignora el bloque.

### Condiciones habituales en este proyecto

| Forma | Significado |
|--------|-------------|
| **`@media (min-width: 1301px) { … }`** | Desde **1301px** de ancho hacia arriba (pantallas grandes). Útil para dar más aire: más `max-width` del contenido, más padding en secciones y nav. |
| **`@media (max-width: 1024px) { … }`** | Hasta **1024px** inclusive. Aquí suele “romperse” el layout en dos columnas del home o del contacto: los bloques pasan a ocupar **100%** del ancho y apilarse. |
| **`@media (max-width: 768px) { … }`** | Hasta **768px** (tablet pequeña / móvil). Cambios fuertes: menú en **columna**, submenús **sin posición flotante**, tipografías de cabecera más pequeñas, imágenes/vídeo de artículo con **menos altura**. |
| **`@media (max-width: 896px) and (orientation: landscape) { … }`** | Móvil o ventana **baja y ancha**. Se prioriza **no desperdiciar altura**: banner más bajo, textos del header más compactos, imágenes del home aún más bajas (~200px). |
| **`… and (orientation: portrait)`** / **`landscape`** | **`portrait`**: ventana más alta que ancha; **`landscape`**: más ancha que alta. Sirve para **refinar** dentro del mismo ancho (misma `max-width`) según cómo sostiene el usuario el dispositivo. |
| **Varias condiciones con `and`** | Ejemplo: **`@media (max-width: 1024px) and (min-width: 769px) and (orientation: portrait)`** = solo tablet “vertical” en ese rango de anchura. Así el CSS no pisa al móvil muy estrecho ni al escritorio ancho. |

### `@media` que no mide el ancho: hover y puntero

```css
@media (hover: hover) and (pointer: fine) {
    .team-flip:hover .team-flip-inner { transform: rotateY(180deg); }
}
```

- **`hover: hover`**: el dispositivo **sí** tiene un mecanismo de hover fiable (típico: ratón en portátil o PC).
- **`pointer: fine`**: el puntero es **preciso** (de nuevo, ratón o trackpad), no un dedo grueso en pantalla.

Si **no** se cumple (muchos móviles), ese bloque **no aplica**: no se fuerza el flip del equipo solo por “hover fantasma”. El flip de **Próximas salidas** sí usa **`:hover` + `:focus-within`** sin esta media query (decisión de diseño distinta para rectángulos más grandes).

### Resumen de anchuras en `style.css`

- **Por defecto** (sin `@media` o fuera de los bloques): reglas base para escritorio corriente.
- **≥ 1301px:** más espacio en `main`, `nav` y secciones.
- **≤ 1024px:** home en columna, contacto en columna, equipo con más flex.
- **≤ 768px:** menú apilado y submenús estáticos; imágenes/vídeo de artículo ~270px de alto.
- **≤ 896px + landscape:** cabecera e imágenes más compactas.

Variables en **`:root`** (`--primary`, `--secondary`, fuentes, etc.) son **globales**: no dependen de `@media`, pero las reglas dentro de cada `@media` pueden **sobrescribir** tamaños o márgenes usando esas mismas variables para mantener la coherencia visual.

## Fichas de montaña y fotos

Cada **`montaña-*.html`** incluye una figura con imagen en **`img/`** y clase **`peak-photo-img`** para que no entren en la regla genérica que fuerza `height` fijo en otras imágenes de artículo; así se respeta **`object-fit: contain`** y el tamaño declarado en **`width`** / **`height`** del archivo.

## Créditos / medios externos

Algunos fondos o cabeceras pueden usar imágenes de **Wikimedia Commons** (URLs en `style.css` o en HTML). La foto del campus en contacto enlaza a Wikimedia con atributo `title` de atribución.

---

Proyecto pensado para **entrega académica o portfolio**: claridad de estructura, accesibilidad básica (`lang`, `title` en iframes, foco en menús, `aria-label` donde ayuda) y mantenimiento solo con HTML/CSS.
