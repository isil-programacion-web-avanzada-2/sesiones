# FASE 13 - Guia del estudiante mejorada

## Proyecto practico desde cero: Mini Notas Cloud

**Curso:** Programacion Web Avanzada  
**Tema:** Despliegue de aplicaciones en la nube  
**Stack:** React + Vite, Node.js + Express, MongoDB Atlas, Render y Vercel  
**Producto final:** una aplicacion basica de notas desplegada en internet.

---

## 1. Que vas a construir

Construiras un proyecto pequeno llamado **Mini Notas Cloud**.

La aplicacion tendra dos partes:

1. **Frontend en React/Vite**  
   Permite ver un formulario y listar notas.

2. **Backend en Node.js/Express**  
   Expone una API REST basica para guardar y listar notas.

3. **Base de datos en MongoDB Atlas**  
   Guarda las notas en la nube.

Luego desplegaras:

- Frontend en **Vercel**.
- Backend en **Render**.
- Base de datos en **MongoDB Atlas**.

Arquitectura final:

```text
Usuario
  ↓
Frontend React en Vercel
  ↓ VITE_API_URL
Backend Express en Render
  ↓ MONGODB_URI
MongoDB Atlas
```

---

## 2. Requisitos previos

Antes de iniciar, debes tener:

- Node.js LTS instalado.
- Visual Studio Code.
- Git instalado.
- Cuenta de GitHub.
- Cuenta de Vercel.
- Cuenta de Render.
- Cuenta de MongoDB Atlas.
- Navegador web actualizado.

Verifica Node y npm:

```bash
node -v
npm -v
```

Si ambos comandos muestran version, puedes continuar.

---

## 3. Estructura general del trabajo

Crearemos dos carpetas separadas:

```text
mini-notas-cloud/
  mini-notas-backend/
  mini-notas-frontend/
```

Tambien se recomienda crear dos repositorios en GitHub:

```text
mini-notas-backend
mini-notas-frontend
```

Esto facilita conectar cada parte con su plataforma:

| Parte | Plataforma | Repositorio recomendado |
|---|---|---|
| Frontend | Vercel | mini-notas-frontend |
| Backend | Render | mini-notas-backend |
| Base de datos | MongoDB Atlas | No aplica |

---

# PARTE A - Crear backend desde cero

## 4. Crear carpeta del backend

Abre una terminal y ejecuta:

```bash
mkdir mini-notas-cloud
cd mini-notas-cloud
mkdir mini-notas-backend
cd mini-notas-backend
npm init -y
```

Instala dependencias:

```bash
npm init -y
npm install express mongoose cors dotenv
npm install -D nodemon
```

si sale error de auditoria ejecuta esto:
```bash
npm audit
npm audit fix
npm audit
```

evitar usar: 
```bash
npm audit fix --force
```

Significado de cada paquete:

| Paquete | Funcion |
|---|---|
| express | Crear el servidor y las rutas API |
| mongoose | Conectar Node.js con MongoDB |
| cors | Permitir comunicacion entre frontend y backend |
| dotenv | Leer variables desde archivo .env en local |
| nodemon | Reiniciar servidor automaticamente en desarrollo |

---

## 5. Configurar package.json del backend

Abre `package.json` y deja los scripts asi:

```json
{
  "name": "mini-notas-backend",
  "version": "1.0.0",
  "description": "API basica para desplegar en Render",
  "main": "src/server.js",
  "scripts": {
    "dev": "nodemon src/server.js",
    "start": "node src/server.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "dotenv": "^16.4.7",
    "express": "^4.21.2",
    "mongoose": "^8.9.5"
  },
  "devDependencies": {
    "nodemon": "^3.1.9"
  }
}
```

Punto clave:

- `npm run dev` se usara localmente.
- `npm start` se usara en Render.

Render necesita un comando de inicio claro. Si `npm start` no existe, el backend no arrancara correctamente.

---

## 6. Crear estructura de archivos del backend

Dentro de `mini-notas-backend`, crea esta estructura:

```text
mini-notas-backend/
  src/
    config/
      database.js
    models/
      Note.js
    routes/
      note.routes.js
    server.js
  .env
  .env.example
  .gitignore
  package.json
```

Puedes crear carpetas desde terminal:

```bash
mkdir -p src/config src/models src/routes
```

En Windows PowerShell, si `mkdir -p` no funciona, crea las carpetas manualmente desde VS Code.

---

## 7. Crear archivo .gitignore del backend

Crea `.gitignore`:

```gitignore
node_modules
.env
.DS_Store
```

Explicacion:

- `node_modules` no se sube a GitHub.
- `.env` no se sube porque contiene variables privadas.
- `.env.example` si se puede subir porque solo muestra la plantilla.

---

## 8. Crear archivo .env.example del backend

Crea `.env.example`:

```bash
PORT=5000
NODE_ENV=development
MONGODB_URI=mongodb+srv://usuario:password@cluster.mongodb.net/mini_notas_cloud?retryWrites=true&w=majority
FRONTEND_URL=http://localhost:5173
```

Luego crea `.env` copiando el mismo contenido:

```bash
PORT=5000
NODE_ENV=development
MONGODB_URI=pegar_aqui_tu_connection_string_real
FRONTEND_URL=http://localhost:5173
```

Por ahora `MONGODB_URI` quedara pendiente hasta crear MongoDB Atlas.

---

## 9. Crear conexion a MongoDB

Archivo: `src/config/database.js`

```js
const mongoose = require('mongoose');

const conectarDB = async () => {
  try {
    if (!process.env.MONGODB_URI) {
      throw new Error('Falta configurar MONGODB_URI');
    }

    await mongoose.connect(process.env.MONGODB_URI);
    console.log('MongoDB conectado correctamente');
  } catch (error) {
    console.error('Error al conectar con MongoDB:', error.message);
    process.exit(1);
  }
};

module.exports = conectarDB;
```

Que hace este archivo:

- Lee `process.env.MONGODB_URI`.
- Intenta conectar con MongoDB Atlas.
- Si conecta, muestra mensaje de exito.
- Si falla, detiene el servidor.

---

## 10. Crear modelo de nota

Archivo: `src/models/Note.js`

```js
const mongoose = require('mongoose');

const noteSchema = new mongoose.Schema(
  {
    title: {
      type: String,
      required: true,
      trim: true,
      maxlength: 80
    },
    content: {
      type: String,
      required: true,
      trim: true,
      maxlength: 300
    }
  },
  {
    timestamps: true
  }
);

module.exports = mongoose.model('Note', noteSchema);
```

Este modelo indica que cada nota tendra:

- `title`: titulo obligatorio.
- `content`: contenido obligatorio.
- `createdAt` y `updatedAt`: generados automaticamente por `timestamps`.

---

## 11. Crear rutas de notas

Archivo: `src/routes/note.routes.js`

```js
const express = require('express');
const Note = require('../models/Note');

const router = express.Router();

router.get('/', async (req, res) => {
  const notes = await Note.find().sort({ createdAt: -1 });
  res.json(notes);
});

router.post('/', async (req, res) => {
  const { title, content } = req.body;

  if (!title || !content) {
    return res.status(400).json({
      message: 'title y content son obligatorios'
    });
  }

  const note = await Note.create({ title, content });
  res.status(201).json(note);
});

module.exports = router;
```

Rutas creadas:

| Metodo | Ruta | Funcion |
|---|---|---|
| GET | /api/notes | Lista todas las notas |
| POST | /api/notes | Crea una nueva nota |

---

## 12. Crear servidor Express

Archivo: `src/server.js`

```js
require('dotenv').config();

const express = require('express');
const cors = require('cors');
const conectarDB = require('./config/database');
const noteRoutes = require('./routes/note.routes');

const app = express();
const PORT = process.env.PORT || 5000;

const allowedOrigins = [
  'http://localhost:5173',
  process.env.FRONTEND_URL
].filter(Boolean);

app.use(cors({
  origin(origin, callback) {
    if (!origin || allowedOrigins.includes(origin)) {
      return callback(null, true);
    }

    return callback(new Error('Origen no permitido por CORS'));
  }
}));

app.use(express.json());

app.get('/', (req, res) => {
  res.json({
    message: 'API Mini Notas funcionando',
    environment: process.env.NODE_ENV || 'development'
  });
});

app.use('/api/notes', noteRoutes);

conectarDB().then(() => {
  app.listen(PORT, () => {
    console.log(`Servidor iniciado en puerto ${PORT}`);
  });
});
```

Que hace este archivo:

- Carga variables de entorno.
- Configura CORS.
- Activa lectura de JSON.
- Crea una ruta base `/`.
- Conecta las rutas `/api/notes`.
- Conecta MongoDB antes de iniciar el servidor.

---

# PARTE B - Crear MongoDB Atlas

## 13. Crear cuenta y proyecto en MongoDB Atlas

Pasos:

1. Ingresa a MongoDB Atlas.
2. Crea cuenta o inicia sesion.
3. Crea un nuevo proyecto, por ejemplo:

```text
mini-notas-cloud
```

4. Crea un cluster gratuito si esta disponible.

La interfaz puede mostrar opciones como **Free** o **Flex**. Lo importante para esta practica es seleccionar una opcion gratuita o de desarrollo.

---

## 14. Crear cluster

En Atlas:

1. Selecciona **Create** o **Build a Database**.
2. Elige una opcion gratuita o de aprendizaje.
3. Selecciona proveedor y region.
4. Deja un nombre como:

```text
Cluster0
```

5. Crea el cluster.

Espera a que Atlas termine la creacion.

---

## 15. Crear usuario de base de datos

En Atlas, crea un usuario para la aplicacion:

```text
Username: admin_app
Password: genera_una_password_segura
```

Importante:

- Este usuario no es tu usuario de inicio de sesion en Atlas.
- Es el usuario que usara el backend para conectarse.
- Guarda la contrasena en un lugar seguro.
- No la subas a GitHub.

---

## 16. Configurar acceso de red

En Atlas, ve a **Network Access**.

Para desarrollo puedes agregar:

```text
0.0.0.0/0
```

Significa que se permite conexion desde cualquier IP.

Advertencia academica:

- Es util para practicar porque Render puede usar IP variable.
- En produccion real se debe restringir el acceso todo lo posible.

---

## 17. Obtener connection string

En el cluster:

1. Clic en **Connect**.
2. Selecciona **Drivers** o **Connect your application**.
3. Selecciona Node.js.
4. Copia una cadena parecida a esta:

```text
mongodb+srv://admin_app:<password>@cluster0.xxxxx.mongodb.net/?retryWrites=true&w=majority
```

Ahora debes editarla:

1. Reemplaza `<password>` por tu password real.
2. Agrega el nombre de la base de datos despues de `.net/`.

Ejemplo final:

```text
mongodb+srv://admin_app:TuPasswordReal@cluster0.xxxxx.mongodb.net/mini_notas_cloud?retryWrites=true&w=majority
```

Si tu password tiene caracteres especiales, Atlas puede pedir codificarlos. Para evitar problemas en clase, usa una contrasena segura pero simple para practica, por ejemplo con letras y numeros.

---

## 18. Pegar connection string en .env local

En `mini-notas-backend/.env`:

```bash
PORT=5000
NODE_ENV=development
MONGODB_URI=mongodb+srv://admin_app:TuPasswordReal@cluster0.xxxxx.mongodb.net/mini_notas_cloud?retryWrites=true&w=majority
FRONTEND_URL=http://localhost:5173
```

Guarda el archivo.

---

## 19. Probar backend local

En la terminal, dentro de `mini-notas-backend`:

```bash
npm run dev
```

Resultado esperado:

```text
MongoDB conectado correctamente
Servidor iniciado en puerto 5000
```

Abre en navegador:

```text
http://localhost:5000/
```

Debe responder algo como:

```json
{
  "message": "API Mini Notas funcionando",
  "environment": "development"
}
```

Prueba listar notas:

```text
http://localhost:5000/api/notes
```

Al inicio debe responder:

```json
[]
```

---

## 20. Probar crear una nota

Puedes usar Thunder Client, Postman o curl.

Ejemplo con curl:

```bash
curl -X POST http://localhost:5000/api/notes \
  -H "Content-Type: application/json" \
  -d "{\"title\":\"Primera nota\",\"content\":\"Probando MongoDB Atlas\"}"
```

En Windows PowerShell, puedes usar:

```powershell
Invoke-RestMethod `
  -Uri "http://localhost:5000/api/notes" `
  -Method POST `
  -ContentType "application/json" `
  -Body '{"title":"Primera nota","content":"Probando MongoDB Atlas"}'
```

Luego vuelve a abrir:

```text
http://localhost:5000/api/notes
```

Debe aparecer la nota creada.

---

# PARTE C - Crear frontend desde cero

## 21. Crear proyecto React con Vite

Vuelve a la carpeta principal:

```bash
cd ..
```

Crea frontend:

```bash
npm create vite@latest mini-notas-frontend -- --template react
cd mini-notas-frontend
npm install
```

Ejecuta:

```bash
npm run dev
```

Abre la URL local que indique Vite, normalmente:

```text
http://localhost:5173
```

---

## 22. Crear variable local del frontend

En `mini-notas-frontend`, crea `.env`:

```bash
VITE_API_URL=http://localhost:5000
```

Crea tambien `.env.example`:

```bash
VITE_API_URL=http://localhost:5000
```

Crea `.gitignore` o verifica que exista:

```gitignore
node_modules
dist
.env
.DS_Store
```

Regla importante:

- En Vite, las variables que el frontend puede leer deben iniciar con `VITE_`.
- Por eso usamos `VITE_API_URL`.
- No coloques `MONGODB_URI` en el frontend.

---

## 23. Reemplazar App.jsx

Archivo: `src/App.jsx`

```jsx
import { useEffect, useState } from 'react';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:5000';

export default function App() {
  const [notes, setNotes] = useState([]);
  const [title, setTitle] = useState('');
  const [content, setContent] = useState('');
  const [message, setMessage] = useState('');

  const loadNotes = async () => {
    try {
      const response = await fetch(`${API_URL}/api/notes`);
      const data = await response.json();
      setNotes(data);
      setMessage('Notas cargadas correctamente');
    } catch (error) {
      setMessage('No se pudo conectar con el backend');
    }
  };

  const createNote = async (event) => {
    event.preventDefault();

    try {
      const response = await fetch(`${API_URL}/api/notes`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ title, content })
      });

      if (!response.ok) {
        throw new Error('Error al crear nota');
      }

      setTitle('');
      setContent('');
      await loadNotes();
      setMessage('Nota creada correctamente');
    } catch (error) {
      setMessage('Error al guardar la nota');
    }
  };

  useEffect(() => {
    loadNotes();
  }, []);

  return (
    <main className="container">
      <section className="hero">
        <p className="tag">Vercel + Render + MongoDB Atlas</p>
        <h1>Mini Notas Cloud</h1>
        <p>
          Proyecto basico para practicar despliegue de frontend,
          backend y base de datos en la nube.
        </p>
      </section>

      <section className="card">
        <h2>Nueva nota</h2>
        <form onSubmit={createNote}>
          <input
            value={title}
            onChange={(event) => setTitle(event.target.value)}
            placeholder="Titulo de la nota"
          />
          <textarea
            value={content}
            onChange={(event) => setContent(event.target.value)}
            placeholder="Contenido de la nota"
          />
          <button type="submit">Guardar nota</button>
        </form>
        <p className="message">{message}</p>
      </section>

      <section className="card">
        <h2>Notas guardadas</h2>
        <div className="list">
          {notes.map((note) => (
            <article className="note" key={note._id}>
              <h3>{note.title}</h3>
              <p>{note.content}</p>
            </article>
          ))}
        </div>
      </section>
    </main>
  );
}
```

---

## 24. Reemplazar style.css

Archivo: `src/style.css`

```css
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, sans-serif;
  color: #e5e7eb;
  background: #0f172a;
}

.container {
  width: min(900px, 92%);
  margin: 0 auto;
  padding: 40px 0;
}

.hero {
  margin-bottom: 24px;
}

.tag {
  color: #38bdf8;
  font-weight: 700;
}

h1 {
  margin: 0;
  font-size: 42px;
}

.card {
  padding: 24px;
  margin-top: 20px;
  border: 1px solid #334155;
  border-radius: 16px;
  background: #111827;
}

form {
  display: grid;
  gap: 12px;
}

input,
textarea,
button {
  width: 100%;
  padding: 12px;
  border-radius: 10px;
  border: 1px solid #334155;
  font-size: 16px;
}

textarea {
  min-height: 110px;
}

button {
  border: none;
  color: #0f172a;
  background: #38bdf8;
  font-weight: 700;
  cursor: pointer;
}

.message {
  color: #fbbf24;
}

.list {
  display: grid;
  gap: 12px;
}

.note {
  padding: 16px;
  border-radius: 12px;
  background: #1f2937;
}

.note h3 {
  margin-top: 0;
}
```

---

## 25. Verificar main.jsx

Archivo: `src/main.jsx`

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.jsx';
import './style.css';

createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

---

## 26. Probar frontend con backend local

Abre dos terminales:

Terminal 1 - backend:

```bash
cd mini-notas-backend
npm run dev
```

Terminal 2 - frontend:

```bash
cd mini-notas-frontend
npm run dev
```

Abre:

```text
http://localhost:5173
```

Prueba:

1. Escribe un titulo.
2. Escribe un contenido.
3. Clic en **Guardar nota**.
4. Verifica que la nota aparezca en la lista.
5. Recarga la pagina.
6. La nota debe seguir ahi porque viene de MongoDB Atlas.

Si esto funciona, ya tienes el proyecto listo para desplegar.

---

# PARTE D - Subir backend a GitHub

## 27. Crear repositorio backend

En GitHub:

1. Crea repositorio nuevo.
2. Nombre sugerido:

```text
mini-notas-backend
```

3. No agregues README desde GitHub si ya tienes archivos locales.

Desde terminal en `mini-notas-backend`:

```bash
git init
git add .
git commit -m "backend inicial mini notas"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/mini-notas-backend.git
git push -u origin main
```

Verifica en GitHub que subieron los archivos.

No debe subir `.env`.

---

# PARTE E - Desplegar backend en Render

## 28. Crear Web Service en Render

En Render:

1. Inicia sesion.
2. Clic en **New**.
3. Selecciona **Web Service**.
4. Conecta tu cuenta de GitHub.
5. Selecciona el repositorio:

```text
mini-notas-backend
```

Configura:

| Campo | Valor recomendado |
|---|---|
| Name | mini-notas-backend |
| Runtime | Node |
| Branch | main |
| Build Command | npm install |
| Start Command | npm start |
| Plan | Free o el disponible para practica |

---

## 29. Configurar variables en Render

En la seccion **Environment Variables**, agrega:

```bash
NODE_ENV=production
MONGODB_URI=tu_connection_string_de_mongodb_atlas
FRONTEND_URL=http://localhost:5173
```

Notas importantes:

- `MONGODB_URI` debe ser la cadena real de MongoDB Atlas.
- `FRONTEND_URL` se actualizara despues con la URL real de Vercel.
- No agregues `VITE_API_URL` en Render; esa variable pertenece al frontend.

---

## 30. Crear servicio y probar backend en Render

Clic en **Create Web Service**.

Render empezara a:

1. Clonar repositorio.
2. Instalar dependencias.
3. Ejecutar `npm start`.
4. Levantar el servidor.

Cuando termine, obtendras una URL parecida a:

```text
https://mini-notas-backend.onrender.com
```

Abre:

```text
https://mini-notas-backend.onrender.com/
```

Resultado esperado:

```json
{
  "message": "API Mini Notas funcionando",
  "environment": "production"
}
```

Luego prueba:

```text
https://mini-notas-backend.onrender.com/api/notes
```

Debe devolver un arreglo de notas.

Nota operativa:

- En plan gratuito, Render puede suspender el servicio despues de un tiempo sin trafico.
- Si demora en responder, espera cerca de un minuto y vuelve a probar.

---

# PARTE F - Subir frontend a GitHub

## 31. Crear repositorio frontend

En GitHub:

1. Crea repositorio nuevo.
2. Nombre sugerido:

```text
mini-notas-frontend
```

Desde terminal en `mini-notas-frontend`:

```bash
git init
git add .
git commit -m "frontend inicial mini notas"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/mini-notas-frontend.git
git push -u origin main
```

Verifica que `.env` no haya subido.

---

# PARTE G - Desplegar frontend en Vercel

## 32. Importar proyecto en Vercel

En Vercel:

1. Inicia sesion con GitHub.
2. Clic en **Add New**.
3. Selecciona **Project**.
4. Importa el repositorio:

```text
mini-notas-frontend
```

Vercel deberia detectar Vite.

Verifica:

| Campo | Valor |
|---|---|
| Framework Preset | Vite |
| Install Command | npm install |
| Build Command | npm run build |
| Output Directory | dist |

---

## 33. Configurar variable VITE_API_URL en Vercel

Antes de desplegar, agrega Environment Variable:

```bash
VITE_API_URL=https://mini-notas-backend.onrender.com
```

Reemplaza la URL con la URL real de tu backend en Render.

Importante:

- En Vite, debe iniciar con `VITE_`.
- `VITE_API_URL` apunta al backend.
- No agregues `MONGODB_URI` en Vercel.

---

## 34. Desplegar frontend

Clic en **Deploy**.

Vercel ejecutara:

1. `npm install`.
2. `npm run build`.
3. Publicacion en una URL publica.

Obtendras una URL parecida a:

```text
https://mini-notas-frontend.vercel.app
```

Abre la URL.

Es probable que la interfaz cargue, pero todavia debas corregir CORS en Render.

---

# PARTE H - Conectar Render con Vercel correctamente

## 35. Actualizar FRONTEND_URL en Render

Cuando ya tengas la URL real de Vercel, vuelve a Render.

En tu servicio backend:

1. Entra a **Environment Variables**.
2. Edita:

```bash
FRONTEND_URL=https://mini-notas-frontend.vercel.app
```

3. Guarda cambios.
4. Ejecuta redeploy o reinicia el servicio.

Esto permite que el backend acepte peticiones desde el dominio real del frontend.

---

## 36. Si cambias VITE_API_URL en Vercel, redeploy

Si te equivocaste en `VITE_API_URL`:

1. Ve a Vercel.
2. Entra al proyecto frontend.
3. Abre **Settings**.
4. Entra a **Environment Variables**.
5. Corrige `VITE_API_URL`.
6. Guarda.
7. Ejecuta un nuevo deployment.

Nota operativa:

- Los cambios de variables en Vercel no se aplican a despliegues anteriores.
- Debes hacer redeploy para que el frontend compile con el nuevo valor.

---

# PARTE I - Validacion final del proyecto

## 37. Checklist de validacion tecnica

Completa esta tabla:

| Validacion | Resultado esperado | Cumple |
|---|---|---|
| Backend local responde | http://localhost:5000/ muestra JSON |  |
| Backend conecta con Atlas | Log: MongoDB conectado correctamente |  |
| POST crea nota | La nota aparece en /api/notes |  |
| Frontend local carga | http://localhost:5173 abre app |  |
| Frontend local crea nota | Se guarda en MongoDB |  |
| Backend Render responde | URL onrender.com muestra JSON |  |
| Frontend Vercel carga | URL vercel.app abre app |  |
| Vercel consume Render | La app lista notas desde backend cloud |  |
| CORS correcto | No hay error CORS en consola |  |
| Variables correctas | VITE_API_URL y MONGODB_URI estan bien ubicadas |  |

---

## 38. Errores comunes y solucion

| Error | Causa probable | Solucion |
|---|---|---|
| Frontend carga pero no muestra notas | VITE_API_URL incorrecta | Corregir variable en Vercel y redeploy |
| Error CORS en navegador | FRONTEND_URL incorrecta en Render | Colocar URL real de Vercel en Render |
| Backend no arranca en Render | Start Command incorrecto | Usar `npm start` y revisar package.json |
| MongoDB no conecta | MONGODB_URI incorrecto | Revisar usuario, password y nombre de BD |
| Deploy exitoso pero app falla | Falta validar integracion | Revisar consola, Network y logs |
| Render demora en responder | Servicio gratuito dormido | Esperar cerca de un minuto y reintentar |
| Variable existe pero no aplica | No se hizo redeploy | Ejecutar nuevo deployment |

---

# PARTE J - Evidencia que debe entregar el estudiante

El estudiante debe entregar:

1. URL del repositorio backend en GitHub.
2. URL del repositorio frontend en GitHub.
3. URL del backend desplegado en Render.
4. URL del frontend desplegado en Vercel.
5. Captura de MongoDB Atlas mostrando coleccion con datos.
6. Captura de variables configuradas sin mostrar secretos completos.
7. Captura de la app creando una nota.
8. README con instrucciones.
9. Video corto explicando el despliegue y los errores corregidos.

---

## 39. README sugerido para el frontend

````md
# Mini Notas Cloud - Frontend

Frontend construido con React y Vite.

## Variables

```bash
VITE_API_URL=https://mi-backend.onrender.com
```

## Scripts

```bash
npm install
npm run dev
npm run build
```

## Despliegue

Publicado en Vercel.
````

---

## 40. README sugerido para el backend

````md
# Mini Notas Cloud - Backend

API construida con Node.js, Express y MongoDB Atlas.

## Variables

```bash
NODE_ENV=production
MONGODB_URI=connection_string_privado
FRONTEND_URL=https://mi-frontend.vercel.app
```

## Scripts

```bash
npm install
npm run dev
npm start
```

## Rutas

- GET /
- GET /api/notes
- POST /api/notes

## Despliegue

Publicado en Render.
````

---

# PARTE K - GitHub Actions opcional

## 41. Workflow basico para frontend

Crea archivo:

```text
.github/workflows/frontend-check.yml
```

Contenido:

```yaml
name: Frontend Check

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Clonar repositorio
        uses: actions/checkout@v4

      - name: Configurar Node.js
        uses: actions/setup-node@v4
        with:
          node-version: lts/*

      - name: Instalar dependencias
        run: npm install

      - name: Construir proyecto
        run: npm run build
```

Este workflow valida que el frontend pueda construirse correctamente.

---

## 42. Workflow basico para backend

Crea archivo:

```text
.github/workflows/backend-check.yml
```

Contenido:

```yaml
name: Backend Check

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  install:
    runs-on: ubuntu-latest

    steps:
      - name: Clonar repositorio
        uses: actions/checkout@v4

      - name: Configurar Node.js
        uses: actions/setup-node@v4
        with:
          node-version: lts/*

      - name: Instalar dependencias
        run: npm install
```

Este workflow no prueba conexion real a MongoDB porque no hemos configurado secrets para CI. Para esta clase basta como validacion inicial.

---

# PARTE L - Resumen final de aprendizaje

Al terminar esta guia, debes poder explicar:

1. Por que el frontend se despliega en Vercel.
2. Por que el backend se despliega en Render.
3. Por que MongoDB Atlas se usa como base de datos cloud.
4. Para que sirve `VITE_API_URL`.
5. Para que sirve `MONGODB_URI`.
6. Para que sirve `FRONTEND_URL`.
7. Por que no se sube `.env` a GitHub.
8. Por que se debe hacer redeploy al cambiar variables en Vercel.
9. Como leer errores basicos en Render Logs.
10. Como validar si la aplicacion completa funciona.

---

## 43. Rubrica rapida de revision sobre 20

| Criterio | Puntaje |
|---|---:|
| Backend local funcional y conectado a Atlas | 4 |
| Frontend local consume backend | 3 |
| Backend desplegado correctamente en Render | 3 |
| Frontend desplegado correctamente en Vercel | 3 |
| Variables de entorno bien ubicadas | 3 |
| Evidencias, README y video explicativo | 3 |
| Buenas practicas: no exponer secretos, revisar logs | 1 |
| Total | 20 |

---

## 44. Nota tecnica operativa

Las plataformas pueden cambiar visualmente. Si un boton cambia de nombre, no memorices la pantalla: busca el flujo equivalente.

Flujo estable:

```text
GitHub -> Vercel -> Render -> MongoDB Atlas
```

Variables clave:

```text
Frontend: VITE_API_URL
Backend: MONGODB_URI, FRONTEND_URL, NODE_ENV
```

Validacion clave:

```text
URL frontend carga
URL backend responde
MongoDB guarda datos
No hay error CORS
```

---

## 45. Referencias tecnicas consultadas

- Vercel Docs - Environment Variables.
- Render Docs - Free Web Services.
- MongoDB Atlas Docs - Free Clusters and Cluster Types.
- GitHub Docs - Building and testing Node.js with GitHub Actions.
