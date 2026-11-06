---
course_id: ISIL-30690
session_id: S11
source_origin: PPT
version: F13-S11-V3-SIMPLE
---


# Guía del estudiante — Sesión 11

**ISIL | Programación Web Avanzada | Lideratec Academy**  
**De lo básico a lo avanzado, sin saltarse pasos.**

**Meta de hoy:** (1) consultar una API y reconocer sus respuestas; (2) iniciar sesión en React y pedir un perfil con JWT. Estos son **solo dos temas**. Haremos 5 ejemplos pequeños y 5 ejercicios de comprobación. No necesitas MongoDB, Redux ni Next.js.

## 1. Mapa de la clase (120 minutos)

| Minutos | Aprendes | ¿Qué verás funcionar? |
|---|---|---|
| Preparación anterior a clase | Abrir dos terminales, crear la API y React | API y página abiertas |
| 0–15 | EJ01 Fetch en Node | Lista de usuarios en consola |
| 15–30 | EJ02 Axios en Node | Misma lista y diagnóstico de 404 |
| 30–55 | EJ03 React + Fetch | Botón que muestra usuarios |
| 55–85 | EJ04 React + Axios | Consultar / registrar, HTTP 201 y 400 |
| 85–115 | EJ05 Login JWT | 200 con Bearer; 401 sin credencial |
| 115–120 | Explicar resultados y tareas | Evidencias de 3 pruebas |

**Cómo usar la guía:** lee el objetivo de cada ejemplo, escribe el archivo exacto, ejecuta el comando, verifica el resultado, explica las líneas clave y recién pasa al siguiente. Los archivos de FASE 15 sirven para **comparar al final**, no para saltarte la escritura.

## 2. Vocabulario imprescindible en lenguaje sencillo

**API:** una aplicación que recibe solicitudes y responde con datos. **GET:** pedir datos. **POST:** enviar datos para crear algo. **JSON:** texto estructurado en objetos o listas. **HTTP 200:** solicitud exitosa; **201:** recurso creado; **400:** datos incorrectos; **401:** falta autenticación válida; **404:** ruta no encontrada. **Frontend:** lo que ves en el navegador (React). **Backend:** programa que atiende las peticiones (Express).

**Node.js:** ejecuta JavaScript fuera del navegador. **npm:** instala bibliotecas y ejecuta comandos del proyecto. **Vite:** prepara y sirve React durante el desarrollo. **JSX:** permite describir la interfaz con etiquetas parecidas a HTML dentro de JavaScript. **useState:** guarda información del componente. **JWT:** token firmado para demostrar una autenticación; solo el backend puede validarlo. **Bearer:** palabra que acompaña al token en la cabecera `Authorization`.

## 3. Preparación desde cero — antes de los ejemplos

### 3.1 Comprueba que tienes Node.js y abre VS Code

En VS Code elige **Terminal → Nueva terminal**. Escribe estas instrucciones una por una. Son comandos de comprobación, todavía no inician el servidor.

```bash
node -v
npm -v
```

Si alguno no se reconoce, instala Node.js desde su sitio oficial y vuelve a abrir VS Code. Para la plantilla Vite utilizada, conviene Node 20.19+ o 22.12+ compatible. Si aparece una versión, puedes continuar.

### 3.2 Terminal A — prepara el backend

En la carpeta donde guardas tus proyectos, escribe:

```bash
mkdir s11-react-api-jwt
cd s11-react-api-jwt
mkdir backend
cd backend
npm init -y
```

**Qué pasó:** `mkdir` crea carpetas; `cd` entra a ellas; `npm init -y` genera `backend/package.json`. Este archivo describe el proyecto: aún NO ejecuta el servidor. **Comprueba** que el explorador muestre `s11-react-api-jwt/backend/package.json`.

### 3.3 Instala SOLO dos paquetes para el primer ejercicio

```bash
npm install express@4 cors
```

**¿Por qué Express?** Node permite crear servidores, pero Express simplifica registrar rutas: `app.get("/api/usuarios", ...)` responderá cuando alguien solicite esa dirección. **¿Por qué CORS?** El navegador cargará React en el puerto 5173 y la API estará en el 4000; CORS permite que el navegador lea la respuesta entre esos orígenes. **CORS no comprueba contraseñas ni JWT.**

`npm install` descarga paquetes a `node_modules` y los anota en `package.json`. No envíes `node_modules` en tus entregas. Todavía **no** instalamos Axios, JWT ni bibliotecas de autenticación: se incorporarán cuando hagan falta.

### 3.4 Crea `backend/server.mjs` y escribe tu primera API

Desde el explorador VS Code: clic derecho en **backend → Nuevo archivo → `server.mjs`**. Pega o escribe por bloques el siguiente código. Este es el **paso 1**, antes de agregar POST o login.

```javascript
import express from "express";
import cors from "cors";

const app = express();
const PORT = 4000;
app.use(cors({ origin: "http://localhost:5173" }));
app.use(express.json());

let usuarios = [
  { id: 1, nombre: "Ana" },
  { id: 2, nombre: "Luis" }
];

app.get("/api/usuarios", (req, res) => {
  res.json(usuarios);
});

app.get("/api/usuarios-mal", (req, res) => {
  res.json({ mensaje: "No es una lista" });
});

app.listen(PORT, () => {
  console.log(`API lista en http://localhost:${PORT}`);
});
```

**Léelo por partes:** `import` carga Express y CORS; `express()` crea la aplicación; `PORT = 4000` determina la dirección; `cors(...)` permite al React local acceder; `express.json()` prepara el backend para leer cuerpos JSON cuando agreguemos POST. `usuarios` es una lista en memoria (se pierde al reiniciar). `app.get` crea la ruta; `res.json(usuarios)` devuelve un arreglo. `/api/usuarios-mal` devolverá **otro formato** para aprender a validarlo. `app.listen` enciende el servidor.

**Ahora ejecuta**, desde la terminal ubicada en `backend`:

```bash
node server.mjs
```

**Deberías ver:** `API lista en http://localhost:4000`. Abre `http://localhost:4000/api/usuarios` en el navegador: verás una lista con Ana y Luis. **No cierres esta terminal.** Si aparece `Cannot find package express`, ejecutaste la instalación en otra carpeta; revisa la ruta. Si aparece `EADDRINUSE`, otro proceso utiliza el puerto 4000.

**Por ahora usa `node server.mjs`**, que ejecuta directamente el archivo. Más adelante `npm start` será un alias que hace lo mismo cuando `package.json` contenga el script correspondiente. En FASE 15 se incluye ese alias configurado.

### 3.5 Terminal B — prepara React (NO detengas la terminal A)

Abre **Terminal → Nueva terminal**; debe ser la **Terminal B**. Ve a la carpeta `s11-react-api-jwt` (desde `backend` utiliza `cd ..`). Después escribe:

```bash
npm create vite@latest frontend -- --template react
cd frontend
npm install
npm run dev
```

**Explicación:** `npm create vite` crea los archivos de React en `frontend`; `cd frontend` entra allí; `npm install` instala React, React DOM y el resto de dependencias de Vite; `npm run dev` inicia la web local. Abre la dirección indicada, normalmente `http://localhost:5173`. **No ejecutes estos comandos dentro de `backend`.**

La plantilla contiene `src/App.jsx`, el componente principal. **JSX** combina JavaScript con etiquetas que React muestra como interfaz. Cuando guardes los cambios, Vite actualizará la pantalla automáticamente.

## 4. EJ01 — Fetch en Node.js: pedir una lista (básico)

**Objetivo:** comprender cómo una solicitud GET obtiene datos. **Punto de partida:** Terminal A muestra `API lista...` y sigue abierta. Abre una **Terminal C** en `backend`; no cierres el servidor.

Crea la carpeta `backend/ejemplos` y el archivo `EJ01_fetch.mjs`. Copia este código:

```javascript
const url = "http://localhost:4000/api/usuarios";

try {
  const respuesta = await fetch(url);
  if (!respuesta.ok) throw new Error(`HTTP ${respuesta.status}`);
  const usuarios = await respuesta.json();
  console.table(usuarios);
} catch (error) {
  console.error("No se pudo consultar:", error.message);
}
```

**Lectura guiada:** `url` indica dónde consultar; `await fetch(url)` espera una respuesta HTTP; `respuesta.ok` vale `true` para respuestas exitosas; `await respuesta.json()` convierte JSON a datos JavaScript; `console.table()` los muestra. `try/catch` controla errores, incluidos los que nosotros lanzamos con `throw`. Fetch **no lanza un error automáticamente por un 404**: debemos revisar `ok`.

**Ejecuta en Terminal C, desde `backend`:**
```bash
node ejemplos/EJ01_fetch.mjs
```

**Resultado:** tabla con 2 filas, Ana y Luis. **Interpreta:** el cliente obtuvo HTTP 200, convirtió JSON y mostró datos. Si ves `fetch failed`, casi siempre la API de la Terminal A no está encendida.

**T01 — Comprueba un 404:** sin modificar EJ01, crea `backend/tareas/T01_fetch_404.mjs` y escribe la variante siguiente; ejecutar no rompe tu ejemplo.

```javascript
const respuesta = await fetch("http://localhost:4000/api/no-existe");
console.log("¿Respuesta OK?", respuesta.ok);
console.log("Estado HTTP:", respuesta.status);
// Fetch entrega una respuesta 404; no lanza excepción solo por ese código.
```

Ejecuta `node tareas/T01_fetch_404.mjs`. Esperado: `false` y `404`. **Pregunta:** ¿por qué un 404 es una respuesta HTTP y no necesariamente una caída de internet?

## 5. EJ02 — Axios en Node.js: pedir lo mismo (básico + comparación)

**Objetivo:** comparar con EJ01, cambiando solo la herramienta HTTP. **Instala ahora Axios en `backend`** (Terminal C):
```bash
npm install axios
```

**¿Qué es Axios?** Es una biblioteca que hace solicitudes HTTP. A diferencia de Fetch, en Axios los datos JSON suelen estar listos en `respuesta.data`, y los estados como 404 hacen que la promesa se rechace bajo la configuración habitual. **Por eso lo instalamos**; Fetch ya venía con Node y no requería instalación.

Crea `backend/ejemplos/EJ02_axios.mjs`:

```javascript
import axios from "axios";
const url = "http://localhost:4000/api/usuarios";

try {
  const respuesta = await axios.get(url);
  console.table(respuesta.data);
} catch (error) {
  console.error("Fallo:", error.response?.status ?? error.message);
}
```

`import axios` incorpora la biblioteca. `axios.get(url)` envía GET; `respuesta.data` contiene el arreglo. En el `catch`, `error.response?.status` consulta el estado si hubo respuesta HTTP; si no hubo respuesta usa `error.message`.

Ejecuta desde `backend`:
```bash
node ejemplos/EJ02_axios.mjs
```
**Resultado:** los mismos dos usuarios. **Conclusión:** Fetch y Axios solicitan el mismo recurso; cambian su sintaxis y cómo exponen los errores.

**T02 — Provoca el 404 en Axios.** Crea `backend/tareas/T02_axios_404.mjs`:

```javascript
import axios from "axios";
try {
  await axios.get("http://localhost:4000/api/no-existe");
} catch (error) {
  console.log("Axios rechazó la petición con estado:", error.response?.status);
}
```

Ejecuta `node tareas/T02_axios_404.mjs`. Debe imprimir `404`. Explica por qué aquí usamos `catch`, mientras que en T01 inspeccionamos `respuesta.ok`.

## 6. EJ03 — React + Fetch: mostrar usuarios en la página (intermedio)

**Objetivo:** pasar de una consola Node a una pantalla React. **No instales más paquetes.** Vuelve a VS Code. Dentro de `frontend/src`, crea `EJ03_UsuariosFetch.jsx`:

```jsx
import { useState } from "react";

export default function EJ03_UsuariosFetch() {
  const [usuarios, setUsuarios] = useState([]);
  const [mensaje, setMensaje] = useState("Pulsa Consultar para comenzar");

  async function consultar() {
    setMensaje("Cargando...");
    try {
      const respuesta = await fetch("http://localhost:4000/api/usuarios");
      if (!respuesta.ok) throw new Error(`HTTP ${respuesta.status}`);
      const datos = await respuesta.json();
      if (!Array.isArray(datos)) throw new Error("La API no devolvió una lista");
      setUsuarios(datos);
      setMensaje(`Se encontraron ${datos.length} usuarios`);
    } catch (error) {
      setUsuarios([]);
      setMensaje(`Error: ${error.message}`);
    }
  }

  return (
    <section>
      <h2>EJ03 - Fetch en React</h2>
      <button onClick={consultar}>Consultar usuarios</button>
      <p role="status">{mensaje}</p>
      <ul>{usuarios.map((u) => <li key={u.id}>{u.nombre}</li>)}</ul>
    </section>
  );
}
```

**Entiende el componente antes de ejecutarlo:** `useState([])` crea un lugar donde React guarda la lista de usuarios. `useState("...")` guarda el mensaje que lee la persona. `setMensaje("Cargando...")` actualiza la interfaz al empezar. `fetch()` consulta la API; `respuesta.ok` verifica HTTP; `Array.isArray(datos)` evita tratar un objeto como lista. `setUsuarios(datos)` solicita que React actualice la página. `onClick={consultar}` conecta botón y función; `.map()` convierte cada usuario en un `<li>`; `key={u.id}` identifica cada elemento de la lista.

Para mostrarlo, **reemplaza solamente `frontend/src/App.jsx`** con este código simple (más adelante lo ampliaremos):

```jsx
import EJ03_UsuariosFetch from "./EJ03_UsuariosFetch.jsx";
export default function App() {
  return <main><EJ03_UsuariosFetch /></main>;
}
```

**Ejecuta:** la Terminal B ya debería mostrar Vite en `http://localhost:5173`. Si está detenida, sitúate en `frontend` y usa `npm run dev`. Haz clic en **Consultar usuarios**. Debes ver Ana y Luis. Si aparece error de CORS, verifica el dominio y puerto que aparecen en el código y confirma que Express sigue abierto.

**T03 — Valida un JSON incorrecto.** En `EJ03_UsuariosFetch.jsx`, modifica **temporalmente** la URL `/api/usuarios` por `/api/usuarios-mal`, guarda y pulsa Consultar. Debe aparecer `La API no devolvió una lista`. Después **restaura** la URL original. En FASE 15 hay un componente de solución `T03_FormatoIncorrecto.jsx`, por si deseas comparar.

**¿Y `useEffect`?** En esta primera práctica hacemos la consulta al pulsar un botón para ver la causa y el efecto. `useEffect` permitiría ejecutarla al montar el componente, pero no hace falta para entender GET. El próximo nivel será automatizarla; no agregues conceptos antes de controlar este comportamiento.

## 7. EJ04 — React + Axios: consultar y registrar (intermedio)

**Objetivo:** realizar GET y POST en la misma aplicación. **Paso 1: prepara el servidor para recibir POST.** En `backend/server.mjs`, coloca este bloque **antes de `app.listen(...)`**:

```javascript
app.post("/api/usuarios", (req, res) => {
  const nombre = typeof req.body?.nombre === "string"
    ? req.body.nombre.trim()
    : "";
  if (!nombre) {
    return res.status(400).json({ mensaje: "El nombre es obligatorio" });
  }
  const nuevo = { id: Date.now(), nombre };
  usuarios.push(nuevo);
  res.status(201).json(nuevo);
});
```

**Explicación:** `app.post` recibe datos nuevos. `req.body` contiene JSON porque antes activamos `express.json()`. Se valida que `nombre` sea texto y no esté vacío. `res.status(400)` responde si faltan datos; `usuarios.push(nuevo)` modifica la lista temporal; `201` indica creación. **Reinicia** Terminal A con `Ctrl+C` y `node server.mjs` para cargar el cambio. Abre la API y comprueba que sigue mostrando usuarios.

**Paso 2: instala Axios esta vez en la carpeta `frontend`**. En una terminal nueva situada en `frontend`, ejecuta:
```bash
npm install axios
```
**¿Por qué otra vez?** `backend` y `frontend` tienen carpetas `node_modules` separadas. Axios usado por Node no se vuelve disponible automáticamente en React.

**Paso 3:** crea `frontend/src/EJ04_UsuariosAxios.jsx`:

```jsx
import { useState } from "react";
import axios from "axios";

export default function EJ04_UsuariosAxios() {
  const [nombre, setNombre] = useState("");
  const [usuarios, setUsuarios] = useState([]);
  const [mensaje, setMensaje] = useState("Consulta o registra un usuario");

  async function consultar() {
    try {
      const respuesta = await axios.get("http://localhost:4000/api/usuarios");
      if (!Array.isArray(respuesta.data)) throw new Error("Formato incorrecto");
      setUsuarios(respuesta.data);
      setMensaje("Lista actualizada");
    } catch (error) {
      setMensaje(`Error GET: ${error.response?.status ?? error.message}`);
    }
  }

  async function guardar(evento) {
    evento.preventDefault();
    try {
      const respuesta = await axios.post("http://localhost:4000/api/usuarios", { nombre });
      setNombre("");
      await consultar();
      setMensaje(`Creado con HTTP ${respuesta.status}: ${respuesta.data.nombre}`);
    } catch (error) {
      setMensaje(`Error POST: ${error.response?.status ?? error.message}`);
    }
  }

  return (
    <section>
      <h2>EJ04 - GET y POST con Axios</h2>
      <button onClick={consultar}>Consultar</button>
      <form onSubmit={guardar} noValidate>
        <label>Nombre: <input value={nombre} onChange={(e) => setNombre(e.target.value)} /></label>
        <button type="submit">Registrar</button>
      </form>
      <p role="status">{mensaje}</p>
      <ul>{usuarios.map((u) => <li key={u.id}>{u.nombre}</li>)}</ul>
    </section>
  );
}
```

**Lee por secciones:** `nombre` guarda lo escrito en el campo; `usuarios` guarda el listado; `mensaje` muestra el resultado. `axios.get()` consulta. `axios.post(..., { nombre })` envía JSON para crear un usuario. `evento.preventDefault()` impide que el formulario recargue la página. `setNombre("")` limpia el input. `error.response?.status` permite mostrar el 400 que envió Express.

**Para verlo:** sustituye temporalmente `frontend/src/App.jsx` por:
```jsx
import EJ04_UsuariosAxios from "./EJ04_UsuariosAxios.jsx";
export default function App() { return <main><EJ04_UsuariosAxios /></main>; }
```

Pulsa **Consultar** (Ana y Luis), escribe `María`, pulsa **Registrar** y observa que aparece el nuevo usuario y un mensaje de HTTP 201. La lista está en memoria: si reinicias el backend, el registro desaparecerá.

**T04 — Verifica el error 400:** pulsa Registrar sin nombre o con espacios. Debe aparecer `Error POST: 400`. El servidor, no el formulario, decide que el valor es inválido. Consulta también `T04_Error400.jsx` del laboratorio como solución alternativa.

## 8. EJ05 — Login JWT + perfil protegido (avanzado, paso a paso)

**Ya dominas GET, POST, 200, 201 y 400.** Solo ahora añadimos autenticación. **Resultado:** el backend devuelve JWT en el login, React lo mantiene temporalmente en memoria y solicita un perfil con `Authorization: Bearer ...`. Sin token el backend debe responder 401.

### 8.1 Instala las 3 dependencias nuevas del backend

En la terminal dentro de `backend` (no en `frontend`) ejecuta:
```bash
npm install jsonwebtoken bcryptjs dotenv
```

**`jsonwebtoken`:** crea un token firmado (`jwt.sign`) y comprueba su firma y caducidad (`jwt.verify`); NO cifra datos. **`bcryptjs`:** compara la clave ingresada con un **hash**; un hash es un valor calculado para no almacenar contraseñas en texto plano. Usaremos una cuenta *ficticia y pública* solo para el laboratorio. **`dotenv`:** carga el secreto del servidor desde `.env` a `process.env`, evitando dejar la clave firmante escrita dentro de `server.mjs`. **No instalamos `cookie-parser`:** todavía no implementaremos cookies ni refresh, para no sobrecargar esta primera clase.

### 8.2 Genera la clave de firma del servidor, no inventes una fija

Crea `backend/generar-secreto.mjs`:

```javascript
import { randomBytes } from "node:crypto";
import { existsSync, writeFileSync } from "node:fs";

if (existsSync(".env")) {
  console.log("Ya existe .env; se conserva su clave actual.");
} else {
  const secreto = randomBytes(32).toString("hex");
  writeFileSync(".env", `JWT_SECRET=${secreto}\n`, { mode: 0o600 });
  console.log("Archivo .env creado con una clave aleatoria.");
}
```

`randomBytes(32)` produce bytes aleatorios para una clave de laboratorio; `toString("hex")` los convierte a texto. `existsSync` evita borrar un `.env` anterior; `writeFileSync` crea el archivo. El `.env` **no se entrega** ni se sube a Git. **Comando en backend**:
```bash
node generar-secreto.mjs
```
Debe mostrarse `Archivo .env creado...` y aparecer `backend/.env`. No publiques su contenido. **Detén** el backend en Terminal A con `Ctrl+C` antes del siguiente cambio.

### 8.3 Ampliemos el backend con tres cambios pequeños (SIN borrar lo anterior)

No escribas las 80 líneas de un servidor de golpe. Ya tienes un archivo que responde GET y POST. **Ahora agregaremos solo tres piezas** y entenderemos qué cambia en cada una. El archivo `server.mjs` siempre conserva su `app.listen(...)` **al final**.

**Microacción A — Agrega las importaciones nuevas en la parte superior de `backend/server.mjs`**, justo debajo de `import cors from "cors";`:

```javascript
import jwt from "jsonwebtoken";
import bcrypt from "bcryptjs";
import "dotenv/config";

if (!process.env.JWT_SECRET) {
  throw new Error("Falta JWT_SECRET. Ejecuta: node generar-secreto.mjs");
}
```

**Por qué:** `jsonwebtoken` firma y verifica JWT, `bcryptjs` compara la clave de prueba con su hash, y `dotenv/config` lee `.env`. La condición impide iniciar con una firma mal configurada. **Todavía no ejecutes:** faltan rutas.

**Microacción B — Agrega el login POST**, debajo de la ruta `app.post("/api/usuarios", ...)` que creaste en EJ04 y **antes de `app.listen`**:

```javascript
const correoDemo = "alumno@demo.test";
// SOLO laboratorio: esta clave de ejemplo es pública; nunca reutilizarla.
const claveHash = await bcrypt.hash("Demo1234!", 10);

app.post("/api/login", async (req, res) => {
  const correo = typeof req.body?.correo === "string"
    ? req.body.correo.trim().toLowerCase()
    : "";
  const clave = typeof req.body?.clave === "string" ? req.body.clave : "";
  const claveCorrecta = await bcrypt.compare(clave, claveHash);
  if (correo !== correoDemo || !claveCorrecta) {
    return res.status(401).json({ mensaje: "Credenciales incorrectas" });
  }
  const token = jwt.sign(
    { sub: "usuario-demo", nombre: "Estudiante de prueba" },
    process.env.JWT_SECRET,
    { expiresIn: "15m" }
  );
  res.json({ token });
});
```

**Por qué:** `req.body` recibe lo que React escribirá en el formulario. `bcrypt.compare` verifica la clave; `401` rechaza las credenciales incorrectas. `jwt.sign` crea un token de acceso que vence en 15 minutos y `res.json({ token })` lo envía al cliente. Las credenciales indicadas son **ficticias y solo para laboratorio**. **No ejecutes todavía:** queda crear la ruta que verifica el token.

**Microacción C — Protege el perfil:** debajo del bloque de login y **antes de `app.listen`**, agrega:

```javascript
function proteger(req, res, next) {
  const autorizacion = req.headers.authorization || "";
  if (!autorizacion.startsWith("Bearer ")) {
    return res.status(401).json({ mensaje: "Falta token Bearer" });
  }
  try {
    req.usuario = jwt.verify(autorizacion.slice(7), process.env.JWT_SECRET);
    next();
  } catch {
    res.status(401).json({ mensaje: "Token inválido o vencido" });
  }
}

app.get("/api/perfil", proteger, (req, res) => {
  res.json({ id: req.usuario.sub, nombre: req.usuario.nombre });
});
```

**Por qué:** `proteger` es un middleware: se ejecuta antes de `/api/perfil`. Si no recibe una cabecera `Authorization: Bearer <token>` responde 401. Si el token es válido, `jwt.verify` permite usar los datos firmados y `next()` continúa hacia la ruta. **La autorización real ocurre en Express, no en React.**

**Ahora sí, verifica:** `app.listen(...)` debe ser el **último bloque** del archivo. Guarda el archivo, vuelve a la **Terminal A** y ejecuta `node server.mjs`. Si aparece `Falta JWT_SECRET`, vuelve al apartado 8.2. Visita `http://localhost:4000/api/perfil` en el navegador: **401 es correcto**, porque todavía no has enviado un token. Si necesitas comparar el archivo completo resultante, está incluido en FASE 15 como `backend/server.mjs`; **todos sus bloques están escritos y explicados aquí, uno por uno**.

### 8.4 Crea la pantalla React de login

Dentro de `frontend/src`, crea `EJ05_LoginJWT.jsx` y escribe:

```jsx
import { useState } from "react";
import axios from "axios";

export default function EJ05_LoginJWT() {
  const [correo, setCorreo] = useState("alumno@demo.test");
  const [clave, setClave] = useState("");
  const [token, setToken] = useState(""); // En memoria; no localStorage.
  const [perfil, setPerfil] = useState(null);
  const [mensaje, setMensaje] = useState("Primero inicia sesión");

  async function iniciar(evento) {
    evento.preventDefault();
    try {
      const { data } = await axios.post("http://localhost:4000/api/login", { correo, clave });
      setToken(data.token);
      setClave("");
      setMensaje("Login correcto. Ahora consulta el perfil.");
    } catch (error) {
      setMensaje(`Login rechazado: HTTP ${error.response?.status ?? "sin respuesta"}`);
    }
  }

  async function consultarPerfil() {
    try {
      const { data } = await axios.get("http://localhost:4000/api/perfil", {
        headers: { Authorization: `Bearer ${token}` }
      });
      setPerfil(data);
      setMensaje("Perfil autorizado: HTTP 200");
    } catch (error) {
      setPerfil(null);
      setMensaje(`Perfil rechazado: HTTP ${error.response?.status ?? "sin respuesta"}`);
    }
  }

  function cerrar() {
    setToken("");
    setPerfil(null);
    setMensaje("Sesión local cerrada. El JWT previo no queda revocado en el servidor.");
  }

  return (
    <section>
      <h2>EJ05 - JWT y perfil protegido</h2>
      <form onSubmit={iniciar}>
        <label>Correo: <input type="email" value={correo} onChange={(e) => setCorreo(e.target.value)} /></label>
        <label>Clave de prueba: <input type="password" value={clave} onChange={(e) => setClave(e.target.value)} /></label>
        <button type="submit">Iniciar sesión</button>
      </form>
      <button onClick={consultarPerfil}>Consultar perfil (Bearer)</button>
      <button onClick={cerrar}>Salir</button>
      <p role="status">{mensaje}</p>
      {perfil && <p>Hola, {perfil.nombre} (ID: {perfil.id})</p>}
    </section>
  );
}
```

**Qué significa cada parte nueva:** `correo` y `clave` son los valores del formulario; `axios.post("/api/login", { correo, clave })` solicita autenticarse; el backend devuelve `{token}`. `setToken(data.token)` guarda el valor **solo en memoria React**, no en `localStorage`. `consultarPerfil()` envía el header `Authorization` con el prefijo obligatorio `Bearer `. `axios.get()` produce éxito si el backend verifica el JWT y muestra `perfil.nombre`; de lo contrario se mostrará 401. `cerrar()` borra el estado local, pero **no revoca mágicamente** un JWT ya emitido en el servidor.

**Activa EJ05:** reemplaza temporalmente `frontend/src/App.jsx` por:
```jsx
import EJ05_LoginJWT from "./EJ05_LoginJWT.jsx";
export default function App() { return <main><EJ05_LoginJWT /></main>; }
```

**Prueba ordenada (no saltes ninguna):**

1. Abre `http://localhost:5173`; antes de iniciar sesión, pulsa **Consultar perfil** → **401**.
2. Usa correo de **laboratorio** `alumno@demo.test` y clave **ficticia** `Demo1234!`; pulsa Iniciar sesión → mensaje de éxito.
3. Pulsa Consultar perfil → **HTTP 200** y “Estudiante de prueba”.
4. Pulsa Salir y otra vez Consultar perfil → **401** (ya no se envía el token correcto).

**T05 — Explica el error 401:** ¿qué cambió entre la petición 200 y la 401? La cabecera `Authorization: Bearer <token>` y la validación del servidor. Un 401 puede tener distintas causas; no siempre significa solamente que falta la palabra `Bearer`.

## 9. Cómo trabajar con FASE 15 SIN perderte

La carpeta de FASE 15 contiene el proyecto **final**. Abre `backend/server.mjs` solo para comparar después del EJ05. Durante EJ01–EJ03 utiliza el código de la sección 3.4; para EJ04 agrega la ruta POST; para EJ05 usa el código final. El backend de FASE 15 requiere instalar **todos** sus paquetes y generar `.env` antes de arrancar.

**Si prefieres iniciar directamente el proyecto de FASE 15:** descomprime `s11-react-api-jwt`, abre dos terminales y ejecuta en `backend`: `npm install`, `node generar-secreto.mjs`, `npm start`. En otra terminal ejecuta en `frontend`: `npm install`, `npm run dev`. La interfaz final trae botones para `EJ03`, `EJ04`, `EJ05` y demostraciones T03–T05. **Esta modalidad no sustituye el aprendizaje paso a paso.**

## 10. Refresh token: siguiente nivel, no un tercer tema obligatorio

En la presentación también se estudia la renovación: un **access token** expira y una aplicación puede solicitar uno nuevo mediante un **refresh token**. Habitualmente el backend controla la renovación y puede mantener el refresh token en una cookie `HttpOnly`, que JavaScript no puede leer directamente. Para hacerlo bien se necesitan medidas adicionales de caducidad, rotación/revocación, CSRF y atributos seguros de cookie. **No programaremos un interceptor de refresh en esta primera guía**, porque primero debemos dominar JWT, Bearer y HTTP 401. El proyecto de FASE 15 se centra exactamente en ese resultado.

**Diferencia importante:** para este laboratorio básico conservamos el token de acceso en memoria. Guardarlo en `localStorage` facilita persistencia, pero expone ese valor al código JavaScript del origen. Cerrar la pestaña borra el token en memoria.

## 11. Si algo falla, revisa solo estas cinco causas

| Síntoma | Comprueba | Solución |
|---|---|---|
| `fetch failed` / `ECONNREFUSED` | Terminal A | Ejecuta `node server.mjs` en backend |
| No aparece React | Terminal B | Ejecuta `npm run dev` en frontend |
| `Cannot find package axios` | Directorio actual | Ejecuta `npm install axios` en el proyecto correcto |
| Error CORS | URL y puertos | Usa `http://localhost:5173` y backend en 4000 |
| `401` en perfil | Login y Bearer | Primero inicia sesión; después consulta perfil |

## 12. Entrega y autoverificación

Tu evidencia consiste en **tres capturas**: (A) EJ01/EJ02 mostrando usuarios en consola, (B) EJ04 mostrando creación HTTP 201 y rechazo 400, y (C) EJ05 mostrando perfil HTTP 200 y respuesta 401. En un texto breve explica qué hace GET, qué hace POST, qué valida `Array.isArray`, qué comprueba `jwt.verify` y por qué un JWT no es una contraseña.

**Antes de dar por concluida la clase** debes responder sin leer: ¿qué terminal ejecuta backend?, ¿qué terminal ejecuta React?, ¿por qué se instalaron dos paquetes primero y otros después?, ¿qué cambia entre Fetch y Axios ante 404?, ¿quién decide si una petición protegida tiene permiso?

**Fuentes técnicas complementarias:** Vite (https://vite.dev/guide/), Fetch (https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch), Axios (https://axios-http.com/docs/intro) y OWASP Session Management (https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html).
