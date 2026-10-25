# Guía del estudiante — React: de JSX a una SPA navegable

**Curso:** Programación Web Avanzada · **Sesión:** S09 · **Tema:** Introducción a React.js y SPA · **Lideratec Academy**

## Propósito de la sesión — lectura de apertura

Imaginemos un portal de cursos cuya pantalla debe mostrar información de distintas personas, responder a clics y permitir cambiar de sección sin solicitar otra página HTML en cada navegación. Con HTML estático, repetir tarjetas manualmente y mantener la pantalla sincronizada con las interacciones termina siendo difícil. En esta clase crearemos una aplicación pequeña en React que resuelve el problema en etapas observables. Primero escribirás JSX y comprobarás por qué los componentes comienzan con mayúscula, cómo funcionan las expresiones entre llaves y qué relación existe entre el documento HTML y la aplicación. Después crearás un componente reutilizable que recibirá datos diferentes por props y un contador que actualizará la interfaz mediante useState. Como refuerzo diferenciado, usarás useEffect para sincronizar el título real de la pestaña con ese estado.

La segunda parte introduce React Router: separarás Inicio y Acerca, conectarás enlaces internos Link y comprobarás el cambio de URL sin una recarga completa. Finalmente, explorarás cómo una ruta con :id reutiliza una sola página para cursos distintos. La meta es que puedas reconstruir las decisiones, detectar errores y explicar los resultados; no solo copiar los archivos.

## Resultado de aprendizaje observable — lectura docente

Al terminar podrás partir de una carpeta vacía y montar una aplicación React mínima, reconocer la función de cada archivo, construir componentes funcionales que reciben props y demostrar cambios de estado con useState. Podrás interpretar por qué useEffect sincroniza datos de React con una API del navegador, y configurarás una navegación SPA donde Inicio y Acerca correspondan a URLs diferentes. También comprobarás la lectura de parámetros al visitar un curso por su identificador.

El dominio no se demuestra por tener un sitio vistoso: se demuestra cuando modificas el nombre del saludo, predices la salida del contador, justificas la dependencia [contador], explicas el papel de BrowserRouter y pruebas correctamente /curso/react, /curso/node y una ruta inexistente. La evidencia será una interfaz funcionando, una URL que cambia al navegar con Link y las respuestas razonadas de las tareas espejo.

### Desempeños verificables

- Crear y ejecutar un proyecto React con JSX.
- Construir y reutilizar un componente funcional con props.
- Actualizar y reiniciar estado con useState.
- Sincronizar document.title con useEffect.
- Configurar BrowserRouter, Routes, Route y Link.
- Resolver un parámetro :id y reconocer un caso inexistente.

## Organización de 120 minutos (aula + estudio independiente)

| Tramo | Tiempo | Actividad |
|---|---:|---|
| Apertura y preparación | 0–20 min | Proyecto vacío, Node y scripts npm. |
| Bloque 1: JSX y componentes | 20–50 min | EJ01 y EJ02; T01/T02 según avance. |
| Bloque 1: estado | 50–75 min | EJ03 y comprobación rápida de T03. |
| Bloque 1: efecto breve | 75–88 min | EJ04 para preparar el código acumulado de EJ05. |
| Bloque 2: navegación SPA | 88–112 min | EJ05, ruta /about y enlaces Link. |
| Cierre y preguntas | 112–120 min | Interpretación y errores frecuentes. |

**Trabajo autónomo:** T04 (refuerzo de useEffect) y EJ06/T06 (ruta dinámica). EJ04 se demuestra brevemente en aula antes de EJ05, para que la construcción acumulativa sea literal. En la guía están resueltos con la misma profundidad para no adelantar contenidos de forma superficial durante las dos horas. La clase se concentra en dos ejes: **interfaz React con estado** y **navegación SPA**.

## Antes de iniciar

**Debes saber:** variables, funciones, imports/exports y eventos de JavaScript; terminal y npm ya trabajados en sesiones anteriores. **Necesitas:** Node.js compatible con Vite (20.19+ o 22.12+), npm, Visual Studio Code o equivalente y navegador web. No necesitas MongoDB, JWT, un backend Node/Express ni una API pública para este laboratorio.

## Antes de programar: ¿qué herramientas vamos a utilizar y para qué?

En esta sesión construiremos un **portal de cursos**. Para no confundir las herramientas, conviene distinguir **qué crea la interfaz**, **qué prepara el proyecto** y **qué se ejecuta en el navegador**. No son lo mismo:

| Concepto | ¿Qué es? | ¿Para qué lo usamos hoy? | Dónde lo reconoces |
|---|---|---|---|
| **Node.js** | Entorno que ejecuta JavaScript fuera del navegador. | Permite ejecutar npm y las herramientas de desarrollo. **No vamos a construir un servidor Express.** | Comando `node --version`. |
| **npm** | Gestor de paquetes que acompaña habitualmente a Node.js. | Instala React, Vite y más adelante React Router. Ejecuta los scripts definidos en el proyecto. | `npm install`, `npm run dev`. |
| **Vite** | Herramienta para desarrollar y compilar aplicaciones web modernas. | Levanta un servidor local que muestra la app mientras programamos y prepara los archivos optimizados para publicación. | `vite.config.js`, scripts `dev`, `build`, `preview`. |
| **React** | Biblioteca de JavaScript para construir interfaces a partir de componentes. | Describe qué mostrar y actualiza la interfaz al cambiar los datos o el estado. | Archivos `App.jsx` y `Saludo.jsx`. |
| **React DOM** | Integración de React con el DOM del navegador. | Coloca los componentes de React dentro del elemento `#root` de `index.html`. | `createRoot(...)` en `src/main.jsx`. |
| **JSX** | Extensión de sintaxis de JavaScript parecida a HTML. | Escribimos la estructura visual junto a expresiones dinámicas dentro de componentes. | Extensión `.jsx` y expresiones como `{nombre}`. |
| **React Router** | Biblioteca que asocia URLs con vistas de React. | Permite cambiar entre Inicio, Acerca y detalles de cursos dentro de una SPA. | Se incorpora recién en **EJ05**. |

### Vite explicado antes de instalarlo

**¿Qué es Vite?** Es la herramienta que facilita ejecutar y preparar nuestro proyecto frontend. **¿Para qué sirve?** Cuando trabajamos, ofrece una dirección local (por ejemplo `http://localhost:5173/`) y actualiza los módulos modificados rápidamente; para una entrega, `npm run build` genera archivos estáticos en `dist/`. **¿Qué no es?** No reemplaza a React, no es la interfaz del usuario y no constituye por sí solo una API ni una base de datos. Es el entorno de desarrollo y compilación.

**¿Por qué Vite aparece en esta clase?** Los navegadores no procesan directamente la sintaxis JSX tal como la escribimos; Vite, con `@vitejs/plugin-react`, prepara los módulos necesarios para mostrarlos. Por eso necesitamos `vite.config.js` y el script `dev`. En este laboratorio crearemos manualmente los archivos para entender su función; no utilizaremos el asistente `npm create vite` porque queremos ver de dónde sale cada pieza.

**Conexión completa:** `index.html` incluye un elemento vacío `<div id="root"></div>` y carga `src/main.jsx`; allí `createRoot` conecta React con ese elemento y muestra `<App />`. El componente `App` devuelve JSX, que termina convertido en elementos visibles del navegador. Vite sirve esos archivos mientras programamos y permite comprobarlos con `npm run build`.

```text
Navegador → index.html (#root) → src/main.jsx (createRoot)
                                → src/App.jsx → componentes + JSX
                   Vite = servidor local y compilación del proyecto
```

### ¿Qué es package.json y por qué no basta con instalar React?

`package.json` describe el proyecto: **`dependencies`** enumera las bibliotecas que necesita la aplicación (por ejemplo `react`, `react-dom` y, desde EJ05, `react-router`); **`devDependencies`** incluye herramientas usadas para desarrollar y compilar (como Vite). **`scripts`** son nombres abreviados de comandos: cuando ejecutas `npm run dev`, npm busca `"dev": "vite"` y lanza Vite. `npm install` lee las dependencias del archivo y descarga paquetes en `node_modules`. No confundas **instalar** (`npm install`), **ejecutar durante el desarrollo** (`npm run dev`) y **comprobar una compilación** (`npm run build`).

**Comprobación de comprensión antes de continuar:** si React construye componentes, ¿por qué seguimos necesitando Vite? Porque un componente escrito en JSX necesita un proceso de desarrollo y transformación para verse y compilarse. Si Vite está activo pero `App.jsx` contiene un error, el servidor puede seguir encendido y la aplicación mostrar el diagnóstico; ambas herramientas resuelven problemas diferentes.

### Preparación exacta desde carpeta vacía

1. Abre la terminal integrada de VS Code con **Terminal > Nuevo terminal**. Sitúate en una carpeta de proyectos y ejecuta `mkdir react-s09-lab`. En Windows y Unix funciona en terminales habituales. Ejecuta `cd react-s09-lab` para ingresar a ella. Comprueba que la terminal ya se encuentra dentro de esa carpeta.
2. Comprueba Node y npm con `node --version` y `npm --version`. Si Node no es compatible, instala una versión LTS desde https://nodejs.org/ y vuelve a comprobar. La instalación requiere acceso a internet; si es un equipo institucional, respeta sus permisos.
3. Ejecuta `npm init -y`. **Por qué:** genera package.json, el catálogo de comandos y dependencias del proyecto. **Comprobación:** `dir` en Windows o `ls` en macOS/Linux muestra package.json.
4. Instala las dependencias base: `npm install react@19.2.0 react-dom@19.2.0`. **React** construye componentes; **react-dom** los muestra en el navegador. Espera el mensaje de instalación sin errores.
5. Instala herramientas de desarrollo: `npm install -D vite@7.3.1 @vitejs/plugin-react@5.1.4`. **¿Por qué `-D`?** Ubica Vite y el plugin entre las dependencias de desarrollo, porque son herramientas para programar y compilar, no componentes visibles de la aplicación. Vite sirve la app en local y el plugin permite integrar React/JSX.
6. Abre package.json y **sustituye su contenido completo** por el bloque de EJ01. Este bloque añade los scripts `dev`, `build`, `preview`; también lista de forma coherente las dependencias que acabas de instalar. Ejecuta `npm install` tras guardarlo.
7. En la raíz, crea `index.html` y `vite.config.js`; dentro de `src`, crea `main.jsx`, `App.jsx`, `index.css`. **No escribas código en un directorio distinto:** src es la carpeta de componentes y punto de entrada. Los contenidos completos están en EJ01, y se explican en los microbloques.
8. Una vez completado EJ01, ejecuta `npm run dev` desde la carpeta react-s09-lab (no desde src). La terminal debe informar una URL local, habitualmente `http://localhost:5173/`. Abre esa dirección en el navegador; la terminal debe permanecer activa durante el trabajo. Si el puerto está ocupado, Vite puede elegir el siguiente: utiliza la dirección real que muestre.

### ¿Por qué esos scripts en package.json?

- `"dev": "vite"`: se activa mediante `npm run dev` para iniciar el servidor de desarrollo y ver cambios al guardar. **Ejecutar en todos los ejemplos.**
- `"build": "vite build"`: se activa mediante `npm run build` para comprobar la compilación de producción. **Ejecutar después de cada ejemplo completado**, preferiblemente desde otra terminal con el mismo directorio.
- `"preview": "vite preview"`: se activa mediante `npm run preview` para previsualizar localmente una compilación ya creada; es opcional y no reemplaza npm run dev.

`npm install` **instala dependencias; no inicia** el servidor. `npm run dev` **inicia** el servidor; no instala. `npm run build` **construye** un directorio dist y permite descubrir errores de compilación. Al acabar, detén el servidor con **Ctrl + C**. Tras EJ05, al agregar react-router, debes ejecutar la instalación indicada allí antes de compilar.

### Estructura que irás completando

```text
react-s09-lab/
├── package.json
├── index.html
├── vite.config.js
└── src/
    ├── main.jsx
    ├── index.css
    ├── App.jsx
    ├── components/
    │   ├── Saludo.jsx         (desde EJ02)
    │   ├── Contador.jsx       (desde EJ03)
    │   └── Navbar.jsx         (desde EJ05)
    └── pages/
        ├── Inicio.jsx        (desde EJ05)
        ├── Acerca.jsx        (desde EJ05)
        └── CursoDetalle.jsx  (desde EJ06)
```

---

# EJ01 — Mostrar contenido dinámico con JSX

## Comprendamos los conceptos nuevos antes de escribir EJ01

**React, interfaz y SPA.** React permite expresar una interfaz como piezas llamadas *componentes*. Una **SPA** (*Single Page Application*, aplicación de una sola página) parte de un documento HTML principal; sus vistas y componentes pueden cambiar mediante JavaScript sin pedir otro documento HTML completo en cada interacción. Eso no significa que la app tenga una sola vista: en EJ05 veremos varias URLs dentro de la misma SPA.

**JSX: qué es y qué hacemos con él.** JSX es una extensión de sintaxis de JavaScript con etiquetas parecidas a HTML. Lo escribimos dentro de un componente para describir lo que aparecerá en pantalla. **No es HTML copiado sin cambios.** Por ejemplo, en `src/App.jsx` el fragmento `<p>Bienvenida, {nombre}.</p>` contiene texto y una expresión JavaScript: si `nombre` vale `'Lucía'`, la pantalla mostrará «Bienvenida, Lucía». Las **llaves `{}`** no se dibujan como texto: indican que React debe evaluar una expresión.

**Reglas prácticas de JSX que usaremos:** (1) los elementos deben cerrarse, por ejemplo `<img />` y `<p>...</p>`; (2) utilizamos `className` en lugar de `class` para asignar la clase CSS; (3) si devolvemos varios elementos, deben tener un contenedor raíz o un fragmento `<>...</>`; (4) las funciones que representan componentes comienzan con mayúscula, por ejemplo `App`. **Archivo `.jsx`:** su extensión advierte que aquí escribimos JavaScript con sintaxis JSX.

**¿Qué papel cumplen los tres archivos principales?** `index.html` reserva el contenedor `#root`; `src/main.jsx` ejecuta `createRoot(document.getElementById('root')).render(<App />)` para montar la aplicación; `src/App.jsx` define la interfaz que aparecerá allí. **No hay que escribir el contenido del portal en el HTML base**: se escribe en el componente.

**Pregunta para razonar:** si cambias `const nombre = 'Lucía'` por `const nombre = 'Ana'`, ¿qué archivo cambia y por qué? Solo `App.jsx` cambia; las llaves `{nombre}` muestran el nuevo valor, Vite actualiza el módulo y el navegador refleja la modificación. **Error conceptual frecuente:** pensar que Vite «escribe la página»; la página la describe React usando JSX.

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

Una página HTML fija no explica todavía cómo React actualiza textos mediante expresiones de JavaScript. Crearemos una aplicación mínima con Vite, conectaremos el punto de entrada de React con App y escribiremos JSX válido. Usaremos className, llaves y etiquetas cerradas para que el mensaje de bienvenida dependa de una variable. La página debe mostrar «Portal de cursos» y «Bienvenida, Lucía». Aprenderemos a verificar la consecuencia ejecutando el servidor local y comprobando el estado visible, antes de cambiar archivos adicionales.

## ¿Qué problema concreto resolveremos?

Una página HTML fija no explica todavía cómo React actualiza textos mediante expresiones de JavaScript. La propuesta será modificar únicamente la responsabilidad necesaria de la aplicación del portal de cursos y comprobar su consecuencia real en el navegador.

**Punto de partida:** carpeta creada en la preparación; todavía no hay una aplicación visible.

**Archivos de este ejemplo:** `index.html`, `vite.config.js`, `package.json`, `src/main.jsx`, `src/index.css`, `src/App.jsx`.

### Paso 1 — Crear la base técnica

**Antes de escribir.** El proyecto requiere un HTML con #root, un compilador JSX y una entrada JavaScript. index.html define el contenedor; vite.config.js transforma JSX; package.json concentra dependencias y scripts. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, localiza `package.json`, `index.html` y `vite.config.js`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 1.1: escribir el bloque 1 en `package.json`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```json
{
  "name": "react-s09-lab",
  "private": true,
  "version": "1.0.0",
  "type": "module",
```

**Por qué se agrega ahora:** Es un manifiesto JSON. scripts indica a npm qué ejecutar; dependencies son bibliotecas de la aplicación y devDependencies herramientas de compilación. No añadas comentarios dentro del JSON. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 4; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.2: escribir el bloque 2 en `package.json`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```json
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
```

**Por qué se agrega ahora:** package.json define scripts de npm: dev ejecuta Vite, build genera dist y preview muestra el build. Estos son comandos con propósitos distintos. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 4; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.3: escribir el bloque 3 en `package.json`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```json
  "dependencies": {
    "react": "^19.2.0",
    "react-dom": "^19.2.0"
  },
```

**Por qué se agrega ahora:** dependencies aporta bibliotecas para la aplicación; devDependencies contiene las herramientas de compilación. No escribas comentarios dentro del JSON. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 4; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.4: escribir el bloque 4 en `package.json`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```json
  "devDependencies": {
    "vite": "^7.3.1",
    "@vitejs/plugin-react": "^5.1.4"
  }
}
```

**Por qué se agrega ahora:** dependencies aporta bibliotecas para la aplicación; devDependencies contiene las herramientas de compilación. No escribas comentarios dentro del JSON. **Estado acumulado:** el archivo contiene los bloques 1 a 4 de 4; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `package.json` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```json
{
  "name": "react-s09-lab",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^19.2.0",
    "react-dom": "^19.2.0"
  },
  "devDependencies": {
    "vite": "^7.3.1",
    "@vitejs/plugin-react": "^5.1.4"
  }
}
```

**Explicación causal.** Es un manifiesto JSON. scripts indica a npm qué ejecutar; dependencies son bibliotecas de la aplicación y devDependencies herramientas de compilación. No añadas comentarios dentro del JSON. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 1.1: escribir el bloque 1 en `index.html`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```html
<!doctype html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Portal de cursos</title>
  </head>
```

**Por qué se agrega ahora:** Define el elemento <div id="root"> en el que React montará la interfaz. El script type="module" abre src/main.jsx; sin esa coincidencia, React no llega al navegador. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 2; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.2: escribir el bloque 2 en `index.html`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```html
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

**Por qué se agrega ahora:** index.html identifica el contenedor root y referencia el punto de entrada. La correspondencia root/main.jsx es indispensable para que la aplicación arranque. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 2; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `index.html` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```html
<!doctype html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Portal de cursos</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

**Explicación causal.** Define el elemento <div id="root"> en el que React montará la interfaz. El script type="module" abre src/main.jsx; sin esa coincidencia, React no llega al navegador. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 1.1: escribir el bloque 1 en `vite.config.js`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```javascript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
```

**Por qué se agrega ahora:** Las importaciones conectan archivos y bibliotecas. Antes de continuar, comprueba que cada ruta relativa coincide con la ubicación del archivo que acabas de crear. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 2; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.2: escribir el bloque 2 en `vite.config.js`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```javascript
export default defineConfig({ plugins: [react()] });
```

**Por qué se agrega ahora:** La configuración del plugin React le indica a Vite cómo transformar JSX y preparar el servidor; no añade una nueva pantalla. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 2; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `vite.config.js` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```javascript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({ plugins: [react()] });
```

**Explicación causal.** El plugin de React permite a Vite procesar JSX y facilita la actualización durante el desarrollo; no es una ruta web ni un componente. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

### Paso 2 — Conectar React al documento

**Antes de escribir.** El navegador no entiende <App /> por sí solo. main.jsx obtiene el elemento root, crea la raíz React y muestra el componente; index.css añade solo estilos legibles. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/main.jsx` y `src/index.css`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 2.1: escribir el bloque 1 en `src/main.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.jsx';
import './index.css';
```

**Por qué se agrega ahora:** Las importaciones conectan archivos y bibliotecas. Antes de continuar, comprueba que cada ruta relativa coincide con la ubicación del archivo que acabas de crear. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 2; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.2: escribir el bloque 2 en `src/main.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

**Por qué se agrega ahora:** createRoot localiza el nodo root y monta App en el documento. Sin el punto de entrada, el HTML solo mostraría un contenedor vacío. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 2; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/main.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.jsx';
import './index.css';

createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

**Explicación causal.** createRoot obtiene #root del HTML e inserta App. React.StrictMode realiza comprobaciones adicionales de desarrollo; en EJ05 BrowserRouter aporta el contexto de navegación. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 2.1: escribir el bloque 1 en `src/index.css`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```css
:root { font-family: system-ui, sans-serif; color: #152240; background: #eff6ff; }
* { box-sizing: border-box; }
body { margin: 0; }
```

**Por qué se agrega ahora:** Las reglas de estilo dan legibilidad y organización al portal. No cambian el estado ni controlan el enrutamiento; su única responsabilidad es visual. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.2: escribir el bloque 2 en `src/index.css`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```css
.contenedor { max-width: 860px; margin: 48px auto; padding: 30px; }
.tarjeta { background: white; border: 1px solid #cbd5e1;
  border-radius: 12px; padding: 18px; margin: 14px 0; }
button { margin: 8px 8px 8px 0; padding: 10px 14px; cursor: pointer; }
```

**Por qué se agrega ahora:** Las reglas de estilo dan legibilidad y organización al portal. No cambian el estado ni controlan el enrutamiento; su única responsabilidad es visual. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.3: escribir el bloque 3 en `src/index.css`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```css
nav { padding: 16px 24px; background: #07152e; display: flex; gap: 20px; }
nav a { color: #1ee7f4; text-decoration: none; font-weight: 700; }
a { color: #075bb1; }
```

**Por qué se agrega ahora:** Las reglas de estilo dan legibilidad y organización al portal. No cambian el estado ni controlan el enrutamiento; su única responsabilidad es visual. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 3; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/index.css` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```css
:root { font-family: system-ui, sans-serif; color: #152240; background: #eff6ff; }
* { box-sizing: border-box; }
body { margin: 0; }
.contenedor { max-width: 860px; margin: 48px auto; padding: 30px; }
.tarjeta { background: white; border: 1px solid #cbd5e1;
  border-radius: 12px; padding: 18px; margin: 14px 0; }
button { margin: 8px 8px 8px 0; padding: 10px 14px; cursor: pointer; }
nav { padding: 16px 24px; background: #07152e; display: flex; gap: 20px; }
nav a { color: #1ee7f4; text-decoration: none; font-weight: 700; }
a { color: #075bb1; }
```

**Explicación causal.** Define colores, tipografía y estilos comunes. No tiene lógica de React; la clase contenedor se asigna mediante className en JSX. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

### Paso 3 — Escribir la primera vista dinámica

**Antes de escribir.** App es una función con mayúscula que devuelve JSX. La variable nombre se interpola entre llaves, className corresponde al atributo CSS y las etiquetas JSX están cerradas. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/App.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 3.1: escribir el bloque 1 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function App() {
  const nombre = 'Lucía';
```

**Por qué se agrega ahora:** La expresión entre llaves inserta el valor real de una variable JavaScript en JSX. No aparecerá el identificador literalmente. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 2; no copies el fragmento otra vez al consultar la versión final.

**Subacción 3.2: escribir el bloque 2 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <p>Bienvenida, {nombre}.</p>
      <p>Aprenderemos React con JSX.</p>
    </main>
  );
}
```

**Por qué se agrega ahora:** La expresión entre llaves inserta el valor real de una variable JavaScript en JSX. No aparecerá el identificador literalmente. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 2; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/App.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
export default function App() {
  const nombre = 'Lucía';

  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <p>Bienvenida, {nombre}.</p>
      <p>Aprenderemos React con JSX.</p>
    </main>
  );
}
```

**Explicación causal.** App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

## Auditoría: archivos completos y dependencias

En esta etapa no debes volver a copiar archivos ya creados que no figuren arriba. Se conservan exactamente desde el ejemplo anterior. Confirma estas responsabilidades nuevas: createRoot obtiene #root del HTML e inserta App. React.StrictMode realiza comprobaciones adicionales de desarrollo; en EJ05 BrowserRouter aporta el contexto de navegación. · App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas.

## Antes de ejecutar: predicción razonada

1. ¿Qué se verá al reemplazar «Lucía» por «Diego»?
2. ¿Aparecerá en la pantalla el valor de nombre o la palabra literal «nombre»?

## Ejecuta, prueba e interpreta

**Terminal de la raíz del proyecto:** si el servidor está detenido, escribe `npm run dev`; si ya sigue activo desde el ejemplo anterior, NO ejecutes una segunda copia. Usa la URL indicada por Vite en el navegador. Después de guardar los cambios, abre otra terminal en la misma carpeta y escribe `npm run build` para verificar que el código pueda compilarse.

**Resultado esperado:** En el navegador se leen «Portal de cursos», «Bienvenida, Lucía.» y «Aprenderemos React con JSX.» La consola de Vite no debe presentar errores de compilación. Si ejecutas `npm run build`, debe finalizar sin errores y crear el directorio `dist/`.

**Interpretación:** Crearemos una aplicación mínima con Vite, conectaremos el punto de entrada de React con App y escribiremos JSX válido. Usaremos className, llaves y etiquetas cerradas para que el mensaje de bienvenida dependa de una variable. La evidencia permite distinguir entre lo que hace la capa visual, lo que controla React y lo que depende del navegador.

## Error controlado, causa y corrección

**Provoca el caso solo si puedes restaurar el código después:** Cambiar className por class, dejar <p> sin cierre o escribir {nombre sin llave final.

**Por qué ocurre:** JSX exige etiquetas bien cerradas y expresiones entre llaves, además de className para clases CSS.

**Cómo corregirlo:** Restaurar className="contenedor", </p> y {nombre}; guardar App.jsx y observar la recompilación de Vite. Repite la prueba y confirma que el comportamiento vuelve a ser el esperado.

## T01 — Tarea espejo: Agregar una segunda expresión JSX

**Enunciado:** En src/App.jsx crea const tema = "JSX" y reemplaza el texto estático del tercer párrafo por «Tema de hoy: {tema}.». No agregues librerías.

**Archivos permitidos:** `src/App.jsx`.

**Restricción:** conserva los demás componentes y funcionalidades de EJ01. No sustituyas la aplicación entera por otra distinta.

**Pista de resolución:** localiza el bloque ya explicado que implementa la misma responsabilidad; agrega o modifica solo ese bloque. Prueba primero el comportamiento original, predice el nuevo, modifica y vuelve a comparar.

**Evidencia y criterio de corrección:** Aparece «Tema de hoy: JSX.» y se mantiene el saludo a Lucía. Adjunta una captura legible y el texto de los archivos que modificaste. Ejecuta `npm run build`; explica oralmente qué cambió y qué quedó igual. La solución completa de esta tarea está reservada a la guía docente.


---

# EJ02 — Reutilizar una interfaz mediante componentes y props

## Comprendamos los conceptos nuevos antes de escribir EJ02

**¿Qué es un componente?** Es una pieza reutilizable de la interfaz. En React moderno suele ser una función JavaScript cuyo nombre empieza con mayúscula y que devuelve JSX. `Saludo.jsx` definirá una tarjeta una sola vez; desde `App.jsx` podremos utilizarla varias veces. Así, cuando cambiemos el diseño de la tarjeta, modificaremos un componente en lugar de editar cada copia.

**¿Qué son las props?** Son datos que un componente *padre* le entrega a un componente *hijo*. En `<Saludo nombre="Lucía" curso="React" />`, `nombre` y `curso` son props. En `function Saludo({ nombre, curso })` se extraen esos valores del objeto de props recibido. Por eso otro `<Saludo nombre="Diego" curso="JavaScript" />` puede usar el mismo archivo y mostrar otro contenido.

**¿Para qué sirven `export default` e `import`?** `export default function Saludo...` deja disponible el componente desde su archivo. `import Saludo from './components/Saludo.jsx'` lo incorpora a `App.jsx`. La ruta del import se resuelve desde el archivo que escribe el import; si no coincide con la estructura real, Vite informa que no encuentra el módulo.

**Diferencia clave:** **props** son datos recibidos desde fuera; **estado** es información que un componente recuerda y puede modificar mediante React. Por ahora `Saludo` solo presenta props; el contador con estado llegará en EJ03. **Pregunta para anticipar:** ¿tenemos que copiar `Saludo.jsx` dos veces para mostrar dos estudiantes? No: basta con invocar el mismo componente dos veces con props diferentes.

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

Repetir a mano el mismo bloque HTML para cada estudiante vuelve difícil mantener el portal. Extraeremos una tarjeta a Saludo.jsx, declararemos props nombre y curso, exportaremos el componente y lo importaremos desde App. Dos usos diferentes producirán dos tarjetas sin copiar la estructura. Deben mostrarse tarjetas de Lucía/React y Diego/JavaScript. Aprenderemos a verificar la consecuencia ejecutando el servidor local y comprobando el estado visible, antes de cambiar archivos adicionales.

## ¿Qué problema concreto resolveremos?

Repetir a mano el mismo bloque HTML para cada estudiante vuelve difícil mantener el portal. La propuesta será modificar únicamente la responsabilidad necesaria de la aplicación del portal de cursos y comprobar su consecuencia real en el navegador.

**Punto de partida:** conserva todos los archivos y el servidor de EJ01. Los archivos no listados se mantienen sin cambios.

**Archivos de este ejemplo:** `src/components/Saludo.jsx`, `src/App.jsx`.

### Paso 1 — Extraer una tarjeta con parámetros

**Antes de escribir.** Saludo recibe un objeto de props y desestructura nombre y curso. El JSX devuelve una section y muestra los valores dados por el padre, sin mantener copias por estudiante. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/components/Saludo.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 1.1: escribir el bloque 1 en `src/components/Saludo.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function Saludo({ nombre, curso }) {
```

**Por qué se agrega ahora:** Saludo recibe datos del padre mediante props; la desestructuración hace disponibles nombre y curso, que no son estados locales. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 2; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.2: escribir el bloque 2 en `src/components/Saludo.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <section className="tarjeta">
      <h2>Hola, {nombre}</h2>
      <p>Curso: {curso}</p>
    </section>
  );
}
```

**Por qué se agrega ahora:** La expresión entre llaves inserta el valor real de una variable JavaScript en JSX. No aparecerá el identificador literalmente. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 2; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/components/Saludo.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
export default function Saludo({ nombre, curso }) {
  return (
    <section className="tarjeta">
      <h2>Hola, {nombre}</h2>
      <p>Curso: {curso}</p>
    </section>
  );
}
```

**Explicación causal.** Las props nombre y curso son valores enviados por el padre. La desestructuración evita repetir props.nombre y props.curso; export default permite importación directa. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

### Paso 2 — Usar el componente dos veces

**Antes de escribir.** App importa Saludo desde la ruta exacta ./components/Saludo.jsx. Cada elemento <Saludo /> pasa props diferentes; React genera una tarjeta por instancia. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/App.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 2.1: escribir el bloque 1 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import Saludo from './components/Saludo.jsx';
```

**Por qué se agrega ahora:** Las importaciones conectan archivos y bibliotecas. Antes de continuar, comprueba que cada ruta relativa coincide con la ubicación del archivo que acabas de crear. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.2: escribir el bloque 2 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function App() {
```

**Por qué se agrega ahora:** App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.3: escribir el bloque 3 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <Saludo nombre="Lucía" curso="React" />
      <Saludo nombre="Diego" curso="JavaScript" />
    </main>
  );
}
```

**Por qué se agrega ahora:** JSX usa className para conectar el elemento con las reglas CSS. Los componentes retornan una estructura bien anidada y con etiquetas cerradas. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 3; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/App.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import Saludo from './components/Saludo.jsx';

export default function App() {
  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <Saludo nombre="Lucía" curso="React" />
      <Saludo nombre="Diego" curso="JavaScript" />
    </main>
  );
}
```

**Explicación causal.** App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

## Auditoría: archivos completos y dependencias

En esta etapa no debes volver a copiar archivos ya creados que no figuren arriba. Se conservan exactamente desde el ejemplo anterior. Confirma estas responsabilidades nuevas: Las props nombre y curso son valores enviados por el padre. La desestructuración evita repetir props.nombre y props.curso; export default permite importación directa. · App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas.

## Antes de ejecutar: predicción razonada

1. ¿Cuántas tarjetas habrá con dos invocaciones de Saludo?
2. ¿Cambiar curso="React" en una instancia afectará a la otra?

## Ejecuta, prueba e interpreta

**Terminal de la raíz del proyecto:** si el servidor está detenido, escribe `npm run dev`; si ya sigue activo desde el ejemplo anterior, NO ejecutes una segunda copia. Usa la URL indicada por Vite en el navegador. Después de guardar los cambios, abre otra terminal en la misma carpeta y escribe `npm run build` para verificar que el código pueda compilarse.

**Resultado esperado:** Se ven exactamente dos tarjetas: «Hola, Lucía / Curso: React» y «Hola, Diego / Curso: JavaScript». La consola de Vite no debe presentar errores de compilación. Si ejecutas `npm run build`, debe finalizar sin errores y crear el directorio `dist/`.

**Interpretación:** Extraeremos una tarjeta a Saludo.jsx, declararemos props nombre y curso, exportaremos el componente y lo importaremos desde App. Dos usos diferentes producirán dos tarjetas sin copiar la estructura. La evidencia permite distinguir entre lo que hace la capa visual, lo que controla React y lo que depende del navegador.

## Error controlado, causa y corrección

**Provoca el caso solo si puedes restaurar el código después:** Escribir import Saludo from "./Saludo.jsx" dentro de src/App.jsx.

**Por qué ocurre:** El archivo se encuentra en src/components, por lo que el import relativo no encuentra el módulo.

**Cómo corregirlo:** Usar exactamente ./components/Saludo.jsx y confirmar que se llama Saludo.jsx con S mayúscula. Repite la prueba y confirma que el comportamiento vuelve a ser el esperado.

## T02 — Tarea espejo: Mostrar una tercera tarjeta usando props

**Enunciado:** En src/App.jsx agrega un tercer Saludo para Ana y Node.js. No dupliques el código de Saludo.jsx.

**Archivos permitidos:** `src/App.jsx`.

**Restricción:** conserva los demás componentes y funcionalidades de EJ02. No sustituyas la aplicación entera por otra distinta.

**Pista de resolución:** localiza el bloque ya explicado que implementa la misma responsabilidad; agrega o modifica solo ese bloque. Prueba primero el comportamiento original, predice el nuevo, modifica y vuelve a comparar.

**Evidencia y criterio de corrección:** Se muestran tres tarjetas y Saludo.jsx sigue definiéndose una sola vez. Adjunta una captura legible y el texto de los archivos que modificaste. Ejecuta `npm run build`; explica oralmente qué cambió y qué quedó igual. La solución completa de esta tarea está reservada a la guía docente.


---

# EJ03 — Actualizar la interfaz con useState

## Comprendamos los conceptos nuevos antes de escribir EJ03

**¿Por qué necesitamos estado?** Hasta ahora mostramos datos recibidos por props, pero necesitamos que un clic cambie un número visible. En un componente React, una variable normal modificada manualmente no notifica a React que debe actualizar la pantalla y puede reinicializarse en otro render. **`useState`** es un Hook que permite a React *recordar* un valor entre renderizados y solicitar una nueva representación cuando ese valor cambia.

**¿Qué es un Hook?** Una función especial de React, reconocible por el prefijo `use`, que añade capacidades al componente funcional. `useState(0)` devuelve dos elementos: `[contador, setContador]`. `contador` contiene el valor actual (empieza en 0) y `setContador` solicita cambiarlo. No son dos contadores: el segundo nombre es la función que actualiza el primero.

**¿Por qué escribimos `setContador(prev => prev + 1)`?** Porque el nuevo valor depende del anterior. `prev` es el valor previo que React facilita cuando procesa la actualización; la función calcula `prev + 1`. `setContador(0)` fija explícitamente cero, por eso sirve para Reiniciar. **¿Qué hace `onClick={incrementar}`?** Entrega la función al botón para ejecutarla cuando haya un clic. `onClick={incrementar()}` la ejecutaría durante el render y no es la intención.

**Render o renderizado** significa que React vuelve a evaluar qué interfaz corresponde al estado actual; normalmente actualiza el DOM necesario, **no recarga el documento completo**. **Prueba mental:** después de pulsar Incrementar dos veces, esperamos 2; al pulsar Reiniciar, 0. Si el número cambia en pantalla sin refrescar el navegador, hemos observado estado y renderizado.

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

Un número declarado como variable local no tiene un mecanismo propio para mantener y representar su cambio entre renders. Crearemos Contador.jsx, importaremos useState, inicializaremos contador en cero, actualizaremos con el valor anterior y conectaremos los botones Incrementar y Reiniciar. El contador cambia 0 → 1 → 2 y puede volver a 0 sin recargar. Aprenderemos a verificar la consecuencia ejecutando el servidor local y comprobando el estado visible, antes de cambiar archivos adicionales.

## ¿Qué problema concreto resolveremos?

Un número declarado como variable local no tiene un mecanismo propio para mantener y representar su cambio entre renders. La propuesta será modificar únicamente la responsabilidad necesaria de la aplicación del portal de cursos y comprobar su consecuencia real en el navegador.

**Punto de partida:** conserva todos los archivos y el servidor de EJ02. Los archivos no listados se mantienen sin cambios.

**Archivos de este ejemplo:** `src/components/Contador.jsx`, `src/App.jsx`.

### Paso 1 — Crear estado y operaciones

**Antes de escribir.** useState(0) declara un valor conservado por React y un setter. El incremento funcional prev => prev + 1 usa el valor previo; reiniciar fija cero. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/components/Contador.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 1.1: escribir el bloque 1 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import { useState } from 'react';
```

**Por qué se agrega ahora:** useState guarda el contador entre renders; setContador solicita a React un nuevo valor. La forma prev => prev + 1 toma como base el estado anterior. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 5; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.2: escribir el bloque 2 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function Contador() {
```

**Por qué se agrega ahora:** El Hook guarda un valor entre renders. Los handlers se pasan a onClick como referencias, no se ejecutan al crear JSX. En EJ04 el efecto sincroniza la pestaña. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 5; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.3: escribir el bloque 3 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  const [contador, setContador] = useState(0);
```

**Por qué se agrega ahora:** useState guarda el contador entre renders; setContador solicita a React un nuevo valor. La forma prev => prev + 1 toma como base el estado anterior. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 5; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.4: escribir el bloque 4 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  const incrementar = () => setContador(prev => prev + 1);
  const reiniciar = () => setContador(0);
```

**Por qué se agrega ahora:** useState guarda el contador entre renders; setContador solicita a React un nuevo valor. La forma prev => prev + 1 toma como base el estado anterior. **Estado acumulado:** el archivo contiene los bloques 1 a 4 de 5; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.5: escribir el bloque 5 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <section className="tarjeta">
      <h2>Interacciones: {contador}</h2>
      <button onClick={incrementar}>Incrementar</button>
      <button onClick={reiniciar}>Reiniciar</button>
    </section>
  );
}
```

**Por qué se agrega ahora:** JSX usa className para conectar el elemento con las reglas CSS. Los componentes retornan una estructura bien anidada y con etiquetas cerradas. **Estado acumulado:** el archivo contiene los bloques 1 a 5 de 5; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/components/Contador.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import { useState } from 'react';

export default function Contador() {
  const [contador, setContador] = useState(0);
  const incrementar = () => setContador(prev => prev + 1);
  const reiniciar = () => setContador(0);

  return (
    <section className="tarjeta">
      <h2>Interacciones: {contador}</h2>
      <button onClick={incrementar}>Incrementar</button>
      <button onClick={reiniciar}>Reiniciar</button>
    </section>
  );
}
```

**Explicación causal.** El Hook guarda un valor entre renders. Los handlers se pasan a onClick como referencias, no se ejecutan al crear JSX. En EJ04 el efecto sincroniza la pestaña. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

### Paso 2 — Integrar estado con la vista

**Antes de escribir.** App importa y representa <Contador /> después de las tarjetas. onClick recibe funciones; la interfaz muestra el valor actualizado en cada render. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/App.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 2.1: escribir el bloque 1 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import Saludo from './components/Saludo.jsx';
import Contador from './components/Contador.jsx';
```

**Por qué se agrega ahora:** Las importaciones conectan archivos y bibliotecas. Antes de continuar, comprueba que cada ruta relativa coincide con la ubicación del archivo que acabas de crear. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.2: escribir el bloque 2 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function App() {
```

**Por qué se agrega ahora:** App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.3: escribir el bloque 3 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <Saludo nombre="Lucía" curso="React" />
      <Saludo nombre="Diego" curso="JavaScript" />
      <Contador />
    </main>
  );
}
```

**Por qué se agrega ahora:** JSX usa className para conectar el elemento con las reglas CSS. Los componentes retornan una estructura bien anidada y con etiquetas cerradas. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 3; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/App.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import Saludo from './components/Saludo.jsx';
import Contador from './components/Contador.jsx';

export default function App() {
  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <Saludo nombre="Lucía" curso="React" />
      <Saludo nombre="Diego" curso="JavaScript" />
      <Contador />
    </main>
  );
}
```

**Explicación causal.** App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

## Auditoría: archivos completos y dependencias

En esta etapa no debes volver a copiar archivos ya creados que no figuren arriba. Se conservan exactamente desde el ejemplo anterior. Confirma estas responsabilidades nuevas: El Hook guarda un valor entre renders. Los handlers se pasan a onClick como referencias, no se ejecutan al crear JSX. En EJ04 el efecto sincroniza la pestaña. · App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas.

## Antes de ejecutar: predicción razonada

1. Después de dos clics en Incrementar, ¿qué número aparece?
2. ¿Qué ocurrirá al pulsar Reiniciar?

## Ejecuta, prueba e interpreta

**Terminal de la raíz del proyecto:** si el servidor está detenido, escribe `npm run dev`; si ya sigue activo desde el ejemplo anterior, NO ejecutes una segunda copia. Usa la URL indicada por Vite en el navegador. Después de guardar los cambios, abre otra terminal en la misma carpeta y escribe `npm run build` para verificar que el código pueda compilarse.

**Resultado esperado:** El valor inicial es 0; Incrementar lo cambia a 1, 2…; Reiniciar vuelve a 0 sin refrescar la página. La consola de Vite no debe presentar errores de compilación. Si ejecutas `npm run build`, debe finalizar sin errores y crear el directorio `dist/`.

**Interpretación:** Crearemos Contador.jsx, importaremos useState, inicializaremos contador en cero, actualizaremos con el valor anterior y conectaremos los botones Incrementar y Reiniciar. La evidencia permite distinguir entre lo que hace la capa visual, lo que controla React y lo que depende del navegador.

## Error controlado, causa y corrección

**Provoca el caso solo si puedes restaurar el código después:** Escribir onClick={incrementar()} en lugar de onClick={incrementar}.

**Por qué ocurre:** Con paréntesis se invoca la función durante el render y puede provocar renderizados repetidos.

**Cómo corregirlo:** Pasar la referencia onClick={incrementar}; al interactuar React la invoca. Repite la prueba y confirma que el comportamiento vuelve a ser el esperado.

## T03 — Tarea espejo: Disminuir sin cruzar el límite cero

**Enunciado:** En src/components/Contador.jsx agrega un botón «Disminuir sin negativos» y una función que reduzca en uno, pero nunca por debajo de 0. Mantén Incrementar y Reiniciar.

**Archivos permitidos:** `src/components/Contador.jsx`.

**Restricción:** conserva los demás componentes y funcionalidades de EJ03. No sustituyas la aplicación entera por otra distinta.

**Pista de resolución:** localiza el bloque ya explicado que implementa la misma responsabilidad; agrega o modifica solo ese bloque. Prueba primero el comportamiento original, predice el nuevo, modifica y vuelve a comparar.

**Evidencia y criterio de corrección:** 0 → Disminuir → 0; 2 → Disminuir → 1. Adjunta una captura legible y el texto de los archivos que modificaste. Ejecuta `npm run build`; explica oralmente qué cambió y qué quedó igual. La solución completa de esta tarea está reservada a la guía docente.


---

# EJ04 — Sincronizar el título del navegador con useEffect

## Comprendamos los conceptos nuevos antes de escribir EJ04

**¿Qué es `useEffect` y para qué sirve?** Es un Hook que permite **sincronizar un componente de React con un sistema externo**. En este ejercicio ese sistema es el navegador: modificaremos `document.title`, que determina el texto de la pestaña. El contador pertenece al estado de React; el título de la pestaña es una propiedad del documento. Necesitamos mantener ambos coherentes.

**Lectura de la sintaxis:** en `useEffect(() => { document.title = `Interacciones: ${contador}`; }, [contador])`, la primera función explica **qué sincronizar** y `[contador]` enumera **qué valor utiliza el efecto y cuándo debe actualizarse**. Tras el render inicial se establece el título y, cuando `contador` cambia, el efecto vuelve a sincronizarlo. Las dependencias no son botones ni eventos: describen la relación con los valores reactivos.

**Diferencia entre estado y efecto:** `useState` almacena y actualiza el valor que muestra el componente; `useEffect` comunica ese valor a algo que React no controla directamente. Para mostrar simplemente «Interacciones: N» dentro del JSX **no necesitamos `useEffect`**. Tampoco debemos poner Hooks dentro de `if`, bucles o funciones de evento: se declaran en el nivel superior del componente.

**Nota importante de desarrollo:** React puede ejecutar comprobaciones adicionales de efectos con `StrictMode`; por eso no se debe afirmar que un efecto con `[]` «se ejecuta exactamente una vez» en cualquier circunstancia. Nuestro ejemplo depende de `[contador]` y solo asigna el título, de modo que repetir esa asignación no genera un efecto indeseado. **Comprobación:** compara el número de la tarjeta con el título real de la pestaña, no con una etiqueta dentro de la página.

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

El contador ya actualiza la pantalla, pero el título de la pestaña sigue siendo el texto inicial del documento. Agregaremos useEffect a Contador.jsx para sincronizar document.title con el estado contador. Declararemos [contador] como dependencia y observaremos el título después de pulsar los botones. La pestaña muestra «Interacciones: 0», luego «Interacciones: 1» y vuelve a cero. Aprenderemos a verificar la consecuencia ejecutando el servidor local y comprobando el estado visible, antes de cambiar archivos adicionales.

## ¿Qué problema concreto resolveremos?

El contador ya actualiza la pantalla, pero el título de la pestaña sigue siendo el texto inicial del documento. La propuesta será modificar únicamente la responsabilidad necesaria de la aplicación del portal de cursos y comprobar su consecuencia real en el navegador.

**Punto de partida:** conserva todos los archivos y el servidor de EJ03. Los archivos no listados se mantienen sin cambios.

**Archivos de este ejemplo:** `src/components/Contador.jsx`.

### Paso 1 — Agregar un efecto dependiente del contador

**Antes de escribir.** El efecto sincroniza una API externa del navegador: document.title. Se importa useEffect, se llama al nivel superior, y [contador] indica el valor que debe observarse. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/components/Contador.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 1.1: escribir el bloque 1 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import { useEffect, useState } from 'react';
```

**Por qué se agrega ahora:** useEffect expresa sincronización externa: document.title pertenece al navegador. La lista [contador] vincula la actualización del título con el valor del estado; no altera el propio contador. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 6; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.2: escribir el bloque 2 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function Contador() {
```

**Por qué se agrega ahora:** El Hook guarda un valor entre renders. Los handlers se pasan a onClick como referencias, no se ejecutan al crear JSX. En EJ04 el efecto sincroniza la pestaña. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 6; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.3: escribir el bloque 3 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  const [contador, setContador] = useState(0);
```

**Por qué se agrega ahora:** useState guarda el contador entre renders; setContador solicita a React un nuevo valor. La forma prev => prev + 1 toma como base el estado anterior. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 6; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.4: escribir el bloque 4 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  const incrementar = () => setContador(prev => prev + 1);
  const reiniciar = () => setContador(0);
```

**Por qué se agrega ahora:** useState guarda el contador entre renders; setContador solicita a React un nuevo valor. La forma prev => prev + 1 toma como base el estado anterior. **Estado acumulado:** el archivo contiene los bloques 1 a 4 de 6; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.5: escribir el bloque 5 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  useEffect(() => {
    document.title = `Interacciones: ${contador}`;
  }, [contador]);
```

**Por qué se agrega ahora:** useEffect expresa sincronización externa: document.title pertenece al navegador. La lista [contador] vincula la actualización del título con el valor del estado; no altera el propio contador. **Estado acumulado:** el archivo contiene los bloques 1 a 5 de 6; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.6: escribir el bloque 6 en `src/components/Contador.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <section className="tarjeta">
      <h2>Interacciones: {contador}</h2>
      <button onClick={incrementar}>Incrementar</button>
      <button onClick={reiniciar}>Reiniciar</button>
    </section>
  );
}
```

**Por qué se agrega ahora:** JSX usa className para conectar el elemento con las reglas CSS. Los componentes retornan una estructura bien anidada y con etiquetas cerradas. **Estado acumulado:** el archivo contiene los bloques 1 a 6 de 6; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/components/Contador.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import { useEffect, useState } from 'react';

export default function Contador() {
  const [contador, setContador] = useState(0);
  const incrementar = () => setContador(prev => prev + 1);
  const reiniciar = () => setContador(0);

  useEffect(() => {
    document.title = `Interacciones: ${contador}`;
  }, [contador]);

  return (
    <section className="tarjeta">
      <h2>Interacciones: {contador}</h2>
      <button onClick={incrementar}>Incrementar</button>
      <button onClick={reiniciar}>Reiniciar</button>
    </section>
  );
}
```

**Explicación causal.** El Hook guarda un valor entre renders. Los handlers se pasan a onClick como referencias, no se ejecutan al crear JSX. En EJ04 el efecto sincroniza la pestaña. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

## Auditoría: archivos completos y dependencias

En esta etapa no debes volver a copiar archivos ya creados que no figuren arriba. Se conservan exactamente desde el ejemplo anterior. Confirma estas responsabilidades nuevas: El Hook guarda un valor entre renders. Los handlers se pasan a onClick como referencias, no se ejecutan al crear JSX. En EJ04 el efecto sincroniza la pestaña.

## Antes de ejecutar: predicción razonada

1. Si el contador aumenta de 0 a 1, ¿qué texto mostrará la pestaña?
2. ¿[contador] es una orden de cambiar el contador o una lista de dependencias?

## Ejecuta, prueba e interpreta

**Terminal de la raíz del proyecto:** si el servidor está detenido, escribe `npm run dev`; si ya sigue activo desde el ejemplo anterior, NO ejecutes una segunda copia. Usa la URL indicada por Vite en el navegador. Después de guardar los cambios, abre otra terminal en la misma carpeta y escribe `npm run build` para verificar que el código pueda compilarse.

**Observación externa:** mira el título de la pestaña del navegador, no solamente el encabezado de la página. Incrementa y reinicia. En modo desarrollo, StrictMode puede ejecutar efectos adicionales para comprobarlos; no interpretes `[]` como garantía universal de «una sola vez».

**Resultado esperado:** El título de la pestaña pasa de «Interacciones: 0» a «Interacciones: 1» tras el clic, y viceversa tras Reiniciar. La consola de Vite no debe presentar errores de compilación. Si ejecutas `npm run build`, debe finalizar sin errores y crear el directorio `dist/`.

**Interpretación:** Agregaremos useEffect a Contador.jsx para sincronizar document.title con el estado contador. Declararemos [contador] como dependencia y observaremos el título después de pulsar los botones. La evidencia permite distinguir entre lo que hace la capa visual, lo que controla React y lo que depende del navegador.

## Error controlado, causa y corrección

**Provoca el caso solo si puedes restaurar el código después:** Eliminar [contador] del useEffect o mover el Hook a un if.

**Por qué ocurre:** Sin dependencias se evalúa tras cada render; los Hooks no se llaman condicionalmente.

**Cómo corregirlo:** Conservar useEffect al nivel superior del componente y declarar [contador]. Repite la prueba y confirma que el comportamiento vuelve a ser el esperado.

## T04 — Tarea espejo: Cambiar el formato del título sincronizado

**Enunciado:** En src/components/Contador.jsx conserva [contador] y modifica el efecto para mostrar «Lideratec | Clics: N». No alteres la actualización del contador.

**Archivos permitidos:** `src/components/Contador.jsx`.

**Restricción:** conserva los demás componentes y funcionalidades de EJ04. No sustituyas la aplicación entera por otra distinta.

**Pista de resolución:** localiza el bloque ya explicado que implementa la misma responsabilidad; agrega o modifica solo ese bloque. Prueba primero el comportamiento original, predice el nuevo, modifica y vuelve a comparar.

**Evidencia y criterio de corrección:** La pestaña muestra «Lideratec | Clics: 1» después de incrementar una vez. Adjunta una captura legible y el texto de los archivos que modificaste. Ejecuta `npm run build`; explica oralmente qué cambió y qué quedó igual. La solución completa de esta tarea está reservada a la guía docente.


---

# EJ05 — Navegar entre Inicio y Acerca sin recargar la SPA

## Comprendamos los conceptos nuevos antes de escribir EJ05

**¿Qué es una SPA y qué papel cumple un router?** En una SPA, varias vistas comparten el mismo documento principal. **React Router** es una biblioteca que conecta la URL del navegador con el componente que debe mostrarse. No reemplaza a React ni a Vite: añade navegación a nuestra interfaz. En el portal, `/` significa **Inicio** y `/about` significa **Acerca**.

**¿Qué significa `BrowserRouter`?** Es el componente que rodea la app y le facilita a React Router el contexto de navegación basado en el historial del navegador. Por eso lo colocamos en `src/main.jsx` *alrededor* de `<App />`. **`Routes`** agrupa las alternativas, y cada **`Route`** asocia `path` (dirección) con `element` (componente que se renderiza), por ejemplo `path="/about"` con `<Acerca />`.

**¿Qué diferencia existe entre `<a href>` y `<Link to>`?** Un enlace HTML tradicional a otro documento suele iniciar una navegación completa del navegador; en cambio, **`Link`** de React Router está pensado para navegación interna entre rutas de nuestra aplicación, actualiza URL y vista sin exigir una recarga completa en el uso habitual. Los enlaces externos siguen pudiendo utilizar `<a href>`. Una navegación SPA **no significa que nunca haya solicitudes de red**: significa que no es necesario sustituir el documento entero para ir de Inicio a Acerca.

**¿Qué vamos a instalar exactamente?** En esta versión del laboratorio usamos React Router 7 en modo declarativo, con el paquete **`react-router`**. En consecuencia `BrowserRouter`, `Routes`, `Route`, `Link` y después `useParams` se importan de `'react-router'`. No mezcles estos imports con una instalación distinta de `react-router-dom`. Ejecutaremos `npm install react-router@7.18.4` **al llegar al paso de EJ05**, no antes de EJ01.

**Prueba conceptual y visible:** en Inicio, pulsa **Acerca** y observa (1) la URL termina en `/about`, (2) el área principal ahora dice «Acerca» y (3) la aplicación mantiene la navegación sin reemplazar el documento completo. **Precisión:** `npm run dev` permite probar esas rutas durante desarrollo; una publicación en servidor real necesita que dicho servidor permita devolver la entrada SPA al abrir directamente una ruta como `/about`.

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

Una aplicación con una sola vista no permite explorar secciones distintas mediante la URL. Instalaremos React Router 7, envolveremos App en BrowserRouter, crearemos Inicio y Acerca, incorporaremos Navbar con Link y declararemos Routes/Route en App. Al pulsar Acerca, la URL cambia a /about y se visualiza Acerca sin recarga completa. Aprenderemos a verificar la consecuencia ejecutando el servidor local y comprobando el estado visible, antes de cambiar archivos adicionales.

## ¿Qué problema concreto resolveremos?

Una aplicación con una sola vista no permite explorar secciones distintas mediante la URL. La propuesta será modificar únicamente la responsabilidad necesaria de la aplicación del portal de cursos y comprobar su consecuencia real en el navegador.

**Punto de partida:** conserva todos los archivos y el servidor de EJ04. Los archivos no listados se mantienen sin cambios.

**Archivos de este ejemplo:** `package.json`, `src/main.jsx`, `src/components/Navbar.jsx`, `src/pages/Inicio.jsx`, `src/pages/Acerca.jsx`, `src/App.jsx`.

**Instalación obligatoria de React Router, exactamente ahora.** En una segunda terminal ubicada en la raíz `react-s09-lab` ejecuta:

```bash
npm install react-router@7.18.4
```
Este comando añade el enrutador. No lo ejecutamos antes porque EJ01–EJ04 aún no utilizan rutas. Verifica con `npm ls react-router` que aparece una versión 7.x; luego continúa modificando los archivos indicados. Todos los imports del enrutador usarán `react-router`, sin mezclar con `react-router-dom`.

### Paso 1 — Instalar el enrutador y aportar su contexto

**Antes de escribir.** react-router se agrega al package.json; BrowserRouter envuelve App en main.jsx. Sin ese contexto, Link y Routes no pueden coordinarse con la URL. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `package.json` y `src/main.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 1.1: escribir el bloque 1 en `package.json`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```json
{
  "name": "react-s09-lab",
  "private": true,
  "version": "1.0.0",
  "type": "module",
```

**Por qué se agrega ahora:** Es un manifiesto JSON. scripts indica a npm qué ejecutar; dependencies son bibliotecas de la aplicación y devDependencies herramientas de compilación. No añadas comentarios dentro del JSON. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 4; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.2: escribir el bloque 2 en `package.json`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```json
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
```

**Por qué se agrega ahora:** package.json define scripts de npm: dev ejecuta Vite, build genera dist y preview muestra el build. Estos son comandos con propósitos distintos. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 4; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.3: escribir el bloque 3 en `package.json`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```json
  "dependencies": {
    "react": "^19.2.0",
    "react-dom": "^19.2.0",
    "react-router": "^7.18.4"
  },
```

**Por qué se agrega ahora:** dependencies aporta bibliotecas para la aplicación; devDependencies contiene las herramientas de compilación. No escribas comentarios dentro del JSON. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 4; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.4: escribir el bloque 4 en `package.json`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```json
  "devDependencies": {
    "vite": "^7.3.1",
    "@vitejs/plugin-react": "^5.1.4"
  }
}
```

**Por qué se agrega ahora:** dependencies aporta bibliotecas para la aplicación; devDependencies contiene las herramientas de compilación. No escribas comentarios dentro del JSON. **Estado acumulado:** el archivo contiene los bloques 1 a 4 de 4; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `package.json` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```json
{
  "name": "react-s09-lab",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^19.2.0",
    "react-dom": "^19.2.0",
    "react-router": "^7.18.4"
  },
  "devDependencies": {
    "vite": "^7.3.1",
    "@vitejs/plugin-react": "^5.1.4"
  }
}
```

**Explicación causal.** Es un manifiesto JSON. scripts indica a npm qué ejecutar; dependencies son bibliotecas de la aplicación y devDependencies herramientas de compilación. No añadas comentarios dentro del JSON. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 1.1: escribir el bloque 1 en `src/main.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import { BrowserRouter } from 'react-router';
import App from './App.jsx';
import './index.css';
```

**Por qué se agrega ahora:** BrowserRouter establece el contexto para que la dirección del navegador y los componentes declarativos puedan coordinarse. Debe envolver a App. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 2; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.2: escribir el bloque 2 en `src/main.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

**Por qué se agrega ahora:** BrowserRouter establece el contexto para que la dirección del navegador y los componentes declarativos puedan coordinarse. Debe envolver a App. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 2; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/main.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import { BrowserRouter } from 'react-router';
import App from './App.jsx';
import './index.css';

createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

**Explicación causal.** createRoot obtiene #root del HTML e inserta App. React.StrictMode realiza comprobaciones adicionales de desarrollo; en EJ05 BrowserRouter aporta el contexto de navegación. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

### Paso 2 — Separar las vistas de navegación

**Antes de escribir.** Inicio conserva el contenido anterior; Acerca es una segunda pantalla y Navbar define enlaces Link. El comportamiento de Link difiere de <a href> para navegación interna SPA. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/pages/Inicio.jsx` y `src/pages/Acerca.jsx` y `src/components/Navbar.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 2.1: escribir el bloque 1 en `src/pages/Inicio.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import Saludo from '../components/Saludo.jsx';
import Contador from '../components/Contador.jsx';
```

**Por qué se agrega ahora:** Las importaciones conectan archivos y bibliotecas. Antes de continuar, comprueba que cada ruta relativa coincide con la ubicación del archivo que acabas de crear. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.2: escribir el bloque 2 en `src/pages/Inicio.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function Inicio() {
```

**Por qué se agrega ahora:** Inicio agrupa el contenido original para mostrarlo solo cuando la ruta / sea activa. Los enlaces a curso aparecen únicamente en EJ06. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.3: escribir el bloque 3 en `src/pages/Inicio.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <Saludo nombre="Lucía" curso="React" />
      <Saludo nombre="Diego" curso="JavaScript" />
      <Contador />
    </main>
  );
}
```

**Por qué se agrega ahora:** JSX usa className para conectar el elemento con las reglas CSS. Los componentes retornan una estructura bien anidada y con etiquetas cerradas. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 3; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/pages/Inicio.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import Saludo from '../components/Saludo.jsx';
import Contador from '../components/Contador.jsx';

export default function Inicio() {
  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <Saludo nombre="Lucía" curso="React" />
      <Saludo nombre="Diego" curso="JavaScript" />
      <Contador />
    </main>
  );
}
```

**Explicación causal.** Inicio agrupa el contenido original para mostrarlo solo cuando la ruta / sea activa. Los enlaces a curso aparecen únicamente en EJ06. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 2.1: escribir el bloque 1 en `src/pages/Acerca.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function Acerca() {
```

**Por qué se agrega ahora:** Acerca es una vista distinta, no un segundo documento HTML; React muestra su JSX al seleccionar la URL /about. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 2; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.2: escribir el bloque 2 en `src/pages/Acerca.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <main className="contenedor">
      <h1>Acerca</h1>
      <p>Esta vista cambia sin recargar el documento completo.</p>
    </main>
  );
}
```

**Por qué se agrega ahora:** JSX usa className para conectar el elemento con las reglas CSS. Los componentes retornan una estructura bien anidada y con etiquetas cerradas. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 2; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/pages/Acerca.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
export default function Acerca() {
  return (
    <main className="contenedor">
      <h1>Acerca</h1>
      <p>Esta vista cambia sin recargar el documento completo.</p>
    </main>
  );
}
```

**Explicación causal.** Acerca es una vista distinta, no un segundo documento HTML; React muestra su JSX al seleccionar la URL /about. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 2.1: escribir el bloque 1 en `src/components/Navbar.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import { Link } from 'react-router';
```

**Por qué se agrega ahora:** Link declara un destino to dentro de la SPA. React Router modifica la ubicación en el cliente sin solicitar otra página HTML completa. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.2: escribir el bloque 2 en `src/components/Navbar.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function Navbar() {
```

**Por qué se agrega ahora:** Cada Link tiene un destino to distinto. Este componente no dibuja una página; deja que el enrutador cambie la ruta del cliente. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.3: escribir el bloque 3 en `src/components/Navbar.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <nav>
      <Link to="/">Inicio</Link>
      <Link to="/about">Acerca</Link>
    </nav>
  );
}
```

**Por qué se agrega ahora:** Link declara un destino to dentro de la SPA. React Router modifica la ubicación en el cliente sin solicitar otra página HTML completa. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 3; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/components/Navbar.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import { Link } from 'react-router';

export default function Navbar() {
  return (
    <nav>
      <Link to="/">Inicio</Link>
      <Link to="/about">Acerca</Link>
    </nav>
  );
}
```

**Explicación causal.** Cada Link tiene un destino to distinto. Este componente no dibuja una página; deja que el enrutador cambie la ruta del cliente. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

### Paso 3 — Asociar URL y componente

**Antes de escribir.** App declara Routes y dos Route. path="/" muestra Inicio y path="/about" muestra Acerca. La barra Navbar permanece fuera de Routes para aparecer en ambas vistas. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/App.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 3.1: escribir el bloque 1 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import { Route, Routes } from 'react-router';
import Navbar from './components/Navbar.jsx';
import Inicio from './pages/Inicio.jsx';
import Acerca from './pages/Acerca.jsx';
```

**Por qué se agrega ahora:** Las importaciones conectan archivos y bibliotecas. Antes de continuar, comprueba que cada ruta relativa coincide con la ubicación del archivo que acabas de crear. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 3.2: escribir el bloque 2 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function App() {
```

**Por qué se agrega ahora:** App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 3.3: escribir el bloque 3 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <>
      <Navbar />
      <Routes>
        <Route path="/" element={<Inicio />} />
        <Route path="/about" element={<Acerca />} />
      </Routes>
    </>
  );
}
```

**Por qué se agrega ahora:** Routes selecciona una vista según la URL; Route asocia path con element. :id es variable solo en EJ06; la barra Navbar externa permanece visible. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 3; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/App.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import { Route, Routes } from 'react-router';
import Navbar from './components/Navbar.jsx';
import Inicio from './pages/Inicio.jsx';
import Acerca from './pages/Acerca.jsx';

export default function App() {
  return (
    <>
      <Navbar />
      <Routes>
        <Route path="/" element={<Inicio />} />
        <Route path="/about" element={<Acerca />} />
      </Routes>
    </>
  );
}
```

**Explicación causal.** App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

## Auditoría: archivos completos y dependencias

En esta etapa no debes volver a copiar archivos ya creados que no figuren arriba. Se conservan exactamente desde el ejemplo anterior. Confirma estas responsabilidades nuevas: createRoot obtiene #root del HTML e inserta App. React.StrictMode realiza comprobaciones adicionales de desarrollo; en EJ05 BrowserRouter aporta el contexto de navegación. · Cada Link tiene un destino to distinto. Este componente no dibuja una página; deja que el enrutador cambie la ruta del cliente. · Inicio agrupa el contenido original para mostrarlo solo cuando la ruta / sea activa. Los enlaces a curso aparecen únicamente en EJ06. · Acerca es una vista distinta, no un segundo documento HTML; React muestra su JSX al seleccionar la URL /about. · App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas.

## Antes de ejecutar: predicción razonada

1. ¿Qué mostrará la URL después de pulsar Acerca?
2. ¿Volver a Inicio exige cargar nuevamente el HTML completo?

## Ejecuta, prueba e interpreta

**Terminal de la raíz del proyecto:** si el servidor está detenido, escribe `npm run dev`; si ya sigue activo desde el ejemplo anterior, NO ejecutes una segunda copia. Usa la URL indicada por Vite en el navegador. Después de guardar los cambios, abre otra terminal en la misma carpeta y escribe `npm run build` para verificar que el código pueda compilarse.

**Paso de navegación:** pulsa los enlaces `Inicio` y `Acerca`; mira la barra de URL antes y después del clic. En `Acerca` debe figurar `/about` y seguir visible la barra común de navegación.

**Resultado esperado:** La navegación superior tiene Inicio y Acerca. Al pulsar Acerca la URL pasa a /about y el contenido principal muestra «Acerca»; el documento no se recarga completo. La consola de Vite no debe presentar errores de compilación. Si ejecutas `npm run build`, debe finalizar sin errores y crear el directorio `dist/`.

**Interpretación:** Instalaremos React Router 7, envolveremos App en BrowserRouter, crearemos Inicio y Acerca, incorporaremos Navbar con Link y declararemos Routes/Route en App. La evidencia permite distinguir entre lo que hace la capa visual, lo que controla React y lo que depende del navegador.

## Error controlado, causa y corrección

**Provoca el caso solo si puedes restaurar el código después:** Importar BrowserRouter desde react-router-dom sin instalar esa dependencia, o usar Link fuera de BrowserRouter.

**Por qué ocurre:** El ejemplo utiliza el paquete react-router; mezclar imports y paquetes genera fallos de resolución/contexto.

**Cómo corregirlo:** Instalar npm install react-router@7.18.4 y mantener todos los imports desde react-router dentro de BrowserRouter. Repite la prueba y confirma que el comportamiento vuelve a ser el esperado.

## T05 — Tarea espejo: Crear una tercera vista con su Link

**Enunciado:** Crea src/pages/Contacto.jsx; importa Contacto y agrega Route /contacto en src/App.jsx; agrega Link Contacto a Navbar. Mantén / y /about.

**Archivos permitidos:** `src/pages/Contacto.jsx`, `src/components/Navbar.jsx`, `src/App.jsx`.

**Restricción:** conserva los demás componentes y funcionalidades de EJ05. No sustituyas la aplicación entera por otra distinta.

**Pista de resolución:** localiza el bloque ya explicado que implementa la misma responsabilidad; agrega o modifica solo ese bloque. Prueba primero el comportamiento original, predice el nuevo, modifica y vuelve a comparar.

**Evidencia y criterio de corrección:** Contacto abre /contacto sin recarga y se siguen viendo Inicio y Acerca. Adjunta una captura legible y el texto de los archivos que modificaste. Ejecuta `npm run build`; explica oralmente qué cambió y qué quedó igual. La solución completa de esta tarea está reservada a la guía docente.


---

# EJ06 — Mostrar detalles mediante una ruta dinámica :id

## Comprendamos los conceptos nuevos antes de escribir EJ06

**¿Qué es una ruta dinámica?** En EJ05 escribimos dos rutas fijas: `/` y `/about`. Una ruta dinámica incluye un **parámetro** cuyo valor cambia en la URL. El patrón `/curso/:id` tiene una parte fija (`/curso/`) y una parte variable (`:id`). Por ejemplo, tanto `/curso/react` como `/curso/node` utilizan **la misma ruta declarada**.

**¿Para qué sirve `useParams`?** Es un Hook de React Router que permite leer los parámetros de la ruta actual. Si el patrón declara `:id`, entonces `const { id } = useParams()` obtiene la cadena `react` o `node`, según la URL. En nuestro `CursoDetalle.jsx` se usa `id` como clave para buscar una descripción en el objeto local `cursos`. No consultamos una API ni una base de datos: la explicación sale de los datos del propio archivo.

**¿Qué hace React cuando cambia el id?** React Router representa `CursoDetalle` para ambas URLs y el componente elige una descripción diferente. Si el parámetro no tiene correspondencia, mostraremos «Curso no encontrado»; así podemos distinguir una ruta que existe de un curso que no está registrado. **Error frecuente:** escribir `path="/curso/id"` sin los dos puntos convierte `id` en texto fijo, no en parámetro.

**Prueba de comprensión:** si mañana agregamos TypeScript, ¿necesitamos una segunda ruta `/curso/typescript` definida manualmente en `App.jsx`? No: basta con agregar una entrada al objeto de cursos y un `Link` a esa URL; la plantilla `/curso/:id` ya resuelve el caso.

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

Crear una ruta diferente para cada curso duplicaría la estructura de páginas. Enlazaremos dos cursos desde Inicio, definiremos /curso/:id, leeremos el parámetro con useParams y seleccionaremos una descripción desde un objeto local. Incluiremos la respuesta visible a un id inexistente. /curso/react y /curso/node muestran contenidos distintos; /curso/otro muestra «Curso no encontrado». Aprenderemos a verificar la consecuencia ejecutando el servidor local y comprobando el estado visible, antes de cambiar archivos adicionales.

## ¿Qué problema concreto resolveremos?

Crear una ruta diferente para cada curso duplicaría la estructura de páginas. La propuesta será modificar únicamente la responsabilidad necesaria de la aplicación del portal de cursos y comprobar su consecuencia real en el navegador.

**Punto de partida:** conserva todos los archivos y el servidor de EJ05. Los archivos no listados se mantienen sin cambios.

**Archivos de este ejemplo:** `src/pages/CursoDetalle.jsx`, `src/pages/Inicio.jsx`, `src/App.jsx`.

### Paso 1 — Leer el identificador desde la ruta

**Antes de escribir.** CursoDetalle usa useParams para recuperar id; cursos es un objeto local de descripciones. Se atiende explícitamente el caso de identificador inexistente. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/pages/CursoDetalle.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 1.1: escribir el bloque 1 en `src/pages/CursoDetalle.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import { useParams } from 'react-router';
```

**Por qué se agrega ahora:** useParams extrae el identificador de la URL; el acceso cursos[id] permite reutilizar el mismo componente. Si no hay descripción, se mostrará el caso no encontrado. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 5; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.2: escribir el bloque 2 en `src/pages/CursoDetalle.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
const cursos = {
  react: 'Interfaz con componentes y Hooks',
  node: 'Servidor con Express',
};
```

**Por qué se agrega ahora:** useParams lee el segmento dinámico id; la tabla cursos resuelve la descripción en memoria sin servidor; el if garantiza una salida comprensible para un identificador desconocido. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 5; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.3: escribir el bloque 3 en `src/pages/CursoDetalle.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function CursoDetalle() {
  const { id } = useParams();
  const descripcion = cursos[id];
```

**Por qué se agrega ahora:** useParams extrae el identificador de la URL; el acceso cursos[id] permite reutilizar el mismo componente. Si no hay descripción, se mostrará el caso no encontrado. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 5; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.4: escribir el bloque 4 en `src/pages/CursoDetalle.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  if (!descripcion) {
    return <main className="contenedor"><h1>Curso no encontrado</h1></main>;
  }
```

**Por qué se agrega ahora:** JSX usa className para conectar el elemento con las reglas CSS. Los componentes retornan una estructura bien anidada y con etiquetas cerradas. **Estado acumulado:** el archivo contiene los bloques 1 a 4 de 5; no copies el fragmento otra vez al consultar la versión final.

**Subacción 1.5: escribir el bloque 5 en `src/pages/CursoDetalle.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <main className="contenedor">
      <h1>Curso: {id}</h1>
      <p>{descripcion}</p>
    </main>
  );
}
```

**Por qué se agrega ahora:** JSX usa className para conectar el elemento con las reglas CSS. Los componentes retornan una estructura bien anidada y con etiquetas cerradas. **Estado acumulado:** el archivo contiene los bloques 1 a 5 de 5; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/pages/CursoDetalle.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import { useParams } from 'react-router';

const cursos = {
  react: 'Interfaz con componentes y Hooks',
  node: 'Servidor con Express',
};

export default function CursoDetalle() {
  const { id } = useParams();
  const descripcion = cursos[id];

  if (!descripcion) {
    return <main className="contenedor"><h1>Curso no encontrado</h1></main>;
  }

  return (
    <main className="contenedor">
      <h1>Curso: {id}</h1>
      <p>{descripcion}</p>
    </main>
  );
}
```

**Explicación causal.** useParams lee el segmento dinámico id; la tabla cursos resuelve la descripción en memoria sin servidor; el if garantiza una salida comprensible para un identificador desconocido. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

### Paso 2 — Ofrecer enlaces a detalles

**Antes de escribir.** Inicio agrega Link a /curso/react y /curso/node. El id viaja en la URL, no por una nueva variable global. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/pages/Inicio.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 2.1: escribir el bloque 1 en `src/pages/Inicio.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import { Link } from 'react-router';
import Saludo from '../components/Saludo.jsx';
import Contador from '../components/Contador.jsx';
```

**Por qué se agrega ahora:** Link declara un destino to dentro de la SPA. React Router modifica la ubicación en el cliente sin solicitar otra página HTML completa. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.2: escribir el bloque 2 en `src/pages/Inicio.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function Inicio() {
```

**Por qué se agrega ahora:** Inicio agrupa el contenido original para mostrarlo solo cuando la ruta / sea activa. Los enlaces a curso aparecen únicamente en EJ06. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 2.3: escribir el bloque 3 en `src/pages/Inicio.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <Saludo nombre="Lucía" curso="React" />
      <Saludo nombre="Diego" curso="JavaScript" />
      <Contador />
      <p><Link to="/curso/react">Ver curso React</Link></p>
      <p><Link to="/curso/node">Ver curso Node.js</Link></p>
    </main>
  );
}
```

**Por qué se agrega ahora:** Link declara un destino to dentro de la SPA. React Router modifica la ubicación en el cliente sin solicitar otra página HTML completa. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 3; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/pages/Inicio.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import { Link } from 'react-router';
import Saludo from '../components/Saludo.jsx';
import Contador from '../components/Contador.jsx';

export default function Inicio() {
  return (
    <main className="contenedor">
      <h1>Portal de cursos</h1>
      <Saludo nombre="Lucía" curso="React" />
      <Saludo nombre="Diego" curso="JavaScript" />
      <Contador />
      <p><Link to="/curso/react">Ver curso React</Link></p>
      <p><Link to="/curso/node">Ver curso Node.js</Link></p>
    </main>
  );
}
```

**Explicación causal.** Inicio agrupa el contenido original para mostrarlo solo cuando la ruta / sea activa. Los enlaces a curso aparecen únicamente en EJ06. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

### Paso 3 — Registrar una única ruta reutilizable

**Antes de escribir.** App importa CursoDetalle y agrega /curso/:id. Los dos enlaces usan la misma plantilla de página y cambian únicamente la identificación del curso. Tu punto de partida son los archivos del ejemplo anterior, salvo EJ01, que parte de la carpeta vacía.

**Acción exacta.** En el explorador de VS Code, `src/App.jsx`. Crea las carpetas y archivos si no existen. Abre cada archivo, selecciona todo su contenido (Ctrl+A) y sustitúyelo por el bloque completo mostrado a continuación; de este modo no queda ningún resto incompatible de la etapa previa. Guarda (Ctrl+S).

**Construcción interna del archivo en microacciones (en orden):** empieza con el archivo vacío o borra el contenido anterior antes de esta etapa. Escribe los fragmentos consecutivamente de arriba a abajo, respetando espacios y llaves. Algunos fragmentos intermedios todavía no compilan: ejecuta únicamente cuando el archivo completo esté terminado.

**Subacción 3.1: escribir el bloque 1 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
import { Route, Routes } from 'react-router';
import Navbar from './components/Navbar.jsx';
import Inicio from './pages/Inicio.jsx';
import Acerca from './pages/Acerca.jsx';
import CursoDetalle from './pages/CursoDetalle.jsx';
```

**Por qué se agrega ahora:** Las importaciones conectan archivos y bibliotecas. Antes de continuar, comprueba que cada ruta relativa coincide con la ubicación del archivo que acabas de crear. **Estado acumulado:** el archivo contiene los bloques 1 a 1 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 3.2: escribir el bloque 2 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
export default function App() {
```

**Por qué se agrega ahora:** App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas. **Estado acumulado:** el archivo contiene los bloques 1 a 2 de 3; no copies el fragmento otra vez al consultar la versión final.

**Subacción 3.3: escribir el bloque 3 en `src/App.jsx`.** Pégalo justo debajo del bloque anterior del MISMO archivo; para el primero empieza en la línea 1.

```jsx
  return (
    <>
      <Navbar />
      <Routes>
        <Route path="/" element={<Inicio />} />
        <Route path="/about" element={<Acerca />} />
        <Route path="/curso/:id" element={<CursoDetalle />} />
      </Routes>
    </>
  );
}
```

**Por qué se agrega ahora:** Routes selecciona una vista según la URL; Route asocia path con element. :id es variable solo en EJ06; la barra Navbar externa permanece visible. **Estado acumulado:** el archivo contiene los bloques 1 a 3 de 3; no copies el fragmento otra vez al consultar la versión final.

**Archivo completo `src/App.jsx` al acabar este microbloque (referencia para cotejar, no añadir una segunda copia):**

```jsx
import { Route, Routes } from 'react-router';
import Navbar from './components/Navbar.jsx';
import Inicio from './pages/Inicio.jsx';
import Acerca from './pages/Acerca.jsx';
import CursoDetalle from './pages/CursoDetalle.jsx';

export default function App() {
  return (
    <>
      <Navbar />
      <Routes>
        <Route path="/" element={<Inicio />} />
        <Route path="/about" element={<Acerca />} />
        <Route path="/curso/:id" element={<CursoDetalle />} />
      </Routes>
    </>
  );
}
```

**Explicación causal.** App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas. **Estado posterior:** el archivo queda guardado con el contenido de esta etapa y puede compararse con la ventana del editor.

**Comprobación de microbloque:** antes de continuar revisa en VS Code que cada archivo aparece en la ruta indicada, que los imports apuntan a nombres existentes y que no hay subrayados de error. Si Vite está activo, observa si el navegador actualiza la vista; si todavía faltan componentes del ejemplo, completa primero los siguientes microbloques.

## Auditoría: archivos completos y dependencias

En esta etapa no debes volver a copiar archivos ya creados que no figuren arriba. Se conservan exactamente desde el ejemplo anterior. Confirma estas responsabilidades nuevas: useParams lee el segmento dinámico id; la tabla cursos resuelve la descripción en memoria sin servidor; el if garantiza una salida comprensible para un identificador desconocido. · Inicio agrupa el contenido original para mostrarlo solo cuando la ruta / sea activa. Los enlaces a curso aparecen únicamente en EJ06. · App es el componente raíz; se importa desde main.jsx. Revisa la lista de imports de cada etapa y el JSX retornado: determina el orden visible de componentes o rutas.

## Antes de ejecutar: predicción razonada

1. ¿Qué diferencia habrá entre /curso/react y /curso/node?
2. ¿Qué ocurrirá con /curso/noexiste?

## Ejecuta, prueba e interpreta

**Terminal de la raíz del proyecto:** si el servidor está detenido, escribe `npm run dev`; si ya sigue activo desde el ejemplo anterior, NO ejecutes una segunda copia. Usa la URL indicada por Vite en el navegador. Después de guardar los cambios, abre otra terminal en la misma carpeta y escribe `npm run build` para verificar que el código pueda compilarse.

**Paso de rutas dinámicas:** pulsa los dos enlaces desde `Inicio` y luego escribe manualmente `/curso/noexiste` después de la dirección local. Revisa las tres salidas.

**Resultado esperado:** Los enlaces /curso/react y /curso/node llevan a descripciones diferentes. La URL /curso/noexiste visualiza «Curso no encontrado». La consola de Vite no debe presentar errores de compilación. Si ejecutas `npm run build`, debe finalizar sin errores y crear el directorio `dist/`.

**Interpretación:** Enlazaremos dos cursos desde Inicio, definiremos /curso/:id, leeremos el parámetro con useParams y seleccionaremos una descripción desde un objeto local. Incluiremos la respuesta visible a un id inexistente. La evidencia permite distinguir entre lo que hace la capa visual, lo que controla React y lo que depende del navegador.

## Error controlado, causa y corrección

**Provoca el caso solo si puedes restaurar el código después:** Escribir path="/curso/id" o leer const { curso } = useParams().

**Por qué ocurre:** Sin dos puntos id es texto literal; además, el nombre de la propiedad de useParams debe coincidir con :id.

**Cómo corregirlo:** Usar path="/curso/:id" y const { id } = useParams(); probar una URL existente y otra desconocida. Repite la prueba y confirma que el comportamiento vuelve a ser el esperado.

## T06 — Tarea espejo: Agregar un curso nuevo sin duplicar la ruta

**Enunciado:** En CursoDetalle.jsx agrega la clave typescript con una descripción; en Inicio.jsx crea el enlace /curso/typescript. No agregues un segundo Route de detalle.

**Archivos permitidos:** `src/pages/CursoDetalle.jsx`, `src/pages/Inicio.jsx`.

**Restricción:** conserva los demás componentes y funcionalidades de EJ06. No sustituyas la aplicación entera por otra distinta.

**Pista de resolución:** localiza el bloque ya explicado que implementa la misma responsabilidad; agrega o modifica solo ese bloque. Prueba primero el comportamiento original, predice el nuevo, modifica y vuelve a comparar.

**Evidencia y criterio de corrección:** El mismo /curso/:id muestra el nuevo curso y conserva los casos react y node. Adjunta una captura legible y el texto de los archivos que modificaste. Ejecuta `npm run build`; explica oralmente qué cambió y qué quedó igual. La solución completa de esta tarea está reservada a la guía docente.

## Cierre de la sesión — lectura docente

Hoy convertimos una carpeta de archivos en un portal React que se puede observar y modificar en el navegador. Primero comprobamos que JSX no es simplemente HTML copiado: la interfaz depende de un componente que devuelve una estructura válida y puede insertar expresiones JavaScript entre llaves. Después evitamos duplicar tarjetas usando props, con lo cual la misma función Saludo representó información distinta para dos estudiantes. Cuando incorporamos useState, dejamos de depender de una variable local sin mecanismo de actualización y pasamos a observar un contador que se incrementa y reinicia como consecuencia de eventos reales. Como profundización, sincronizamos el título de la pestaña con useEffect y entendimos por qué su dependencia importa.

A continuación, separaste dos vistas, enlazaste sus URLs mediante React Router y verificaste que la navegación dentro de la SPA no obliga a descargar de nuevo el documento completo. La ruta dinámica :id mostró cómo una sola página puede recibir identificadores distintos sin duplicar componentes. Los errores controlados aportaron un criterio importante: un import, un nombre de prop, una dependencia o un path incorrecto puede impedir una función que parecía sencilla. En cada caso pudimos detectar la causa, corregirla y volver a probar.

Para consolidar el aprendizaje, repasa las tareas espejo y explica por qué cada modificación funciona. No basta con que el navegador «se vea bien»: el criterio de dominio es poder predecir y justificar su comportamiento. Si necesitas comparar tu trabajo con archivos completos, el laboratorio descargable de FASE 15 es opcional; la explicación y todos los pasos esenciales ya están aquí. Puedes reforzar estos temas con el blog de Lideratec Academy https://lideratecacademy.com/blog/ y el canal público https://www.youtube.com/@LideratecAcademy. La próxima sesión debe partir de la comprensión de componentes, estado y rutas, no de supuestos adicionales.
