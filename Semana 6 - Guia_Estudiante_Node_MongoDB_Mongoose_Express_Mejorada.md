---
title: "Guia para estudiantes - Node.js, MongoDB, Mongoose y Express"
subtitle: "Lideratec Academy - Programacion Web Avanzada"
lang: es
geometry: margin=1in
fontsize: 10pt
mainfont: DejaVu Sans
monofont: DejaVu Sans Mono
---


# Guia para estudiantes - Practica guiada Node.js, MongoDB, Mongoose y Express

**Sesion:** Conexion de Node.js con MongoDB, uso de Mongoose, servicios REST con Express y buenas practicas de manejo de datos.  
**Fuente base:** PPT de la unidad activa.  
**Actualizacion tecnica simple:** MongoDB 8 se usa como referencia de la unidad. Para laboratorio, usa la version estable definida por el docente o por la pagina oficial. El objetivo de esta guia es que puedas instalar, conectar, ejecutar y validar una API REST local.

## Proposito de la practica

Construir paso a paso una API backend con Node.js, Express, Mongoose y MongoDB. La practica te indicara que carpetas crear, que archivos copiar, como levantar MongoDB, como iniciar Node.js y como probar endpoints REST.

## Resultado de aprendizaje observable

Al finalizar, podras **instalar**, **configurar**, **copiar**, **ejecutar**, **validar** y **explicar** una API REST basica conectada a MongoDB mediante Mongoose.

## Duracion sugerida

120 a 150 minutos.

## Requisitos previos minimos

- Tener Node.js instalado.
- Tener MongoDB Community Server instalado.
- Tener `mongosh` instalado o disponible.
- Tener MongoDB Compass instalado de forma opcional.
- Usar CMD, PowerShell, Terminal de VS Code o una terminal equivalente.
- Tener editor de codigo, preferentemente Visual Studio Code.

---

## Parte 0 - Verificar MongoDB antes de programar

Antes de crear el proyecto Node.js, verifica que MongoDB este funcionando.

### Paso 0.1 - Abrir CMD

Abre CMD o PowerShell y ejecuta:

~~~bat
mongod --version
~~~

Si aparece la version de MongoDB, el servidor esta instalado.

Luego ejecuta:

~~~bat
mongosh --version
~~~

Si aparece la version de `mongosh`, la consola esta instalada.

### Paso 0.2 - Probar conexion local

Ejecuta:

~~~bat
mongosh "mongodb://localhost:27017"
~~~

Resultado esperado:

~~~text
test>
~~~

Si no conecta, revisa que el servicio **MongoDB Server** este iniciado en `services.msc`.

### Paso 0.3 - Crear base de prueba

Dentro de `mongosh`, copia y ejecuta:

~~~javascript
use mi_base_datos
~~~

Luego copia y ejecuta:

~~~javascript
db.usuarios.insertOne({
  nombre: "Ana",
  email: "ana@correo.com",
  edad: 20
})
~~~

Consulta:

~~~javascript
db.usuarios.find()
~~~

**Que debes observar:** aparece el documento insertado. Esto confirma que MongoDB funciona.

---

## Parte 1 - Crear el proyecto Node.js

### Paso 1.1 - Crear carpeta del proyecto

En CMD o PowerShell, copia:

~~~bat
mkdir proyecto-node-mongodb
cd proyecto-node-mongodb
~~~

### Paso 1.2 - Inicializar Node.js

Copia:

~~~bat
npm init -y
~~~

Esto crea el archivo:

~~~text
package.json
~~~

### Paso 1.3 - Instalar dependencias

Copia:

~~~bat
npm install express mongoose dotenv helmet cors
~~~

Estas dependencias se usaran asi:

- `express`: crear servidor y rutas REST.
- `mongoose`: conectar y modelar datos en MongoDB.
- `dotenv`: leer variables desde `.env`.
- `helmet`: seguridad basica de cabeceras.
- `cors`: habilitar control basico de origenes.

### Paso 1.4 - Abrir proyecto en VS Code

Copia:

~~~bat
code .
~~~

Si el comando `code .` no funciona, abre Visual Studio Code manualmente y selecciona la carpeta `proyecto-node-mongodb`.

---

## Parte 2 - Crear carpetas y archivos

En VS Code, crea esta estructura exactamente:

~~~text
proyecto-node-mongodb/
│
├── config/
│   └── database.js
│
├── models/
│   └── Usuario.js
│
├── controllers/
│   └── usuarioController.js
│
├── routes/
│   └── usuarioRoutes.js
│
├── .env
├── server.js
└── package.json
~~~

**Actividad para ti:** antes de copiar codigo, escribe para que crees que sirve cada carpeta.

- `config`: _______________________________
- `models`: _______________________________
- `controllers`: __________________________
- `routes`: _______________________________
- `server.js`: ____________________________

---

## Parte 3 - Copiar archivo `.env`

Crea el archivo `.env` en la raiz del proyecto y copia:

~~~env
MONGO_URI=mongodb://localhost:27017/mi_base_datos
PORT=3000
~~~

**Que debes observar:** este archivo queda al mismo nivel que `server.js` y `package.json`.

**Error comun a evitar:** crear el archivo como `.env.txt`. Debe llamarse exactamente `.env`.

---

## Parte 4 - Copiar `config/database.js`

Crea el archivo `config/database.js` y copia:

~~~javascript
const mongoose = require('mongoose');
require('dotenv').config();

async function conectarDB() {
  try {
    await mongoose.connect(process.env.MONGO_URI);
    console.log('Base de datos conectada correctamente');
  } catch (error) {
    console.error('Error al conectar con MongoDB:', error.message);
    process.exit(1);
  }
}

module.exports = conectarDB;
~~~

**Que debes observar:** el codigo usa `process.env.MONGO_URI`, no escribe la URI directamente.

**Espacio para responder:**

- Que archivo contiene `MONGO_URI`?
- Que funcion ejecuta la conexion?
- Que ocurre si MongoDB no esta activo?

---

## Parte 5 - Copiar `models/Usuario.js`

Crea el archivo `models/Usuario.js` y copia:

~~~javascript
const mongoose = require('mongoose');

const usuarioSchema = new mongoose.Schema({
  nombre: {
    type: String,
    required: true
  },
  email: {
    type: String,
    required: true,
    unique: true
  },
  edad: {
    type: Number,
    required: true
  }
});

module.exports = mongoose.model('Usuario', usuarioSchema);
~~~

**Que debes observar:** el modelo define `nombre`, `email` y `edad`.

**Actividad para ti:** identifica que campo tiene restriccion `unique`.

---

## Parte 6 - Copiar `controllers/usuarioController.js`

Crea el archivo `controllers/usuarioController.js` y copia:

~~~javascript
const Usuario = require('../models/Usuario');

exports.crearUsuario = async (req, res) => {
  try {
    const nuevo = await Usuario.create(req.body);
    res.status(201).json(nuevo);
  } catch (error) {
    res.status(400).json({
      error: error.message
    });
  }
};

exports.listarUsuarios = async (req, res) => {
  try {
    const usuarios = await Usuario.find();
    res.json(usuarios);
  } catch (error) {
    res.status(500).json({
      error: error.message
    });
  }
};

exports.actualizarUsuario = async (req, res) => {
  try {
    const actualizado = await Usuario.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true }
    );

    res.json(actualizado);
  } catch (error) {
    res.status(400).json({
      error: error.message
    });
  }
};

exports.eliminarUsuario = async (req, res) => {
  try {
    await Usuario.findByIdAndDelete(req.params.id);
    res.json({ mensaje: 'Usuario eliminado' });
  } catch (error) {
    res.status(400).json({
      error: error.message
    });
  }
};
~~~

**Que debes observar:** cada funcion representa una operacion CRUD.

- `crearUsuario`: crear.
- `listarUsuarios`: consultar.
- `actualizarUsuario`: actualizar.
- `eliminarUsuario`: eliminar.

---

## Parte 7 - Copiar `routes/usuarioRoutes.js`

Crea el archivo `routes/usuarioRoutes.js` y copia:

~~~javascript
const express = require('express');
const router = express.Router();

const {
  crearUsuario,
  listarUsuarios,
  actualizarUsuario,
  eliminarUsuario
} = require('../controllers/usuarioController');

router.post('/usuarios', crearUsuario);
router.get('/usuarios', listarUsuarios);
router.put('/usuarios/:id', actualizarUsuario);
router.delete('/usuarios/:id', eliminarUsuario);

module.exports = router;
~~~

**Que debes observar:** las rutas usan GET, POST, PUT y DELETE.

**Espacio para responder:**

- Que metodo se usa para crear?
- Que metodo se usa para listar?
- Que significa `:id` en una ruta?

---

## Parte 8 - Copiar `server.js`

Crea el archivo `server.js` en la raiz del proyecto y copia:

~~~javascript
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
require('dotenv').config();

const conectarDB = require('./config/database');
const usuarioRoutes = require('./routes/usuarioRoutes');

const app = express();

app.use(helmet());
app.use(cors());
app.use(express.json({ limit: '10kb' }));

async function iniciarServidor() {
  await conectarDB();

  app.use('/api', usuarioRoutes);

  const PORT = process.env.PORT || 3000;

  app.listen(PORT, () => {
    console.log(`Servidor ejecutandose en puerto ${PORT}`);
  });
}

iniciarServidor();
~~~

**Que debes observar:** primero se configura Express, luego se conecta MongoDB, luego se montan rutas y finalmente se levanta el servidor.

---

## Parte 9 - Levantar Node.js

Antes de ejecutar Node.js, confirma que MongoDB esta activo:

~~~bat
mongosh "mongodb://localhost:27017"
~~~

Si conecta, sal de `mongosh` con:

~~~javascript
exit
~~~

Luego, en la carpeta del proyecto, ejecuta:

~~~bat
node server.js
~~~

Resultado esperado:

~~~text
Base de datos conectada correctamente
Servidor ejecutandose en puerto 3000
~~~

**Error comun:** ejecutar `node server.js` desde una carpeta distinta. Debes estar dentro de `proyecto-node-mongodb`.

---

## Parte 10 - Probar la API REST

Puedes usar Postman, Hoppscotch, Thunder Client o una herramienta similar.

### Crear usuario

Metodo:

~~~text
POST
~~~

URL:

~~~text
http://localhost:3000/api/usuarios
~~~

Body JSON:

~~~json
{
  "nombre": "Luis",
  "email": "luis@correo.com",
  "edad": 21
}
~~~

Resultado esperado: respuesta JSON con el usuario creado.

### Listar usuarios

Metodo:

~~~text
GET
~~~

URL:

~~~text
http://localhost:3000/api/usuarios
~~~

Resultado esperado: lista de usuarios.

### Actualizar usuario

Primero copia el `_id` de un usuario existente. Luego usa:

~~~text
PUT
~~~

URL:

~~~text
http://localhost:3000/api/usuarios/PEGAR_ID_AQUI
~~~

Body JSON:

~~~json
{
  "edad": 22
}
~~~

Resultado esperado: usuario actualizado.

### Eliminar usuario

Metodo:

~~~text
DELETE
~~~

URL:

~~~text
http://localhost:3000/api/usuarios/PEGAR_ID_AQUI
~~~

Resultado esperado:

~~~json
{
  "mensaje": "Usuario eliminado"
}
~~~

---

## Parte 11 - Verificar en MongoDB Compass

1. Abre MongoDB Compass.
2. Conecta con:

~~~text
mongodb://localhost:27017
~~~

3. Abre la base:

~~~text
mi_base_datos
~~~

4. Abre la coleccion:

~~~text
usuarios
~~~

5. Verifica que aparezcan los documentos creados desde la API.

---

## Actividades para resolver

### Actividad 1 - Reconocimiento

Indica que archivo contiene cada responsabilidad:

- Conexion MongoDB: ______________________
- Modelo de datos: _______________________
- Logica CRUD: ___________________________
- Rutas REST: ____________________________
- Arranque del servidor: _________________

### Actividad 2 - Modificacion guiada

Crea un nuevo usuario cambiando `nombre`, `email` y `edad`. Registra el resultado.

### Actividad 3 - Aplicacion

Modifica la edad de un usuario existente usando PUT. Copia el `_id` usado y escribe el resultado observado.

### Actividad 4 - Integracion

Explica el flujo completo cuando se envia un POST a `/api/usuarios`:

1. Ruta que recibe: ______________________
2. Controlador que procesa: ______________
3. Modelo que se usa: ____________________
4. Base de datos donde se guarda: ________
5. Respuesta esperada: ___________________

---

## Errores comunes a evitar

- Ejecutar Node.js sin MongoDB activo.
- Crear `.env.txt` en lugar de `.env`.
- Escribir `MONGO_URL` en `.env` y usar `MONGO_URI` en el codigo.
- Instalar dependencias en otra carpeta.
- Olvidar `app.use(express.json())`.
- Probar PUT o DELETE sin pegar un `_id` real.
- Copiar rutas con espacios o errores de mayusculas.

---

## Checklist final de aprendizaje

- [ ] Puedo verificar que MongoDB esta activo.
- [ ] Puedo crear un proyecto Node.js desde cero.
- [ ] Puedo instalar Express, Mongoose, dotenv, helmet y cors.
- [ ] Puedo crear `.env` con `MONGO_URI`.
- [ ] Puedo copiar `database.js` y explicar `mongoose.connect`.
- [ ] Puedo crear un modelo `Usuario`.
- [ ] Puedo crear controladores CRUD.
- [ ] Puedo crear rutas REST con GET, POST, PUT y DELETE.
- [ ] Puedo levantar el servidor con `node server.js`.
- [ ] Puedo probar la API y verificar datos en MongoDB Compass.

---

## Referencias de refuerzo

Blog: https://lideratecacademy.com/  
Canal YouTube: https://www.youtube.com/@LideratecAcademy
