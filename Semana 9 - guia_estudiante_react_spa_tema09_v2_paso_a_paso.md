---
title: "Guía del estudiante - React.js y SPA paso a paso"
subtitle: "Programación Web Avanzada - Tema 09"
author: "Lideratec Academy"
date: "2026"
lang: es-PE
geometry: margin=1in
fontsize: 10.5pt
mainfont: DejaVu Sans
monofont: DejaVu Sans Mono
colorlinks: true
linkcolor: blue
urlcolor: blue
---

# Guía del estudiante - React.js y SPA paso a paso

## Tema de la sesión

**React.js y SPA: JSX, componentes, Hooks y navegación**

Esta guía está diseñada para que construyas una mini aplicación React desde cero. No se asume que ya tengas archivos creados dentro de `src`, excepto los que genera Vite al iniciar el proyecto.

## Resultado final esperado

Al terminar, tendrás una SPA con:

- Una página de inicio `/`.
- Una página `about` en `/about`.
- Una ruta dinámica `/producto/:id`.
- Componentes reutilizables.
- JSX aplicado correctamente.
- `useState` para estado local.
- `useEffect` para cargar datos.
- React Router para navegar sin recargar la página completa.

## Antes de empezar

Necesitas:

- Node.js instalado.
- npm disponible.
- Visual Studio Code u otro editor.
- Navegador web.
- Terminal o CMD.

Valida en la terminal:

```bash
node -v
npm -v
```

Si alguno de los comandos no responde, primero instala o corrige Node.js antes de continuar.

## Estructura que vas a construir

Al finalizar, tu carpeta `src` debe quedar así:

```text
react-spa-tema09/
├─ src/
│  ├─ components/
│  │  ├─ Navbar.jsx
│  │  ├─ TituloSesion.jsx
│  │  ├─ TarjetaCurso.jsx
│  │  ├─ PerfilEstudiante.jsx
│  │  ├─ Contador.jsx
│  │  └─ Usuarios.jsx
│  ├─ pages/
│  │  ├─ Home.jsx
│  │  ├─ About.jsx
│  │  └─ ProductoDetalle.jsx
│  ├─ App.jsx
│  ├─ App.css
│  ├─ index.css
│  └─ main.jsx
├─ package.json
└─ vite.config.js
```

# Parte 1 - Crear el proyecto React

## Paso 1. Crear el proyecto con Vite

Abre una terminal en la carpeta donde guardas tus proyectos. Por ejemplo, en Windows puedes usar `Documentos`, `Escritorio` o una carpeta llamada `proyectos`.

Ejecuta:

```bash
npm create vite@latest react-spa-tema09 -- --template react
```

**Qué debe ocurrir:** se crea una carpeta llamada `react-spa-tema09`.

## Paso 2. Entrar a la carpeta del proyecto

Ejecuta:

```bash
cd react-spa-tema09
```

**Ahora tu terminal debe estar ubicada dentro de la carpeta del proyecto.**

## Paso 3. Instalar dependencias iniciales

Ejecuta:

```bash
npm install
```

**Qué debe ocurrir:** npm descarga las dependencias y aparece una carpeta `node_modules`.

## Paso 4. Abrir el proyecto en Visual Studio Code

Si usas VS Code, ejecuta:

```bash
code .
```

Si el comando no funciona, abre VS Code manualmente y selecciona:

```text
File > Open Folder > react-spa-tema09
```

## Paso 5. Probar que React funciona

En la terminal, ejecuta:

```bash
npm run dev
```

Abre la URL que aparezca, normalmente:

```text
http://localhost:5173/
```

**Resultado esperado:** ves la página inicial generada por Vite.

# Parte 2 - Limpiar el proyecto base

## Paso 6. Abrir el archivo `src/App.jsx`

Ruta exacta:

```text
src/App.jsx
```

Borra todo su contenido y pega temporalmente este código:

```jsx
export default function App() {
  return (
    <main>
      <h1>React.js y SPA</h1>
      <p>Proyecto base funcionando.</p>
    </main>
  );
}
```

Guarda el archivo.

**Resultado esperado:** en el navegador debe aparecer el título `React.js y SPA`.

## Paso 7. Abrir el archivo `src/App.css`

Ruta exacta:

```text
src/App.css
```

Borra todo su contenido y pega:

```css
.app {
  max-width: 1100px;
  margin: 0 auto;
  padding: 32px;
}

.card {
  border: 1px solid #d0d7de;
  border-radius: 12px;
  padding: 20px;
  margin: 16px 0;
  background: #ffffff;
}

.navbar {
  display: flex;
  gap: 16px;
  padding: 16px;
  border-bottom: 1px solid #d0d7de;
  margin-bottom: 24px;
}

.navbar a {
  color: #0b63ce;
  text-decoration: none;
  font-weight: 700;
}

.navbar a:hover {
  text-decoration: underline;
}

button {
  margin-right: 8px;
  padding: 10px 14px;
  border: 1px solid #0b63ce;
  border-radius: 8px;
  background: #0b63ce;
  color: white;
  cursor: pointer;
}

button.secondary {
  background: white;
  color: #0b63ce;
}

.error {
  color: #b00020;
  font-weight: 700;
}

.loading {
  color: #555;
}
```

Guarda el archivo.

## Paso 8. Abrir el archivo `src/index.css`

Ruta exacta:

```text
src/index.css
```

Borra todo su contenido y pega:

```css
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
  background: #f6f8fa;
  color: #1f2328;
}

h1, h2, h3 {
  color: #0b1f3a;
}
```

Guarda el archivo.

# Parte 3 - Crear componentes con JSX

## Paso 9. Crear la carpeta `components`

En VS Code, dentro de `src`, crea una carpeta llamada:

```text
components
```

La ruta debe quedar así:

```text
src/components
```

## Paso 10. Crear el archivo `TituloSesion.jsx`

Dentro de `src/components`, crea el archivo:

```text
TituloSesion.jsx
```

Pega este código:

```jsx
export default function TituloSesion() {
  return (
    <section className="card">
      <h1>React.js y SPA</h1>
      <p>JSX, componentes, Hooks y navegación sin recarga completa.</p>
    </section>
  );
}
```

**Qué estás practicando:** componente funcional que retorna JSX.

## Paso 11. Crear el archivo `TarjetaCurso.jsx`

Dentro de `src/components`, crea:

```text
TarjetaCurso.jsx
```

Pega:

```jsx
export default function TarjetaCurso() {
  const curso = "Programación Web Avanzada";
  const tema = "Tema 09: Introducción a React.js y SPA";

  return (
    <section className="card">
      <h2>{curso}</h2>
      <p>{tema}</p>
      <p>
        JSX permite escribir una estructura similar a HTML dentro de JavaScript.
      </p>
    </section>
  );
}
```

**Qué estás practicando:** uso de variables dentro de JSX mediante `{}`.

## Paso 12. Crear el archivo `PerfilEstudiante.jsx`

Dentro de `src/components`, crea:

```text
PerfilEstudiante.jsx
```

Pega:

```jsx
export default function PerfilEstudiante({ nombre, ciclo }) {
  return (
    <section className="card">
      <h2>Perfil del estudiante</h2>
      <p>Nombre: {nombre}</p>
      <p>Ciclo: {ciclo}</p>
      <p>Objetivo: construir una SPA básica con React.</p>
    </section>
  );
}
```

**Qué estás practicando:** props. El componente recibe `nombre` y `ciclo` desde otro archivo.

## Paso 13. Probar los tres componentes en `src/App.jsx`

Abre:

```text
src/App.jsx
```

Reemplaza todo el contenido por:

```jsx
import "./App.css";
import TituloSesion from "./components/TituloSesion";
import TarjetaCurso from "./components/TarjetaCurso";
import PerfilEstudiante from "./components/PerfilEstudiante";

export default function App() {
  return (
    <main className="app">
      <TituloSesion />
      <TarjetaCurso />
      <PerfilEstudiante nombre="Estudiante ISIL" ciclo="Avanzado" />
    </main>
  );
}
```

Guarda y revisa el navegador.

**Resultado esperado:** ves tres tarjetas: título, curso y perfil.

## Errores comunes de esta parte

| Error | Causa | Solución |
|---|---|---|
| `Failed to resolve import` | Nombre de archivo mal escrito | Verifica mayúsculas, minúsculas y ruta |
| Pantalla en blanco | Error de JSX | Mira la consola del navegador o terminal |
| `class` no funciona como esperas | En JSX se usa `className` | Cambia `class` por `className` |
| Componente no renderiza | El nombre no inicia en mayúscula | Usa `TituloSesion`, no `tituloSesion` |

# Parte 4 - Usar Hook `useState`

## Paso 14. Crear el archivo `Contador.jsx`

Dentro de `src/components`, crea:

```text
Contador.jsx
```

Pega:

```jsx
import { useState } from "react";

export default function Contador() {
  const [contador, setContador] = useState(0);

  const incrementar = () => {
    setContador((valorAnterior) => valorAnterior + 1);
  };

  const reiniciar = () => {
    setContador(0);
  };

  return (
    <section className="card">
      <h2>Contador con useState</h2>
      <p>Valor actual: {contador}</p>
      <button onClick={incrementar}>Incrementar</button>
      <button className="secondary" onClick={reiniciar}>Reiniciar</button>
    </section>
  );
}
```

**Qué estás practicando:** estado local en un componente funcional.

## Paso 15. Agregar `Contador` en `src/App.jsx`

Abre:

```text
src/App.jsx
```

Agrega el import:

```jsx
import Contador from "./components/Contador";
```

Y dentro del `return`, debajo de `PerfilEstudiante`, agrega:

```jsx
<Contador />
```

Tu archivo completo debe quedar así:

```jsx
import "./App.css";
import TituloSesion from "./components/TituloSesion";
import TarjetaCurso from "./components/TarjetaCurso";
import PerfilEstudiante from "./components/PerfilEstudiante";
import Contador from "./components/Contador";

export default function App() {
  return (
    <main className="app">
      <TituloSesion />
      <TarjetaCurso />
      <PerfilEstudiante nombre="Estudiante ISIL" ciclo="Avanzado" />
      <Contador />
    </main>
  );
}
```

**Resultado esperado:** al hacer clic en `Incrementar`, el valor cambia sin recargar la página.

# Parte 5 - Usar Hook `useEffect`

## Paso 16. Crear el archivo `Usuarios.jsx`

Dentro de `src/components`, crea:

```text
Usuarios.jsx
```

Pega:

```jsx
import { useEffect, useState } from "react";

export default function Usuarios() {
  const [usuarios, setUsuarios] = useState([]);
  const [cargando, setCargando] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function cargarUsuarios() {
      try {
        const respuesta = await fetch("https://jsonplaceholder.typicode.com/users");
        const datos = await respuesta.json();
        setUsuarios(datos);
      } catch (e) {
        setError("No se pudieron cargar los usuarios.");
      } finally {
        setCargando(false);
      }
    }

    cargarUsuarios();
  }, []);

  return (
    <section className="card">
      <h2>Usuarios con useEffect</h2>

      {cargando && <p className="loading">Cargando usuarios...</p>}
      {error && <p className="error">{error}</p>}

      <ul>
        {usuarios.map((usuario) => (
          <li key={usuario.id}>{usuario.name}</li>
        ))}
      </ul>
    </section>
  );
}
```

**Qué estás practicando:** efecto secundario para cargar datos externos.

## Paso 17. Agregar `Usuarios` en `src/App.jsx`

Abre:

```text
src/App.jsx
```

Agrega el import:

```jsx
import Usuarios from "./components/Usuarios";
```

Luego agrega el componente debajo de `Contador`:

```jsx
<Usuarios />
```

Tu `src/App.jsx` temporal queda así:

```jsx
import "./App.css";
import TituloSesion from "./components/TituloSesion";
import TarjetaCurso from "./components/TarjetaCurso";
import PerfilEstudiante from "./components/PerfilEstudiante";
import Contador from "./components/Contador";
import Usuarios from "./components/Usuarios";

export default function App() {
  return (
    <main className="app">
      <TituloSesion />
      <TarjetaCurso />
      <PerfilEstudiante nombre="Estudiante ISIL" ciclo="Avanzado" />
      <Contador />
      <Usuarios />
    </main>
  );
}
```

**Resultado esperado:** aparece una lista de usuarios.

**Si falla:** verifica conexión a internet. Si no hay internet, el mensaje de error debe mostrarse sin romper la aplicación.

# Parte 6 - Instalar y configurar React Router

## Paso 18. Detener el servidor temporalmente

En la terminal donde está corriendo Vite, presiona:

```text
Ctrl + C
```

Si pregunta si deseas terminar el proceso, confirma con `S` o `Y`, según tu sistema.

## Paso 19. Instalar React Router DOM

Ejecuta:

```bash
npm install react-router-dom
```

**Qué debe ocurrir:** se agrega React Router a las dependencias del proyecto.

## Paso 20. Volver a levantar el servidor

Ejecuta:

```bash
npm run dev
```

Mantén esta terminal abierta.

## Paso 21. Envolver la aplicación con `BrowserRouter` en `src/main.jsx`

Abre:

```text
src/main.jsx
```

Reemplaza todo su contenido por:

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import "./index.css";
import App from "./App.jsx";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>
);
```

**Qué estás haciendo:** habilitas el sistema de rutas para toda la aplicación.

# Parte 7 - Crear páginas de la SPA

## Paso 22. Crear la carpeta `pages`

Dentro de `src`, crea una carpeta llamada:

```text
pages
```

Debe quedar así:

```text
src/pages
```

## Paso 23. Crear la página `Home.jsx`

Dentro de `src/pages`, crea:

```text
Home.jsx
```

Pega:

```jsx
import TituloSesion from "../components/TituloSesion";
import TarjetaCurso from "../components/TarjetaCurso";
import PerfilEstudiante from "../components/PerfilEstudiante";
import Contador from "../components/Contador";
import Usuarios from "../components/Usuarios";

export default function Home() {
  return (
    <>
      <TituloSesion />
      <TarjetaCurso />
      <PerfilEstudiante nombre="Estudiante ISIL" ciclo="Avanzado" />
      <Contador />
      <Usuarios />
    </>
  );
}
```

## Paso 24. Crear la página `About.jsx`

Dentro de `src/pages`, crea:

```text
About.jsx
```

Pega:

```jsx
export default function About() {
  return (
    <section className="card">
      <h1>Acerca de esta SPA</h1>
      <p>
        Esta aplicación demuestra cómo React organiza la interfaz mediante JSX,
        componentes, Hooks y navegación interna.
      </p>
      <p>
        Al cambiar de ruta, React Router actualiza la vista sin recargar toda la página.
      </p>
    </section>
  );
}
```

## Paso 25. Crear la página `ProductoDetalle.jsx`

Dentro de `src/pages`, crea:

```text
ProductoDetalle.jsx
```

Pega:

```jsx
import { useParams } from "react-router-dom";

export default function ProductoDetalle() {
  const { id } = useParams();

  return (
    <section className="card">
      <h1>Detalle de producto</h1>
      <p>Ruta dinámica actual: /producto/{id}</p>
      <p>ID recibido con useParams: {id}</p>
    </section>
  );
}
```

**Qué estás practicando:** rutas dinámicas y lectura de parámetros.

# Parte 8 - Crear la navegación

## Paso 26. Crear el archivo `Navbar.jsx`

Dentro de `src/components`, crea:

```text
Navbar.jsx
```

Pega:

```jsx
import { Link } from "react-router-dom";

export default function Navbar() {
  return (
    <nav className="navbar">
      <Link to="/">Inicio</Link>
      <Link to="/about">Acerca</Link>
      <Link to="/producto/1">Producto 1</Link>
      <Link to="/producto/2">Producto 2</Link>
    </nav>
  );
}
```

**Importante:** usa `Link`, no `<a>`, porque `Link` permite navegación SPA sin recarga completa.

# Parte 9 - Declarar rutas en `App.jsx`

## Paso 27. Reemplazar `src/App.jsx` con rutas

Abre:

```text
src/App.jsx
```

Reemplaza todo su contenido por:

```jsx
import "./App.css";
import { Route, Routes } from "react-router-dom";
import Navbar from "./components/Navbar";
import Home from "./pages/Home";
import About from "./pages/About";
import ProductoDetalle from "./pages/ProductoDetalle";

export default function App() {
  return (
    <div>
      <Navbar />
      <main className="app">
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/producto/:id" element={<ProductoDetalle />} />
        </Routes>
      </main>
    </div>
  );
}
```

**Qué estás haciendo:** conectas URLs con componentes de página.

# Parte 10 - Verificación final

## Paso 28. Probar la ruta principal

Abre:

```text
http://localhost:5173/
```

Debe mostrar:

- Título de sesión.
- Tarjeta del curso.
- Perfil del estudiante.
- Contador.
- Lista de usuarios.

## Paso 29. Probar la ruta `/about`

Haz clic en `Acerca` o abre:

```text
http://localhost:5173/about
```

Debe mostrarse la página acerca de la SPA.

## Paso 30. Probar la ruta dinámica `/producto/1`

Haz clic en `Producto 1` o abre:

```text
http://localhost:5173/producto/1
```

Debe mostrarse:

```text
ID recibido con useParams: 1
```

## Paso 31. Probar la ruta dinámica `/producto/2`

Haz clic en `Producto 2` o abre:

```text
http://localhost:5173/producto/2
```

Debe mostrarse:

```text
ID recibido con useParams: 2
```

## Paso 32. Confirmar que no hay recarga completa

Haz clic entre `Inicio`, `Acerca`, `Producto 1` y `Producto 2`.

**Observación esperada:** cambia la vista y la URL, pero no se recarga todo el documento HTML.

# Checklist de entrega del estudiante

Marca cada punto antes de entregar:

- [ ] El proyecto se llama `react-spa-tema09`.
- [ ] El proyecto ejecuta con `npm run dev`.
- [ ] Existe la carpeta `src/components`.
- [ ] Existe la carpeta `src/pages`.
- [ ] Existe `src/components/TituloSesion.jsx`.
- [ ] Existe `src/components/TarjetaCurso.jsx`.
- [ ] Existe `src/components/PerfilEstudiante.jsx`.
- [ ] Existe `src/components/Contador.jsx`.
- [ ] Existe `src/components/Usuarios.jsx`.
- [ ] Existe `src/components/Navbar.jsx`.
- [ ] Existe `src/pages/Home.jsx`.
- [ ] Existe `src/pages/About.jsx`.
- [ ] Existe `src/pages/ProductoDetalle.jsx`.
- [ ] `src/main.jsx` usa `BrowserRouter`.
- [ ] `src/App.jsx` usa `Routes` y `Route`.
- [ ] La ruta `/` funciona.
- [ ] La ruta `/about` funciona.
- [ ] La ruta `/producto/1` funciona.
- [ ] La ruta `/producto/2` funciona.
- [ ] El contador incrementa sin recargar la página.
- [ ] La lista de usuarios se muestra o aparece un error controlado si no hay internet.

# Preguntas de reflexión

1. ¿Qué archivo habilita el enrutador principal de la aplicación?
2. ¿Qué archivo declara las rutas?
3. ¿Qué diferencia hay entre `Link` y una etiqueta `<a>` tradicional?
4. ¿Por qué JSX usa `className` y no `class`?
5. ¿Qué problema resuelve `useState`?
6. ¿Qué problema resuelve `useEffect`?
7. ¿Para qué sirve `useParams`?
8. ¿Por qué esta aplicación puede considerarse una SPA?

# Errores frecuentes y solución

| Problema | Posible causa | Solución |
|---|---|---|
| `npm run dev` no funciona | No ejecutaste `npm install` | Ejecuta `npm install` |
| `BrowserRouter is not defined` | Falta import en `main.jsx` | Importa desde `react-router-dom` |
| `Routes is not defined` | Falta import en `App.jsx` | Importa `Routes` y `Route` |
| `useParams is not defined` | Falta import en `ProductoDetalle.jsx` | Importa `useParams` |
| Se recarga la página al navegar | Usaste `<a>` | Usa `Link` |
| No aparece un componente | Ruta o nombre mal escrito | Verifica mayúsculas y ubicación |
| Error con `class` | JSX no usa `class` | Cambia a `className` |

# Entrega sugerida

Entrega una captura de pantalla o repositorio donde se vea:

1. La ruta `/` funcionando.
2. La ruta `/about` funcionando.
3. La ruta `/producto/1` funcionando.
4. El árbol de archivos del proyecto.
5. El componente `Contador.jsx`.
6. El archivo `App.jsx` con las rutas.

# Nota técnica de vigencia

Esta guía mantiene `react-router-dom` porque es coherente con el material de clase. Si el docente decide actualizar a React Router 7 con el paquete `react-router`, se deben cambiar los imports de `react-router-dom` a `react-router` de forma consistente en todo el proyecto.
