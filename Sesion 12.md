# GUÍA DEL ESTUDIANTE | SESIÓN 12
## Introducción a Next.js y optimización de React
**Elaborado por el docente · Lideratec Academy · Programación Web Avanzada**

**Proyecto integrador:** catálogo académico con Next.js (App Router), rutas dinámicas y comparación SSR/SSG/ISR.

**Resultados de aprendizaje:** crear un proyecto local, reconocer componentes y navegación, comparar estrategias de renderizado, crear rutas dinámicas y preparar una compilación de producción. Tiempo sugerido: 120 minutos para la práctica dirigida; las tareas complementarias pueden realizarse después de clase. El despliegue remoto se explica como extensión, no requiere cuenta para aprobar la práctica.

## Objetivo de la sesión
**Objetivo general:** desarrollar y ejecutar una aplicación web sencilla con **Next.js y React**, aprendiendo cómo se organizan sus archivos, cómo se reutilizan componentes y cómo se accede a páginas con diferentes estrategias de renderizado. Al finalizar, el estudiante debe poder **explicar y demostrar** la diferencia entre SSG, SSR e ISR, así como crear enlaces hacia detalles de productos utilizando rutas dinámicas.

### ¿Qué vamos a aprender y cómo sabremos que lo logramos?
1. **Reconocer las herramientas:** explicar qué son Node.js, npm, React, Next.js, TypeScript, TSX y App Router; verificar su instalación.
2. **Construir una web organizada:** identificar qué función cumplen `app/`, `lib/` y `components/`; mostrar tres productos en una página.
3. **Navegar con `Link`:** abrir otras páginas de la aplicación y explicar por qué `href` indica una ruta.
4. **Comparar SSG, SSR e ISR:** señalar qué contenido se prepara con antelación, qué cambia por solicitud y qué puede regenerarse periódicamente.
5. **Usar rutas dinámicas:** abrir `/productos/1` y `/blog/bienvenida` empleando una plantilla de página para varios registros.
6. **Probar y entregar:** ejecutar `npm run dev`, `npm run build` y `npm run start`, identificar los resultados y documentar las evidencias.

**Producto final verificable:** un catálogo navegable con portada, páginas `/ssg`, `/ssr`, `/isr`, detalles de productos y artículos; compilación de producción correcta.

**Dinámica de cada ejemplo:** primero comprenderemos el propósito, luego crearemos el archivo indicado, escribiremos el código, explicaremos sus instrucciones y finalmente ejecutaremos una comprobación. No avances al siguiente ejemplo si la prueba actual falla.

## 1. Conceptos desde cero
**React:** biblioteca con componentes de interfaz. **Next.js:** framework sobre React que agrega estructura de rutas, servidor y opciones de renderizado. **Framework:** conjunto de convenciones y herramientas que organiza la aplicación. **TypeScript:** JavaScript con comprobación de tipos. **TSX:** archivo de TypeScript que contiene JSX; JSX permite expresar etiquetas visuales dentro del código. **App Router:** enrutamiento basado en carpetas dentro de `app/`. **Componente:** función que devuelve elementos de interfaz. **Prop:** dato que recibe un componente. **Link:** componente de Next.js para navegar internamente sin recargar por completo el documento.

**SSR:** HTML generado al llegar cada solicitud. **SSG:** HTML generado previamente durante `build`. **ISR:** HTML estático que puede regenerarse transcurrido un periodo; no equivale a actualización exacta cada N segundos. **Ruta dinámica:** una plantilla cuyo segmento variable se escribe entre corchetes, por ejemplo `app/productos/[id]/page.tsx`. **Slug:** identificador legible de URL como `bienvenida`. **Build:** compilación y preparación del proyecto para producción.

## 2. Antes de empezar: herramientas y comprobación
Instalar Node.js desde el sitio oficial `https://nodejs.org/` (se recomienda una edición LTS compatible con Next.js 15; Node 20 o posterior). Instalar Visual Studio Code desde `https://code.visualstudio.com/`. No se requiere MongoDB, API externa ni cuenta Vercel. Abre una terminal y escribe:

```bash
node -v
npm -v
```

**Qué hace:** `node -v` muestra la versión del entorno de ejecución; `npm -v` la del administrador de paquetes. **Resultado esperado:** dos números de versión. **Error habitual:** «comando no encontrado»; instalar Node y reiniciar la terminal.

## 3. EJ01 · Crear e iniciar Next.js (15 minutos)
En una carpeta para tus proyectos ejecuta:

```bash
npx create-next-app@15 next-catalogo
```

`npx` ejecuta el generador; `create-next-app@15` fija la familia 15 del generador; `next-catalogo` es la carpeta del proyecto. El asistente puede variar: elige **TypeScript: Sí**, **ESLint: Sí**, **Tailwind: No**, **src/: No**, **App Router: Sí**, **alias: por defecto**. Si el programa plantea preguntas adicionales, conserva valores predeterminados. Entra al proyecto y prueba:

```bash
cd next-catalogo
npm run dev
```

`cd` cambia de carpeta; `npm run dev` ejecuta el script `dev` del `package.json` y levanta el servidor de desarrollo. Abre `http://localhost:3000`. **Comprobación:** aparece la página inicial. Detén con Ctrl+C. **Error:** si el puerto 3000 está ocupado, Next.js puede ofrecer 3001: utiliza el puerto anunciado.

**Importante:** para reproducir exactamente los archivos del laboratorio, puedes optar por la variante descargable de F15, cuya dependencia Next.js está fijada a `15.5.0`. No combines directorios ni copies `package.json` encima de una instalación en marcha; la guía construye el mismo resultado gradualmente.

### Comprendamos EJ01, instrucción por instrucción
**¿Qué estamos haciendo?** Preparando el ambiente que ejecutará nuestra aplicación. Node.js permite ejecutar herramientas JavaScript fuera del navegador; npm instala dependencias; `npx` ejecuta el asistente que genera el proyecto.

- `npx create-next-app@15 next-catalogo`: crea una carpeta nueva con archivos iniciales, como `package.json` (scripts y dependencias), `app/page.tsx` (la portada) y `app/layout.tsx` (la estructura compartida).
- **TypeScript: Sí:** permite detectar errores de tipos; no cambia el resultado visual de la página.
- **App Router: Sí:** Next.js crea rutas a partir de carpetas dentro de `app/`.
- `npm run dev`: inicia el servidor de desarrollo y observa cambios guardados; la URL debe ser la indicada por la terminal.
- **Antes de ejecutar, predice:** ¿en qué archivo buscarías el contenido de `/`? **Respuesta que debes comprobar:** `app/page.tsx`.
- **Mini práctica:** abre `app/page.tsx`, cambia un texto visible, guarda y observa el navegador. Deshaz el cambio antes de EJ02.
- **Si falla:** `ENOENT` suele significar que no estás en la carpeta con `package.json`; `node` no reconocido indica instalación incompleta; un puerto ocupado obliga a usar el puerto anunciado.


### EJ01 — Antes de ejecutar: ¿qué instala cada herramienta y para qué sirve?

No todos los comandos instalan algo. Debemos diferenciar **instalar un programa**, **crear un proyecto**, **instalar paquetes** y **ejecutar una aplicación**.

|Elemento|¿Qué es?|¿Por qué lo necesitamos?|¿Qué se instala o crea?|
|---|---|---|---|
|Node.js|Entorno que ejecuta JavaScript fuera del navegador.|Permite ejecutar el servidor de desarrollo y las herramientas de Next.js.|Se instala una sola vez en el equipo.| 
|npm|Administrador de paquetes que viene con Node.js.|Descarga las dependencias del proyecto y ejecuta los scripts.|Viene normalmente instalado junto con Node.js.| 
|npx|Ejecutor de herramientas de npm.|Permite iniciar un generador sin instalarlo globalmente.|Puede descargar temporalmente `create-next-app`.| 
|create-next-app|Asistente oficial para iniciar proyectos Next.js.|Ahorra la creación manual de decenas de archivos iniciales.|Genera una nueva carpeta y su estructura.| 
|Next.js|Framework de React con rutas, servidor y opciones de renderizado.|Es el núcleo de la aplicación que construiremos.|Se agrega como dependencia local.| 
|React y React DOM|Bibliotecas para construir y representar componentes.|Next.js las utiliza para mostrar la interfaz.|Se agregan como dependencias locales.| 
|TypeScript|JavaScript con comprobación de tipos.|Nos ayuda a detectar errores de datos y propiedades.|El generador prepara sus archivos y dependencias.| 
|ESLint|Herramienta de análisis de código.|Detecta patrones problemáticos y fomenta buenas prácticas.|El generador configura el proyecto.| 
|Visual Studio Code|Editor de código.|Nos permite organizar carpetas y editar archivos.|Se instala por separado; no es obligatorio si dispones de otro editor.| 

**Importante:** instalar Node.js no instala tu aplicación; `npm run dev` tampoco instala Next.js: únicamente ejecuta una herramienta ya declarada en el proyecto.

### EJ01 — Diseccionemos el comando, símbolo por símbolo

```bash
npx create-next-app@15 next-catalogo
```

- `npx`: busca y ejecuta un programa distribuido mediante npm. Es el *ejecutor*, no el nombre del proyecto.
- `create-next-app`: nombre del asistente oficial que prepara la estructura inicial.
- `@15`: solicita la **versión principal 15 del generador**, no un nombre de carpeta. Se usa para mantener el laboratorio dentro de la familia Next.js 15. La versión exacta de las dependencias generadas puede variar según la publicación disponible dentro de esa familia.
- `next-catalogo`: nombre de la **carpeta nueva** donde estarán los archivos. Puedes cambiar el nombre, pero para seguir el laboratorio sin confusiones mantenlo igual.
- Los espacios separan el ejecutor, el paquete ejecutado y el argumento de carpeta.

**¿Qué ocurrirá al presionar Enter?** Si `npx` necesita descargar el generador puede pedir autorización; después aparecerán preguntas de configuración. El generador creará una carpeta `next-catalogo`, escribirá archivos como `package.json`, instalará dependencias y preparará los scripts `dev`, `build` y `start`. Necesitas conexión a internet en esta creación inicial.

**Decisiones de configuración y razón de cada una:**

|Pregunta habitual|Elegir|Motivo de aprendizaje|
|---|---|---|
|Would you like to use TypeScript?|Yes|Usaremos archivos `.ts` y `.tsx` con tipos.| 
|Would you like to use ESLint?|Yes|Mantiene comprobaciones de calidad.| 
|Would you like to use Tailwind CSS?|No|Usaremos CSS simple para evitar añadir otra tecnología.| 
|Would you like your code inside a `src/` directory?|No|La carpeta `app/` quedará directamente en la raíz, igual que en los ejemplos.| 
|Would you like to use App Router?|Yes|Las rutas se crearán a través de carpetas `app/`.| 
|Would you like to customize the import alias?|No|Mantendremos las importaciones relativas presentes en el laboratorio.| 

El texto exacto y orden de las preguntas dependen de la versión del generador. **Si el asistente ofrece un modo de ejecución o compilador adicional**, usa la opción predeterminada. No mezcles la estructura `src/app` con `app`, pues cambiarían las rutas de los archivos indicados en la guía.

### EJ01 — ¿Qué archivos acaba de crear?

- `package.json`: manifiesto del proyecto; registra dependencias y scripts. **No es una página web.**
- `node_modules/`: carpeta que npm llena con los paquetes instalados. **No la debes crear manualmente ni entregar**.
- `package-lock.json`: registra versiones resueltas para reproducir una instalación.
- `app/page.tsx`: muestra la página principal `/`. `page` es el nombre reservado de Next.js.
- `app/layout.tsx`: estructura HTML compartida que envuelve las páginas.
- `app/globals.css`: estilos generales.
- `tsconfig.json`: configuración del verificador TypeScript.
- `next.config.ts` (si el generador lo crea): configuración adicional de Next.js.

### EJ01 — Entendamos los comandos siguientes

```bash
cd next-catalogo
npm run dev
```

1. `cd` significa *change directory*: entra a la carpeta nueva. Debes estar dentro de ella para que npm encuentre `package.json`.
2. `npm` lee ese archivo; `run` indica que ejecutará un script; `dev` es el nombre del script. En este laboratorio corresponde a `next dev`.
3. La terminal mostrará la dirección local, por ejemplo `http://localhost:3000`. **localhost es tu propia computadora**, no una publicación en internet.
4. Mantén esa terminal abierta; ve al navegador y abre la dirección que aparezca. Cambia un título en `app/page.tsx`, guarda y comprueba que el navegador actualiza la vista.
5. `Ctrl+C` detiene el servidor; no borra el proyecto ni desinstala paquetes.

**Prueba de comprensión:** ¿qué pasa si ejecutas `npm run dev` desde la carpeta superior? Lo normal es un error porque npm no encontrará el `package.json` esperado. ¿Qué pasa si cierras la terminal del servidor? La aplicación local dejará de responder.

**Si aparece una pregunta de confirmación de `npx`:** confirma únicamente que estás ejecutando el paquete oficial `create-next-app`; no copies ni compartas claves de acceso. **Si aparece `EACCES` o `EPERM`:** evita forzar instalaciones globales; comprueba permisos de tu carpeta de proyectos. **Si aparece un error de red:** comprueba conexión e inténtalo de nuevo.

**Correspondencia con FASE_15:** el ZIP ya contiene un `package.json` con dependencias fijadas. Para usar ese proyecto ya construido, descomprime, entra a `next-catalogo`, ejecuta `npm install` y luego `npm run dev`. `npm install` lee `package.json` y descarga los paquetes; no es necesario volver a ejecutar `create-next-app` sobre esa misma carpeta.

## 4. Estructura mínima del proyecto
Trabajaremos sobre la raíz `next-catalogo`, es decir, la carpeta que contiene `package.json`. Cada encabezado siguiente indica **ruta exacta y contenido completo**. En VS Code: Archivo → Abrir carpeta → `next-catalogo`. Crea las carpetas desde el explorador lateral cuando no existan; los nombres entre corchetes son literales.

```text
next-catalogo/
├── app/
│   ├── layout.tsx
│   ├── globals.css
│   ├── page.tsx
│   ├── ssg/page.tsx
│   ├── ssr/page.tsx
│   ├── isr/page.tsx
│   ├── productos/[id]/page.tsx
│   └── blog/[slug]/page.tsx
├── components/ProductoCard.tsx
├── lib/datos.ts
├── package.json
├── tsconfig.json
└── next.config.ts
```

**Regla de trabajo:** no crees dos carpetas `app`. Si ya existen `app/page.tsx` o `app/layout.tsx`, reemplaza su contenido. El código de abajo coincide con la versión docente de F15.


## EJ02 · Datos, componente y portada

Crea `lib` y `components` desde VS Code. `datos.ts` contiene arrays locales; `ProductoCard` recibe props tipadas; `layout.tsx` agrega navegación global; `page.tsx` representa `/`. El archivo CSS aporta estilos sin instalar librerías adicionales.

### Archivo: `lib/datos.ts` — escribe exactamente lo siguiente

```ts
// Datos locales: no se necesita API ni base de datos externa.
export const productos = [
  { id: '1', nombre: 'Teclado mecánico', precio: 160 },
  { id: '2', nombre: 'Mouse inalámbrico', precio: 85 },
  { id: '3', nombre: 'Monitor 24 pulgadas', precio: 650 }
];
export const posts = [
  { slug: 'bienvenida', titulo: 'Bienvenida a Next.js', cuerpo: 'Una ruta para cada artículo.' },
  { slug: 'renderizado', titulo: 'SSR, SSG e ISR', cuerpo: 'Elegimos según la frecuencia de cambio.' }
];
```

**Lectura guiada de `lib/datos.ts`**

1. `// Datos locales...` es un comentario: explica la intención y no se ejecuta.
2. `export` hace que otros archivos puedan importar la variable. Sin `export`, el archivo `app/page.tsx` no podría usarla de esta forma.
3. `const productos = [...]` crea un arreglo; `const` impide reasignar la variable, pero no significa que su contenido sea una base de datos.
4. Cada elemento `{ id, nombre, precio }` es un objeto. `id` es texto (`'1'`), `nombre` es texto y `precio` es número (`160`, sin comillas). Esta distinción importa al comparar identificadores.
5. `posts` es otro arreglo; `slug` es la parte legible de la URL, por ejemplo `bienvenida`.
6. **Prueba breve:** cambia temporalmente `nombre: 'Teclado mecánico'` por `nombre: 'Teclado de prueba'`, guarda y observa la portada después de crearla. Devuelve el nombre original.
7. **Fallo típico:** falta de coma entre objetos o uso de comillas curvas; TypeScript señalará la línea donde el arreglo deja de ser válido.



### Archivo: `components/ProductoCard.tsx` — escribe exactamente lo siguiente

```tsx
import Link from 'next/link';
export default function ProductoCard({ id, nombre, precio }: { id: string; nombre: string; precio: number }) {
  return <div className="card"><h2>{nombre}</h2><p>Precio: S/ {precio}</p>
    <Link href={`/productos/${id}`}>Ver detalle</Link></div>;
}
```

**Lectura guiada de `components/ProductoCard.tsx`**

1. `import Link from 'next/link'`: incorpora el componente que genera un enlace a otra ruta interna de Next.js.
2. `export default function ProductoCard(...)`: declara y exporta el componente; `default` permite importarlo sin llaves.
3. `{ id, nombre, precio }`: extrae tres valores del objeto de propiedades (**desestructuración**).
4. `: { id: string; nombre: string; precio: number }`: declara qué tipos acepta el componente; un precio como `'ciento'` no sería válido.
5. `return <div className="card">...`: devuelve interfaz en JSX. En React se usa `className` para conectar con la regla `.card` del CSS.
6. `{nombre}` y `{precio}` muestran valores; las llaves dentro de JSX permiten insertar expresiones JavaScript.
7. ``href={`/productos/${id}`}``: es una plantilla de texto; para `id='3'` genera `/productos/3`. Los acentos graves `` ` `` son importantes; no son comillas simples.
8. **Predice y verifica:** al recibir el objeto del monitor, ¿qué precio muestra? `S/ 650`. Antes de EJ06 el enlace todavía puede responder 404; no es un error del componente.



### Archivo: `app/globals.css` — escribe exactamente lo siguiente

```css
* { box-sizing: border-box; }
body { font-family: Arial, sans-serif; margin: 0; background: #f2f5fa; color: #152238; }
header { background: #172b4d; color: white; padding: 22px; }
header a { color: #c2f5ea; margin-right: 16px; }
main { max-width: 900px; padding: 24px; margin: auto; }
.card { padding: 16px; margin: 14px 0; background: white; border: 1px solid #d8e1ec; border-radius: 9px; }
a { color: #0f5c94; } code { background: #e4e9ee; padding: 3px; }
```

**Lectura guiada de `app/globals.css`**

- `* { box-sizing: border-box }`: incluye relleno y borde dentro de las dimensiones CSS declaradas.
- `body`: da fuente, margen, color de fondo y color del texto a toda la página.
- `header` y `header a`: distinguen la navegación del resto de la pantalla.
- `main`: limita el ancho del contenido y crea márgenes horizontales automáticos.
- `.card`: define fondo, borde, relleno y esquinas de cada tarjeta; se activa mediante `className="card"`.
- **Verificación:** guarda el CSS y revisa si las tarjetas tienen bordes y la cabecera es oscura. Si no cambia nada, comprueba que `app/layout.tsx` importe `./globals.css`.



### Archivo: `app/layout.tsx` — escribe exactamente lo siguiente

```tsx
import './globals.css';
import Link from 'next/link';
import type { ReactNode } from 'react';

export const metadata = { title: 'Catálogo Next.js | Sesión 12', description: 'Laboratorio de rutas y renderizado' };
export default function RootLayout({ children }: { children: ReactNode }) {
  return <html lang="es"><body>
    <header><strong>Lideratec Academy · Catálogo Next.js</strong><nav>
      <Link href="/">Inicio</Link><Link href="/ssg">SSG</Link>
      <Link href="/ssr">SSR</Link><Link href="/isr">ISR</Link>
    </nav></header>{children}
  </body></html>;
}
```

**Lectura guiada de `app/layout.tsx`**

1. `import './globals.css'`: aplica el CSS general a las páginas que comparten el layout.
2. `import Link ...`: reutiliza el componente de navegación, sin depender de URLs externas.
3. `import type { ReactNode }`: importa un tipo que admite elementos de React (no añade un elemento visual).
4. `metadata`: aporta título y descripción de la página para el navegador y los metadatos.
5. `RootLayout({ children })`: recibe el contenido de cada ruta. En la anotación `{ children: ReactNode }` se comprueba su tipo.
6. `<html lang="es">` y `<body>`: definen la estructura HTML principal.
7. `<header> ... <nav>`: permanece visible al visitar `/`, `/ssg` o `/ssr`.
8. `{children}`: **es imprescindible**; Next.js coloca allí la página correspondiente. Sin él, podrías ver la cabecera, pero no el contenido de la ruta.
9. **Prueba:** cambia de ruta mediante el menú: la cabecera se conserva, pero el contenido central cambia.



### Archivo: `app/page.tsx` — escribe exactamente lo siguiente

```tsx
import Link from 'next/link';
import ProductoCard from '../components/ProductoCard';
import { productos, posts } from '../lib/datos';
export default function Home() {
  return <main><h1>Catálogo académico</h1><p>Proyecto integrador con App Router.</p>
    <h2>Productos</h2>{productos.map(p => <ProductoCard key={p.id} {...p} />)}
    <h2>Artículos</h2>{posts.map(p => <p key={p.slug}><Link href={`/blog/${p.slug}`}>{p.titulo}</Link></p>)}
  </main>;
}
```

**Lectura guiada de `app/page.tsx`**

1. Las instrucciones `import` traen el componente `ProductoCard`, los arreglos locales y `Link`.
2. `export default function Home()` define el componente principal de la ruta raíz `/`.
3. `<h1>` y `<h2>` expresan jerarquía visual: título principal y secciones.
4. `productos.map(p => ...)` recorre **todos** los elementos. Si hay tres productos, renderiza tres tarjetas.
5. `<ProductoCard key={p.id} {...p} />`: `key` identifica cada tarjeta dentro de la lista y `{...p}` distribuye las propiedades `id`, `nombre`, `precio` hacia el componente.
6. `posts.map(...)` crea un enlace por artículo. No reutiliza `ProductoCard` porque un artículo tiene otras propiedades.
7. ``href={`/blog/${p.slug}`}`` forma una URL con el campo `slug` de cada artículo.
8. **Prueba:** la portada debe tener tres tarjetas y dos artículos. Haz clic en uno; las páginas de detalle se completan en EJ06.
9. **Fallo típico:** escribir `productos.map(...)` sin llaves `{}` dentro de JSX provoca un error o muestra un contenido incorrecto.



**Ejecuta y comprueba:** en la raíz del proyecto, inicia `npm run dev` (si ya está activo, guarda los archivos y actualiza el navegador). 
Abre `/` y comprueba tres productos y dos enlaces de artículos. Si aparece «Cannot find module», verifica la ruta exacta y las mayúsculas del archivo.


### Comprendamos EJ02, archivo por archivo
**¿Qué estamos construyendo?** Una portada que toma información de un archivo de datos, genera tarjetas reutilizables y ofrece navegación. Es importante comprender **el recorrido del dato**: `lib/datos.ts` → `app/page.tsx` → `ProductoCard` → HTML visible.

- **`lib/datos.ts`**: `export const` permite importar los arreglos en otros archivos; cada objeto de `productos` tiene `id`, `nombre` y `precio`. `posts` contiene artículos con `slug` para sus URLs. No hay base de datos: son datos de práctica.
- **`components/ProductoCard.tsx`**: `import Link` trae navegación interna; `export default function` declara el componente; `{ id, nombre, precio }` recibe un objeto de propiedades (**props**). Los tipos `string` y `number` sirven para que TypeScript detecte valores incompatibles.
- **`href={\`/productos/${id}\`}`**: construye una URL según el identificador: si `id` es `2`, el enlace apunta a `/productos/2`. El enlace podrá abrirse cuando terminemos EJ06.
- **`app/globals.css`**: las reglas CSS aplican colores, espacios y tarjetas. `className="card"` conecta el JSX con `.card` del CSS.
- **`app/layout.tsx`**: importa el CSS una sola vez, declara metadatos y coloca una cabecera compartida. `{children}` es el lugar donde Next.js inserta cada página visitada; sin este espacio, la página no se mostraría.
- **`app/page.tsx`**: `import` reutiliza datos y componentes. `productos.map(...)` recorre el arreglo y produce una tarjeta por producto; `key={p.id}` identifica cada elemento repetido para React; `{...p}` pasa las propiedades del objeto al componente. `posts.map(...)` produce enlaces para los artículos.

**Antes de ejecutar, predice:** ¿cuántas tarjetas se verán? **Tres**, porque `productos` tiene tres registros. ¿Cuántos enlaces de blog? **Dos**.

**Comprobación guiada:** inicia `npm run dev`, visita `/` y observa el encabezado, tres tarjetas y dos enlaces. Haz clic en un producto: antes de EJ06 puede aparecer 404, **es normal** porque todavía no creamos la ruta de detalle. Comprueba el texto `S/ 160` del primer producto. Si las tarjetas aparecen sin formato, revisa la importación `./globals.css`.

## EJ03 · SSG: HTML estable

SSG conviene a contenido estable. `force-static` especifica la intención de prerenderizado. Se observa de forma fiable con `npm run build`, no mediante suposiciones sobre el modo dev.

### Archivo: `app/ssg/page.tsx` — escribe exactamente lo siguiente

```tsx
export const dynamic = 'force-static';
export default function SSGPage() {
  return <main><h1>SSG: contenido estable</h1>
    <p>Esta página se prepara al construir el proyecto (npm run build).</p>
    <p>Ejemplo: información institucional que cambia poco.</p>
  </main>;
}
```

**Lectura guiada de `app/ssg/page.tsx`**

1. La carpeta `ssg` crea la ruta `/ssg` gracias a App Router; no necesitas añadir un archivo de rutas manual.
2. `export const dynamic = 'force-static'` solicita que la página se trate como estática para este laboratorio.
3. `SSGPage()` es el componente de página; el nombre de la función es libre, pero el archivo debe llamarse `page.tsx`.
4. El `return` describe texto **estable**, no lee datos de cada petición.
5. **Prueba:** abre `/ssg` mientras `npm run dev` está activo; después, en la verificación de producción, observa la clasificación de la ruta en `npm run build`.
6. **Advertencia:** que una marca temporal permanezca igual en desarrollo no demostraría por sí sola SSG; la comprobación relevante se hace en producción.



**Ejecuta y comprueba:** en la raíz del proyecto, inicia `npm run dev` (si ya está activo, guarda los archivos y actualiza el navegador). 
Abre `/ssg` y comprueba el título; la comprobación de prerenderizado se realizará con `npm run build`.


### Comprendamos EJ03: SSG sin confusiones
**Pregunta que resuelve:** si una página cambia muy poco, ¿es necesario calcularla en cada visita? **SSG (Static Site Generation)** permite preparar HTML de antemano, normalmente en la compilación de producción.

- `export const dynamic = 'force-static'`: solicita a Next.js tratar la página como estática en este laboratorio.
- `export default function SSGPage()`: es el componente que Next.js mostrará al visitar `/ssg`.
- `return <main>...</main>`: devuelve los elementos visuales; `<h1>` es el título y `<p>` los párrafos.
- **Antes de ejecutar, predice:** al abrir `/ssg` varias veces, el texto permanece igual. ¿Por qué? Porque no estamos calculando ningún dato nuevo por solicitud.
- **Verificación correcta:** en desarrollo confirma que existe la página; posteriormente ejecuta `npm run build` y observa el resultado de compilación. No confundas abrir una ruta en modo `dev` con demostrar su estrategia de producción.

## EJ04 · SSR: HTML por solicitud

SSR se usa cuando cada solicitud necesita contenido actualizado. `force-dynamic` hace explícita esa estrategia. `new Date()` genera un instante en el servidor.

### Archivo: `app/ssr/page.tsx` — escribe exactamente lo siguiente

```tsx
export const dynamic = 'force-dynamic';
export default function SSRPage() {
  const instante = new Date().toISOString();
  return <main><h1>SSR: renderizado por solicitud</h1>
    <p>Instante calculado en el servidor: <code>{instante}</code></p>
    <p>Recarga para observar una nueva respuesta del servidor.</p>
  </main>;
}
```

**Lectura guiada de `app/ssr/page.tsx`**

1. `export const dynamic = 'force-dynamic'` indica renderizado dinámico, de modo que el servidor procesa esta página por solicitud.
2. `const instante = ...`: crea una variable local para una sola representación de la página.
3. `new Date()` consulta el reloj del entorno que ejecuta la función; `.toISOString()` convierte la fecha a un texto estándar.
4. `{instante}` coloca esa cadena dentro del `<code>` de la página. Aquí `<code>` es una etiqueta HTML semántica, no una instrucción de ejecución.
5. **Prueba:** abre `/ssr`, espera unos segundos, recarga y comprueba que cambia el instante. En producción repite la comparación.
6. **No confundir:** SSR es una decisión de renderizado; no equivale a realizar una petición de API desde el navegador.



**Ejecuta y comprueba:** en la raíz del proyecto, inicia `npm run dev` (si ya está activo, guarda los archivos y actualiza el navegador). 
Abre `/ssr`; recarga y comprueba que el instante del servidor cambia. Si no cambia, revisa que `force-dynamic` esté fuera de la función.


### Comprendamos EJ04: SSR por solicitud
**Pregunta que resuelve:** ¿qué pasa si el servidor necesita generar contenido nuevo cada vez que alguien pide la página? **SSR (Server-Side Rendering)** permite renderizar al atender la solicitud.

- `export const dynamic = 'force-dynamic'`: fuerza renderizado dinámico por solicitud en este ejemplo.
- `new Date()`: crea una fecha/hora; `.toISOString()` la convierte en texto estandarizado, legible y comparable.
- `{instante}` dentro del JSX: inserta el valor calculado en el HTML que recibe el navegador.
- **Antes de ejecutar, predice:** ¿qué sucede si recargas `/ssr` unos segundos después? La marca temporal cambia porque el servidor ejecuta la página de nuevo.
- **Verificación:** abre `/ssr`, anota la marca temporal y recarga. Para una comprobación de producción ejecuta más tarde `npm run build` y `npm run start`.
- **Diferencia clave:** tanto SSG como SSR producen HTML desde el entorno de Next.js, pero **no** lo producen necesariamente en el mismo momento.

## EJ05 · ISR: regeneración

ISR admite contenido inicialmente estático y revalidación con intervalo. `revalidate = 60` significa **elegibilidad para revalidar** tras 60 segundos, no un temporizador de actualización exacto.

### Archivo: `app/isr/page.tsx` — escribe exactamente lo siguiente

```tsx
export const dynamic = 'force-static';
export const revalidate = 60;
export default function ISRPage() {
  const generado = new Date().toISOString();
  return <main><h1>ISR: contenido con revalidación</h1>
    <p>HTML generado: <code>{generado}</code></p>
    <p>La regeneración puede ocurrir después de 60 segundos, al recibir una solicitud.</p>
  </main>;
}
```

**Lectura guiada de `app/isr/page.tsx`**

1. La carpeta `isr` expone la ruta `/isr`.
2. `dynamic = 'force-static'` mantiene la ruta como candidata a servir HTML prerenderizado.
3. `revalidate = 60` fija el intervalo mínimo en **segundos** después del cual una nueva solicitud puede activar la revalidación. No es un `setInterval` y no garantiza que todos vean un cambio exactamente al segundo 60.
4. `generado` almacena la hora en que se produjo la representación, una pista para comparar versiones.
5. **Prueba:** ejecuta `npm run build`, después `npm run start`, anota la hora de `/isr`; tras más de 60 segundos realiza más visitas. Puede aparecer primero una versión anterior mientras se regenera.
6. **Error conceptual común:** probar ISR solo mediante `npm run dev` y afirmar que el caché productivo no funciona.



**Ejecuta y comprueba:** en la raíz del proyecto, inicia `npm run dev` (si ya está activo, guarda los archivos y actualiza el navegador). 
Abre `/isr`; verifica que no haya errores. La regeneración debe estudiarse con build/producción y tráfico posterior al intervalo, no mediante recargas de desarrollo.


### Comprendamos EJ05: ISR paso a paso
**Pregunta que resuelve:** ¿cómo servir contenido estático sin tener que reconstruir todo el sitio cada vez que cambia? **ISR (Incremental Static Regeneration)** permite que una página estática se vuelva a generar después de cumplir condiciones de revalidación.

- `dynamic = 'force-static'`: indica que esta ruta se produce de manera estática.
- `revalidate = 60`: establece un intervalo mínimo de 60 segundos antes de que la página pueda revalidarse; **no** significa que se actualizará sola, exactamente en el segundo 60.
- `const generado = new Date().toISOString()`: representa el instante en que se generó el contenido de la página, útil para observar versiones.
- **Predicción:** dos visitas muy cercanas normalmente muestran la misma marca; una solicitud posterior al intervalo puede iniciar la regeneración y una visita subsiguiente ver contenido nuevo.
- **Verificación adecuada:** detén `dev`, ejecuta `npm run build` y `npm run start`; visita `/isr`, anota el valor, espera más de 60 segundos y realiza nuevas solicitudes. La entrega de una página anterior mientras se regenera puede ser normal.
- **Comparación rápida:** SSG = generación anticipada; SSR = generación en cada petición; ISR = estática con oportunidades de regeneración.

## EJ06 · Dos rutas dinámicas

`[id]` y `[slug]` son segmentos variables. `generateStaticParams()` indica qué páginas se generan durante el build; `await params` es la forma moderna de leer los parámetros; `notFound()` maneja un dato no válido. `dynamicParams = false` evita generar rutas que no estén listadas.

### Archivo: `app/productos/[id]/page.tsx` — escribe exactamente lo siguiente

```tsx
import { notFound } from 'next/navigation';
import { productos } from '../../../lib/datos';
export const dynamicParams = false;
export function generateStaticParams() {
  return productos.map(producto => ({ id: producto.id }));
}
export default async function ProductoPage({ params }: { params: Promise<{ id: string }> }) {
  const { id } = await params;
  const producto = productos.find(item => item.id === id);
  if (!producto) notFound();
  return <main><h1>{producto.nombre}</h1><p>Código: {producto.id}</p>
    <p>Precio: S/ {producto.precio}</p></main>;
}
```

**Lectura guiada de `app/productos/[id]/page.tsx`**

1. La carpeta `[id]` significa que el segmento de URL es variable; sirve tanto para `/productos/1` como para `/productos/2`.
2. `notFound` es una función de Next.js que permite terminar con una respuesta de página inexistente.
3. `productos` importa el catálogo del archivo `lib/datos.ts` (los tres `../` suben tres niveles desde la carpeta `[id]`).
4. `dynamicParams = false` limita las URLs a los parámetros disponibles en la compilación.
5. `generateStaticParams()` recorre cada producto y devuelve objetos `{ id: '1' }`, `{ id: '2' }`, `{ id: '3' }`; Next.js usa esa lista para preparar las rutas.
6. `async` permite que la función espere valores asíncronos; `params: Promise<{ id: string }>` indica que Next.js 15 proporciona los parámetros como promesa.
7. `const { id } = await params` espera la promesa y extrae el identificador.
8. `productos.find(item => item.id === id)` busca **un** registro que coincida, no una lista; `===` compara sin conversiones automáticas de tipo.
9. `if (!producto) notFound()` corta el flujo si no se encontró un producto. Así evitamos acceder a `producto.nombre` cuando `producto` no existe.
10. **Prueba:** `/productos/1` muestra el teclado, `/productos/3` el monitor y `/productos/999` debe mostrar 404.



### Archivo: `app/blog/[slug]/page.tsx` — escribe exactamente lo siguiente

```tsx
import { notFound } from 'next/navigation';
import { posts } from '../../../lib/datos';
export const dynamicParams = false;
export function generateStaticParams() {
  return posts.map(post => ({ slug: post.slug }));
}
export default async function BlogPage({ params }: { params: Promise<{ slug: string }> }) {
  const { slug } = await params;
  const post = posts.find(item => item.slug === slug);
  if (!post) notFound();
  return <main><h1>{post.titulo}</h1><p>{post.cuerpo}</p>
    <p>Slug: <code>{slug}</code></p></main>;
}
```

**Lectura guiada de `app/blog/[slug]/page.tsx`**

1. `[slug]` es equivalente a `[id]` pero la URL usa palabras legibles, como `/blog/bienvenida`.
2. `posts` importa el arreglo de artículos; `generateStaticParams()` devuelve `{slug: 'bienvenida'}` y `{slug: 'renderizado'}`.
3. `params` llega como promesa en Next.js 15; `await params` permite leer `slug`.
4. `posts.find(...)` localiza el artículo correcto; `notFound()` maneja un slug desconocido.
5. `{post.titulo}` y `{post.cuerpo}` insertan los datos obtenidos, sin duplicar un archivo por artículo.
6. **Prueba:** `/blog/bienvenida`, `/blog/renderizado` y `/blog/no-existe` (404). Si la página falla, revisa las llaves del objeto `slug` y el nombre de la carpeta.



**Ejecuta y comprueba:** en la raíz del proyecto, inicia `npm run dev` (si ya está activo, guarda los archivos y actualiza el navegador). 
Abre `/productos/1`, `/productos/2`, `/blog/bienvenida` y `/blog/renderizado`. Prueba `/productos/999`: debe devolver 404. Si falla, confirma que la carpeta se llama literalmente `[id]`.


### Comprendamos EJ06: una plantilla, muchas URLs
**Problema:** no tendría sentido crear `app/productos/1/page.tsx`, `app/productos/2/page.tsx` y otro archivo por cada producto. Una **ruta dinámica** utiliza un único archivo `[id]/page.tsx` para resolver varios identificadores.

- **`[id]` y `[slug]`**: los corchetes indican un segmento variable de la URL. `/productos/2` entrega el parámetro `id = '2'`; `/blog/bienvenida` entrega `slug = 'bienvenida'`.
- **`generateStaticParams()`**: recorre los datos conocidos (`map`) y enumera qué páginas se pueden generar anticipadamente.
- **`params: Promise<{ id: string }>` y `await params`**: reciben el identificador de la URL siguiendo el contrato de Next.js 15; `await` permite obtener el objeto con su valor.
- **`productos.find(item => item.id === id)`**: busca el registro cuyo identificador coincide con el de la URL; a diferencia de `map`, `find` devuelve como máximo un elemento.
- **`if (!producto) notFound()`**: si no existe el producto, Next.js responde mostrando su página de no encontrado.
- **`dynamicParams = false`**: limita las rutas disponibles a los parámetros enumerados; aquí un identificador inexistente da 404.

**Antes de ejecutar, predice:** `/productos/1` mostrará Teclado mecánico; `/productos/999` no encontrará registro; `/blog/renderizado` mostrará el segundo artículo.

**Comprobación escalonada:** crea primero `app/productos/[id]/page.tsx`, guarda y prueba `/productos/1` y `/productos/999`; luego crea `app/blog/[slug]/page.tsx` y prueba `/blog/bienvenida`. Si ves 404 en todas las URLs, revisa si la carpeta se llama exactamente `[id]` y no `id`.

## Comprobación integral antes de compilar
1. Comprueba que la terminal esté dentro de `next-catalogo` usando `pwd` (macOS/Linux) o `cd` sin argumentos (Windows). También puedes buscar `package.json` en el explorador de VS Code.
2. Comprueba que cada archivo termine en `.tsx` o `.ts` según se indicó; **no** guardes `page.tsx.txt`.
3. Antes de `npm run build`, detén el servidor de desarrollo con **Ctrl+C** en su terminal. Los comandos `build` y `start` se ejecutan uno después de otro en **la misma raíz del proyecto**.
4. Si se muestra un error, lee la **primera** línea que indique archivo y número de línea, corrige ese archivo y vuelve a ejecutar; no continúes mientras falle.

## 5. Validación de desarrollo y producción (10 minutos)
Primero detén `npm run dev` mediante Ctrl+C. **No ejecutes `build` en paralelo con `dev` usando la misma carpeta `.next`.** Luego:

```bash
npm run build
npm run start
```

`build` compila y detecta errores de tipos; `start` levanta el sitio compilado. **Comprobación:** `/`, `/ssg`, `/ssr`, `/isr`, `/productos/1` y `/blog/bienvenida` abren correctamente. Revisa el resumen de rutas de la compilación para distinguir prerenderizado y renderizado dinámico; ISR puede mostrar tiempos de revalidación según versión. Si `build` falla, corrige el primer error y vuelve a ejecutar.

### Tabla de predicciones
|Ruta|Estrategia|Qué comparar|
|---|---|---|
|`/ssg`|SSG|HTML estable construido previamente|
|`/ssr`|SSR|Marca temporal que varía entre solicitudes|
|`/isr`|ISR|Contenido estable con posible regeneración después del intervalo|
|`/productos/1`|SSG + ruta dinámica|Datos obtenidos por `id`|
|`/blog/bienvenida`|SSG + ruta dinámica|Datos obtenidos por `slug`|

## 6. Prácticas de aplicación — T01 a T03 (20 minutos)
**T01:** añade en `lib/datos.ts` el producto `{ id: '4', nombre: 'Audífonos', precio: 120 }`; predice la URL y comprueba `/productos/4`.

**T02:** añade la frase «Atención de lunes a viernes» a `/ssg` y cambia el tiempo de ISR de 60 a 120 en `/isr`; señala la diferencia conceptual entre los dos modos y SSR. Verifica mediante compilación.

**T03:** agrega a `posts` el artículo `{ slug: 'vercel', titulo: 'Despliegue', cuerpo: 'Preparación de producción.' }`; verifica `/blog/vercel` y una compilación exitosa.

**Entrega:** ZIP del proyecto sin `node_modules`, sin `.next`, sin `.env.local`, más un README breve con evidencias: captura del navegador, salida final de `npm run build` y explicación de SSR/SSG/ISR en palabras propias.

## 7. Publicación en Vercel (extensión opcional, 10 minutos)
El despliegue **requiere servicios externos y no es necesario para probar el laboratorio local**. Tras validar `npm run build`, sube el repositorio a GitHub/GitLab/Bitbucket y en `https://vercel.com` importa el repositorio, confirma la detección de Next.js y despliega. Verifica las rutas en la URL publicada. No subas `.env.local`, `node_modules` ni `.next`. Para trabajo local no necesitas cuenta Vercel.

## 8. Solución de errores frecuentes
- **`npm` o `node` no existe:** instala Node.js y reinicia terminal.
- **`ENOENT package.json`:** ejecutas comandos fuera de la carpeta raíz.
- **`Module not found`:** ruta o mayúsculas distintas; revisa nombre exacto.
- **Página 404 inesperada:** comprobar `page.tsx` y carpetas `[id]`/`[slug]`.
- **ISR no parece regenerar:** probar con `build` y `start`, esperar el intervalo y solicitudes posteriores; dev no demuestra caché productiva.
- **Puerto ocupado:** cierra la instancia previa o usa el puerto anunciado.

## 9. Cierre y autoevaluación
Explica por qué un blog estable puede usar SSG, un panel cambiante SSR y un catálogo de actualización periódica ISR. Muestra la ruta `/productos/2` sin crear un archivo particular para cada producto. El laboratorio se considera logrado cuando `npm run build` termina sin errores y las seis rutas verificadas funcionan.

**Referencias técnicas oficiales:** `https://nextjs.org/docs`, `https://nextjs.org/docs/app/api-reference/functions/generate-static-params`, `https://vercel.com/docs`. El laboratorio utiliza Next.js 15 y no habilita Cache Components, para mantener el comportamiento explícito de las opciones de renderizado de esta sesión.


## Anexo A · Cómo leer un comando antes de copiarlo

**Regla de clase:** antes de ejecutar cualquier instrucción, responde «¿dónde estoy?, ¿qué cambia en mi equipo?, ¿cómo lo compruebo?».

|Comando|¿Instala?|¿Qué hace exactamente?|¿Cómo verifico?|
|---|---|---|---|
|`node -v`|No|Muestra la versión de Node.js.|Aparece un número de versión.| 
|`npm -v`|No|Muestra la versión de npm.|Aparece un número de versión.| 
|`npx create-next-app@15 next-catalogo`|Sí, durante la preparación|Ejecuta el generador y prepara archivos y dependencias del proyecto.|Existe `next-catalogo/package.json`.| 
|`cd next-catalogo`|No|Entra en la carpeta del proyecto.|La ruta de la terminal termina en `next-catalogo`.| 
|`npm install`|Sí|Descarga las dependencias descritas en `package.json` (necesario si se abre un proyecto ZIP sin `node_modules`).|Aparece `node_modules/` y npm termina sin errores.| 
|`npm run dev`|No|Arranca la aplicación en modo desarrollo.|El navegador abre la dirección anunciada.| 
|`npm run build`|No|Compila y prepara la aplicación para producción.|Finaliza con éxito y muestra un resumen de rutas.| 
|`npm run start`|No|Ejecuta la compilación de producción ya creada.|La web responde en localhost.| 
|`Ctrl+C`|No|Detiene el proceso activo de esa terminal.|El proceso deja de servir páginas.| 

**Nota importante sobre `npm run build`:** si aparece «script missing», revisa el bloque `scripts` de `package.json`; si aparece «next is not recognized», comprueba que ejecutaste `npm install` en el proyecto correcto.

## Anexo B · ¿Qué significan las extensiones de archivos?

- **`.ts`**: TypeScript sin etiquetas JSX; lo usamos en `lib/datos.ts` para guardar objetos.
- **`.tsx`**: TypeScript con JSX; lo usamos en páginas y componentes que devuelven interfaz.
- **`.css`**: reglas visuales de la aplicación.
- **`.json`**: datos de configuración; `package.json` no es código de una página.
- **`page.tsx`**: archivo especial de App Router que representa una ruta pública.
- **`layout.tsx`**: archivo especial de App Router que envuelve páginas relacionadas.

## Anexo C · Comprobación final: evidencia vs. predicción

1. **EJ01:** captura de la portada del proyecto inicial y terminal mostrando `npm run dev`.
2. **EJ02:** captura de tres tarjetas y dos enlaces; identifica de qué archivo procede el nombre del primer producto.
3. **EJ03:** captura del contenido de `/ssg` y salida de producción que identifica la ruta estática.
4. **EJ04:** dos capturas de `/ssr` tras solicitudes separadas, con horas distintas.
5. **EJ05:** evidencia de compilación y una explicación de cuándo puede regenerarse la ruta; evita afirmar actualización automática exacta.
6. **EJ06:** capturas de `/productos/1`, `/blog/bienvenida` y de un 404 previsto.

**Cierre en una frase:** no basta con que el código se vea bien: el estudiante debe saber qué archivo creó, qué instrucción escribió, para qué sirve, qué resultado observó y cómo corregir un fallo.
