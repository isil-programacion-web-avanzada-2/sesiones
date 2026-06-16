
---
title: "Guia del estudiante - Preparacion de aplicaciones React para produccion"
author: "Elaborado por el docente"
lang: es
geometry: margin=1.8cm
toc: true
toc-depth: 2
fontsize: 10pt
---

# Guia del estudiante - Preparacion de aplicaciones React para produccion

**Elaborado por el docente**  
**Proyecto academico:** Lideratec Academy  
**Blog:** https://lideratecacademy.com/  
**Canal YouTube:** https://www.youtube.com/@LideratecAcademy

## Proposito de la practica

Esta practica te guiara paso a paso para preparar una aplicacion React para produccion. Trabajaras minificacion, optimizacion del bundle, configuracion de Webpack y Babel, variables de entorno, logs y monitoreo basico.

La meta no es solo generar un build. La meta es comprender que una aplicacion lista para produccion debe ser mas ligera, compatible, configurable y observable.

## Resultado de aprendizaje observable

Al finalizar, podras **configurar y explicar** un flujo basico de preparacion para produccion en React, identificando que hace cada archivo, comando y tecnica utilizada.

## Duracion sugerida

- Trabajo guiado: 100 a 120 minutos.
- Trabajo autonomo posterior: 40 a 60 minutos.

## Requisitos previos minimos

Debes contar con conocimientos basicos de:

- JavaScript moderno.
- Componentes React.
- Terminal o consola.
- Uso basico de `npm`.
- Estructura de carpetas en un proyecto frontend.

## Preparacion del entorno

### Herramientas necesarias

| Herramienta | Para que sirve | Validacion rapida |
|---|---|---|
| Node.js LTS | Permite ejecutar `npm` e instalar dependencias JavaScript. | `node -v` |
| npm | Gestiona paquetes del proyecto. | `npm -v` |
| Visual Studio Code u otro editor | Permite crear y editar archivos del proyecto. | Abrir la carpeta del proyecto |
| Navegador web | Permite probar la aplicacion compilada. | Abrir `http://localhost:3000` |

### Enlaces oficiales de referencia

- Node.js: https://nodejs.org/
- React: https://react.dev/
- Webpack: https://webpack.js.org/
- Babel: https://babeljs.io/
- Terser: https://terser.org/
- Sentry: https://sentry.io/

### Validar instalacion

Abre una terminal y ejecuta:

~~~bash
node -v
npm -v
~~~

**Que es:** comandos de verificacion de version.  
**Para que sirve:** comprueban que Node.js y npm estan disponibles.  
**Resultado esperado:** se muestran numeros de version, por ejemplo `v20.x.x` y `10.x.x`.  
**Error comun:** que aparezca `node no se reconoce` o `npm no se reconoce`.  
**Como corregirlo:** reinstala Node.js desde la pagina oficial, marca la opcion de agregarlo al PATH y reinicia la terminal.

## Nota tecnica de actualizacion operativa

Para esta practica se trabajara con una configuracion manual de React, Webpack y Babel porque permite observar el proceso de produccion con claridad. Si usas proyectos modernos creados con Vite, recuerda que las variables expuestas al cliente normalmente usan el prefijo `VITE_` y se leen con `import.meta.env`.

Tambien debes recordar una regla de seguridad: una variable publica del frontend no debe contener secretos reales. Si una variable queda dentro del build del cliente, un usuario con conocimientos tecnicos podria inspeccionarla. Usa el frontend para configuracion publica y reserva secretos reales para backend o infraestructura segura.

---

# Desarrollo paso a paso por bloques

## Bloque 1 - Crear la base del proyecto

**Objetivo del bloque:**  
Crear una estructura minima para una aplicacion React que pueda ser procesada por Webpack y Babel.

**Concepto trabajado:**  
Preparacion inicial de una aplicacion web para produccion.

**Explicacion breve:**  
Antes de optimizar, necesitas un proyecto organizado. La estructura separa codigo fuente, configuracion de build, variables de entorno y archivos de salida.

**Ejemplo guiado:**

~~~bash
mkdir react-produccion-guia
cd react-produccion-guia
npm init -y
~~~

**Paso a paso:**

1. Crea la carpeta `react-produccion-guia`.
2. Entra a la carpeta con `cd react-produccion-guia`.
3. Ejecuta `npm init -y` para crear `package.json`.

**Que es `package.json`:** archivo que describe el proyecto, sus scripts y dependencias.  
**Para que sirve:** permite instalar paquetes y definir comandos como `npm run build`.  
**Por que se usa:** Webpack, Babel, React y plugins se gestionan como dependencias npm.  
**Como verificar que funciono:** debe existir un archivo llamado `package.json`.

**Actividad para ti:**  
Crea una carpeta nueva y ejecuta los comandos. Luego abre la carpeta en tu editor.

**Espacio para responder:**

- Que archivo se creo despues de `npm init -y`?
- Para que sirve ese archivo?
- Que error aparece si ejecutas `npm` fuera de una carpeta de proyecto?

**Resultado esperado:**  
Tienes una carpeta de proyecto con `package.json` creado.

**Error comun a evitar:**  
Crear archivos fuera de la carpeta del proyecto.

**Mini reto:**  
Agrega en `package.json` una propiedad `description` con una descripcion breve del proyecto.

---

## Bloque 2 - Instalar React, Webpack, Babel y plugins de produccion

**Objetivo del bloque:**  
Instalar las dependencias necesarias para ejecutar React, compilar con Webpack, transformar JSX con Babel y minificar con Terser.

**Concepto trabajado:**  
Herramientas de compilacion y optimizacion.

**Explicacion breve:**  
React permite construir la interfaz. Webpack empaqueta modulos. Babel transforma JavaScript moderno y JSX. Terser reduce el peso del JavaScript final. HtmlWebpackPlugin genera el archivo HTML final. DotenvWebpack permite inyectar variables de entorno durante el build.

**Ejemplo guiado:**

~~~bash
npm install react react-dom @sentry/react
npm install -D webpack webpack-cli webpack-dev-server babel-loader @babel/core @babel/preset-env @babel/preset-react terser-webpack-plugin html-webpack-plugin dotenv-webpack webpack-bundle-analyzer
~~~

**Paso a paso:**

1. Ejecuta el primer comando para instalar dependencias de ejecucion.
2. Ejecuta el segundo comando para instalar dependencias de desarrollo.
3. Verifica que se haya creado la carpeta `node_modules`.
4. Abre `package.json` y revisa `dependencies` y `devDependencies`.

**Que es `dependencies`:** paquetes necesarios para que la aplicacion funcione.  
**Que es `devDependencies`:** paquetes necesarios para construir, compilar o desarrollar.  
**Resultado esperado:** `package.json` lista los paquetes instalados.  
**Error comun:** cortar el comando o instalar fuera de la carpeta del proyecto.  
**Como verificar:** ejecuta `npm list webpack --depth=0`.

**Actividad para ti:**  
Identifica en tu `package.json` cuales paquetes pertenecen a React, cuales a Webpack y cuales a Babel.

**Espacio para responder:**

- Que paquete permite usar JSX en React?
- Que paquete minifica JavaScript en produccion?
- Que paquete genera el HTML final?

**Resultado esperado:**  
El proyecto tiene las dependencias necesarias para continuar.

**Error comun a evitar:**  
Instalar todo como dependencia de produccion cuando son herramientas de build.

**Mini reto:**  
Ejecuta `npm list react --depth=0` y anota la version instalada.

---

## Bloque 3 - Crear archivos base de React

**Objetivo del bloque:**  
Crear una aplicacion React minima que pueda ser compilada.

**Concepto trabajado:**  
Punto de entrada de una aplicacion React.

**Explicacion breve:**  
Webpack necesita un archivo de entrada. En esta practica sera `src/index.jsx`. Desde ahi se carga el componente principal `App.jsx`.

**Ejemplo guiado:**

Crea la siguiente estructura:

~~~text
react-produccion-guia/
  public/
    index.html
  src/
    index.jsx
    App.jsx
~~~

**Archivo sugerido:** `public/index.html`  
**Objetivo del codigo:** entregar un contenedor donde React renderizara la aplicacion.

~~~html
<!doctype html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>React Produccion</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
~~~

**Explicacion paso a paso:**

- `<!doctype html>` indica que el documento usa HTML moderno.
- `<html lang="es">` define el idioma del documento.
- `<div id="root"></div>` es el contenedor donde React montara la interfaz.

**Archivo sugerido:** `src/index.jsx`  
**Objetivo del codigo:** iniciar React y renderizar `App`.

~~~jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App';

createRoot(document.getElementById('root')).render(<App />);
~~~

**Explicacion paso a paso:**

- `import React from 'react';` permite usar React.
- `createRoot` crea la raiz de renderizado.
- `App` es el componente principal.
- `document.getElementById('root')` busca el contenedor HTML.
- `.render(<App />)` muestra el componente en pantalla.

**Archivo sugerido:** `src/App.jsx`  
**Objetivo del codigo:** mostrar una interfaz simple para validar el proyecto.

~~~jsx
import React from 'react';

function App() {
  return (
    <main>
      <h1>Aplicacion React preparada para produccion</h1>
      <p>Esta practica trabaja minificacion, Webpack, Babel, variables, logs y monitoreo.</p>
    </main>
  );
}

export default App;
~~~

**Paso a paso:**

1. Crea la carpeta `public` y el archivo `index.html`.
2. Crea la carpeta `src`.
3. Crea `index.jsx` y `App.jsx`.
4. Guarda todos los archivos.

**Actividad para ti:**  
Cambia el texto del parrafo por una descripcion propia de lo que significa preparar una app para produccion.

**Espacio para responder:**

- Que funcion cumple `createRoot`?
- Que relacion hay entre `index.html` e `index.jsx`?
- Que pasaria si el `id="root"` no existe?

**Resultado esperado:**  
La aplicacion tiene un punto de entrada y un componente principal.

**Error comun a evitar:**  
Escribir `root` diferente en HTML y JavaScript.

**Mini reto:**  
Agrega una lista con tres conceptos: minificacion, variables de entorno y monitoreo.

---

## Bloque 4 - Configurar Babel

**Objetivo del bloque:**  
Configurar Babel para transformar JavaScript moderno y JSX.

**Concepto trabajado:**  
Transpilacion con Babel.

**Explicacion breve:**  
Babel convierte sintaxis moderna y JSX en codigo que puede ser procesado dentro del flujo de compilacion. Webpack empaqueta; Babel transforma.

**Archivo sugerido:** `.babelrc`  
**Objetivo del codigo:** indicar a Babel que presets debe usar.

~~~json
{
  "presets": [
    ["@babel/preset-env", { "useBuiltIns": "entry", "corejs": 3 }],
    ["@babel/preset-react", { "runtime": "automatic" }]
  ],
  "plugins": ["@babel/plugin-syntax-dynamic-import"]
}
~~~

**Paso a paso:**

1. Crea el archivo `.babelrc` en la raiz del proyecto.
2. Copia la configuracion.
3. Guarda el archivo.
4. Instala el plugin faltante si aparece error:

~~~bash
npm install -D @babel/plugin-syntax-dynamic-import core-js
~~~

**Explicacion de elementos:**

- `@babel/preset-env`: transforma caracteristicas modernas de JavaScript.
- `useBuiltIns`: indica como tratar compatibilidad adicional mediante polyfills.
- `corejs`: especifica la version de Core JS usada por Babel.
- `@babel/preset-react`: transforma JSX.
- `runtime: automatic`: evita importar React manualmente solo para JSX en proyectos modernos.
- `@babel/plugin-syntax-dynamic-import`: permite reconocer importaciones dinamicas usadas en lazy loading.

**Actividad para ti:**  
Explica con tus palabras la diferencia entre Webpack y Babel antes de configurar Webpack.

**Espacio para responder:**

- Babel empaqueta archivos o transforma sintaxis?
- Que preset permite procesar JSX?
- Por que la compatibilidad importa en produccion?

**Resultado esperado:**  
El proyecto tiene reglas de transpilacion para JavaScript moderno y React.

**Error comun a evitar:**  
Creer que Babel reemplaza a Webpack. Son herramientas complementarias.

**Mini reto:**  
Elimina temporalmente `@babel/preset-react` y explica que tipo de error esperarias al compilar JSX.

---

## Bloque 5 - Configurar Webpack para produccion

**Objetivo del bloque:**  
Crear una configuracion de Webpack que genere un bundle optimizado para produccion.

**Concepto trabajado:**  
Entry, output, loaders, plugins, minificacion y cache busting.

**Explicacion breve:**  
Webpack toma un archivo de entrada, analiza dependencias, aplica loaders, ejecuta plugins y genera bundles de salida. En produccion puede usar nombres con `contenthash` para mejorar cache y Terser para minificar.

**Archivo sugerido:** `webpack.config.js`  
**Objetivo del codigo:** definir como se construira la aplicacion.

~~~javascript
const path = require('path');
const TerserPlugin = require('terser-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const Dotenv = require('dotenv-webpack');
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');

module.exports = (env, argv) => {
  const modo = argv.mode || 'development';
  const analizar = Boolean(env && env.analyze);

  return {
    mode: modo,
    entry: './src/index.jsx',
    output: {
      filename: 'js/[name].[contenthash].js',
      path: path.resolve(__dirname, 'dist'),
      clean: true
    },
    resolve: {
      extensions: ['.js', '.jsx']
    },
    module: {
      rules: [
        {
          test: /\.jsx?$/,
          exclude: /node_modules/,
          use: 'babel-loader'
        }
      ]
    },
    optimization: {
      minimize: modo === 'production',
      minimizer: [
        new TerserPlugin({
          terserOptions: {
            compress: {
              drop_console: modo === 'production',
              drop_debugger: true,
              passes: 2
            },
            format: {
              comments: false
            }
          },
          extractComments: false,
          parallel: true
        })
      ]
    },
    plugins: [
      new HtmlWebpackPlugin({
        template: './public/index.html'
      }),
      new Dotenv({
        path: `./.env.${modo}`,
        safe: false,
        systemvars: true
      }),
      ...(analizar
        ? [new BundleAnalyzerPlugin({ analyzerMode: 'static', openAnalyzer: false })]
        : [])
    ],
    devServer: {
      static: './dist',
      port: 3000,
      open: true
    }
  };
};
~~~

**Paso a paso:**

1. Crea `webpack.config.js` en la raiz del proyecto.
2. Copia el codigo completo.
3. Verifica que la ruta `entry` apunte a `./src/index.jsx`.
4. Verifica que `HtmlWebpackPlugin` use `./public/index.html`.
5. Guarda el archivo.

**Explicacion de elementos clave:**

- `entry`: archivo desde donde Webpack inicia el grafo de dependencias.
- `output`: carpeta y nombre de archivos generados.
- `contenthash`: agrega una huella al nombre del archivo para cache busting.
- `babel-loader`: conecta Webpack con Babel.
- `TerserPlugin`: minifica JavaScript.
- `drop_console`: elimina logs en produccion cuando esta activado.
- `Dotenv`: carga variables segun el modo.
- `BundleAnalyzerPlugin`: permite generar un reporte visual del bundle.

**Actividad para ti:**  
Ubica en el codigo las secciones `entry`, `output`, `module`, `optimization` y `plugins`.

**Espacio para responder:**

- Que carpeta genera Webpack como salida?
- Que hace `clean: true`?
- Por que `contenthash` ayuda en produccion?

**Resultado esperado:**  
El proyecto tiene una configuracion de build para desarrollo y produccion.

**Error comun a evitar:**  
Escribir mal la expresion `test: /\.jsx?$/`, porque el loader dejaria de procesar archivos JSX.

**Mini reto:**  
Cambia temporalmente el puerto de `3000` a `3001` y explica que deberias observar al iniciar el servidor.

---

## Bloque 6 - Agregar scripts de ejecucion y generar build

**Objetivo del bloque:**  
Definir comandos para ejecutar la aplicacion en desarrollo, construir para produccion y analizar el bundle.

**Concepto trabajado:**  
Build de produccion, minificacion y analisis del bundle.

**Explicacion breve:**  
Los scripts permiten ejecutar comandos largos con nombres simples. `npm run build` debe generar la carpeta `dist` con archivos optimizados.

**Archivo sugerido:** `package.json`  
**Objetivo del codigo:** agregar comandos reutilizables.

Busca la seccion `scripts` y reemplazala por:

~~~json
{
  "scripts": {
    "start": "webpack serve --mode development",
    "build": "webpack --mode production",
    "analyze": "webpack --mode production --env analyze=true"
  }
}
~~~

**Paso a paso:**

1. Abre `package.json`.
2. Localiza `scripts`.
3. Agrega `start`, `build` y `analyze`.
4. Ejecuta:

~~~bash
npm run start
~~~

5. Verifica que el navegador abra `http://localhost:3000`.
6. Deten el servidor con `Ctrl + C`.
7. Ejecuta:

~~~bash
npm run build
~~~

8. Verifica que aparezca la carpeta `dist`.

**Que observar:**

- En desarrollo se abre un servidor local.
- En produccion se genera `dist`.
- El archivo JavaScript final debe tener un nombre con hash.
- Los espacios y comentarios del codigo final se reducen.

**Actividad para ti:**  
Ejecuta `npm run analyze` y ubica el archivo HTML de reporte que se genera.

**Espacio para responder:**

- Que diferencia hay entre `start` y `build`?
- Que indica que la minificacion se aplico?
- Que informacion entrega el analizador de bundle?

**Resultado esperado:**  
Puedes ejecutar la app en desarrollo y generar un build de produccion.

**Error comun a evitar:**  
Modificar `package.json` dejando comas sobrantes o llaves mal cerradas.

**Mini reto:**  
Anota el nombre exacto del archivo `.js` generado en `dist/js`.

---

## Bloque 7 - Aplicar lazy loading y tree shaking

**Objetivo del bloque:**  
Reducir el bundle inicial mediante carga diferida y eliminar codigo no utilizado cuando sea posible.

**Concepto trabajado:**  
Code splitting, lazy loading y tree shaking.

**Explicacion breve:**  
`React.lazy` permite cargar componentes solo cuando son necesarios. Tree shaking elimina exportaciones que no se usan, siempre que se usen modulos ES6 y build de produccion.

**Archivo sugerido:** `src/paginas/PaginaInicio.jsx`

~~~jsx
export default function PaginaInicio() {
  return <h2>Pagina de inicio</h2>;
}
~~~

**Archivo sugerido:** `src/paginas/PanelUsuario.jsx`

~~~jsx
export default function PanelUsuario() {
  return <h2>Panel de usuario</h2>;
}
~~~

**Archivo sugerido:** `src/paginas/ConfiguracionAvanzada.jsx`

~~~jsx
export default function ConfiguracionAvanzada() {
  return <h2>Configuracion avanzada</h2>;
}
~~~

**Archivo sugerido:** `src/App.jsx`

~~~jsx
import React, { lazy, Suspense, useState } from 'react';

const PaginaInicio = lazy(() => import('./paginas/PaginaInicio'));
const PanelUsuario = lazy(() => import('./paginas/PanelUsuario'));
const ConfiguracionAvanzada = lazy(() => import('./paginas/ConfiguracionAvanzada'));

function App() {
  const [vista, setVista] = useState('inicio');

  const seleccionarVista = () => {
    if (vista === 'inicio') return <PaginaInicio />;
    if (vista === 'usuario') return <PanelUsuario />;
    return <ConfiguracionAvanzada />;
  };

  return (
    <main>
      <h1>React para produccion</h1>
      <nav>
        <button onClick={() => setVista('inicio')}>Inicio</button>
        <button onClick={() => setVista('usuario')}>Usuario</button>
        <button onClick={() => setVista('configuracion')}>Configuracion</button>
      </nav>
      <Suspense fallback={<p>Cargando contenido...</p>}>
        {seleccionarVista()}
      </Suspense>
    </main>
  );
}

export default App;
~~~

**Archivo sugerido:** `src/utilidades.js`

~~~javascript
export const formatearFecha = (fecha) => {
  return new Date(fecha).toLocaleDateString('es-ES');
};

export const validarEmail = (email) => {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};

export const calcularImpuesto = (monto) => {
  return monto * 0.18;
};
~~~

**Uso en `src/App.jsx`:** agrega debajo de los imports:

~~~jsx
import { formatearFecha } from './utilidades';
~~~

Y dentro del `main`, antes del `nav`:

~~~jsx
<p>Fecha de build academico: {formatearFecha(new Date())}</p>
~~~

**Paso a paso:**

1. Crea la carpeta `src/paginas`.
2. Crea los tres componentes.
3. Reemplaza `App.jsx` por la version con `lazy` y `Suspense`.
4. Crea `utilidades.js`.
5. Importa solo `formatearFecha`.
6. Ejecuta `npm run build`.

**Que observar:**

- Webpack puede generar mas de un archivo JavaScript.
- Los componentes diferidos pueden separarse en chunks.
- Solo se importa una funcion desde `utilidades.js`.

**Actividad para ti:**  
Agrega una cuarta vista llamada `ReporteBundle` y cargala con `lazy`.

**Espacio para responder:**

- Que hace `Suspense`?
- Que diferencia hay entre code splitting y lazy loading?
- Por que importar solo una funcion ayuda al tree shaking?

**Resultado esperado:**  
La aplicacion usa carga diferida y mantiene imports selectivos.

**Error comun a evitar:**  
Olvidar el `default export` en componentes cargados con `lazy`.

**Mini reto:**  
Genera el reporte con `npm run analyze` y observa si aparecen chunks separados.

---

## Bloque 8 - Configurar variables de entorno, logs y monitoreo

**Objetivo del bloque:**  
Separar configuracion del codigo y registrar eventos importantes de la aplicacion.

**Concepto trabajado:**  
Variables de entorno, logs y monitoreo frontend.

**Explicacion breve:**  
Las variables de entorno permiten cambiar configuraciones segun el modo. Los logs permiten registrar eventos importantes. El monitoreo permite capturar errores globales y enviarlos a una herramienta externa cuando corresponda.

**Archivo sugerido:** `.env.development`

~~~env
REACT_APP_API_URL=http://localhost:5000/api
REACT_APP_ENTORNO=desarrollo
REACT_APP_HABILITAR_LOGS=true
REACT_APP_SENTRY_DSN=
~~~

**Archivo sugerido:** `.env.production`

~~~env
REACT_APP_API_URL=https://api.miempresa.com/v1
REACT_APP_ENTORNO=produccion
REACT_APP_HABILITAR_LOGS=false
REACT_APP_SENTRY_DSN=
~~~

**Archivo sugerido:** `.env.example`

~~~env
REACT_APP_API_URL=
REACT_APP_ENTORNO=
REACT_APP_HABILITAR_LOGS=
REACT_APP_SENTRY_DSN=
~~~

**Importante:** no coloques claves privadas reales en archivos expuestos al frontend.

**Archivo sugerido:** `src/config/configuracion.js`

~~~javascript
const configuracion = {
  api: {
    urlBase: process.env.REACT_APP_API_URL || 'http://localhost:5000/api'
  },
  entorno: process.env.REACT_APP_ENTORNO || 'desarrollo',
  logs: {
    habilitados: process.env.REACT_APP_HABILITAR_LOGS === 'true'
  },
  monitoreo: {
    sentryDsn: process.env.REACT_APP_SENTRY_DSN || ''
  }
};

export default configuracion;
~~~

**Archivo sugerido:** `src/utils/logger.js`

~~~javascript
import configuracion from '../config/configuracion';

const niveles = {
  info: 'INFO',
  warn: 'ADVERTENCIA',
  error: 'ERROR',
  debug: 'DEPURACION'
};

export const logger = {
  info: (mensaje, datos = {}) => {
    if (configuracion.logs.habilitados) {
      console.info(`[${niveles.info}] ${mensaje}`, datos);
    }
  },
  warn: (mensaje, datos = {}) => {
    console.warn(`[${niveles.warn}] ${mensaje}`, datos);
  },
  error: (mensaje, datos = {}) => {
    console.error(`[${niveles.error}] ${mensaje}`, datos);
  },
  debug: (mensaje, datos = {}) => {
    if (configuracion.entorno === 'desarrollo') {
      console.debug(`[${niveles.debug}] ${mensaje}`, datos);
    }
  }
};
~~~

**Archivo sugerido:** `src/monitoring/globalErrorHandler.js`

~~~javascript
import { logger } from '../utils/logger';

export const inicializarMonitoreo = () => {
  window.onerror = (mensaje, origen, linea, columna, error) => {
    logger.error('Error global detectado', {
      mensaje,
      origen,
      linea,
      columna,
      error
    });
  };
};
~~~

**Modificar `src/index.jsx`:**

~~~jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App';
import { inicializarMonitoreo } from './monitoring/globalErrorHandler';

inicializarMonitoreo();

createRoot(document.getElementById('root')).render(<App />);
~~~

**Paso a paso:**

1. Crea los archivos `.env.development`, `.env.production` y `.env.example`.
2. Crea las carpetas `src/config`, `src/utils` y `src/monitoring`.
3. Agrega `configuracion.js`, `logger.js` y `globalErrorHandler.js`.
4. Modifica `index.jsx` para iniciar el monitoreo global.
5. Ejecuta `npm run start` y observa la consola.
6. Ejecuta `npm run build` y verifica que el build compile.

**Actividad para ti:**  
Agrega un `logger.info` dentro de `App.jsx` para registrar que la aplicacion inicio en un entorno determinado.

**Espacio para responder:**

- Por que `.env.example` no debe contener valores reales?
- Que diferencia hay entre `warn` y `error`?
- Que captura `window.onerror`?

**Resultado esperado:**  
La aplicacion usa variables de entorno, logger basico y captura de errores globales.

**Error comun a evitar:**  
Guardar tokens privados reales en variables visibles del frontend.

**Mini reto:**  
Cambia `REACT_APP_HABILITAR_LOGS` entre `true` y `false`, reinicia el servidor y observa la consola.

---

# Actividades guiadas

## Actividad 1 - Reconocimiento

**Objetivo:** identificar que hace cada archivo principal del proyecto.  
**Instrucciones:** completa la tabla.

| Archivo | Funcion principal | Concepto asociado |
|---|---|---|
| `webpack.config.js` |  |  |
| `.babelrc` |  |  |
| `.env.development` |  |  |
| `src/utils/logger.js` |  |  |
| `src/monitoring/globalErrorHandler.js` |  |  |

**Que observar:** relacion entre archivo y etapa de produccion.  
**Que responder:** completa cada celda con una frase breve.  
**Resultado esperado:** identificas configuracion, transpilacion, variables, logs y monitoreo.  
**Criterio de validacion rapida:** tus respuestas deben mencionar al menos una palabra clave tecnica por archivo.

## Actividad 2 - Replica guiada

**Objetivo:** replicar el flujo de build.  
**Instrucciones:** ejecuta:

~~~bash
npm run build
~~~

Luego revisa la carpeta `dist`.

**Que modificar:** no modifiques codigo; solo ejecuta y observa.  
**Que observar:** archivos generados con hash y HTML final.  
**Que responder:**

- Que archivos aparecieron en `dist`?
- El archivo JavaScript tiene hash en el nombre?
- Que indica que el build fue exitoso?

**Resultado esperado:** carpeta `dist` generada.  
**Criterio de validacion rapida:** existe al menos un archivo `.js` dentro de `dist/js`.

## Actividad 3 - Modificacion controlada

**Objetivo:** cambiar una variable de entorno y validar su efecto.  
**Instrucciones:** cambia `REACT_APP_ENTORNO` en `.env.development` por:

~~~env
REACT_APP_ENTORNO=practica
~~~

**Que modificar:** solo el valor de la variable.  
**Que observar:** reinicia el servidor y valida si la configuracion se actualiza.  
**Que responder:**

- Por que se debe reiniciar el servidor despues de cambiar `.env`?
- Que pasaria si la variable no tiene el prefijo esperado?
- Que tipo de informacion no debe guardarse aqui?

**Resultado esperado:** la aplicacion lee el nuevo valor al reiniciar.  
**Criterio de validacion rapida:** no hay error de compilacion y el valor se puede consultar desde configuracion.

## Actividad 4 - Aplicacion

**Objetivo:** agregar una vista diferida nueva.  
**Instrucciones:** crea un componente `ReporteBundle.jsx` y cargalo con `React.lazy`.

**Codigo base:**

~~~jsx
export default function ReporteBundle() {
  return <h2>Reporte de bundle</h2>;
}
~~~

**Que modificar:** `App.jsx`, agregando import dinamico, boton y condicion de render.  
**Que observar:** al ejecutar el build, Webpack puede generar chunks adicionales.  
**Que responder:**

- Que linea usa `lazy`?
- Que muestra `Suspense` mientras carga?
- Por que esta tecnica ayuda al bundle inicial?

**Resultado esperado:** nueva vista cargada bajo demanda.  
**Criterio de validacion rapida:** al hacer clic en el boton, aparece `Reporte de bundle`.

## Actividad 5 - Integracion

**Objetivo:** integrar build, variables, logs y monitoreo en una explicacion tecnica.  
**Instrucciones:** redacta un parrafo tecnico de 8 a 10 lineas explicando como tu aplicacion queda preparada para produccion.

**Que modificar:** no modifiques codigo; redacta una explicacion.  
**Que observar:** debes mencionar minificacion, Webpack, Babel, variables, logs y monitoreo.  
**Que responder:** usa tus propias palabras.

**Resultado esperado:** explicacion clara sin copiar codigo completo.  
**Criterio de validacion rapida:** tu parrafo incluye al menos cinco conceptos tecnicos de la practica.

## Actividad 6 - Validacion con preguntas

Responde:

1. Que diferencia hay entre minificacion y tree shaking?
2. Que problema resuelve Babel?
3. Que problema resuelve Webpack?
4. Por que no se deben guardar secretos reales en frontend?
5. Que diferencia hay entre logs y monitoreo?
6. Que evidencia revisarias antes de decir que el build esta listo?

---

# Checklist final de aprendizaje

Marca cada punto cuando lo hayas completado:

- [ ] Puedo explicar que es minificacion.
- [ ] Puedo diferenciar Webpack y Babel.
- [ ] Puedo ejecutar `npm run start`.
- [ ] Puedo ejecutar `npm run build`.
- [ ] Puedo identificar la carpeta `dist`.
- [ ] Puedo explicar que hace `contenthash`.
- [ ] Puedo usar variables de entorno publicas sin colocar secretos reales.
- [ ] Puedo explicar que hace un logger.
- [ ] Puedo explicar que captura `window.onerror`.
- [ ] Puedo justificar por que una app en produccion debe ser observable.

# Cierre de la practica

Una aplicacion React lista para produccion no se define solo por compilar. Debe reducir peso, organizar dependencias, transformar codigo moderno, separar configuracion por entorno y registrar eventos importantes. La preparacion para produccion es una practica de calidad, rendimiento y mantenimiento.

**Recursos de refuerzo:**

- Blog: https://lideratecacademy.com/
- Canal YouTube: https://www.youtube.com/@LideratecAcademy
