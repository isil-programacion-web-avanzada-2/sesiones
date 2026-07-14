---
title: "Guía del estudiante v2 - Clase guiada paso a paso: Seguridad web con Express y React"
author: "Lideratec Academy"
date: "Programación Web Avanzada - Tema 15"
lang: es
geometry: margin=1.7cm
fontsize: 10pt
mainfont: "DejaVu Sans"
monofont: "DejaVu Sans Mono"
colorlinks: true
linkcolor: blue
urlcolor: blue
header-includes:
  - \usepackage{fancyhdr}
  - \pagestyle{fancy}
  - \fancyhead[L]{Lideratec Academy}
  - \fancyhead[R]{Seguridad web}
  - \fancyfoot[C]{\thepage}
  - \setlength{\headheight}{15pt}
  - \usepackage{fvextra}
  - \DefineVerbatimEnvironment{Highlighting}{Verbatim}{breaklines,commandchars=\\\{\}}
  - \usepackage{listings}
  - \lstset{breaklines=true,breakatwhitespace=true,basicstyle=\ttfamily\small,columns=fullflexible}
---


# Guía del estudiante v2 - Clase práctica paso a paso

## Tema de la sesión

**Seguridad y buenas prácticas en aplicaciones web con HTTPS, SSL/TLS, protección contra XSS y CSRF, Helmet.js y Lighthouse.**

Esta guía fue recreada para que puedas seguir una clase práctica desde cero. No asume que ya tienes un proyecto creado. Vas a construir una mini aplicación con:

- Backend en **Express**.
- Frontend en **React con Vite**.
- Validación de datos con **express-validator**.
- Sanitización visual con **DOMPurify**.
- Encabezados de seguridad con **Helmet.js**.
- Flujo didáctico de **token CSRF**.
- Revisión final con **Lighthouse**.

> Nota importante: esta práctica es académica. El objetivo es comprender los conceptos y aplicar una base segura. En producción se deben revisar dependencias, arquitectura, hosting, certificados reales, cookies, dominios y políticas de seguridad.

---

## Resultado de aprendizaje

Al finalizar la clase podrás construir y explicar una aplicación Express + React que aplica una checklist inicial de seguridad:

1. Diferencia entre HTTP y HTTPS.
2. Uso conceptual de certificado SSL/TLS.
3. Backend Express con Helmet.js.
4. Validación y limpieza de entradas en Express.
5. Frontend React que evita renderizar HTML inseguro.
6. Uso de DOMPurify cuando se necesita mostrar HTML.
7. Flujo didáctico de token CSRF para solicitudes sensibles.
8. Auditoría básica con Lighthouse.

---

## Duración sugerida para clase práctica de 2 horas

| Momento | Tiempo | Qué harás |
|---|---:|---|
| Preparación del entorno | 10 min | Crear carpetas, backend y frontend |
| Backend base Express | 20 min | Crear servidor, rutas y Helmet |
| Validación Express | 20 min | Crear ruta de comentarios segura |
| Frontend React | 25 min | Crear formulario y conexión con backend |
| XSS y DOMPurify | 20 min | Probar renderizado seguro y sanitización |
| CSRF didáctico | 15 min | Obtener token y enviarlo en POST |
| Lighthouse + cierre | 10 min | Auditar y completar checklist |

---

## Requisitos previos

Antes de comenzar, verifica que tienes instalado:

```bash
node -v
npm -v
```

Versiones recomendadas:

- Node.js 20 o superior.
- npm 10 o superior.
- Navegador Google Chrome.
- Editor Visual Studio Code.

Si `node -v` o `npm -v` no funcionan, instala Node.js desde su sitio oficial antes de continuar.

---

# Parte 1 - Crear el proyecto desde cero

## Paso 1. Crear carpeta principal

Abre una terminal y ejecuta:

```bash
mkdir seguridad-web-practica
cd seguridad-web-practica
```

**Qué debe pasar:** ahora estás dentro de una carpeta vacía llamada `seguridad-web-practica`.

**Verificación:**

```bash
pwd
```

o en Windows:

```bash
cd
```

---

## Paso 2. Crear carpetas para backend y frontend

```bash
mkdir backend frontend
```

Estructura esperada:

```text
seguridad-web-practica/
├── backend/
└── frontend/
```

**Qué significa:** separaremos el servidor Express del cliente React para entender mejor qué responsabilidad tiene cada capa.

---

# Parte 2 - Backend Express seguro paso a paso

## Paso 3. Inicializar backend

```bash
cd backend
npm init -y
```

**Qué se crea:** un archivo `package.json`.

**Para qué sirve:** registra dependencias, scripts y configuración básica del proyecto Node.js.

---

## Paso 4. Instalar dependencias del backend

```bash
npm install express cors helmet express-validator cookie-parser dotenv
npm install --save-dev nodemon
```

### ¿Para qué sirve cada dependencia?

| Dependencia | Uso en la práctica |
|---|---|
| express | Crear servidor y rutas API |
| cors | Permitir conexión desde React en desarrollo |
| helmet | Configurar encabezados HTTP de seguridad |
| express-validator | Validar y limpiar entradas del usuario |
| cookie-parser | Leer cookies, útil para el ejemplo CSRF |
| dotenv | Leer variables de entorno desde `.env` |
| nodemon | Reiniciar servidor automáticamente en desarrollo |

---

## Paso 5. Configurar `package.json`

Abre `backend/package.json` y reemplaza la sección `scripts` por esta:

```json
{
  "scripts": {
    "dev": "nodemon src/server.js",
    "start": "node src/server.js"
  }
}
```

Si tu `package.json` tiene más propiedades, no las borres. Solo cambia `scripts`.

**Resultado esperado:** podrás ejecutar el backend con:

```bash
npm run dev
```

---

## Paso 6. Crear estructura de archivos backend

Desde la carpeta `backend`, ejecuta:

```bash
mkdir src
mkdir src/routes src/middlewares
```

Crea estos archivos:

```bash
touch src/server.js
touch src/routes/comments.routes.js
touch src/middlewares/csrf-demo.js
touch .env
```

En Windows PowerShell, si `touch` no funciona, usa:

```powershell
New-Item src/server.js
New-Item src/routes/comments.routes.js
New-Item src/middlewares/csrf-demo.js
New-Item .env
```

Estructura esperada:

```text
backend/
├── .env
├── package.json
└── src/
    ├── server.js
    ├── middlewares/
    │   └── csrf-demo.js
    └── routes/
        └── comments.routes.js
```

---

## Paso 7. Crear variables de entorno

En `backend/.env` escribe:

```env
PORT=3001
FRONTEND_URL=http://localhost:5173
NODE_ENV=development
```

**Qué significa:**

- `PORT`: puerto donde corre Express.
- `FRONTEND_URL`: origen permitido para React.
- `NODE_ENV`: ambiente de ejecución.

**Error común:** escribir `localhost` sin `http://`. Para CORS necesitamos el origen completo.

---

## Paso 8. Crear middleware CSRF didáctico

Abre `src/middlewares/csrf-demo.js` y pega:

```js
const crypto = require('crypto');

const tokensActivos = new Set();

function crearTokenCsrf(req, res) {
  const token = crypto.randomBytes(24).toString('hex');
  tokensActivos.add(token);

  res.cookie('csrf_token_demo', token, {
    httpOnly: true,
    sameSite: 'strict',
    secure: false, // En producción con HTTPS debe ser true.
    maxAge: 15 * 60 * 1000
  });

  res.json({ csrfToken: token });
}

function validarTokenCsrf(req, res, next) {
  const tokenHeader = req.get('X-CSRF-Token');
  const tokenCookie = req.cookies.csrf_token_demo;

  if (!tokenHeader || !tokenCookie) {
    return res.status(403).json({
      error: 'Token CSRF requerido',
      detalle: 'La solicitud debe incluir cookie y encabezado X-CSRF-Token.'
    });
  }

  if (tokenHeader !== tokenCookie || !tokensActivos.has(tokenHeader)) {
    return res.status(403).json({
      error: 'Token CSRF inválido',
      detalle: 'El token enviado no coincide con el token generado por el servidor.'
    });
  }

  next();
}

module.exports = {
  crearTokenCsrf,
  validarTokenCsrf
};
```

### Explicación clara

Este archivo simula un flujo CSRF seguro para clase:

1. El servidor crea un token aleatorio.
2. El token se guarda en una cookie.
3. El token también se envía al frontend en JSON.
4. React lo devuelve en el encabezado `X-CSRF-Token`.
5. Express compara cookie y encabezado.
6. Si no coinciden, rechaza la solicitud con estado `403`.

> Esta implementación es didáctica. En producción se debe usar una estrategia mantenida y alineada con la arquitectura real del proyecto.

---

## Paso 9. Crear rutas de comentarios con validación

Abre `src/routes/comments.routes.js` y pega:

```js
const express = require('express');
const { body, validationResult } = require('express-validator');
const { validarTokenCsrf } = require('../middlewares/csrf-demo');

const router = express.Router();

const comentarios = [];

router.get('/', (req, res) => {
  res.json({
    total: comentarios.length,
    comentarios
  });
});

router.post(
  '/',
  validarTokenCsrf,
  [
    body('nombre')
      .trim()
      .escape()
      .isLength({ min: 3, max: 50 })
      .withMessage('El nombre debe tener entre 3 y 50 caracteres.'),

    body('comentario')
      .trim()
      .escape()
      .isLength({ min: 5, max: 300 })
      .withMessage('El comentario debe tener entre 5 y 300 caracteres.')
  ],
  (req, res) => {
    const errores = validationResult(req);

    if (!errores.isEmpty()) {
      return res.status(400).json({
        mensaje: 'Datos inválidos',
        errores: errores.array()
      });
    }

    const nuevoComentario = {
      id: Date.now(),
      nombre: req.body.nombre,
      comentario: req.body.comentario,
      fecha: new Date().toISOString()
    };

    comentarios.push(nuevoComentario);

    res.status(201).json({
      mensaje: 'Comentario guardado de forma segura',
      comentario: nuevoComentario
    });
  }
);

module.exports = router;
```

### Explicación paso a paso

- `router.get('/')`: devuelve todos los comentarios guardados en memoria.
- `router.post('/')`: permite guardar un comentario nuevo.
- `validarTokenCsrf`: bloquea solicitudes sin token.
- `body('nombre')`: valida el campo nombre.
- `trim()`: elimina espacios al inicio y final.
- `escape()`: convierte caracteres HTML especiales en texto seguro.
- `isLength()`: limita cantidad de caracteres.
- `validationResult(req)`: recoge errores de validación.
- `status(400)`: indica que el cliente envió datos inválidos.
- `status(201)`: indica que se creó un recurso.

---

## Paso 10. Crear servidor principal Express

Abre `src/server.js` y pega:

```js
require('dotenv').config();

const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const cookieParser = require('cookie-parser');
const comentariosRouter = require('./routes/comments.routes');
const { crearTokenCsrf } = require('./middlewares/csrf-demo');

const app = express();
const PORT = process.env.PORT || 3001;
const FRONTEND_URL = process.env.FRONTEND_URL || 'http://localhost:5173';

app.use(helmet());

app.use(cors({
  origin: FRONTEND_URL,
  credentials: true
}));

app.use(express.json());
app.use(cookieParser());

app.get('/api/health', (req, res) => {
  res.json({
    estado: 'OK',
    mensaje: 'Backend Express funcionando',
    seguridad: ['Helmet activo', 'Validación activa', 'CSRF demo disponible']
  });
});

app.get('/api/csrf-token', crearTokenCsrf);

app.use('/api/comments', comentariosRouter);

app.use((req, res) => {
  res.status(404).json({
    error: 'Ruta no encontrada'
  });
});

app.listen(PORT, () => {
  console.log(`Servidor Express activo en http://localhost:${PORT}`);
});
```

### ¿Qué hace este archivo?

1. Carga variables de entorno.
2. Crea la aplicación Express.
3. Activa Helmet.js.
4. Permite solicitudes desde React usando CORS.
5. Permite recibir JSON.
6. Lee cookies.
7. Crea una ruta de salud.
8. Crea una ruta para pedir token CSRF.
9. Conecta la ruta de comentarios.
10. Maneja rutas no encontradas.

---

## Paso 11. Ejecutar backend

Desde la carpeta `backend`:

```bash
npm run dev
```

Resultado esperado:

```text
Servidor Express activo en http://localhost:3001
```

Prueba en el navegador:

```text
http://localhost:3001/api/health
```

Deberías ver una respuesta JSON similar a:

```json
{
  "estado": "OK",
  "mensaje": "Backend Express funcionando",
  "seguridad": ["Helmet activo", "Validación activa", "CSRF demo disponible"]
}
```

---

## Paso 12. Probar errores de validación con Postman, Thunder Client o REST Client

Solicita primero el token:

```http
GET http://localhost:3001/api/csrf-token
```

Copia el valor `csrfToken`.

Luego intenta enviar un comentario inválido:

```http
POST http://localhost:3001/api/comments
Content-Type: application/json
X-CSRF-Token: PEGA_AQUI_EL_TOKEN

{
  "nombre": "A",
  "comentario": "Hi"
}
```

Resultado esperado:

```json
{
  "mensaje": "Datos inválidos",
  "errores": [
    { "msg": "El nombre debe tener entre 3 y 50 caracteres." },
    { "msg": "El comentario debe tener entre 5 y 300 caracteres." }
  ]
}
```

Ahora prueba con datos válidos:

```http
POST http://localhost:3001/api/comments
Content-Type: application/json
X-CSRF-Token: PEGA_AQUI_EL_TOKEN

{
  "nombre": "Ana Torres",
  "comentario": "Esta aplicación valida datos antes de guardarlos."
}
```

Resultado esperado:

```json
{
  "mensaje": "Comentario guardado de forma segura",
  "comentario": {
    "id": 123456789,
    "nombre": "Ana Torres",
    "comentario": "Esta aplicación valida datos antes de guardarlos.",
    "fecha": "..."
  }
}
```

---

# Parte 3 - Frontend React paso a paso

## Paso 13. Crear proyecto React con Vite

Abre otra terminal. Desde `seguridad-web-practica/frontend`, ejecuta:

```bash
cd ../frontend
npm create vite@latest . -- --template react
npm install
npm install dompurify
```

Si Vite pregunta si deseas continuar, responde `y`.

---

## Paso 14. Crear variable de entorno frontend

En la carpeta `frontend`, crea un archivo `.env`:

```env
VITE_API_URL=http://localhost:3001
```

**Qué significa:** React usará esta URL para conectarse al backend.

---

## Paso 15. Crear carpetas del frontend

```bash
mkdir src/components src/services
```

Crea archivos:

```bash
touch src/services/api.js
touch src/components/CommentForm.jsx
touch src/components/HtmlPreview.jsx
touch src/components/SecurityChecklist.jsx
```

En Windows PowerShell:

```powershell
New-Item src/services/api.js
New-Item src/components/CommentForm.jsx
New-Item src/components/HtmlPreview.jsx
New-Item src/components/SecurityChecklist.jsx
```

---

## Paso 16. Crear servicio API

Abre `src/services/api.js` y pega:

```js
const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3001';

export async function obtenerTokenCsrf() {
  const respuesta = await fetch(`${API_URL}/api/csrf-token`, {
    credentials: 'include'
  });

  if (!respuesta.ok) {
    throw new Error('No se pudo obtener el token CSRF');
  }

  return respuesta.json();
}

export async function listarComentarios() {
  const respuesta = await fetch(`${API_URL}/api/comments`, {
    credentials: 'include'
  });

  if (!respuesta.ok) {
    throw new Error('No se pudieron cargar los comentarios');
  }

  return respuesta.json();
}

export async function guardarComentario(datos, tokenCsrf) {
  const respuesta = await fetch(`${API_URL}/api/comments`, {
    method: 'POST',
    credentials: 'include',
    headers: {
      'Content-Type': 'application/json',
      'X-CSRF-Token': tokenCsrf
    },
    body: JSON.stringify(datos)
  });

  const cuerpo = await respuesta.json();

  if (!respuesta.ok) {
    throw cuerpo;
  }

  return cuerpo;
}
```

### Explicación clara

- `credentials: 'include'`: permite enviar y recibir cookies entre React y Express.
- `obtenerTokenCsrf()`: pide el token al backend.
- `guardarComentario()`: envía nombre, comentario y token.
- `X-CSRF-Token`: encabezado que el backend verificará.

---

## Paso 17. Crear formulario de comentarios

Abre `src/components/CommentForm.jsx` y pega:

```jsx
import { useEffect, useState } from 'react';
import { guardarComentario, listarComentarios, obtenerTokenCsrf } from '../services/api';

export function CommentForm() {
  const [tokenCsrf, setTokenCsrf] = useState('');
  const [nombre, setNombre] = useState('');
  const [comentario, setComentario] = useState('');
  const [comentarios, setComentarios] = useState([]);
  const [mensaje, setMensaje] = useState('');
  const [errores, setErrores] = useState([]);

  async function cargarDatosIniciales() {
    const token = await obtenerTokenCsrf();
    setTokenCsrf(token.csrfToken);

    const lista = await listarComentarios();
    setComentarios(lista.comentarios);
  }

  useEffect(() => {
    cargarDatosIniciales().catch(() => {
      setMensaje('No se pudo conectar con el backend.');
    });
  }, []);

  async function manejarEnvio(evento) {
    evento.preventDefault();
    setMensaje('');
    setErrores([]);

    try {
      const respuesta = await guardarComentario({ nombre, comentario }, tokenCsrf);
      setMensaje(respuesta.mensaje);
      setNombre('');
      setComentario('');

      const lista = await listarComentarios();
      setComentarios(lista.comentarios);
    } catch (error) {
      setMensaje(error.mensaje || error.error || 'Error al guardar comentario.');
      setErrores(error.errores || []);
    }
  }

  return (
    <section className="card">
      <h2>Formulario seguro</h2>
      <p>Este formulario envía datos al backend con validación y token CSRF.</p>

      <form onSubmit={manejarEnvio}>
        <label>
          Nombre
          <input
            value={nombre}
            onChange={(e) => setNombre(e.target.value)}
            placeholder="Ejemplo: Ana Torres"
          />
        </label>

        <label>
          Comentario
          <textarea
            value={comentario}
            onChange={(e) => setComentario(e.target.value)}
            placeholder="Escribe un comentario de al menos 5 caracteres"
          />
        </label>

        <button type="submit">Guardar comentario</button>
      </form>

      {mensaje && <p className="mensaje">{mensaje}</p>}

      {errores.length > 0 && (
        <ul className="errores">
          {errores.map((error, index) => (
            <li key={index}>{error.msg}</li>
          ))}
        </ul>
      )}

      <h3>Comentarios guardados</h3>
      {comentarios.length === 0 ? (
        <p>Aún no hay comentarios.</p>
      ) : (
        <ul>
          {comentarios.map((item) => (
            <li key={item.id}>
              <strong>{item.nombre}</strong>: {item.comentario}
            </li>
          ))}
        </ul>
      )}
    </section>
  );
}
```

### ¿Qué debes observar?

Cuando escribes datos inválidos, el backend responde con errores. Cuando escribes datos válidos, el comentario se guarda. React muestra el comentario como texto, no como código ejecutable.

---

## Paso 18. Crear componente para HTML sanitizado

Abre `src/components/HtmlPreview.jsx` y pega:

```jsx
import { useState } from 'react';
import DOMPurify from 'dompurify';

export function HtmlPreview() {
  const [htmlUsuario, setHtmlUsuario] = useState('<p>Texto con <strong>negrita</strong></p>');

  const htmlSeguro = DOMPurify.sanitize(htmlUsuario, {
    ALLOWED_TAGS: ['p', 'strong', 'em', 'b', 'i', 'br', 'ul', 'ol', 'li'],
    ALLOWED_ATTR: []
  });

  return (
    <section className="card">
      <h2>Vista previa HTML sanitizada</h2>
      <p>
        Este ejemplo permite algunas etiquetas de formato, pero elimina contenido no permitido.
      </p>

      <textarea
        value={htmlUsuario}
        onChange={(e) => setHtmlUsuario(e.target.value)}
      />

      <h3>HTML original escrito por el usuario</h3>
      <pre>{htmlUsuario}</pre>

      <h3>Resultado sanitizado con DOMPurify</h3>
      <div
        className="preview"
        dangerouslySetInnerHTML={{ __html: htmlSeguro }}
      />
    </section>
  );
}
```

### Prueba segura para clase

Escribe este contenido en el textarea:

```html
<p>Hola <strong>clase</strong></p>
<script>alert('prueba')</script>
```

**Resultado esperado:** se conserva el párrafo y la negrita, pero el script no debe ejecutarse.

**Importante:** no uses este ejemplo para atacar sitios reales. Aquí se usa solo para verificar que la sanitización funciona en un entorno de práctica.

---

## Paso 19. Crear checklist visual

Abre `src/components/SecurityChecklist.jsx` y pega:

```jsx
export function SecurityChecklist() {
  const items = [
    'HTTPS protege la comunicación en producción',
    'React escapa texto por defecto',
    'DOMPurify sanitiza HTML permitido',
    'Express valida entradas antes de guardar',
    'Helmet.js agrega encabezados HTTP de seguridad',
    'Token CSRF protege solicitudes sensibles',
    'Lighthouse ayuda a auditar buenas prácticas'
  ];

  return (
    <section className="card">
      <h2>Checklist de seguridad web</h2>
      <ul>
        {items.map((item) => (
          <li key={item}>[OK] {item}</li>
        ))}
      </ul>
    </section>
  );
}
```

---

## Paso 20. Conectar todo en `App.jsx`

Abre `src/App.jsx` y reemplaza todo por:

```jsx
import './App.css';
import { CommentForm } from './components/CommentForm';
import { HtmlPreview } from './components/HtmlPreview';
import { SecurityChecklist } from './components/SecurityChecklist';

function App() {
  return (
    <main className="container">
      <header className="hero">
        <h1>Seguridad web con Express y React</h1>
        <p>
          Práctica guiada: HTTPS, XSS, CSRF, Helmet.js, validación y Lighthouse.
        </p>
      </header>

      <SecurityChecklist />
      <CommentForm />
      <HtmlPreview />
    </main>
  );
}

export default App;
```

---

## Paso 21. Agregar estilos básicos

Abre `src/App.css` y reemplaza todo por:

```css
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #0f172a;
  color: #e5e7eb;
}

.container {
  width: min(1000px, 92%);
  margin: 0 auto;
  padding: 32px 0;
}

.hero {
  background: #111827;
  border: 1px solid #334155;
  border-radius: 16px;
  padding: 24px;
  margin-bottom: 20px;
}

.card {
  background: #1e293b;
  border: 1px solid #334155;
  border-radius: 16px;
  padding: 20px;
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 14px;
  font-weight: bold;
}

input,
textarea {
  display: block;
  width: 100%;
  margin-top: 6px;
  padding: 10px;
  border-radius: 8px;
  border: 1px solid #64748b;
  font-size: 16px;
}

textarea {
  min-height: 100px;
}

button {
  background: #38bdf8;
  color: #082f49;
  border: none;
  border-radius: 8px;
  padding: 12px 18px;
  font-weight: bold;
  cursor: pointer;
}

button:hover {
  background: #7dd3fc;
}

.mensaje {
  background: #0f766e;
  padding: 10px;
  border-radius: 8px;
}

.errores {
  background: #7f1d1d;
  padding: 12px 24px;
  border-radius: 8px;
}

.preview {
  background: #f8fafc;
  color: #0f172a;
  padding: 16px;
  border-radius: 8px;
}

pre {
  white-space: pre-wrap;
  background: #020617;
  padding: 12px;
  border-radius: 8px;
  overflow-x: auto;
}
```

---

## Paso 22. Ejecutar frontend

Desde la carpeta `frontend`:

```bash
npm run dev
```

Abre:

```text
http://localhost:5173
```

**Resultado esperado:** debes ver la aplicación con checklist, formulario y vista previa HTML.

---

# Parte 4 - Pruebas guiadas

## Prueba 1. Verificar conexión backend

Abre en navegador:

```text
http://localhost:3001/api/health
```

Marca:

- [ ] El backend responde `estado: OK`.
- [ ] Se muestra Helmet activo.
- [ ] Se muestra CSRF demo disponible.

---

## Prueba 2. Validación en Express

En el formulario React escribe:

```text
Nombre: A
Comentario: Ok
```

Presiona **Guardar comentario**.

Resultado esperado:

- El backend rechaza los datos.
- La interfaz muestra errores.
- No se guarda el comentario.

Ahora escribe:

```text
Nombre: María López
Comentario: Comentario válido para probar seguridad.
```

Resultado esperado:

- El backend acepta los datos.
- El comentario aparece en la lista.

---

## Prueba 3. React escapa contenido por defecto

En el comentario escribe un texto con apariencia de HTML:

```html
<strong>Hola</strong>
```

Resultado esperado:

- En la lista de comentarios debe mostrarse como texto o como contenido escapado según la respuesta del backend.
- No debe ejecutarse nada como código.

---

## Prueba 4. DOMPurify permite formato seguro

En la sección de vista previa HTML escribe:

```html
<p>Texto con <strong>negrita</strong> y <em>énfasis</em></p>
```

Resultado esperado:

- El texto se muestra con formato.
- Las etiquetas permitidas se conservan.

Ahora escribe:

```html
<p>Texto válido</p><script>alert('prueba')</script>
```

Resultado esperado:

- El párrafo se conserva.
- El script no se ejecuta.
- DOMPurify elimina lo no permitido.

---

## Prueba 5. CSRF didáctico

En `src/services/api.js`, comenta temporalmente esta línea:

```js
'X-CSRF-Token': tokenCsrf
```

Intenta guardar un comentario válido.

Resultado esperado:

```text
Token CSRF requerido
```

Luego vuelve a activar la línea.

**Qué aprendiste:** el backend no acepta solicitudes sensibles si falta el token.

---

## Prueba 6. Helmet.js

Abre DevTools en Chrome, pestaña **Network**.

1. Recarga `http://localhost:3001/api/health`.
2. Selecciona la solicitud.
3. Revisa **Response Headers**.
4. Busca encabezados agregados por Helmet.

Puedes encontrar encabezados como:

```text
x-content-type-options: nosniff
x-frame-options: SAMEORIGIN
strict-transport-security: max-age=...
```

> En desarrollo local puede que algunos encabezados se comporten distinto según HTTP/HTTPS. Lo importante es reconocer que Helmet agrega reglas de seguridad a las respuestas.

---

## Prueba 7. Lighthouse

1. Abre `http://localhost:5173` en Chrome.
2. Presiona `F12`.
3. Abre la pestaña **Lighthouse**.
4. Selecciona **Best Practices / Mejores prácticas**.
5. Ejecuta el análisis.
6. Revisa advertencias.

**Interpretación esperada:**

- Si Lighthouse indica que no hay HTTPS, recuerda que estás en entorno local.
- En producción, la aplicación debe servirse por HTTPS.
- Lighthouse ayuda a encontrar señales, pero no reemplaza auditoría completa de seguridad.

---

# Parte 5 - HTTPS y certificados: explicación práctica sin complicar el laboratorio

En producción, HTTPS se aplica con certificados reales. Para clase local, no es obligatorio instalar un certificado real porque el foco es entender el flujo. El concepto es:

```text
Usuario -> HTTPS/TLS -> Servidor Express o plataforma de hosting
```

Ejemplo conceptual de Express con HTTPS:

```js
const https = require('https');
const fs = require('fs');
const express = require('express');

const app = express();

const opcionesSsl = {
  key: fs.readFileSync('/ruta/privkey.pem'),
  cert: fs.readFileSync('/ruta/fullchain.pem')
};

https.createServer(opcionesSsl, app).listen(443, () => {
  console.log('Servidor HTTPS activo');
});
```

**No copies rutas de certificado al azar.** En un servidor real, esas rutas dependen del sistema, dominio y herramienta usada.

---

# Parte 6 - Problemas frecuentes y solución

## Error 1. CORS bloquea la solicitud

Mensaje típico:

```text
Access to fetch at ... has been blocked by CORS policy
```

Solución:

1. Verifica que `FRONTEND_URL=http://localhost:5173` en backend.
2. Verifica que `app.use(cors({ origin: FRONTEND_URL, credentials: true }))` esté antes de las rutas.
3. Reinicia backend.

---

## Error 2. No se guarda comentario por CSRF

Causa probable:

- No se pidió token.
- No se envió encabezado `X-CSRF-Token`.
- No se incluyó `credentials: 'include'`.

Solución:

1. Revisa `obtenerTokenCsrf()`.
2. Revisa `guardarComentario()`.
3. Verifica que el navegador tenga cookie `csrf_token_demo`.

---

## Error 3. React no conecta con Express

Solución:

1. Backend debe correr en `http://localhost:3001`.
2. Frontend debe correr en `http://localhost:5173`.
3. `.env` del frontend debe tener `VITE_API_URL=http://localhost:3001`.
4. Reinicia Vite si cambiaste `.env`.

---

## Error 4. DOMPurify no funciona

Solución:

1. Verifica instalación:

```bash
npm install dompurify
```

2. Verifica importación:

```js
import DOMPurify from 'dompurify';
```

3. Reinicia frontend.

---

# Parte 7 - Checklist final de entrega

Marca cada punto antes de terminar:

- [ ] El backend inicia con `npm run dev`.
- [ ] El frontend inicia con `npm run dev`.
- [ ] `/api/health` responde correctamente.
- [ ] Helmet.js está activo.
- [ ] El formulario rechaza datos inválidos.
- [ ] El formulario acepta datos válidos.
- [ ] El POST falla si falta `X-CSRF-Token`.
- [ ] DOMPurify elimina HTML no permitido.
- [ ] Lighthouse fue ejecutado al menos una vez.
- [ ] Puedes explicar la diferencia entre HTTPS, XSS y CSRF.

---

# Parte 8 - Entrega del estudiante

Sube a tu LMS o repositorio:

1. Captura del backend funcionando.
2. Captura del frontend funcionando.
3. Captura de validación con error.
4. Captura de comentario guardado correctamente.
5. Captura de DOMPurify limpiando contenido no permitido.
6. Captura de Lighthouse.
7. Respuesta breve:

```text
¿Qué capa de seguridad te parece más importante y por qué?
```

---

# Rúbrica breve sobre 20 puntos

| Criterio | Puntaje |
|---|---:|
| Proyecto creado correctamente desde cero | 3 |
| Backend Express funcional con Helmet | 3 |
| Validación y sanitización en Express | 4 |
| Frontend React conectado al backend | 3 |
| DOMPurify aplicado correctamente | 3 |
| CSRF didáctico entendido y probado | 2 |
| Lighthouse y checklist final | 2 |
| **Total** | **20** |

---

# Cierre de aprendizaje

Una aplicación segura no depende de una sola herramienta. HTTPS protege la comunicación, React ayuda con el renderizado seguro, DOMPurify sanitiza HTML, Express valida datos, Helmet.js agrega encabezados y Lighthouse permite auditar buenas prácticas. La seguridad se construye por capas.

## Referencias técnicas sugeridas

- Express - Security best practices: https://expressjs.com/en/advanced/best-practice-security.html
- Helmet.js: https://helmetjs.github.io/
- express-validator: https://express-validator.github.io/docs/
- DOMPurify: https://github.com/cure53/DOMPurify
- Lighthouse: https://developer.chrome.com/docs/lighthouse/overview
- Let’s Encrypt: https://letsencrypt.org/
