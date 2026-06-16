
# Guia del estudiante - Introduccion a Next.js y optimizacion de React

**Elaborado por el docente**  
**Proyecto academico:** Lideratec Academy  
**Blog:** https://lideratecacademy.com/  
**Canal YouTube:** https://www.youtube.com/@LideratecAcademy

## 1. Proposito de la practica

Esta guia te acompaña paso a paso en la creacion de un proyecto con **Next.js**, entendiendo como este framework basado en **React** permite construir aplicaciones web rapidas, escalables y optimizadas.

Durante la practica aprenderas a:

1. Crear un proyecto Next.js desde cero.
2. Reconocer la estructura principal de un proyecto con App Router.
3. Modificar la pagina inicial.
4. Crear y reutilizar componentes.
5. Crear rutas internas.
6. Diferenciar SSR, SSG e ISR.
7. Implementar una ruta dinamica basica.
8. Preparar el proyecto para desplegarlo en Vercel.

## 2. Resultado de aprendizaje observable

Al finalizar, podras **construir, ejecutar, modificar y validar una aplicacion basica en Next.js**, diferenciando cuando usar renderizado dinamico, generacion estatica y rutas dinamicas dentro de un flujo de trabajo profesional.

## 3. Duracion sugerida

| Momento | Tiempo estimado |
|---|---:|
| Preparacion del entorno | 20 min |
| Creacion del proyecto | 20 min |
| Modificacion inicial y componentes | 30 min |
| Navegacion y rutas | 25 min |
| SSR, SSG, ISR y rutas dinamicas | 35 min |
| Preparacion para Vercel | 25 min |
| Actividades y validacion final | 25 min |
| **Total sugerido** | **180 min** |

## 4. Requisitos previos minimos

Antes de iniciar, debes conocer de forma basica:

- HTML: etiquetas, estructura de una pagina y elementos de texto.
- JavaScript: funciones, variables, objetos y arreglos.
- React: componentes funcionales y props.
- Terminal: ejecutar comandos, entrar a carpetas y verificar resultados.
- Git basico: crear commits y subir codigo a un repositorio, si se realizara despliegue.

No necesitas dominar Next.js previamente. La practica parte desde la creacion del proyecto.

## 5. Preparacion del entorno

### 5.1 Herramientas necesarias

| Herramienta | Para que sirve | Enlace oficial |
|---|---|---|
| Node.js | Ejecutar JavaScript fuera del navegador y usar npm | https://nodejs.org/ |
| npm | Instalar paquetes y ejecutar scripts del proyecto | Incluido con Node.js |
| Visual Studio Code | Editar el codigo del proyecto | https://code.visualstudio.com/ |
| Git | Controlar versiones y subir codigo a repositorio | https://git-scm.com/ |
| GitHub, GitLab o Bitbucket | Alojar el repositorio remoto | https://github.com/ |
| Vercel | Publicar la aplicacion Next.js | https://vercel.com/ |

### 5.2 Version minima recomendada

Para esta practica usa **Node.js 20.9 o superior**. Esta version permite trabajar con la documentacion actual de Next.js y reduce errores al instalar dependencias.

### 5.3 Instalacion recomendada de Node.js

1. Entra a https://nodejs.org/.
2. Descarga la version LTS disponible para tu sistema operativo.
3. Ejecuta el instalador.
4. Acepta las opciones por defecto, salvo que tu docente indique otra configuracion.
5. Reinicia la terminal despues de instalar.
6. Verifica la instalacion.

~~~bash
node -v
npm -v
~~~

**Que es `node -v`:** comando que muestra la version instalada de Node.js.  
**Que es `npm -v`:** comando que muestra la version instalada de npm.  
**Resultado esperado:** ver numeros de version, por ejemplo `v20.x.x` o superior para Node.js.

**Error comun:** la terminal responde que `node` o `npm` no se reconoce.  
**Correccion:** cerrar y abrir la terminal. Si el error continua, reinstalar Node.js desde la pagina oficial y verificar que se agregue al PATH.

### 5.4 Preparacion de Visual Studio Code

1. Abre Visual Studio Code.
2. Instala extensiones utiles:
   - ESLint.
   - Prettier.
   - JavaScript and TypeScript Nightly, opcional.
3. Abre una carpeta de trabajo, por ejemplo `Documentos/NextJS`.
4. Abre la terminal integrada con `Ctrl + ñ` o desde `Terminal > New Terminal`.

### 5.5 Nota tecnica de vigencia de la practica

En proyectos nuevos se recomienda trabajar con **App Router**, porque es la estructura moderna de Next.js. Tambien se mantiene la explicacion de Pages Router para reconocer codigo existente o proyectos antiguos, pero la practica principal de esta guia se centra en App Router.

---

# Bloque 1 - Comprender que problema resuelve Next.js

**Objetivo del bloque:**  
Identificar por que Next.js mejora una aplicacion React tradicional cuando se requiere rendimiento, SEO, rutas integradas y despliegue profesional.

**Concepto trabajado:**  
Next.js como framework basado en React.

**Explicacion breve:**  
React permite construir interfaces, pero una aplicacion React tradicional suele renderizar principalmente en el navegador. Next.js agrega capacidades listas para produccion: renderizado en servidor, generacion estatica, rutas automaticas, optimizacion de imagenes, division de codigo y despliegue simple.

**Ejemplo guiado:**

~~~text
React tradicional:
Navegador -> descarga JavaScript -> renderiza interfaz

Next.js:
Servidor o build -> genera HTML inicial -> navegador recibe una pagina mas lista
~~~

**Paso a paso:**

1. Reconoce que Next.js no reemplaza React: lo usa como base.
2. Identifica que Next.js decide como generar cada pagina.
3. Observa que esa decision impacta rendimiento, SEO y experiencia del usuario.

**Actividad para ti:**  
Escribe dos casos donde usarias Next.js: uno informativo y otro dinamico.

**Espacio para responder:**

- Caso informativo: ______________________________________
- Caso dinamico: _________________________________________
- Motivo de usar Next.js: ________________________________

**Resultado esperado:**  
Distingues que Next.js sirve para aplicaciones profesionales, blogs, tiendas, dashboards, plataformas educativas y sistemas empresariales.

**Error comun a evitar:**  
Pensar que Next.js es otro lenguaje. Next.js es un framework de React.

**Mini reto:**  
Explica en una frase por que una pagina con buen SEO puede beneficiarse de Next.js.

---

# Bloque 2 - Crear el proyecto con create-next-app

**Objetivo del bloque:**  
Crear un proyecto Next.js desde cero usando la herramienta oficial de inicializacion.

**Concepto trabajado:**  
`create-next-app`.

**Explicacion breve:**  
`create-next-app` genera automaticamente la estructura base de un proyecto Next.js. Instala dependencias, crea carpetas principales y configura opciones como TypeScript, ESLint, Tailwind CSS y App Router.

**Ejemplo guiado:**

~~~bash
npx create-next-app@latest mi-proyecto-next
cd mi-proyecto-next
npm run dev
~~~

**Paso a paso:**

1. Abre la terminal en tu carpeta de trabajo.
2. Ejecuta `npx create-next-app@latest mi-proyecto-next`.
3. Cuando el asistente pregunte, selecciona:
   - TypeScript: Yes.
   - ESLint: Yes.
   - Tailwind CSS: opcional, recomendado si se desea estilos rapidos.
   - App Router: Yes.
   - Import alias: puedes mantener `@/*`.
4. Entra a la carpeta con `cd mi-proyecto-next`.
5. Ejecuta `npm run dev`.
6. Abre `http://localhost:3000` en el navegador.

**Que significa cada comando:**

| Comando | Significado |
|---|---|
| `npx create-next-app@latest mi-proyecto-next` | Descarga y ejecuta el generador oficial de proyectos Next.js. |
| `cd mi-proyecto-next` | Entra a la carpeta creada. |
| `npm run dev` | Inicia el servidor de desarrollo. |

**Actividad para ti:**  
Crea el proyecto y registra que aparece en la terminal cuando el servidor inicia correctamente.

**Espacio para responder:**

- Ruta donde cree el proyecto: ____________________________
- Puerto mostrado por la terminal: ________________________
- URL abierta en navegador: _______________________________

**Resultado esperado:**  
La pagina inicial de Next.js aparece en el navegador.

**Error comun a evitar:**  
Ejecutar `npm run dev` fuera de la carpeta del proyecto. Debes estar dentro de `mi-proyecto-next`.

**Mini reto:**  
Deten el servidor con `Ctrl + C` y vuelve a iniciarlo con `npm run dev`.

---

# Bloque 3 - Reconocer la estructura del proyecto

**Objetivo del bloque:**  
Comprender para que sirven las carpetas y archivos principales de un proyecto Next.js.

**Concepto trabajado:**  
Estructura base con App Router.

**Explicacion breve:**  
La estructura del proyecto indica donde colocar paginas, componentes, imagenes, configuracion y dependencias. En App Router, la carpeta `app/` es central porque define rutas y paginas.

**Ejemplo guiado:**

~~~text
mi-proyecto-next/
├─ app/
│  ├─ layout.tsx
│  └─ page.tsx
├─ public/
├─ package.json
├─ next.config.ts
└─ tsconfig.json
~~~

**Paso a paso:**

1. Abre el proyecto en Visual Studio Code.
2. Ubica la carpeta `app/`.
3. Abre `app/page.tsx`.
4. Abre `app/layout.tsx`.
5. Ubica `public/`.
6. Abre `package.json`.

**Que debes observar:**

| Elemento | Funcion |
|---|---|
| `app/` | Carpeta principal del sistema de rutas. |
| `app/page.tsx` | Pagina inicial que se muestra en `/`. |
| `app/layout.tsx` | Estructura comun de la aplicacion. |
| `public/` | Archivos estaticos como imagenes e iconos. |
| `package.json` | Scripts y dependencias del proyecto. |
| `next.config.ts` | Configuracion avanzada de Next.js. |

**Actividad para ti:**  
Indica que archivo se muestra cuando visitas `http://localhost:3000`.

**Espacio para responder:**

- Archivo principal de la ruta `/`: ________________________
- Carpeta de rutas: _______________________________________
- Archivo de scripts: _____________________________________

**Resultado esperado:**  
Identificas donde modificar la pagina inicial y donde revisar los scripts del proyecto.

**Error comun a evitar:**  
Crear paginas fuera de `app/` cuando estas usando App Router.

**Mini reto:**  
Abre `package.json` y localiza los scripts `dev`, `build` y `start`.

---

# Bloque 4 - Modificar la pagina inicial

**Objetivo del bloque:**  
Editar la pagina principal y comprobar la actualizacion automatica en el navegador.

**Concepto trabajado:**  
Flujo de desarrollo en tiempo real.

**Explicacion breve:**  
Next.js actualiza el navegador cuando guardas cambios en archivos de la aplicacion. Esto permite probar rapidamente el resultado mientras desarrollas.

**Ejemplo guiado:**

Archivo sugerido: `app/page.tsx`

~~~tsx
export default function Home() {
  return (
    <main>
      <h1>Bienvenido a mi primer proyecto con Next.js</h1>
      <p>Este proyecto ha sido creado correctamente.</p>
      <p>Estoy aprendiendo a optimizar React con Next.js.</p>
    </main>
  )
}
~~~

**Explicacion del codigo:**

- `export default function Home()` declara el componente principal de la pagina.
- `return` devuelve la interfaz que se mostrara en el navegador.
- `<main>` representa el contenido principal.
- `<h1>` muestra el titulo.
- `<p>` muestra parrafos descriptivos.

**Paso a paso:**

1. Abre `app/page.tsx`.
2. Reemplaza el contenido por el ejemplo.
3. Guarda el archivo.
4. Mira el navegador.
5. Verifica que el texto cambio sin crear otra pagina.

**Actividad para ti:**  
Cambia el titulo por uno relacionado con tu propio proyecto.

**Espacio para responder:**

- Nuevo titulo elegido: ___________________________________
- Parrafo adicional agregado: _____________________________
- Que observe en el navegador: ____________________________

**Resultado esperado:**  
El navegador muestra el nuevo titulo y los nuevos parrafos.

**Error comun a evitar:**  
Olvidar cerrar etiquetas JSX, por ejemplo escribir `<h1>` sin `</h1>`.

**Mini reto:**  
Agrega una lista con tres beneficios de Next.js.

---

# Bloque 5 - Crear un componente reutilizable

**Objetivo del bloque:**  
Crear un componente y usarlo dentro de la pagina principal.

**Concepto trabajado:**  
Componente reutilizable en Next.js.

**Explicacion breve:**  
Un componente es una parte independiente de la interfaz que puede reutilizarse. En Next.js se crean componentes como funciones de React. Esto evita repetir codigo y permite organizar mejor la aplicacion.

**Ejemplo guiado:**

Archivo sugerido: `components/Saludo.tsx`

~~~tsx
type SaludoProps = {
  nombre: string
}

export default function Saludo({ nombre }: SaludoProps) {
  return <h2>Hola {nombre}, bienvenido a Next.js</h2>
}
~~~

Archivo sugerido: `app/page.tsx`

~~~tsx
import Saludo from "../components/Saludo"

export default function Home() {
  return (
    <main>
      <h1>Proyecto inicial con Next.js</h1>
      <Saludo nombre="Estudiante" />
    </main>
  )
}
~~~

**Explicacion del codigo:**

- `type SaludoProps` define que el componente recibira un dato llamado `nombre`.
- `nombre: string` indica que ese dato debe ser texto.
- `Saludo({ nombre })` recibe la propiedad enviada desde la pagina.
- `{nombre}` inserta el valor dentro del JSX.
- `import Saludo` permite usar el componente dentro de `app/page.tsx`.

**Paso a paso:**

1. Crea la carpeta `components` en la raiz del proyecto.
2. Dentro crea `Saludo.tsx`.
3. Copia el codigo del componente.
4. Abre `app/page.tsx`.
5. Importa y usa el componente.
6. Guarda y revisa el navegador.

**Actividad para ti:**  
Cambia el valor de `nombre` por tu nombre o por el nombre de tu grupo.

**Espacio para responder:**

- Nombre enviado al componente: ___________________________
- Archivo donde cree el componente: _______________________
- Resultado observado: ___________________________________

**Resultado esperado:**  
El navegador muestra el saludo generado por el componente.

**Error comun a evitar:**  
Escribir mal la ruta de importacion. Si `components` esta en la raiz y `page.tsx` esta dentro de `app/`, la ruta relativa puede ser `../components/Saludo`.

**Mini reto:**  
Agrega una segunda prop llamada `curso` y muestra el nombre del curso debajo del saludo.

---

# Bloque 6 - Crear una ruta de contacto y navegar con Link

**Objetivo del bloque:**  
Crear una nueva pagina y navegar hacia ella usando el sistema de rutas de Next.js.

**Concepto trabajado:**  
Rutas basadas en archivos y componente `Link`.

**Explicacion breve:**  
Next.js crea rutas usando carpetas y archivos. Si creas `app/contacto/page.tsx`, automaticamente tendras la ruta `/contacto`.

**Ejemplo guiado:**

Archivo sugerido: `app/contacto/page.tsx`

~~~tsx
export default function Contacto() {
  return (
    <main>
      <h1>Pagina de Contacto</h1>
      <p>Esta es una nueva ruta creada con Next.js.</p>
    </main>
  )
}
~~~

Archivo sugerido: `app/page.tsx`

~~~tsx
import Link from "next/link"
import Saludo from "../components/Saludo"

export default function Home() {
  return (
    <main>
      <h1>Proyecto inicial con Next.js</h1>
      <Saludo nombre="Estudiante" />
      <Link href="/contacto">Ir a Contacto</Link>
    </main>
  )
}
~~~

**Explicacion del codigo:**

- `app/contacto/page.tsx` crea la ruta `/contacto`.
- `Link` permite navegar entre paginas sin recargar toda la aplicacion.
- `href="/contacto"` indica la ruta destino.

**Paso a paso:**

1. Dentro de `app/`, crea una carpeta llamada `contacto`.
2. Dentro de `contacto`, crea `page.tsx`.
3. Copia el codigo de la pagina de contacto.
4. Abre `app/page.tsx`.
5. Importa `Link` desde `next/link`.
6. Agrega el enlace hacia `/contacto`.
7. Guarda y prueba el enlace en el navegador.

**Actividad para ti:**  
Crea una segunda ruta llamada `/acerca` con un titulo y un parrafo.

**Espacio para responder:**

- Ruta creada: ____________________________________________
- Archivo creado: ________________________________________
- Texto mostrado en la pagina: ____________________________

**Resultado esperado:**  
Puedes navegar desde la pagina inicial hacia `/contacto` y volver escribiendo `/` en el navegador.

**Error comun a evitar:**  
Crear `app/contacto.tsx` en vez de `app/contacto/page.tsx` cuando trabajas con App Router.

**Mini reto:**  
Agrega enlaces desde la pagina de contacto hacia la pagina principal.

---

# Bloque 7 - Diferenciar SSR, SSG e ISR

**Objetivo del bloque:**  
Decidir que estrategia de generacion de pagina conviene segun el tipo de informacion.

**Concepto trabajado:**  
Server Side Rendering, Static Site Generation e Incremental Static Regeneration.

**Explicacion breve:**  
Next.js permite elegir como y cuando se genera una pagina. Esta decision impacta el rendimiento, el SEO, el consumo de servidor y la experiencia del usuario.

| Estrategia | Cuando conviene | Ejemplo |
|---|---|---|
| SSR | Datos que deben estar actualizados en cada solicitud | Dashboard, precios, estados, reportes personalizados |
| SSG | Contenido estable generado durante el build | Blog, portafolio, pagina institucional |
| ISR | Contenido estatico que se actualiza cada cierto tiempo | Catalogo, blog con cambios moderados |

**Ejemplo guiado:**

~~~text
Caso 1: Dashboard de ventas en tiempo real -> SSR
Caso 2: Pagina institucional de una universidad -> SSG
Caso 3: Catalogo de productos que cambia cada minuto -> ISR
~~~

**Paso a paso:**

1. Pregunta si la informacion cambia constantemente.
2. Pregunta si depende del usuario autenticado.
3. Pregunta si puede generarse antes del despliegue.
4. Decide la estrategia.
5. Valida si priorizas velocidad, actualizacion o personalizacion.

**Actividad para ti:**  
Clasifica los siguientes casos.

| Caso | SSR, SSG o ISR | Justificacion breve |
|---|---|---|
| Perfil de usuario autenticado | | |
| Blog academico | | |
| Catalogo de cursos actualizado cada hora | | |
| Reporte administrativo personalizado | | |

**Resultado esperado:**  
Puedes explicar por que no todas las paginas deben generarse de la misma forma.

**Error comun a evitar:**  
Creer que SSR siempre es mejor. SSR es util cuando se necesita contenido actualizado por solicitud, pero consume mas recursos que una pagina estatica.

**Mini reto:**  
Escribe un ejemplo propio donde ISR sea mas conveniente que SSG puro.

---

# Bloque 8 - Crear una ruta dinamica con App Router

**Objetivo del bloque:**  
Crear una plantilla de pagina que muestre contenido diferente segun un parametro de la URL.

**Concepto trabajado:**  
Rutas dinamicas con `[slug]` y `generateStaticParams`.

**Explicacion breve:**  
Una ruta dinamica permite crear una sola pagina flexible. Por ejemplo, en vez de crear una pagina para cada articulo, puedes crear `app/blog/[slug]/page.tsx` y reutilizar esa plantilla para varios articulos.

**Ejemplo guiado:**

Archivo sugerido: `app/blog/[slug]/page.tsx`

~~~tsx
type Post = {
  slug: string
  titulo: string
  contenido: string
}

const posts: Post[] = [
  {
    slug: "que-es-nextjs",
    titulo: "Que es Next.js",
    contenido: "Next.js permite optimizar aplicaciones React."
  },
  {
    slug: "ssr-vs-ssg",
    titulo: "SSR vs SSG",
    contenido: "SSR genera contenido por solicitud y SSG durante el build."
  }
]

export function generateStaticParams() {
  return posts.map((post) => ({
    slug: post.slug
  }))
}

type Props = {
  params: Promise<{ slug: string }>
}

export default async function PostPage({ params }: Props) {
  const { slug } = await params
  const post = posts.find((item) => item.slug === slug)

  if (!post) {
    return <h1>Articulo no encontrado</h1>
  }

  return (
    <article>
      <h1>{post.titulo}</h1>
      <p>{post.contenido}</p>
    </article>
  )
}
~~~

**Explicacion del codigo:**

- `Post` define la forma de los datos.
- `posts` contiene datos de ejemplo para no depender de una API externa.
- `generateStaticParams()` indica que valores dinamicos existen.
- `[slug]` representa el segmento dinamico de la URL.
- `params` contiene el valor recibido desde la URL.
- `find` busca el articulo que coincide con el `slug`.
- Si no existe el articulo, se muestra un mensaje de no encontrado.

**Paso a paso:**

1. Dentro de `app/`, crea la carpeta `blog`.
2. Dentro de `blog`, crea la carpeta `[slug]`.
3. Dentro de `[slug]`, crea `page.tsx`.
4. Copia el codigo.
5. Guarda.
6. Abre `http://localhost:3000/blog/que-es-nextjs`.
7. Abre `http://localhost:3000/blog/ssr-vs-ssg`.
8. Compara los resultados.

**Actividad para ti:**  
Agrega un tercer articulo al arreglo `posts` y abre su ruta en el navegador.

**Espacio para responder:**

- Nuevo slug creado: ______________________________________
- URL probada: ____________________________________________
- Titulo mostrado: ________________________________________

**Resultado esperado:**  
La misma plantilla muestra articulos diferentes segun el `slug`.

**Error comun a evitar:**  
Nombrar la carpeta como `slug` sin corchetes. Debe ser `[slug]` para que sea dinamica.

**Mini reto:**  
Agrega un enlace desde la pagina principal hacia uno de los articulos.

---

# Bloque 9 - Reconocer un ejemplo de renderizado dinamico

**Objetivo del bloque:**  
Comprender como se podria representar una pagina que necesita datos actualizados por solicitud.

**Concepto trabajado:**  
Renderizado dinamico equivalente al objetivo de SSR en App Router.

**Explicacion breve:**  
Cuando una pagina necesita datos actualizados en cada visita, puede forzarse el comportamiento dinamico. Esto es util para dashboards, reportes personalizados o informacion que cambia con frecuencia.

**Ejemplo guiado:**

Archivo sugerido: `app/productos/[id]/page.tsx`

~~~tsx
export const dynamic = "force-dynamic"

type Producto = {
  id: string
  nombre: string
  descripcion: string
}

const productos: Producto[] = [
  { id: "1", nombre: "Laptop academica", descripcion: "Equipo para practicas web." },
  { id: "2", nombre: "Plan LMS", descripcion: "Servicio para clases virtuales." }
]

type Props = {
  params: Promise<{ id: string }>
}

export default async function ProductoPage({ params }: Props) {
  const { id } = await params
  const producto = productos.find((item) => item.id === id)
  const fechaConsulta = new Date().toLocaleString()

  if (!producto) {
    return <h1>Producto no encontrado</h1>
  }

  return (
    <main>
      <h1>{producto.nombre}</h1>
      <p>{producto.descripcion}</p>
      <p>Consulta generada: {fechaConsulta}</p>
    </main>
  )
}
~~~

**Explicacion del codigo:**

- `dynamic = "force-dynamic"` indica que la pagina debe tratarse como dinamica.
- `productos` simula datos consultables.
- `params` recibe el `id` desde la URL.
- `fechaConsulta` permite observar que se genera un valor asociado al momento de consulta.
- La ruta `/productos/1` y `/productos/2` usan la misma plantilla.

**Paso a paso:**

1. Crea `app/productos/[id]/page.tsx`.
2. Copia el codigo.
3. Abre `http://localhost:3000/productos/1`.
4. Abre `http://localhost:3000/productos/2`.
5. Recarga la pagina y observa la fecha de consulta.

**Actividad para ti:**  
Agrega un tercer producto y prueba su URL.

**Espacio para responder:**

- Nuevo ID creado: ________________________________________
- URL probada: ____________________________________________
- Resultado observado: ____________________________________

**Resultado esperado:**  
La pagina muestra datos diferentes segun el `id`.

**Error comun a evitar:**  
Confundir `id` con `slug`. Ambos son parametros dinamicos, pero deben coincidir con el nombre de la carpeta: `[id]` produce `params.id`; `[slug]` produce `params.slug`.

**Mini reto:**  
Cambia el texto de error para que sea mas claro para un usuario final.

---

# Bloque 10 - Preparar el proyecto para Vercel

**Objetivo del bloque:**  
Dejar el proyecto listo para subirse a un repositorio y publicarse en Vercel.

**Concepto trabajado:**  
Deploy con Git y Vercel.

**Explicacion breve:**  
Vercel permite publicar aplicaciones Next.js de forma rapida. El flujo recomendado es subir el codigo a GitHub, GitLab o Bitbucket, importar el repositorio desde Vercel y ejecutar el despliegue.

**Ejemplo guiado:**

Archivo sugerido: `package.json`

~~~json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  }
}
~~~

Archivo sugerido: `.gitignore`

~~~gitignore
node_modules
.next
.env.local
.vercel
~~~

Comandos sugeridos:

~~~bash
git init
git add .
git commit -m "Proyecto inicial Next.js"
git branch -M main
git remote add origin URL_DEL_REPOSITORIO
git push -u origin main
~~~

**Explicacion de los comandos:**

- `git init` inicia el control de versiones.
- `git add .` prepara los archivos para el commit.
- `git commit` crea una version del proyecto.
- `git branch -M main` define la rama principal.
- `git remote add origin` conecta el proyecto local con el repositorio remoto.
- `git push` sube el codigo.

**Paso a paso para Vercel:**

1. Crea una cuenta o inicia sesion en https://vercel.com/.
2. Selecciona `New Project`.
3. Importa el repositorio.
4. Verifica que Vercel detecte Next.js.
5. Revisa el comando de build: `npm run build`.
6. Agrega variables de entorno si tu proyecto las necesita.
7. Haz clic en `Deploy`.
8. Espera que termine el build.
9. Abre la URL generada.

**Actividad para ti:**  
Revisa tu `package.json` y verifica que existan los scripts necesarios.

**Espacio para responder:**

- Existe script `dev`: Si / No
- Existe script `build`: Si / No
- Existe script `start`: Si / No
- Repositorio remoto creado: Si / No

**Resultado esperado:**  
El proyecto queda preparado para publicarse o se publica correctamente en Vercel.

**Error comun a evitar:**  
Subir `.env.local` al repositorio. Este archivo puede contener claves o configuracion local y debe quedar fuera del repositorio.

**Mini reto:**  
Haz un cambio pequeño en el titulo, genera un nuevo commit y verifica que Vercel cree un nuevo despliegue.

---

# Bloque 11 - Validacion final del aprendizaje

**Objetivo del bloque:**  
Comprobar que entiendes el flujo completo de creacion, modificacion, rutas y despliegue.

**Concepto trabajado:**  
Validacion integral de proyecto Next.js.

**Explicacion breve:**  
Validar no es solo mirar que la pagina abra. Tambien debes verificar estructura, rutas, componentes, comportamiento esperado y preparacion para despliegue.

**Checklist final:**

| Criterio | Si/No | Evidencia |
|---|---|---|
| El proyecto inicia con `npm run dev` | | |
| La pagina principal fue modificada | | |
| Existe un componente reutilizable | | |
| Existe una ruta `/contacto` | | |
| Existe una ruta dinamica de blog | | |
| Existe una ruta dinamica de productos | | |
| Se entiende la diferencia entre SSR, SSG e ISR | | |
| El proyecto tiene scripts `dev`, `build` y `start` | | |
| El repositorio ignora `node_modules`, `.next` y `.env.local` | | |

**Actividad para ti:**  
Escribe un resumen tecnico de 5 lineas explicando que construiste y que estrategia de renderizado usarias para una pagina informativa, una pagina personalizada y un catalogo.

**Espacio para responder:**

1. _______________________________________________________
2. _______________________________________________________
3. _______________________________________________________
4. _______________________________________________________
5. _______________________________________________________

**Resultado esperado:**  
Puedes explicar y demostrar el flujo basico de desarrollo con Next.js.

**Error comun a evitar:**  
Concentrarse solo en copiar codigo sin explicar que hace cada archivo.

**Mini reto:**  
Presenta tu proyecto en 2 minutos indicando: estructura, rutas, componente creado, ruta dinamica y preparacion para despliegue.

---

# Actividades guiadas progresivas

## Actividad 1 - Reconocimiento

**Objetivo:** identificar elementos principales de un proyecto Next.js.

**Instrucciones:**

1. Abre tu proyecto.
2. Ubica `app/`, `public/`, `package.json` y `next.config.ts`.
3. Completa la tabla.

| Elemento | Funcion | Evidencia en mi proyecto |
|---|---|---|
| `app/` | | |
| `public/` | | |
| `package.json` | | |
| `next.config.ts` | | |

**Resultado esperado:** reconoces la funcion principal de cada elemento.

**Criterio de validacion rapida:** puedes señalar el archivo que controla la pagina inicial.

## Actividad 2 - Replica guiada

**Objetivo:** reproducir una pagina inicial funcional.

**Instrucciones:**

1. Copia el ejemplo de `app/page.tsx` del Bloque 4.
2. Ejecuta `npm run dev`.
3. Abre el navegador.
4. Registra el resultado.

**Que modificar:** cambia el titulo principal.

**Que observar:** el navegador debe reflejar el cambio despues de guardar.

**Criterio de validacion rapida:** el titulo actualizado aparece en `/`.

## Actividad 3 - Modificacion controlada

**Objetivo:** modificar un componente reutilizable.

**Instrucciones:**

1. Usa `components/Saludo.tsx`.
2. Agrega una prop adicional llamada `curso`.
3. Muestra el curso debajo del saludo.

**Codigo base:**

~~~tsx
type SaludoProps = {
  nombre: string
  curso: string
}

export default function Saludo({ nombre, curso }: SaludoProps) {
  return (
    <section>
      <h2>Hola {nombre}</h2>
      <p>Curso: {curso}</p>
    </section>
  )
}
~~~

**Que responder:** ¿por que `curso` debe agregarse en el tipo y tambien en los parametros del componente?

**Criterio de validacion rapida:** se muestra el nombre del curso en la pagina.

## Actividad 4 - Aplicacion

**Objetivo:** crear una ruta nueva.

**Instrucciones:**

1. Crea `app/acerca/page.tsx`.
2. Agrega titulo, descripcion y enlace para volver a `/`.
3. Prueba `http://localhost:3000/acerca`.

**Resultado esperado:** la pagina abre desde la URL y desde un enlace.

**Criterio de validacion rapida:** no aparece error 404.

## Actividad 5 - Integracion

**Objetivo:** integrar ruta dinamica y navegacion.

**Instrucciones:**

1. Usa la ruta `app/blog/[slug]/page.tsx`.
2. Agrega al menos tres articulos.
3. En la pagina principal, agrega enlaces a dos articulos.
4. Comprueba que cada enlace abra contenido distinto.

**Que responder:** ¿por que una sola plantilla puede mostrar varios articulos?

**Criterio de validacion rapida:** cada `slug` muestra un titulo diferente.

## Actividad 6 - Validacion con preguntas

Responde brevemente:

1. ¿Que diferencia hay entre React y Next.js?
2. ¿Cuando usarias SSR?
3. ¿Cuando usarias SSG?
4. ¿Que aporta ISR?
5. ¿Que representa `[slug]` en una ruta?
6. ¿Por que no debes subir `.env.local` al repositorio?

---

# Cierre de la practica

En esta sesion construiste una base funcional con Next.js: creaste un proyecto, modificaste la pagina inicial, agregaste componentes, implementaste rutas, interpretaste estrategias de renderizado y preparaste el proyecto para despliegue.

La idea central es que Next.js no solo permite crear interfaces con React, sino tambien decidir como se entrega cada pagina al usuario: de forma dinamica, estatica, incremental o mediante rutas flexibles.

**Referencia de continuidad:**  
Lideratec Academy - https://lideratecacademy.com/  
Canal YouTube - https://www.youtube.com/@LideratecAcademy
