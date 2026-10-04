---
course_id: PWA
session_id: S06
module_id: TEMA_06
course_version: 1.0
source_origin: PPT
status: validated
---

# Guía del estudiante - Tema 06 - Conexión de Node.js con bases de datos (MongoDB)

## 1. Propósito de la sesión


En esta clase-laboratorio construirás, desde una carpeta vacía, una API REST de usuarios con Node.js y Express conectada a MongoDB mediante Mongoose. El objetivo no es copiar un proyecto terminado: vas a crear cada archivo en el momento en que su responsabilidad se vuelve necesaria, observarás qué cambia después de cada paso y comprobarás el resultado con peticiones HTTP.

Al terminar tendrás una estructura modular con conexión a base de datos, modelo de Mongoose, controlador, rutas REST y servidor. Podrás crear, listar, consultar por id, actualizar y eliminar usuarios. También aplicarás las buenas prácticas de la sesión: variables de entorno, validación del esquema, manejo de errores, `helmet`, `cors` y límite de tamaño para el JSON recibido.

**Evidencia final observable:** `POST /api/usuarios` guarda un documento en MongoDB y devuelve `201`; `GET`, `PUT/PATCH` y `DELETE` trabajan sobre ese documento desde la API.

## 2. Resultado observable

### Lectura sugerida del docente


Hoy vamos a transformar un servidor Express en un backend que trabaja con datos persistentes. Hasta ahora una API puede responder correctamente y, aun así, no tener una base de datos real detrás. En esta sesión separaremos responsabilidades: la configuración de MongoDB estará en `config/database.js`; la forma de los datos estará en `models/Usuario.js`; la lógica de cada operación estará en `controllers/usuarioController.js`; las URLs y métodos HTTP estarán en `routes/usuarioRoutes.js`; y `server.js` coordinará el arranque. Cada capa tendrá una razón concreta para existir.

No necesitas memorizar el proyecto completo. Lo importante es poder explicar el flujo: una petición llega a Express, una ruta identifica la operación, un controlador utiliza el modelo de Mongoose, Mongoose interactúa con MongoDB y finalmente el servidor devuelve una respuesta HTTP. Si puedes construir, ejecutar, modificar y diagnosticar ese flujo, habrás alcanzado el objetivo de la sesión.

### Desempeños observables
- Preparar un proyecto Node.js para Express y Mongoose.
- Conectar MongoDB usando una URI almacenada en `.env`.
- Definir un esquema y modelo de Mongoose.
- Implementar operaciones CRUD REST.
- Interpretar respuestas `201`, `200`, `400` y `404`.
- Diagnosticar errores de conexión, validación e identificadores.
- Aplicar una estructura modular y middlewares básicos.

### Criterio de dominio
Dominas la sesión si puedes explicar qué responsabilidad tiene cada archivo, predecir qué ocurrirá antes de una petición, modificar una operación sin romper las demás y comprobar en MongoDB que los cambios realmente se persistieron.

## 3. Antes de iniciar

### Debe saber
- Crear y ejecutar un proyecto Node.js con `npm`.
- Reconocer una ruta Express y los métodos HTTP GET, POST, PUT/PATCH y DELETE.
- Comprender `req`, `res`, `req.body` y `req.params` a nivel de la sesión previa.
- Usar `async/await` y `try/catch` en operaciones asíncronas.

### Debe tener disponible al comenzar la parte práctica
- Node.js LTS y npm. Para una instalación nueva, Node.js 24 LTS es una opción adecuada; Node.js 22 también permanece en LTS.
- Un editor de código como Visual Studio Code.
- Una terminal de Windows (PowerShell o CMD).
- Acceso para instalar MongoDB Community Server 8.0 si todavía no está instalado.
- Una herramienta para enviar peticiones HTTP, por ejemplo Thunder Client o Postman.

> **Importante:** no se asume que MongoDB ya esté instalado. La sección 4 incluye una ruta para verificarlo y otra para instalarlo desde cero.

### Herramientas opcionales
- **MongoDB Compass:** recomendable para observar visualmente bases, colecciones y documentos, pero no es necesario para que la API funcione.
- **mongosh:** útil para validar MongoDB desde consola, pero actualmente se instala por separado del servidor MongoDB.

### No se asumirá todavía
No necesitas autenticación, autorización, despliegue, MongoDB Atlas, relaciones avanzadas, índices manuales ni arquitectura de producción.

## 4. Preparación del entorno desde cero

Esta guía utiliza **Windows** como ruta principal de aula porque el material de la sesión trabaja con rutas y comandos de Windows. Si utilizas macOS o Linux, la lógica del laboratorio es la misma, pero la instalación del motor debe seguir la guía oficial específica de tu sistema.

### 4.1 Verifica Node.js y npm

Abre PowerShell o CMD y ejecuta:

```bash
node --version
npm --version
```

**Resultado esperado:** aparecen dos números de versión.

**Si `node` no se reconoce:** no continúes todavía. Node.js es un prerrequisito cubierto en las sesiones anteriores; corrige la instalación o el `PATH` antes de seguir.

### 4.2 Determina si MongoDB ya está instalado

Abre **PowerShell** y ejecuta:

```powershell
Get-Service MongoDB -ErrorAction SilentlyContinue
```

Interpreta el resultado:

- Si aparece un servicio llamado `MongoDB`, sigue la **Ruta A**.
- Si no aparece ningún servicio, sigue la **Ruta B**.
- Si sabes que MongoDB fue instalado sin servicio, sigue la **Ruta C**.

---

### Ruta A - MongoDB ya está instalado como servicio

#### Paso A1 - Comprueba su estado

```powershell
Get-Service MongoDB
```

**Resultado esperado:** `Status` debe indicar `Running`.

Si aparece `Stopped`, abre PowerShell con permisos administrativos y ejecuta:

```powershell
Start-Service MongoDB
```

Vuelve a comprobar:

```powershell
Get-Service MongoDB
```

#### Paso A2 - Valida el motor

Si tienes `mongosh` instalado:

```bash
mongosh "mongodb://127.0.0.1:27017"
```

Dentro del shell ejecuta:

```javascript
db.runCommand({ ping: 1 })
```

Debes observar una respuesta cuyo campo `ok` indique éxito.

Si **no tienes `mongosh`**, no es un bloqueo. Puedes usar MongoDB Compass con esta conexión:

```text
mongodb://127.0.0.1:27017
```

Si tampoco tienes Compass, basta con confirmar que el servicio está en `Running`; la conexión real desde Node.js se comprobará en `EJ02`.

---

### Ruta B - MongoDB NO está instalado

Esta es la ruta recomendada para un equipo nuevo de laboratorio.

#### Paso B1 - Descarga MongoDB Community Server

Abre el centro oficial:

```text
https://www.mongodb.com/try/download/community
```

Selecciona:

```text
Version: 8.0
Platform: Windows
Package: msi
```

Descarga el instalador `.msi`.

#### Paso B2 - Ejecuta el instalador

1. Abre el archivo `.msi` descargado.
2. Avanza con el asistente.
3. En **Setup Type**, selecciona **Complete** para una instalación académica estándar.
4. En **Service Configuration**, mantén activada **Install MongoD as a Service**.
5. Mantén la cuenta de servicio predeterminada **Network Service user** salvo que tu institución indique otra configuración.
6. **MongoDB Compass es opcional.** Puedes instalarlo porque facilita la observación visual de los documentos, pero el laboratorio no depende de él.
7. Completa la instalación.

**Qué ocurre al instalar como servicio:** Windows registra MongoDB como un servicio y, en una instalación correcta, lo inicia automáticamente. En esta ruta no necesitas crear manualmente `C:\data\db`.

#### Paso B3 - Comprueba la instalación

Abre una nueva PowerShell:

```powershell
Get-Service MongoDB
```

**Resultado esperado:** el servicio aparece en estado `Running`.

También puedes verificar el ejecutable usando la ruta habitual de MongoDB 8.0:

```powershell
& "C:\Program Files\MongoDB\Server\8.0\bin\mongod.exe" --version
```

**Nota:** si instalaste otra subversión o cambiaste la ruta durante el asistente, ajusta la ubicación.

#### Paso B4 - Sobre `mongosh`

El servidor MongoDB y `mongosh` son componentes distintos. Si `mongosh` no se reconoce después de instalar MongoDB Server, no significa que el servidor esté mal instalado. Puedes instalar `mongosh` por separado o continuar y validar la conexión desde Node.js en `EJ02`.

---

### Ruta C - MongoDB instalado sin servicio de Windows

Usa esta ruta solo si deliberadamente instalaste los binarios sin servicio.

#### Paso C1 - Crea un directorio de datos

En PowerShell con permisos adecuados:

```powershell
New-Item -ItemType Directory -Force C:\data\db
```

#### Paso C2 - Inicia `mongod` manualmente

```powershell
& "C:\Program Files\MongoDB\Server\8.0\bin\mongod.exe" --dbpath "C:\data\db"
```

Deja esa terminal abierta mientras trabajas. Cuando MongoDB está listo, el proceso queda esperando conexiones en el puerto local configurado.

> Para una clase universitaria inicial se recomienda la instalación como servicio porque reduce pasos operativos y evita depender de mantener una terminal adicional abierta.

---

### 4.3 Checkpoint obligatorio antes de programar

No avances hasta poder marcar:

- [ ] `node --version` funciona.
- [ ] `npm --version` funciona.
- [ ] MongoDB está instalado.
- [ ] El servicio MongoDB está `Running`, o `mongod` está ejecutándose manualmente.
- [ ] El puerto local esperado es `27017`.
- [ ] Sabes que Compass y `mongosh` son opcionales para este laboratorio.

### 4.4 Crea la carpeta del proyecto

En una ubicación de práctica:

```bash
mkdir api-usuarios-mongodb
cd api-usuarios-mongodb
npm init -y
```

**Qué cambia:** aparece `package.json`, que identifica el proyecto Node.js y registra sus dependencias.

### 4.5 Instala solo las dependencias que ya necesitamos

Ejecuta:

```bash
npm install express mongoose dotenv
```

Por ahora instalamos:

- `express`: servidor y rutas HTTP;
- `mongoose`: conexión, esquemas, modelos y operaciones con MongoDB;
- `dotenv`: lectura de variables desde `.env`.

**No instalamos todavía `cors` ni `helmet`.** Se incorporarán en `EJ08`, cuando aparezca la necesidad de los middlewares globales. Así cada dependencia se introduce en el momento en que cumple una responsabilidad visible.

### 4.6 Estructura inicial antes de EJ01

En este punto la carpeta debe verse aproximadamente así:

```text
api-usuarios-mongodb/
├── node_modules/
├── package-lock.json
└── package.json
```

No crees aún `config`, `models`, `controllers` ni `routes`. Cada carpeta aparecerá cuando el ejemplo correspondiente la necesite.

### 4.7 Evidencia de preparación

Conserva estas evidencias para mostrar al docente:

1. salida de `node --version`;
2. salida de `Get-Service MongoDB` o evidencia del proceso `mongod` activo;
3. árbol inicial de la carpeta `api-usuarios-mongodb`;
4. `package.json` con `express`, `mongoose` y `dotenv` instalados.

Con el entorno preparado, recién ahora comienza `EJ01`.

# EJ01 - Crear el proyecto y comprobar un servidor Express mínimo

## Qué vamos a construir / resolver
Un punto de entrada `server.js` que levante Express y exponga `GET /health`. Este primer hito separa el problema de HTTP del problema de base de datos: primero comprobamos que el servidor web funciona.

## Archivos involucrados
`package.json`, `server.js`.

### Paso 1 - Importar Express y crear la aplicación

**Estado antes.** La carpeta tiene `package.json` y dependencias, pero todavía no existe un servidor.

**¿Por qué necesitamos este paso?** Express necesita una instancia de aplicación que reciba las rutas y middlewares.

**Escribe ahora:**

```javascript
const express = require('express');
const app = express();
```

**Explicación detallada.** `require('express')` carga el paquete instalado. `express()` crea el objeto `app`, que representa la aplicación HTTP sobre la que registraremos rutas.

**Elementos nuevos.** `express`: dependencia; `app`: aplicación Express.

**Estado después.** Existe una aplicación en memoria, pero aún no escucha un puerto.

**Por qué podemos avanzar.** Podemos añadir una ruta sencilla y después iniciar la escucha.


### Paso 2 - Crear una ruta de salud

**Estado antes.** La aplicación existe, pero no hay una URL que permita comprobarla.

**¿Por qué necesitamos este paso?** Necesitamos una evidencia mínima de que Express puede recibir una petición y responder.

**Escribe ahora:**

```javascript
app.get('/health', (req, res) => {
  res.json({ estado: 'ok' });
});
```

**Explicación detallada.** `app.get` registra una ruta GET. `/health` es la dirección. `res.json()` envía una respuesta JSON. En este ejemplo no usamos `req`, pero Express lo entrega porque representa la petición entrante.

**Elementos nuevos.** Ruta `/health`; método GET; respuesta JSON.

**Estado después.** La aplicación ya sabe responder a `/health`, pero aún no hay proceso escuchando.

**Por qué podemos avanzar.** Solo falta abrir un puerto.


### Paso 3 - Escuchar en el puerto 3001

**Estado antes.** Tenemos una aplicación y una ruta, pero ningún puerto de red asociado.

**¿Por qué necesitamos este paso?** `app.listen` inicia el servidor HTTP y mantiene el proceso activo.

**Escribe ahora:**

```javascript
app.listen(3001, () => {
  console.log('Servidor en http://localhost:3001');
});
```

**Explicación detallada.** El primer argumento es el puerto. La función callback se ejecuta cuando el servidor queda escuchando. El mensaje de consola es evidencia de arranque, no una respuesta HTTP.

**Elementos nuevos.** Puerto 3001; callback de inicio.

**Estado después.** El servidor está listo para recibir peticiones locales.

**Por qué podemos avanzar.** Podemos ejecutar y comprobar antes de añadir MongoDB.


## Archivo completo al terminar EJ01
```javascript
const express = require('express');
const app = express();

app.get('/health', (req, res) => {
  res.json({ estado: 'ok' });
});

app.listen(3001, () => {
  console.log('Servidor en http://localhost:3001');
});
```

## Antes de ejecutar: predicción
1. ¿Qué mensaje aparecerá en la terminal?
2. ¿Qué JSON esperas al abrir `http://localhost:3001/health`?

## Ejecuta / valida
```bash
node server.js
```
Visita o consulta `GET http://localhost:3001/health`. Debes recibir `200` y `{"estado":"ok"}`. Si aparece `EADDRINUSE`, el puerto está ocupado: cierra el proceso anterior o usa otro puerto de laboratorio.

## T01 - Tarea espejo de EJ01

**Enunciado.** Añade `GET /estado` para responder `{ "api": "usuarios", "estado": "activa" }`.

**Archivos/artefactos a modificar.** `server.js`.

**Restricción.** No elimines `/health` ni cambies todavía la conexión a MongoDB.

**Pista.** Repite el patrón `app.get(ruta, callback)` usado en el ejemplo.

**Evidencia que debes mostrar.** Captura o texto de la respuesta de `/estado`.

**Cómo saber si está correcta.** La petición devuelve HTTP 200 y exactamente un objeto JSON con `api` y `estado`.


# EJ02 - Conectar la aplicación con MongoDB mediante Mongoose

## Qué vamos a construir / resolver
Separaremos la conexión en `config/database.js` y guardaremos la URI en `.env`. El servidor solo debe iniciar después de que MongoDB acepte la conexión.

### Paso 1 - Crear `.env`

**Estado antes.** El puerto y la base de datos todavía están implícitos o escritos directamente en código.

**¿Por qué necesitamos este paso?** La cadena de conexión cambia por entorno y no debe quedar dispersa dentro del código fuente.

**Escribe ahora:**

```text
MONGO_URI=mongodb://127.0.0.1:27017/web_avanzada
PORT=3001
```

**Explicación detallada.** `MONGO_URI` apunta a MongoDB local en el puerto 27017 y selecciona la base `web_avanzada`. Usamos `127.0.0.1` para evitar problemas de resolución IPv6 que pueden ocurrir con `localhost` en entornos Node.js modernos.

**Elementos nuevos.** Variable de entorno `MONGO_URI`; variable `PORT`.

**Estado después.** La configuración existe fuera del código.

**Por qué podemos avanzar.** Ahora necesitamos cargarla y conectar Mongoose.


### Paso 2 - Crear `config/database.js`

**Estado antes.** Existe la URI, pero ningún módulo tiene todavía la responsabilidad de abrir la conexión.

**¿Por qué necesitamos este paso?** Una función dedicada evita mezclar la conexión con rutas o controladores.

**Escribe ahora:**

```javascript
const mongoose = require('mongoose');

async function conectarDB() {
  if (!process.env.MONGO_URI) {
    throw new Error('MONGO_URI no está definida en el archivo .env');
  }

  await mongoose.connect(process.env.MONGO_URI);
  console.log('Base de datos conectada');
}

module.exports = conectarDB;
```

**Explicación detallada.** `mongoose.connect()` devuelve una promesa, por eso `conectarDB` es `async` y usa `await`. La comprobación de `MONGO_URI` produce un error claro si olvidamos `.env`. `module.exports` permite que `server.js` reutilice la función.

**Elementos nuevos.** `mongoose`, `conectarDB`, `process.env.MONGO_URI`, exportación CommonJS.

**Estado después.** Tenemos una función de conexión reutilizable, pero aún no se llama.

**Por qué podemos avanzar.** Integraremos la función en el arranque del servidor.


### Paso 3 - Hacer que el servidor espere la conexión

**Estado antes.** EJ01 iniciaba Express inmediatamente, aunque MongoDB pudiera estar apagado.

**¿Por qué necesitamos este paso?** La API no debería anunciarse como lista si la capa de datos crítica no está disponible.

**Escribe ahora:**

```javascript
require('dotenv').config();
const conectarDB = require('./config/database');

async function iniciarServidor() {
  try {
    await conectarDB();
    const PORT = process.env.PORT || 3001;
    app.listen(PORT, () => {
      console.log(`Servidor en http://localhost:${PORT}`);
    });
  } catch (error) {
    console.error('No se pudo iniciar la aplicación:', error.message);
    process.exit(1);
  }
}

iniciarServidor();
```

**Explicación detallada.** `dotenv.config()` carga `.env` en `process.env`. `await conectarDB()` obliga a esperar. Solo si la conexión tiene éxito se ejecuta `app.listen`. El `catch` captura el fallo inicial y `process.exit(1)` termina el proceso con estado de error.

**Elementos nuevos.** `dotenv`, función de arranque, `try/catch`, `process.exit(1)`.

**Estado después.** Servidor y base de datos quedan coordinados.

**Por qué podemos avanzar.** Podemos empezar a modelar datos porque la conexión ya existe.


## Resultado esperado
Al ejecutar `node server.js`, con MongoDB activo, la terminal debe mostrar primero `Base de datos conectada` y después `Servidor en http://localhost:3001`. Si detienes MongoDB o cambias la URI a un puerto incorrecto, el servidor no debe quedar escuchando.

## T02 - Tarea espejo de EJ02

**Enunciado.** Cambia el nombre de la base en `.env` a `web_avanzada_tarea`, reinicia la aplicación y valida que la conexión siga siendo exitosa.

**Archivos/artefactos a modificar.** `.env`.

**Restricción.** No cambies host ni puerto; modifica únicamente el segmento final de la URI.

**Pista.** La URI termina en `/nombre_de_base`.

**Evidencia que debes mostrar.** Mensaje de conexión exitosa y valor final de `MONGO_URI` ocultando cualquier credencial si existiera.

**Cómo saber si está correcta.** La aplicación conecta sin modificar `database.js`.


# EJ03 - Definir el esquema y modelo `Usuario`

## Qué vamos a construir / resolver
Crearemos `models/Usuario.js`. Un esquema define qué campos esperamos y qué validaciones aplica Mongoose; el modelo es la interfaz que después utilizarán los controladores para ejecutar CRUD.

### Paso 1 - Definir campos y tipos

**Estado antes.** MongoDB puede almacenar documentos flexibles, pero nuestra aplicación todavía no expresa qué forma debería tener un usuario.

**¿Por qué necesitamos este paso?** Necesitamos una estructura explícita para validar datos antes de guardarlos.

**Escribe ahora:**

```javascript
const mongoose = require('mongoose');

const usuarioSchema = new mongoose.Schema({
  nombre: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  edad: { type: Number, min: 0 }
});
```

**Explicación detallada.** Cada propiedad describe un campo. `String` y `Number` definen tipos. `required: true` exige `nombre` y `email`. `unique: true` solicita un índice único para `email`. `min: 0` evita edades negativas.

**Elementos nuevos.** `usuarioSchema`; validadores `required`, `unique` y `min`.

**Estado después.** La forma esperada del documento está definida, pero aún no tenemos una API de operaciones sobre la colección.

**Por qué podemos avanzar.** Compilaremos el esquema como modelo.


### Paso 2 - Exportar el modelo

**Estado antes.** El esquema por sí solo describe, pero los controladores necesitarán métodos como `find`, `create` y `findByIdAndUpdate`.

**¿Por qué necesitamos este paso?** Mongoose crea un modelo a partir del esquema y lo asocia con una colección.

**Escribe ahora:**

```javascript
module.exports = mongoose.model('Usuario', usuarioSchema);
```

**Explicación detallada.** `mongoose.model('Usuario', usuarioSchema)` compila el modelo. Mongoose utilizará el nombre para gestionar la colección correspondiente. Exportarlo permite `require('../models/Usuario')` desde el controlador.

**Elementos nuevos.** Modelo `Usuario`.

**Estado después.** La capa de datos ya puede ser utilizada desde la lógica de negocio.

**Por qué podemos avanzar.** Ahora podemos implementar la primera operación: crear.


## Archivo completo al terminar EJ03
```javascript
const mongoose = require('mongoose');

const usuarioSchema = new mongoose.Schema({
  nombre: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  edad: { type: Number, min: 0 }
});

module.exports = mongoose.model('Usuario', usuarioSchema);
```

## T03 - Tarea espejo de EJ03

**Enunciado.** Agrega al esquema un campo `activo` de tipo `Boolean` con valor por defecto `true`.

**Archivos/artefactos a modificar.** `models/Usuario.js`.

**Restricción.** No elimines ni cambies los campos existentes.

**Pista.** Mongoose permite declarar `default` dentro del objeto de configuración del campo.

**Evidencia que debes mostrar.** Fragmento final del esquema y explicación de qué ocurre si el cliente no envía `activo`.

**Cómo saber si está correcta.** El campo usa `type: Boolean` y `default: true` sin afectar las validaciones existentes.


# EJ04 - Crear usuarios con POST

## Qué vamos a construir / resolver
Implementaremos el flujo `POST /api/usuarios`: ruta -> controlador -> modelo -> MongoDB -> respuesta `201`. Aquí aparece por primera vez la persistencia observable desde la API.

### Paso 1 - Crear el controlador de alta

**Estado antes.** Tenemos el modelo, pero no existe una función que transforme una petición HTTP en una operación de Mongoose.

**¿Por qué necesitamos este paso?** El controlador concentra la lógica de la operación y evita colocarla directamente en el archivo de rutas.

**Escribe ahora:**

```javascript
const Usuario = require('../models/Usuario');

exports.crearUsuario = async (req, res) => {
  try {
    const nuevo = await Usuario.create(req.body);
    res.status(201).json(nuevo);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};
```

**Explicación detallada.** `Usuario.create(req.body)` valida y guarda. `201` indica creación exitosa. Los errores de validación se transforman aquí en una respuesta `400`, adecuada para una entrada inválida en este laboratorio.

**Elementos nuevos.** Controlador `crearUsuario`; `req.body`; estado HTTP 201/400.

**Estado después.** Existe la lógica, pero ninguna URL la invoca.

**Por qué podemos avanzar.** La conectaremos desde un router.


### Paso 2 - Crear `routes/usuarioRoutes.js`

**Estado antes.** El controlador existe, pero Express no conoce todavía la dirección pública para usarlo.

**¿Por qué necesitamos este paso?** El router asocia método HTTP y ruta con una función de controlador.

**Escribe ahora:**

```javascript
const express = require('express');
const router = express.Router();
const { crearUsuario } = require('../controllers/usuarioController');

router.post('/usuarios', crearUsuario);

module.exports = router;
```

**Explicación detallada.** `express.Router()` crea un módulo de rutas. `router.post` registra POST `/usuarios`. Más adelante `server.js` montará este router bajo `/api`, por lo que la URL final será `/api/usuarios`.

**Elementos nuevos.** `router`; destructuring de `crearUsuario`; ruta POST.

**Estado después.** La ruta modular existe, pero falta montarla en la aplicación principal.

**Por qué podemos avanzar.** Registraremos el router y el parser JSON.


### Paso 3 - Permitir JSON y montar `/api`

**Estado antes.** Express necesita interpretar el body JSON antes de que el controlador pueda leer `req.body`.

**¿Por qué necesitamos este paso?** Sin `express.json()`, `req.body` no contendrá el objeto JSON esperado.

**Escribe ahora:**

```javascript
app.use(express.json());
app.use('/api', usuarioRoutes);
```

**Explicación detallada.** `express.json()` es middleware: procesa el cuerpo JSON y lo coloca en `req.body`. `app.use('/api', usuarioRoutes)` añade el prefijo `/api` a las rutas del router.

**Elementos nuevos.** Middleware JSON; prefijo `/api`.

**Estado después.** POST `/api/usuarios` queda conectado de extremo a extremo.

**Por qué podemos avanzar.** Podemos enviar un documento y comprobar su persistencia.


## Petición de prueba
`POST http://localhost:3001/api/usuarios` con body JSON:
```json
{
  "nombre": "Carlos",
  "email": "carlos@isil.pe",
  "edad": 20
}
```
Debes obtener `201` y un objeto que incluya `_id`. Comprueba el documento en MongoDB con Compass o `mongosh`.

## T04 - Tarea espejo de EJ04

**Enunciado.** Envía un POST sin el campo `email` y analiza la respuesta. Luego corrige la petición y crea el usuario correctamente.

**Archivos/artefactos a modificar.** No necesitas modificar código; utiliza `POST /api/usuarios`.

**Restricción.** No elimines `required: true` del esquema para “hacer pasar” la prueba.

**Pista.** El error debe provenir de la validación, no de un `if` improvisado en la ruta.

**Evidencia que debes mostrar.** Respuesta fallida y respuesta exitosa posterior.

**Cómo saber si está correcta.** La primera petición devuelve 400 por falta de email y la segunda devuelve 201 con documento persistido.


# EJ05 - Leer usuarios: lista y detalle

## Qué vamos a construir / resolver
Añadiremos dos lecturas: `GET /api/usuarios` y `GET /api/usuarios/:id`. La primera devuelve la colección; la segunda utiliza el parámetro dinámico `:id`.

### Paso 1 - Listar todos

**Estado antes.** Ya podemos crear datos, pero no tenemos una operación para observarlos desde la API.

**¿Por qué necesitamos este paso?** `find()` recupera los documentos de la colección y permite comprobar que la persistencia funciona.

**Escribe ahora:**

```javascript
exports.listarUsuarios = async (req, res) => {
  try {
    const usuarios = await Usuario.find();
    res.json(usuarios);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

**Explicación detallada.** `Usuario.find()` sin filtro devuelve una lista. Una falla interna de consulta se representa con 500 porque el problema no proviene necesariamente de datos enviados por el cliente.

**Elementos nuevos.** Controlador `listarUsuarios`; `Usuario.find()`; estado 500.

**Estado después.** La lógica de listado existe.

**Por qué podemos avanzar.** Registraremos GET `/usuarios`.


### Paso 2 - Obtener por id

**Estado antes.** La lista muestra todos los usuarios, pero una API REST también necesita identificar un recurso concreto.

**¿Por qué necesitamos este paso?** El segmento `:id` captura el identificador desde la URL.

**Escribe ahora:**

```javascript
exports.obtenerUsuario = async (req, res) => {
  try {
    const usuario = await Usuario.findById(req.params.id);
    if (!usuario) {
      return res.status(404).json({ error: 'Usuario no encontrado' });
    }
    res.json(usuario);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};
```

**Explicación detallada.** `req.params.id` contiene el valor de `:id`. `findById` puede devolver `null`; por eso distinguimos “id válido pero no encontrado” con 404. Un id con formato inválido puede producir un error de casteo y aquí se responde 400.

**Elementos nuevos.** Parámetro `id`; `findById`; 404.

**Estado después.** Tenemos dos controladores de lectura.

**Por qué podemos avanzar.** Solo falta asociarlos al router.


Añade en `routes/usuarioRoutes.js`:
```javascript
router.get('/usuarios', listarUsuarios);
router.get('/usuarios/:id', obtenerUsuario);
```
Asegúrate de importar ambas funciones desde el controlador. Prueba primero la lista, copia un `_id` real y úsalo en la ruta de detalle.

## T05 - Tarea espejo de EJ05

**Enunciado.** Realiza tres pruebas: listar todos, consultar un `_id` existente y consultar un id válido que no exista.

**Archivos/artefactos a modificar.** Endpoints GET de usuarios.

**Restricción.** No cambies el controlador para fabricar un 404; usa una petición real.

**Pista.** Primero usa `GET /api/usuarios` para obtener un id verdadero.

**Evidencia que debes mostrar.** Códigos de estado y bodies de las tres peticiones.

**Cómo saber si está correcta.** Lista devuelve 200, detalle existente devuelve 200 y recurso inexistente devuelve 404.


# EJ06 - Actualizar con PUT y PATCH

## Qué vamos a construir / resolver
Implementaremos actualización por id usando `findByIdAndUpdate`. Utilizaremos `{ new: true, runValidators: true }` para devolver el documento actualizado y mantener activas las validaciones del esquema.

### Paso 1 - Crear el controlador de actualización

**Estado antes.** Tenemos lectura y creación, pero no existe una transición controlada del estado de un usuario existente.

**¿Por qué necesitamos este paso?** La actualización debe localizar por id, aplicar los campos enviados y devolver el estado posterior.

**Escribe ahora:**

```javascript
exports.actualizarUsuario = async (req, res) => {
  try {
    const actualizado = await Usuario.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true, runValidators: true }
    );

    if (!actualizado) {
      return res.status(404).json({ error: 'Usuario no encontrado' });
    }

    res.json(actualizado);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};
```

**Explicación detallada.** El id proviene de la ruta y los cambios del body. `new: true` pide el documento posterior a la actualización. `runValidators: true` hace que las reglas del esquema también se comprueben en esta operación.

**Elementos nuevos.** `findByIdAndUpdate`; opciones `new` y `runValidators`.

**Estado después.** La lógica actualiza y valida.

**Por qué podemos avanzar.** Añadiremos las rutas PUT/PATCH.


En el router añade:
```javascript
router.put('/usuarios/:id', actualizarUsuario);
router.patch('/usuarios/:id', actualizarUsuario);
```
Prueba con un usuario real. Antes de ejecutar, anota su `edad`. Envía `{ "edad": 21 }`, vuelve a consultar y compara estado antes/después.

## T06 - Tarea espejo de EJ06

**Enunciado.** Usa `PATCH /api/usuarios/:id` para cambiar solo `nombre` de un usuario y valida que `email` y `edad` permanezcan iguales.

**Archivos/artefactos a modificar.** Ruta PATCH y controlador existente.

**Restricción.** No envíes el documento completo; envía solo `nombre`.

**Pista.** Compara un GET antes y después.

**Evidencia que debes mostrar.** Body del PATCH y ambos estados del usuario.

**Cómo saber si está correcta.** Solo cambia `nombre`; los otros campos conservan sus valores.


# EJ07 - Eliminar un usuario

## Qué vamos a construir / resolver
Añadiremos `DELETE /api/usuarios/:id` con `findByIdAndDelete`. La respuesta confirmará la eliminación y una segunda consulta permitirá demostrar el cambio de estado.

### Paso 1 - Crear controlador de eliminación

**Estado antes.** Un usuario puede crearse y modificarse, pero aún no existe la operación Delete del CRUD.

**¿Por qué necesitamos este paso?** Necesitamos borrar un documento concreto por `_id` y distinguir el caso donde no existe.

**Escribe ahora:**

```javascript
exports.eliminarUsuario = async (req, res) => {
  try {
    const eliminado = await Usuario.findByIdAndDelete(req.params.id);
    if (!eliminado) {
      return res.status(404).json({ error: 'Usuario no encontrado' });
    }
    res.json({ mensaje: 'Usuario eliminado' });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};
```

**Explicación detallada.** `findByIdAndDelete` devuelve el documento eliminado o `null`. Si existe, respondemos con una confirmación. Si no existe, 404. Un formato de id inválido se trata como entrada incorrecta en este laboratorio.

**Elementos nuevos.** `findByIdAndDelete`; estado posterior sin documento.

**Estado después.** La operación Delete está implementada.

**Por qué podemos avanzar.** Registraremos la ruta y validaremos con GET.


Añade en el router:
```javascript
router.delete('/usuarios/:id', eliminarUsuario);
```
Crea un usuario de prueba, guarda su `_id`, elimínalo y luego ejecuta GET por el mismo id. El GET posterior debe responder 404.

## T07 - Tarea espejo de EJ07

**Enunciado.** Elimina el mismo `_id` dos veces y explica por qué las respuestas son diferentes.

**Archivos/artefactos a modificar.** Endpoint DELETE.

**Restricción.** No insertes código especial para la segunda petición.

**Pista.** La primera operación cambia el estado de la colección; la segunda trabaja sobre ese nuevo estado.

**Evidencia que debes mostrar.** Dos respuestas DELETE consecutivas.

**Cómo saber si está correcta.** La primera devuelve confirmación; la segunda devuelve 404 porque el documento ya no existe.


# EJ08 - Integrar estructura modular y buenas prácticas

## Qué vamos a construir / resolver
Consolidaremos el proyecto final y añadiremos los middlewares de seguridad/configuración mencionados en la sesión: `helmet`, `cors` y límite de JSON. También revisaremos que el servidor solo se levante después de conectar MongoDB.

### Paso 1 - Instalar y activar middlewares globales

**Estado antes.** La API funciona, pero aún usa el parser JSON sin límite y no incorpora los middlewares de seguridad/configuración vistos en la sesión.

**¿Por qué necesitamos este paso?** `cors` y `helmet` son dependencias nuevas. Primero debemos instalarlas y recién después importarlas y activarlas. Los middlewares globales se ejecutan antes de las rutas y permiten establecer comportamiento común.

**Instala ahora:**

```bash
npm install cors helmet
```

Comprueba que `package.json` ya contiene ambas dependencias.

**En la parte superior de `server.js`, agrega:**

```javascript
const cors = require('cors');
const helmet = require('helmet');
```

**Después de `const app = express();`, configura:**

```javascript
app.use(helmet());
app.use(cors());
app.use(express.json({ limit: '10kb' }));
```

**Explicación detallada.** `helmet()` añade cabeceras HTTP de protección. `cors()` añade cabeceras CORS para permitir el consumo desde navegadores según su configuración. `express.json({ limit: '10kb' })` evita aceptar cuerpos JSON arbitrariamente grandes en este laboratorio.

**Elementos nuevos.** Helmet; CORS; límite de body.

**Estado después.** Las peticiones atraviesan estos middlewares antes de llegar a `/api`.

**Por qué podemos avanzar.** Revisaremos la estructura completa y el orden de arranque.


## Estructura final
```text
api-usuarios-mongodb/
├── config/
│   └── database.js
├── models/
│   └── Usuario.js
├── controllers/
│   └── usuarioController.js
├── routes/
│   └── usuarioRoutes.js
├── .env
├── .gitignore
├── package.json
└── server.js
```

## `server.js` completo final
```javascript
require('dotenv').config();
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const conectarDB = require('./config/database');
const usuarioRoutes = require('./routes/usuarioRoutes');

const app = express();

app.use(helmet());
app.use(cors());
app.use(express.json({ limit: '10kb' }));

app.get('/health', (req, res) => {
  res.json({ estado: 'ok' });
});

app.use('/api', usuarioRoutes);

async function iniciarServidor() {
  try {
    await conectarDB();
    const PORT = process.env.PORT || 3001;
    app.listen(PORT, () => {
      console.log(`Servidor en http://localhost:${PORT}`);
    });
  } catch (error) {
    console.error('No se pudo iniciar la aplicación:', error.message);
    process.exit(1);
  }
}

iniciarServidor();
```

## Auditoría del flujo completo
| Elemento | Qué hace | Por qué existe | Evidencia |
|---|---|---|---|
| `.env` | Guarda URI y puerto | Separa configuración | Cambiar base no exige editar JS |
| `database.js` | Abre conexión | Aísla infraestructura de datos | Mensaje de conexión |
| `Usuario.js` | Define esquema/modelo | Valida y ofrece métodos CRUD | Error al faltar un requerido |
| controlador | Ejecuta operaciones | Separa lógica de rutas | Respuestas 201/200/400/404 |
| router | Mapea métodos y URLs | Mantiene rutas organizadas | Endpoints `/api/usuarios` |
| `server.js` | Integra y arranca | Coordina la aplicación | Servidor solo inicia tras DB |

## Predicción final
Antes de probar, responde: ¿qué parte del flujo se ejecuta primero al iniciar el proceso?, ¿qué archivo conoce la forma de un usuario?, ¿qué archivo decide qué función atiende POST?, ¿qué respuesta esperas si falta `email`?

## T08 - Tarea espejo de EJ08

**Enunciado.** Explica y demuestra las tres buenas prácticas globales: `helmet`, `cors` y límite `10kb`. Como modificación, cambia temporalmente el límite a `5kb` y vuelve a dejarlo en `10kb` al terminar.

**Archivos/artefactos a modificar.** `server.js`.

**Restricción.** No elimines los middlewares ni desactives validaciones para simplificar la prueba.

**Pista.** Inspecciona cabeceras de una respuesta y razona dónde se ejecutan los middlewares respecto de las rutas.

**Evidencia que debes mostrar.** Breve registro de lo observado y el `server.js` restaurado con `10kb`.

**Cómo saber si está correcta.** Puedes explicar la función de cada middleware y el proyecto final sigue respondiendo correctamente.



## Recursos oficiales de apoyo para la preparación

- Descarga de MongoDB Community Server: `https://www.mongodb.com/try/download/community`
- Instalación oficial MongoDB 8.0 en Windows: `https://www.mongodb.com/docs/v8.0/tutorial/install-mongodb-on-windows/`
- Conexiones Mongoose: `https://mongoosejs.com/docs/connections.html`

Estos enlaces complementan la guía; no sustituyen los pasos anteriores.

# 5. Checklist de validación final
- [ ] Sé comprobar si MongoDB está instalado.
- [ ] MongoDB está activo como servicio o proceso manual.
- [ ] Puedo explicar por qué `mongosh` y Compass son opcionales.
- [ ] MongoDB usa la conexión local `127.0.0.1:27017`.
- [ ] `node server.js` muestra conexión y servidor.
- [ ] `GET /health` responde 200.
- [ ] POST crea y devuelve 201.
- [ ] GET lista y consulta detalle.
- [ ] PUT/PATCH modifica y respeta validaciones.
- [ ] DELETE elimina y luego el recurso responde 404.
- [ ] `.env` no se incluye en entregas públicas.
- [ ] Puedes explicar el recorrido request -> ruta -> controlador -> modelo -> MongoDB -> response.

# 6. Cierre de sesión


En esta sesión construiste una API REST que ya no depende de datos temporales: Express recibe las peticiones, las rutas seleccionan la operación, los controladores ejecutan la lógica y Mongoose utiliza un modelo para trabajar con MongoDB. El resultado importante no es solo que las peticiones respondan, sino que puedes comprobar el cambio de estado directamente en la base de datos.

También aplicaste decisiones que hacen el proyecto más mantenible: moviste la conexión a un módulo específico, mantuviste la URI fuera del código, separaste modelo, controlador y rutas, activaste validaciones y distinguiste respuestas de creación, error de entrada y recurso inexistente. Los errores controlados te permitieron comprobar que la aplicación no solo funciona en el caso feliz.

Repasa especialmente la diferencia entre **Schema** y **Model**, el papel de `req.body` y `req.params`, y por qué el servidor espera la conexión antes de escuchar el puerto. Estos conceptos serán la base para continuar integrando y robusteciendo el backend.

Puedes reforzar lo trabajado con los recursos de **Lideratec Academy**: https://lideratecacademy.com/blog/ y https://www.youtube.com/@LideratecAcademy. Si deseas validar tu resultado o trabajar desde una versión ya materializada, puedes utilizar opcionalmente el laboratorio completo de FASE 15.
