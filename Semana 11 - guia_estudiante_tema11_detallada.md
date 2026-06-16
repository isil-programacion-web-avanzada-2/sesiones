---
title: "Guia del estudiante - Tema 11"
subtitle: "Consumo de APIs y manejo de sesiones en React"
author: "Lideratec Academy"
date: "2026"
geometry: margin=0.72in
fontsize: 10pt
mainfont: DejaVu Serif
monofont: DejaVu Sans Mono
header-includes:
  - \usepackage{fvextra}
  - \DefineVerbatimEnvironment{Highlighting}{Verbatim}{breaklines,breakanywhere,commandchars=\\\{\}}
  - \RecustomVerbatimEnvironment{Verbatim}{Verbatim}{breaklines,breakanywhere}
---

# Guia del estudiante - desde cero

## Tema 11: Consumo de APIs y manejo de sesiones en React

Esta guia corrige el punto critico de la version anterior: **no asume que ya sabes crear un proyecto React**. Vas a iniciar desde cero, crear un proyecto, instalar dependencias, escribir archivos, ejecutar la aplicacion y comprobar el resultado en el navegador.

Al terminar, tendras una mini aplicacion React que permite:

1. Consultar usuarios con **Fetch**.
2. Consultar usuarios con **Axios**.
3. Manejar errores de API.
4. Validar respuestas antes de mostrarlas.
5. Simular login con JWT.
6. Enviar un token con `Authorization: Bearer`.
7. Simular token vencido, refresh token y logout.

> Nota: el login y el refresh se simulan en frontend para fines didacticos. En una aplicacion real, el JWT y el refresh token los genera y valida el backend.

---

# 1. Requisitos antes de empezar

## 1.1 Programas necesarios

Necesitas tener instalado:

- **Node.js LTS**.
- **npm**, que normalmente se instala junto con Node.js.
- **Visual Studio Code** o un editor similar.
- Un navegador moderno, por ejemplo Chrome, Edge, Firefox o Brave.

## 1.2 Verificar Node.js y npm

Abre una terminal.

En Windows puedes usar:

- PowerShell.
- Terminal de Windows.
- Terminal integrada de VS Code.

En macOS o Linux puedes usar la terminal del sistema.

Ejecuta:

```bash
node -v
```

Debe aparecer una version, por ejemplo:

```bash
v22.x.x
```

Luego ejecuta:

```bash
npm -v
```

Debe aparecer una version, por ejemplo:

```bash
10.x.x
```

Si alguno de los comandos no funciona, instala Node.js desde su sitio oficial y vuelve a abrir la terminal.

## 1.3 Crear una carpeta de trabajo

Elige una carpeta donde guardar tus practicas. Por ejemplo:

```bash
mkdir practicas-react
cd practicas-react
```

En Windows, si prefieres crearla manualmente, puedes crear una carpeta llamada `practicas-react` y luego abrirla desde VS Code.

---

# 2. Crear el proyecto React desde cero

Usaremos Vite porque permite crear un proyecto React moderno de forma rapida.

Ejecuta este comando dentro de tu carpeta de trabajo:

```bash
npm create vite@latest tema11-react-apis-jwt -- --template react
```

Cuando termine, entra a la carpeta del proyecto:

```bash
cd tema11-react-apis-jwt
```

Instala las dependencias iniciales:

```bash
npm install
```

Ejecuta el proyecto:

```bash
npm run dev
```

La terminal mostrara una URL similar a:

```bash
http://localhost:5173/
```

Abre esa URL en el navegador.

## Resultado esperado

Debes ver la pagina inicial de Vite + React.

Si ves la pagina, el proyecto fue creado correctamente.

## Error comun

**Error:** ejecutar `npm install` fuera de la carpeta del proyecto.

**Solucion:** verifica que estas dentro de:

```bash
tema11-react-apis-jwt
```

Puedes comprobarlo con:

```bash
pwd
```

o, en Windows:

```bash
cd
```

---

# 3. Abrir el proyecto en VS Code

Desde la carpeta del proyecto ejecuta:

```bash
code .
```

Si el comando no funciona, abre VS Code manualmente y selecciona:

```text
File > Open Folder > tema11-react-apis-jwt
```

En el explorador de archivos de VS Code deberias ver algo parecido a:

```text
tema11-react-apis-jwt/
  node_modules/
  public/
  src/
  package.json
  index.html
  vite.config.js
```

---

# 4. Limpiar el proyecto inicial

Vamos a dejar el proyecto listo para la practica.

## 4.1 Editar `src/App.jsx`

Abre el archivo:

```text
src/App.jsx
```

Borra todo su contenido y coloca:

```jsx
function App() {
  return (
    <main className="container">
      <h1>Tema 11: React + APIs + JWT</h1>
      <p>
        Practica guiada de Fetch, Axios, manejo de errores,
        validacion de respuestas y sesion con JWT.
      </p>
    </main>
  );
}

export default App;
```

## 4.2 Editar `src/index.css`

Abre:

```text
src/index.css
```

Borra todo y coloca:

```css
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #f4f7fb;
  color: #172033;
}

button {
  cursor: pointer;
}

.container {
  width: min(1000px, 92%);
  margin: 32px auto;
}

.card {
  background: white;
  border: 1px solid #dbe3ef;
  border-radius: 12px;
  padding: 18px;
  margin: 18px 0;
}

.row {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  align-items: center;
}

button {
  border: 0;
  border-radius: 8px;
  padding: 10px 14px;
  background: #0b5ed7;
  color: white;
  font-weight: bold;
}

button.secondary {
  background: #475569;
}

button.danger {
  background: #b91c1c;
}

input {
  padding: 10px;
  border: 1px solid #cbd5e1;
  border-radius: 8px;
}

.success {
  color: #166534;
  font-weight: bold;
}

.error {
  color: #b91c1c;
  font-weight: bold;
}

pre {
  background: #111827;
  color: #e5e7eb;
  padding: 14px;
  border-radius: 10px;
  overflow-x: auto;
}
```

## 4.3 Comprobar el navegador

Guarda los archivos. La pagina debe actualizarse automaticamente.

Si no se actualiza, revisa que `npm run dev` siga activo en la terminal.

---

# 5. Instalar Axios

La clase compara Fetch y Axios. Fetch ya viene disponible en el navegador. Axios si se instala.

Deten el servidor solo si es necesario con:

```bash
Ctrl + C
```

Instala Axios:

```bash
npm install axios
```

Vuelve a ejecutar el proyecto:

```bash
npm run dev
```

## Verificar instalacion

Abre `package.json` y busca algo parecido a:

```json
"dependencies": {
  "axios": "...",
  "react": "...",
  "react-dom": "..."
}
```

---

# 6. Crear estructura de carpetas

Dentro de `src`, crea estas carpetas:

```text
src/
  api/
  components/
```

Puedes crearlas desde VS Code con clic derecho sobre `src`.

La estructura esperada sera:

```text
src/
  api/
  components/
  App.jsx
  index.css
  main.jsx
```

---

# 7. Primera practica: consumir API con Fetch

Usaremos una API publica de prueba:

```text
https://jsonplaceholder.typicode.com/users
```

## 7.1 Crear archivo `usersFetch.js`

Crea el archivo:

```text
src/api/usersFetch.js
```

Escribe:

```js
export const obtenerUsuariosConFetch = async () => {
  const respuesta = await fetch(
    "https://jsonplaceholder.typicode.com/users"
  );

  if (!respuesta.ok) {
    throw new Error(`Error HTTP: ${respuesta.status}`);
  }

  const datos = await respuesta.json();

  if (!Array.isArray(datos)) {
    throw new Error("La respuesta no es una lista de usuarios");
  }

  return datos;
};
```

## 7.2 Que hace este archivo

- Ejecuta una solicitud HTTP con `fetch`.
- Valida si la respuesta fue exitosa con `respuesta.ok`.
- Convierte la respuesta a JSON.
- Verifica que los datos sean un arreglo.
- Devuelve la lista de usuarios.

## 7.3 Crear componente `FetchUsers.jsx`

Crea el archivo:

```text
src/components/FetchUsers.jsx
```

Escribe:

```jsx
import { useState } from "react";
import { obtenerUsuariosConFetch } from "../api/usersFetch";

function FetchUsers() {
  const [usuarios, setUsuarios] = useState([]);
  const [cargando, setCargando] = useState(false);
  const [error, setError] = useState("");

  const cargarUsuarios = async () => {
    try {
      setCargando(true);
      setError("");
      const datos = await obtenerUsuariosConFetch();
      setUsuarios(datos);
    } catch (err) {
      setError(err.message);
      setUsuarios([]);
    } finally {
      setCargando(false);
    }
  };

  return (
    <section className="card">
      <h2>1. Usuarios con Fetch</h2>
      <p>
        Fetch requiere validar manualmente si la respuesta HTTP fue correcta.
      </p>

      <button onClick={cargarUsuarios}>Cargar usuarios con Fetch</button>

      {cargando && <p>Cargando datos...</p>}
      {error && <p className="error">{error}</p>}

      <ul>
        {usuarios.map((usuario) => (
          <li key={usuario.id}>
            {usuario.name} - {usuario.email}
          </li>
        ))}
      </ul>
    </section>
  );
}

export default FetchUsers;
```

## 7.4 Conectar el componente en `App.jsx`

Edita `src/App.jsx`:

```jsx
import FetchUsers from "./components/FetchUsers";

function App() {
  return (
    <main className="container">
      <h1>Tema 11: React + APIs + JWT</h1>
      <p>
        Practica guiada de Fetch, Axios, manejo de errores,
        validacion de respuestas y sesion con JWT.
      </p>

      <FetchUsers />
    </main>
  );
}

export default App;
```

## 7.5 Resultado esperado

En el navegador debe aparecer una tarjeta con el boton:

```text
Cargar usuarios con Fetch
```

Al hacer clic, se debe mostrar una lista de usuarios.

## 7.6 Error comun

**Error:** pantalla en blanco.

**Posibles causas:**

- Escribiste mal la ruta de importacion.
- El archivo se llama distinto.
- Falta guardar el archivo.
- Hay un error de sintaxis.

Abre la consola del navegador con `F12` para ver el mensaje.

---

# 8. Segunda practica: consumir API con Axios

## 8.1 Crear archivo `usersAxios.js`

Crea:

```text
src/api/usersAxios.js
```

Escribe:

```js
import axios from "axios";

export const obtenerUsuariosConAxios = async () => {
  const { data } = await axios.get(
    "https://jsonplaceholder.typicode.com/users"
  );

  if (!Array.isArray(data)) {
    throw new Error("La respuesta no es una lista de usuarios");
  }

  return data;
};
```

## 8.2 Que cambia con Axios

Con Fetch se usa:

```js
const datos = await respuesta.json();
```

Con Axios se usa:

```js
const { data } = await axios.get(url);
```

Axios entrega los datos directamente en `data`.

## 8.3 Crear componente `AxiosUsers.jsx`

Crea:

```text
src/components/AxiosUsers.jsx
```

Escribe:

```jsx
import { useState } from "react";
import { obtenerUsuariosConAxios } from "../api/usersAxios";

function AxiosUsers() {
  const [usuarios, setUsuarios] = useState([]);
  const [cargando, setCargando] = useState(false);
  const [error, setError] = useState("");

  const cargarUsuarios = async () => {
    try {
      setCargando(true);
      setError("");
      const datos = await obtenerUsuariosConAxios();
      setUsuarios(datos);
    } catch (err) {
      const mensaje = err.response
        ? `Error del servidor: ${err.response.status}`
        : err.message;

      setError(mensaje);
      setUsuarios([]);
    } finally {
      setCargando(false);
    }
  };

  return (
    <section className="card">
      <h2>2. Usuarios con Axios</h2>
      <p>
        Axios simplifica la lectura de datos usando la propiedad data.
      </p>

      <button onClick={cargarUsuarios}>Cargar usuarios con Axios</button>

      {cargando && <p>Cargando datos...</p>}
      {error && <p className="error">{error}</p>}

      <ul>
        {usuarios.map((usuario) => (
          <li key={usuario.id}>
            {usuario.name} - {usuario.email}
          </li>
        ))}
      </ul>
    </section>
  );
}

export default AxiosUsers;
```

## 8.4 Conectar en `App.jsx`

Edita `src/App.jsx`:

```jsx
import FetchUsers from "./components/FetchUsers";
import AxiosUsers from "./components/AxiosUsers";

function App() {
  return (
    <main className="container">
      <h1>Tema 11: React + APIs + JWT</h1>
      <p>
        Practica guiada de Fetch, Axios, manejo de errores,
        validacion de respuestas y sesion con JWT.
      </p>

      <FetchUsers />
      <AxiosUsers />
    </main>
  );
}

export default App;
```

## 8.5 Resultado esperado

Ahora debes ver dos tarjetas:

1. Usuarios con Fetch.
2. Usuarios con Axios.

Ambas deben cargar usuarios desde la misma API.

---

# 9. Tercera practica: provocar y manejar un error

Ahora comprobaremos que el manejo de errores funciona.

## 9.1 Romper temporalmente la URL de Fetch

En `src/api/usersFetch.js`, cambia la URL por una ruta incorrecta:

```js
"https://jsonplaceholder.typicode.com/ruta-inexistente"
```

Guarda y presiona el boton de Fetch.

## 9.2 Resultado esperado

Debe aparecer un mensaje similar a:

```text
Error HTTP: 404
```

## 9.3 Restaurar la URL correcta

Vuelve a dejar:

```js
"https://jsonplaceholder.typicode.com/users"
```

## 9.4 Aprendizaje

Esta prueba demuestra que una aplicacion profesional no solo debe funcionar cuando todo esta bien. Tambien debe responder cuando la API falla.

---

# 10. Cuarta practica: simular login con JWT

En una aplicacion real, el backend valida correo y clave. Para practicar desde cero sin backend, simularemos esa validacion en un archivo de servicio.

## 10.1 Crear archivo `authApi.js`

Crea:

```text
src/api/authApi.js
```

Escribe:

```js
const ACCESS_TOKEN_VALIDO = "jwt-demo-access-token";
const ACCESS_TOKEN_RENOVADO = "jwt-demo-access-token-renovado";

export const loginSimulado = async ({ correo, clave }) => {
  await esperar(600);

  if (correo === "demo@lideratec.com" && clave === "123456") {
    return {
      token: ACCESS_TOKEN_VALIDO,
      usuario: {
        nombre: "Estudiante Lideratec",
        correo,
      },
    };
  }

  throw new Error("Credenciales invalidas");
};

export const obtenerPerfilProtegido = async (token) => {
  await esperar(600);

  if (!token) {
    const error = new Error("No hay token");
    error.status = 401;
    throw error;
  }

  if (token === "token-vencido") {
    const error = new Error("Token vencido");
    error.status = 401;
    throw error;
  }

  return {
    nombre: "Estudiante Lideratec",
    rol: "Frontend Junior",
    permiso: "Ruta protegida autorizada",
  };
};

export const refrescarTokenSimulado = async () => {
  await esperar(600);

  return {
    token: ACCESS_TOKEN_RENOVADO,
  };
};

const esperar = (ms) => {
  return new Promise((resolve) => setTimeout(resolve, ms));
};
```

## 10.2 Crear componente `LoginJwt.jsx`

Crea:

```text
src/components/LoginJwt.jsx
```

Escribe:

```jsx
import { useState } from "react";
import {
  loginSimulado,
  obtenerPerfilProtegido,
  refrescarTokenSimulado,
} from "../api/authApi";

function LoginJwt() {
  const [correo, setCorreo] = useState("demo@lideratec.com");
  const [clave, setClave] = useState("123456");
  const [token, setToken] = useState(localStorage.getItem("token") || "");
  const [perfil, setPerfil] = useState(null);
  const [mensaje, setMensaje] = useState("");
  const [error, setError] = useState("");

  const iniciarSesion = async () => {
    try {
      setError("");
      setMensaje("Iniciando sesion...");

      const data = await loginSimulado({ correo, clave });

      localStorage.setItem("token", data.token);
      setToken(data.token);
      setMensaje("Sesion iniciada. Token guardado.");
    } catch (err) {
      setError(err.message);
      setMensaje("");
    }
  };

  const cargarPerfil = async () => {
    try {
      setError("");
      setMensaje("Consultando ruta protegida...");

      console.log("Authorization: Bearer", token);

      const data = await obtenerPerfilProtegido(token);
      setPerfil(data);
      setMensaje("Perfil protegido cargado correctamente.");
    } catch (err) {
      setPerfil(null);
      setMensaje("");

      if (err.status === 401) {
        setError("Error 401: token ausente, invalido o vencido.");
      } else {
        setError(err.message);
      }
    }
  };

  const simularTokenVencido = () => {
    localStorage.setItem("token", "token-vencido");
    setToken("token-vencido");
    setPerfil(null);
    setMensaje("Token vencido simulado.");
    setError("");
  };

  const renovarToken = async () => {
    try {
      setError("");
      setMensaje("Solicitando refresh token...");

      const data = await refrescarTokenSimulado();
      localStorage.setItem("token", data.token);
      setToken(data.token);
      setMensaje("Nuevo access token recibido.");
    } catch (err) {
      setError(err.message);
      setMensaje("");
    }
  };

  const cerrarSesion = () => {
    localStorage.removeItem("token");
    setToken("");
    setPerfil(null);
    setMensaje("Sesion cerrada. Token eliminado.");
    setError("");
  };

  return (
    <section className="card">
      <h2>3. Login, JWT, Refresh y Logout</h2>

      <div className="row">
        <input
          value={correo}
          onChange={(e) => setCorreo(e.target.value)}
          placeholder="Correo"
        />
        <input
          value={clave}
          onChange={(e) => setClave(e.target.value)}
          placeholder="Clave"
          type="password"
        />
      </div>

      <div className="row" style={{ marginTop: "12px" }}>
        <button onClick={iniciarSesion}>Login</button>
        <button onClick={cargarPerfil}>Cargar perfil protegido</button>
        <button className="secondary" onClick={simularTokenVencido}>
          Simular token vencido
        </button>
        <button className="secondary" onClick={renovarToken}>
          Refresh token
        </button>
        <button className="danger" onClick={cerrarSesion}>
          Logout
        </button>
      </div>

      {mensaje && <p className="success">{mensaje}</p>}
      {error && <p className="error">{error}</p>}

      <h3>Access Token actual</h3>
      <pre>{token || "Sin token"}</pre>

      <h3>Perfil protegido</h3>
      <pre>{perfil ? JSON.stringify(perfil, null, 2) : "Sin perfil"}</pre>
    </section>
  );
}

export default LoginJwt;
```

## 10.3 Conectar en `App.jsx`

Edita `src/App.jsx`:

```jsx
import FetchUsers from "./components/FetchUsers";
import AxiosUsers from "./components/AxiosUsers";
import LoginJwt from "./components/LoginJwt";

function App() {
  return (
    <main className="container">
      <h1>Tema 11: React + APIs + JWT</h1>
      <p>
        Practica guiada de Fetch, Axios, manejo de errores,
        validacion de respuestas y sesion con JWT.
      </p>

      <FetchUsers />
      <AxiosUsers />
      <LoginJwt />
    </main>
  );
}

export default App;
```

---

# 11. Probar el flujo completo JWT

## 11.1 Login exitoso

Usa estos datos:

```text
Correo: demo@lideratec.com
Clave: 123456
```

Presiona:

```text
Login
```

Resultado esperado:

```text
Sesion iniciada. Token guardado.
```

Debe aparecer:

```text
jwt-demo-access-token
```

## 11.2 Cargar ruta protegida

Presiona:

```text
Cargar perfil protegido
```

Resultado esperado:

```json
{
  "nombre": "Estudiante Lideratec",
  "rol": "Frontend Junior",
  "permiso": "Ruta protegida autorizada"
}
```

## 11.3 Ver la cabecera Authorization

Abre la consola del navegador con `F12`.

Debe verse un mensaje como:

```text
Authorization: Bearer jwt-demo-access-token
```

Esto representa la cabecera que se enviaria al backend en una aplicacion real.

## 11.4 Simular token vencido

Presiona:

```text
Simular token vencido
```

Luego presiona:

```text
Cargar perfil protegido
```

Resultado esperado:

```text
Error 401: token ausente, invalido o vencido.
```

## 11.5 Renovar token

Presiona:

```text
Refresh token
```

Resultado esperado:

```text
Nuevo access token recibido.
```

Ahora vuelve a presionar:

```text
Cargar perfil protegido
```

El perfil debe cargarse correctamente.

## 11.6 Cerrar sesion

Presiona:

```text
Logout
```

Resultado esperado:

```text
Sesion cerrada. Token eliminado.
```

El token debe quedar como:

```text
Sin token
```

---

# 12. Resumen tecnico de lo aprendido

## Fetch

- Es nativo.
- Requiere validar `respuesta.ok`.
- Requiere convertir la respuesta con `respuesta.json()`.
- Es util para solicitudes simples o proyectos sin dependencias adicionales.

## Axios

- Es una libreria externa.
- Se instala con `npm install axios`.
- Entrega datos en `data`.
- Facilita leer errores con `error.response`.
- Es util en aplicaciones medianas o grandes.

## Validacion

Antes de mostrar datos, debes comprobar que tienen la estructura esperada.

Ejemplo:

```js
if (!Array.isArray(datos)) {
  throw new Error("La respuesta no es una lista");
}
```

## JWT

- Permite autenticar solicitudes.
- Se envia normalmente como `Authorization: Bearer <token>`.
- No debe confundirse con una sesion segura por si solo.
- Su seguridad depende de expiracion, almacenamiento y validacion.

## Refresh Token

- Sirve para pedir un nuevo access token.
- Suele usarse cuando aparece un error 401 por token vencido.
- En una app real, deberia gestionarse desde backend y protegerse mejor, por ejemplo con cookie HTTPOnly.

## Logout

Cerrar sesion implica:

1. Eliminar token.
2. Limpiar estado de usuario.
3. Redirigir o volver a pantalla de login.

---

# 13. Lista de comprobacion del estudiante

Marca cada punto cuando lo hayas logrado.

- [ ] Instale Node.js y verifique `node -v`.
- [ ] Cree el proyecto con Vite.
- [ ] Ejecute `npm install`.
- [ ] Ejecute `npm run dev`.
- [ ] Limpie `App.jsx` e `index.css`.
- [ ] Instale Axios.
- [ ] Cree las carpetas `api` y `components`.
- [ ] Cree `usersFetch.js`.
- [ ] Cree `FetchUsers.jsx`.
- [ ] Cargue usuarios con Fetch.
- [ ] Cree `usersAxios.js`.
- [ ] Cree `AxiosUsers.jsx`.
- [ ] Cargue usuarios con Axios.
- [ ] Provoque un error 404 y lo controle.
- [ ] Cree `authApi.js`.
- [ ] Cree `LoginJwt.jsx`.
- [ ] Hice login con credenciales demo.
- [ ] Cargue perfil protegido.
- [ ] Simule token vencido.
- [ ] Renove token.
- [ ] Cerre sesion correctamente.

---

# 14. Problemas frecuentes y soluciones

## El comando `npm create vite@latest` no funciona

Verifica Node y npm:

```bash
node -v
npm -v
```

Si no aparecen versiones, instala Node.js.

## La pagina queda en blanco

Abre la consola del navegador. Normalmente el error indica:

- Import mal escrito.
- Archivo inexistente.
- Error de sintaxis.
- Componente no exportado.

## Axios no se reconoce

Ejecuta dentro del proyecto:

```bash
npm install axios
```

Luego reinicia:

```bash
npm run dev
```

## No aparecen usuarios

Verifica:

- Conexion a internet.
- URL correcta.
- Consola del navegador.
- Que el boton llama a la funcion correcta.

## El token no se elimina

Abre DevTools del navegador y revisa Application > Local Storage.

Presiona Logout y confirma que la clave `token` desaparece.

---

# 15. Entrega sugerida para evaluacion

Entrega capturas o evidencias de:

1. Proyecto corriendo en el navegador.
2. Usuarios cargados con Fetch.
3. Usuarios cargados con Axios.
4. Error 404 controlado.
5. Login exitoso.
6. Perfil protegido cargado.
7. Error 401 por token vencido.
8. Refresh token simulado.
9. Logout con token eliminado.

Incluye una breve respuesta:

```text
¿Por que no basta con guardar un token para tener una sesion segura?
```

