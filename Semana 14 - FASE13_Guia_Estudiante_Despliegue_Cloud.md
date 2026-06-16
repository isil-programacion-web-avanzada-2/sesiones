# Guia del estudiante: Despliegue cloud de aplicaciones web

**Elaborado por el docente**  
**Proyecto academico:** Lideratec Academy  
**Blog:** https://lideratecacademy.com/  
**Canal YouTube:** https://www.youtube.com/@LideratecAcademy

---

## 1. Presentacion de la sesion

En esta guia aprenderas a llevar una aplicacion web desde el entorno local hacia internet usando un flujo cloud moderno. La sesion trabaja cuatro componentes principales:

1. **Vercel** para publicar el frontend.
2. **Render** para ejecutar el backend.
3. **MongoDB Atlas** para almacenar datos en la nube.
4. **GitHub Actions** como introduccion a CI/CD.

La idea central es comprender que una aplicacion web moderna no se publica como una sola pieza. Normalmente se separa en frontend, backend y base de datos. Cada parte se despliega en una plataforma adecuada y se conecta mediante variables de entorno.

---

## 2. Objetivo de aprendizaje

Al finalizar la sesion, podras **aplicar un flujo basico de despliegue cloud** para publicar una aplicacion web separando frontend, backend y base de datos, configurando variables de entorno y validando la conexion entre servicios.

---

## 3. Resultado observable

Al terminar la practica, deberias poder explicar y ejecutar este flujo:

```text
GitHub -> Vercel -> Render -> MongoDB Atlas
```

Y tambien deberias poder identificar estas variables:

| Variable | Donde se configura | Para que sirve |
|---|---|---|
| `VITE_API_URL` | Vercel | Indica al frontend la URL del backend. |
| `PORT` | Render | Puerto usado por el servidor. Render puede asignarlo automaticamente. |
| `NODE_ENV` | Render | Indica que la aplicacion corre en produccion. |
| `MONGODB_URI` | Render | Cadena de conexion hacia MongoDB Atlas. |
| `FRONTEND_URL` | Render | URL del frontend permitida para CORS. |

---

## 4. Herramientas necesarias

| Herramienta | Uso en la sesion | Enlace oficial | Validacion rapida |
|---|---|---|---|
| GitHub | Repositorio del codigo | https://github.com/ | Iniciar sesion y ver el repositorio. |
| Vercel | Despliegue del frontend | https://vercel.com/ | Acceder al dashboard. |
| Render | Despliegue del backend | https://render.com/ | Acceder al dashboard. |
| MongoDB Atlas | Base de datos cloud | https://www.mongodb.com/atlas | Acceder al proyecto de Atlas. |
| Node.js y npm | Ejecutar y construir proyectos JavaScript | https://nodejs.org/ | `node -v` y `npm -v`. |
| Visual Studio Code | Edicion del proyecto | https://code.visualstudio.com/ | Abrir la carpeta del proyecto. |

### Requisitos minimos

- Cuenta activa en GitHub.
- Proyecto frontend versionado en GitHub.
- Proyecto backend versionado en GitHub.
- Node.js instalado.
- Acceso a internet.
- Navegador actualizado.

### Validar Node.js y npm

Abre una terminal y ejecuta:

```bash
node -v
npm -v
```

**Resultado esperado:** se muestran dos versiones, por ejemplo `v22.x.x` o `v24.x.x` para Node.js y una version de npm.

**Error comun:** el comando no se reconoce.  
**Correccion:** reinstala Node.js desde la pagina oficial y reinicia la terminal.

---

## 5. Nota tecnica operativa

Las plataformas cloud pueden cambiar su interfaz, nombres de botones o versiones recomendadas. Lo importante es conservar el flujo tecnico:

1. Conectar repositorio.
2. Configurar comandos.
3. Configurar variables de entorno.
4. Desplegar.
5. Validar URL, logs y conexion.

Tambien debes considerar estas recomendaciones operativas:

- Si cambias variables de entorno en Vercel, debes hacer un nuevo deployment para que el frontend use el nuevo valor.
- En Render, un servicio gratuito puede suspenderse si no recibe trafico durante un tiempo. La primera peticion posterior puede tardar mas.
- En MongoDB Atlas, la interfaz puede mostrar opciones actuales como Free o Flex. El flujo principal sigue siendo crear base de datos, usuario, acceso de red y connection string.
- En GitHub Actions, usa versiones actuales de las acciones cuando prepares una practica real.

---

# Bloques de aprendizaje

## Bloque 1 - Entender el despliegue cloud

**Objetivo del bloque:**  
Diferenciar entre ejecutar una aplicacion en local y publicarla en internet.

**Concepto trabajado:**  
Deployment o despliegue.

**Explicacion breve:**  
Deployment significa llevar una aplicacion desde tu computadora local hacia un entorno accesible por internet. Cuando ejecutas una app con `npm run dev`, solo esta disponible para desarrollo. Cuando la despliegas, otros usuarios pueden abrir una URL publica y usarla.

**Ejemplo guiado:**

```text
Local: http://localhost:5173
Produccion: https://mi-frontend.vercel.app
```

**Paso a paso:**

1. Ejecutas el proyecto localmente para desarrollar.
2. Subes el codigo a GitHub para versionarlo.
3. Conectas el repositorio a una plataforma cloud.
4. La plataforma construye y publica una URL.
5. Verificas que la aplicacion sea accesible desde el navegador.

**Actividad para ti:**  
Escribe en tus palabras la diferencia entre `localhost` y una URL publica.

**Espacio para responder:**

- ¿Que significa deployment?
- ¿Por que `npm run dev` no es lo mismo que desplegar?
- ¿Que evidencia demuestra que una aplicacion esta publicada?

**Resultado esperado:**  
Puedes explicar que deployment es publicar una aplicacion para usuarios reales.

**Error comun a evitar:**  
Creer que si la aplicacion funciona en local, ya esta lista para usuarios externos.

**Mini reto:**  
Dibuja el flujo: computadora local -> GitHub -> plataforma cloud -> URL publica.

---

## Bloque 2 - Reconocer la arquitectura frontend, backend y base de datos

**Objetivo del bloque:**  
Identificar el rol de cada componente en una aplicacion web moderna.

**Concepto trabajado:**  
Separacion frontend, backend y base de datos.

**Explicacion breve:**  
El frontend es la interfaz que ve el usuario. El backend procesa la logica del servidor. La base de datos guarda informacion. En esta sesion, el frontend se publica en Vercel, el backend se ejecuta en Render y la base de datos se configura en MongoDB Atlas.

**Ejemplo guiado:**

```text
Usuario -> Frontend en Vercel -> Backend en Render -> MongoDB Atlas
```

**Paso a paso:**

1. El usuario abre la URL del frontend.
2. El frontend llama a la API usando `VITE_API_URL`.
3. El backend procesa la solicitud.
4. El backend se conecta a MongoDB Atlas usando `MONGODB_URI`.
5. La respuesta vuelve al frontend.

**Actividad para ti:**  
Completa la tabla:

| Componente | Plataforma | Funcion |
|---|---|---|
| Frontend | | |
| Backend | | |
| Base de datos | | |

**Espacio para responder:**

- ¿Que ve el usuario?
- ¿Donde se procesa la logica del servidor?
- ¿Donde se almacenan los datos?

**Resultado esperado:**  
Puedes explicar el flujo completo de una aplicacion desplegada.

**Error comun a evitar:**  
Pensar que Vercel y Render hacen exactamente lo mismo.

**Mini reto:**  
Explica por que el frontend no debe conectarse directamente a MongoDB Atlas.

---

## Bloque 3 - Publicar el frontend con Vercel

**Objetivo del bloque:**  
Comprender el procedimiento para publicar el frontend desde GitHub.

**Concepto trabajado:**  
Despliegue frontend con Vercel.

**Explicacion breve:**  
Vercel permite conectar un repositorio, detectar proyectos frontend como React/Vite, ejecutar el build y publicar el resultado en una URL publica. En proyectos Vite, la carpeta de salida comunmente es `dist`.

**Ejemplo guiado:**

```text
Repositorio GitHub -> Vercel -> Build -> URL vercel.app
```

**Paso a paso:**

1. Ingresa a https://vercel.com/.
2. Inicia sesion con GitHub.
3. Selecciona **Add New** y luego **Project**.
4. Elige el repositorio del frontend.
5. Verifica que el framework detectado sea Vite si tu proyecto usa Vite.
6. Revisa la configuracion:
   - Install Command: `npm install`
   - Build Command: `npm run build`
   - Output Directory: `dist`
7. No publiques todavia si falta configurar `VITE_API_URL`.

**Actividad para ti:**  
Escribe que hace cada comando:

```bash
npm install
npm run build
```

**Espacio para responder:**

- ¿Que significa Build Command?
- ¿Que significa Output Directory?
- ¿Por que Vercel necesita acceso al repositorio?

**Resultado esperado:**  
Puedes preparar un proyecto frontend para despliegue en Vercel.

**Error comun a evitar:**  
Hacer deploy sin revisar la configuracion del build.

**Mini reto:**  
Busca en tu `package.json` el script que ejecuta el build.

---

## Bloque 4 - Configurar `VITE_API_URL` en Vercel

**Objetivo del bloque:**  
Configurar la URL del backend para que el frontend pueda consumir la API.

**Concepto trabajado:**  
Variables de entorno en frontend.

**Explicacion breve:**  
En Vite, las variables disponibles para el frontend deben comenzar con `VITE_`. Por eso usamos `VITE_API_URL`. Esta variable guarda la URL del backend publicado en Render.

**Ejemplo guiado:**

```text
Key: VITE_API_URL
Value: https://mi-app-backend.onrender.com
```

**Uso dentro del codigo frontend:**

```javascript
const API_URL = import.meta.env.VITE_API_URL;

async function obtenerUsuarios() {
  const respuesta = await fetch(`${API_URL}/api/usuarios`);
  const datos = await respuesta.json();
  return datos;
}
```

**Explicacion linea por linea:**

| Linea | Que hace |
|---|---|
| `const API_URL = import.meta.env.VITE_API_URL;` | Lee la variable configurada en Vercel. |
| `fetch(...)` | Hace una solicitud HTTP hacia el backend. |
| `respuesta.json()` | Convierte la respuesta en datos JavaScript. |
| `return datos;` | Devuelve la informacion para usarla en la interfaz. |

**Paso a paso:**

1. Entra al proyecto en Vercel.
2. Abre **Settings**.
3. Entra a **Environment Variables**.
4. Agrega `VITE_API_URL`.
5. Coloca como valor la URL publica del backend.
6. Guarda el cambio.
7. Ejecuta un nuevo deployment o redeploy.

**Actividad para ti:**  
Indica si las siguientes variables son correctas para Vite:

| Variable | Correcta para frontend Vite? | Motivo |
|---|---|---|
| `API_URL` | | |
| `VITE_API_URL` | | |
| `MONGODB_URI` | | |

**Espacio para responder:**

- ¿Por que la variable debe empezar con `VITE_`?
- ¿Por que no se debe poner `MONGODB_URI` en el frontend?
- ¿Que debes hacer despues de cambiar una variable en Vercel?

**Resultado esperado:**  
El frontend usa la URL correcta para llamar al backend.

**Error comun a evitar:**  
Cambiar `VITE_API_URL` y olvidar hacer redeploy.

**Mini reto:**  
Escribe una URL ficticia de backend y explica en que lugar la configurarias.

---

## Bloque 5 - Desplegar el backend con Render

**Objetivo del bloque:**  
Crear un Web Service para ejecutar una API Node.js/Express.

**Concepto trabajado:**  
Despliegue backend en Render.

**Explicacion breve:**  
Render ejecuta servicios backend. Un backend no es solo una carpeta de archivos; necesita arrancar un proceso de servidor, escuchar solicitudes y responder. Para eso se crea un Web Service.

**Ejemplo guiado:**

```text
Repositorio backend -> Render Web Service -> URL onrender.com
```

**Paso a paso:**

1. Ingresa a https://render.com/.
2. Inicia sesion con GitHub.
3. Selecciona **New** y luego **Web Service**.
4. Conecta el repositorio del backend.
5. Configura:
   - Environment: `Node`
   - Branch: `main`
   - Build Command: `npm install`
   - Start Command: `npm start`
6. Selecciona el plan disponible para practica.
7. No finalices sin revisar variables de entorno.

**Ejemplo de `package.json`:**

```json
{
  "scripts": {
    "start": "node src/server.js"
  }
}
```

**Explicacion:**

| Elemento | Funcion |
|---|---|
| `scripts` | Agrupa comandos del proyecto. |
| `start` | Comando que Render ejecutara con `npm start`. |
| `node src/server.js` | Inicia el servidor desde el archivo indicado. |

**Actividad para ti:**  
Revisa tu `package.json` y responde: ¿que archivo inicia tu servidor?

**Espacio para responder:**

- ¿Que es un Web Service?
- ¿Por que Render necesita un Start Command?
- ¿Que pasa si `npm start` no existe?

**Resultado esperado:**  
El backend queda configurado para iniciar en Render.

**Error comun a evitar:**  
Configurar `npm start` sin tener el script `start` en `package.json`.

**Mini reto:**  
Propone un Start Command alternativo si tu archivo principal se llama `server.js` en la raiz.

---

## Bloque 6 - Configurar variables del backend y CORS

**Objetivo del bloque:**  
Configurar variables necesarias para que el backend funcione en produccion.

**Concepto trabajado:**  
Variables de entorno backend y CORS.

**Explicacion breve:**  
El backend necesita variables privadas y de configuracion. Algunas indican modo de ejecucion, otras conectan la base de datos y otras permiten solicitudes desde el frontend.

**Variables recomendadas:**

```text
PORT=5000
NODE_ENV=production
MONGODB_URI=mongodb+srv://admin_app:PasswordSeguro@cluster0.abc123.mongodb.net/mi_base_datos?retryWrites=true&w=majority
FRONTEND_URL=https://mi-frontend.vercel.app
```

**Ejemplo de servidor Express con CORS:**

```javascript
const express = require('express');
const cors = require('cors');
require('dotenv').config();

const app = express();

app.use(cors({
  origin: process.env.FRONTEND_URL
}));

app.use(express.json());

app.get('/', (req, res) => {
  res.json({ mensaje: 'API funcionando' });
});

const PORT = process.env.PORT || 5000;

app.listen(PORT, () => {
  console.log(`Servidor ejecutandose en puerto ${PORT}`);
});
```

**Explicacion linea por linea:**

| Linea o bloque | Que hace |
|---|---|
| `require('express')` | Importa Express para crear el servidor. |
| `require('cors')` | Importa CORS para controlar origenes permitidos. |
| `require('dotenv').config()` | Permite leer variables desde `.env` en desarrollo local. |
| `const app = express()` | Crea la aplicacion Express. |
| `origin: process.env.FRONTEND_URL` | Permite solicitudes desde la URL del frontend. |
| `app.use(express.json())` | Permite recibir JSON en las solicitudes. |
| `app.get('/')` | Define una ruta de prueba. |
| `process.env.PORT || 5000` | Usa el puerto del entorno o 5000 por defecto. |
| `app.listen(...)` | Inicia el servidor. |

**Paso a paso:**

1. En Render, entra al Web Service del backend.
2. Abre **Environment** o **Environment Variables**.
3. Agrega `NODE_ENV` con valor `production`.
4. Agrega `FRONTEND_URL` con la URL de Vercel.
5. Agrega `MONGODB_URI` cuando tengas la cadena de Atlas.
6. Guarda los cambios.
7. Ejecuta un nuevo despliegue si corresponde.
8. Revisa los logs.

**Actividad para ti:**  
Explica que error ocurriria si `FRONTEND_URL` apunta a una URL incorrecta.

**Espacio para responder:**

- ¿Para que sirve CORS?
- ¿Por que `MONGODB_URI` no debe subirse a GitHub?
- ¿Que variable permite controlar el origen del frontend?

**Resultado esperado:**  
El backend acepta solicitudes desde el frontend correcto y conserva secretos fuera del codigo.

**Error comun a evitar:**  
Usar un nombre de variable distinto entre Render y el codigo. Por ejemplo, configurar `MONGO_URI` pero leer `process.env.MONGODB_URI`.

**Mini reto:**  
Indica que variables son publicas y cuales deben tratarse como privadas.

---

## Bloque 7 - Configurar MongoDB Atlas y conectar con Mongoose

**Objetivo del bloque:**  
Crear la base de datos cloud y conectar el backend usando `MONGODB_URI`.

**Concepto trabajado:**  
MongoDB Atlas, connection string y Mongoose.

**Explicacion breve:**  
MongoDB Atlas permite usar MongoDB en la nube. El backend se conecta usando una cadena llamada connection string. Esa cadena se guarda en `MONGODB_URI` dentro del entorno del backend.

**Paso a paso en Atlas:**

1. Ingresa a https://www.mongodb.com/atlas.
2. Crea o abre tu proyecto.
3. Crea un cluster gratuito o el tipo inicial disponible para practica.
4. Crea un usuario de base de datos.
5. Genera una contraseña segura y guardala.
6. Otorga permisos de lectura y escritura para la base de datos de practica.
7. Configura acceso de red.
8. Para desarrollo, puedes permitir temporalmente `0.0.0.0/0` si la practica lo requiere.
9. Para produccion, limita el acceso a direcciones necesarias.
10. Abre **Connect** y selecciona conexion para aplicacion.
11. Copia el connection string.
12. Reemplaza la contraseña y agrega el nombre de la base de datos.

**Ejemplo de connection string:**

```text
mongodb+srv://admin_app:PasswordSeguro@cluster0.abc123.mongodb.net/mi_base_datos?retryWrites=true&w=majority
```

**Codigo de conexion:**

```javascript
const mongoose = require('mongoose');

const conectarDB = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URI);
    console.log('MongoDB conectado exitosamente');
  } catch (error) {
    console.error('Error al conectar MongoDB:', error.message);
    process.exit(1);
  }
};

module.exports = conectarDB;
```

**Explicacion linea por linea:**

| Linea | Que hace |
|---|---|
| `const mongoose = require('mongoose')` | Importa Mongoose para conectar Node.js con MongoDB. |
| `const conectarDB = async () =>` | Declara una funcion asincrona de conexion. |
| `try` | Intenta ejecutar la conexion. |
| `mongoose.connect(process.env.MONGODB_URI)` | Usa la variable de entorno con la URI de Atlas. |
| `console.log(...)` | Muestra confirmacion si la conexion funciona. |
| `catch (error)` | Captura errores de conexion. |
| `console.error(...)` | Muestra el problema en los logs. |
| `process.exit(1)` | Detiene el proceso si la base de datos no conecta. |
| `module.exports = conectarDB` | Exporta la funcion para usarla en el servidor. |

**Uso en `server.js`:**

```javascript
const conectarDB = require('./src/config/database');

conectarDB();
```

**Paso a paso:**

1. Crea el archivo `src/config/database.js`.
2. Copia el codigo de conexion.
3. Importa `conectarDB` en `server.js`.
4. Ejecuta `conectarDB()` antes de iniciar o antes de montar rutas segun tu estructura.
5. Configura `MONGODB_URI` en Render.
6. Despliega nuevamente.
7. Revisa logs.

**Actividad para ti:**  
Indica tres causas posibles de error al conectar con MongoDB Atlas.

**Espacio para responder:**

- ¿Que es el connection string?
- ¿Por que se guarda en `MONGODB_URI`?
- ¿Que mensaje esperas ver en los logs si la conexion funciona?

**Resultado esperado:**  
El backend muestra `MongoDB conectado exitosamente` en los logs.

**Error comun a evitar:**  
Dejar `<password>` sin reemplazar en el connection string.

**Mini reto:**  
Explica por que el usuario de base de datos no es lo mismo que tu cuenta personal de MongoDB Atlas.

---

## Bloque 8 - Introduccion a CI/CD con GitHub Actions

**Objetivo del bloque:**  
Comprender como automatizar una validacion basica del proyecto.

**Concepto trabajado:**  
CI/CD y workflows YAML.

**Explicacion breve:**  
CI significa integracion continua. Sirve para verificar cambios con frecuencia. CD significa despliegue continuo. Sirve para publicar cambios verificados. GitHub Actions permite automatizar tareas cuando ocurre un evento en el repositorio.

**Ejemplo guiado:**

```yaml
name: Ejecutar Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Clonar repositorio
        uses: actions/checkout@v6

      - name: Configurar Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '24.x'

      - name: Instalar dependencias
        run: npm install

      - name: Ejecutar tests
        run: npm test
```

**Explicacion por bloque:**

| Seccion | Funcion |
|---|---|
| `name` | Nombre visible del workflow. |
| `on` | Eventos que activan el workflow. |
| `push` | Ejecuta el flujo cuando se sube codigo. |
| `pull_request` | Ejecuta el flujo cuando se abre o actualiza un pull request. |
| `jobs` | Agrupa las tareas. |
| `runs-on` | Define el sistema donde corre el job. |
| `steps` | Pasos individuales del job. |
| `npm install` | Instala dependencias. |
| `npm test` | Ejecuta las pruebas del proyecto. |

**Paso a paso:**

1. En tu repositorio, crea la carpeta `.github/workflows`.
2. Dentro, crea el archivo `tests.yml`.
3. Copia el workflow.
4. Haz commit y push.
5. Abre la pestana **Actions** en GitHub.
6. Verifica si el workflow se ejecuto correctamente.

**Actividad para ti:**  
Identifica que parte del YAML responde a cada pregunta:

| Pregunta | Respuesta |
|---|---|
| ¿Cuando se ejecuta? | |
| ¿Donde se ejecuta? | |
| ¿Que comandos ejecuta? | |

**Espacio para responder:**

- ¿Que significa CI?
- ¿Que significa CD?
- ¿Por que conviene ejecutar tests antes de desplegar?

**Resultado esperado:**  
GitHub Actions ejecuta el workflow al hacer push o pull request.

**Error comun a evitar:**  
Crear el archivo YAML fuera de `.github/workflows`.

**Mini reto:**  
Explica que pasaria si el proyecto no tiene configurado el script `test`.

---

# Actividades guiadas progresivas

## Actividad 1 - Reconocimiento

**Titulo:** Identificar componentes del despliegue cloud.  
**Objetivo:** Reconocer que plataforma corresponde a cada pieza.

**Instrucciones:** Completa la tabla.

| Pieza | Plataforma | Variable relacionada |
|---|---|---|
| Frontend | | |
| Backend | | |
| Base de datos | | |
| Automatizacion | | |

**Que observar:** La aplicacion no es una sola pieza.  
**Que responder:** Explica el rol de cada plataforma.  
**Resultado esperado:** Mapa correcto de componentes.  
**Criterio de validacion rapida:** Identificas Vercel, Render, MongoDB Atlas y GitHub Actions.

---

## Actividad 2 - Replica guiada

**Titulo:** Preparar despliegue frontend en Vercel.  
**Objetivo:** Simular la configuracion necesaria antes del deploy.

**Instrucciones:** Escribe los valores que usarias.

| Configuracion | Valor |
|---|---|
| Framework | |
| Build Command | |
| Output Directory | |
| Variable frontend | |
| Valor de la variable | |

**Codigo base o caso:** Proyecto React/Vite en GitHub.  
**Que modificar:** Agregar `VITE_API_URL`.  
**Que observar:** La URL del backend debe ser publica.  
**Que responder:** ¿Que pasa si usas `localhost` en produccion?  
**Resultado esperado:** Configuracion lista para desplegar.  
**Criterio de validacion rapida:** `VITE_API_URL` existe y apunta al backend.

---

## Actividad 3 - Modificacion controlada

**Titulo:** Ajustar backend para CORS.  
**Objetivo:** Usar `FRONTEND_URL` para permitir solicitudes desde Vercel.

**Codigo base:**

```javascript
app.use(cors());
```

**Que modificar:** Cambiarlo por:

```javascript
app.use(cors({
  origin: process.env.FRONTEND_URL
}));
```

**Que observar:** El backend queda configurado para aceptar el origen del frontend.  
**Que responder:** ¿Por que conviene no dejar CORS completamente abierto en produccion?  
**Resultado esperado:** CORS depende de `FRONTEND_URL`.  
**Criterio de validacion rapida:** El codigo lee `process.env.FRONTEND_URL`.

---

## Actividad 4 - Aplicacion

**Titulo:** Preparar `MONGODB_URI`.  
**Objetivo:** Transformar un connection string de Atlas en una variable de Render.

**Codigo base o caso:**

```text
mongodb+srv://admin_app:<password>@cluster0.abc123.mongodb.net/?retryWrites=true&w=majority
```

**Que modificar:**

1. Reemplazar `<password>`.
2. Agregar el nombre de base de datos.
3. Guardar como `MONGODB_URI` en Render.

**Resultado esperado:**

```text
mongodb+srv://admin_app:PasswordSeguro@cluster0.abc123.mongodb.net/mi_base_datos?retryWrites=true&w=majority
```

**Que observar:** No se pega la URI en el frontend.  
**Que responder:** ¿Que error ocurre si no reemplazas `<password>`?  
**Criterio de validacion rapida:** La URI tiene usuario, password, host y nombre de base de datos.

---

## Actividad 5 - Integracion

**Titulo:** Validar flujo completo.  
**Objetivo:** Confirmar que cada pieza se conecta correctamente.

**Instrucciones:** Completa el checklist.

| Validacion | Cumple? | Evidencia |
|---|---|---|
| Frontend abre en Vercel | | |
| Backend responde en Render | | |
| `VITE_API_URL` apunta a Render | | |
| `MONGODB_URI` esta en Render | | |
| Logs muestran conexion a MongoDB | | |
| Frontend consume datos del backend | | |

**Resultado esperado:** Aplicacion integrada.  
**Criterio de validacion rapida:** Puedes mostrar URL de frontend, URL de backend y logs de conexion.

---

## Actividad 6 - Validacion con preguntas

**Titulo:** Diagnostico de errores frecuentes.  
**Objetivo:** Resolver problemas mediante razonamiento por capas.

**Caso 1:** El frontend abre, pero no muestra datos.  
**Pregunta:** ¿Que revisarias primero?

**Caso 2:** Render muestra error de conexion a MongoDB.  
**Pregunta:** ¿Que revisarías en `MONGODB_URI`?

**Caso 3:** GitHub Actions no se ejecuta.  
**Pregunta:** ¿Donde debe estar ubicado el archivo YAML?

**Resultado esperado:** Diagnostico por componentes.  
**Criterio de validacion rapida:** Tus respuestas mencionan variable, plataforma y evidencia.

---

# Validacion final

Antes de cerrar la sesion, verifica que puedas responder:

1. ¿Que plataforma publica el frontend?
2. ¿Que plataforma ejecuta el backend?
3. ¿Que plataforma almacena los datos?
4. ¿Que variable conecta el frontend con el backend?
5. ¿Que variable conecta el backend con MongoDB Atlas?
6. ¿Que herramienta automatiza tareas desde GitHub?
7. ¿Por que se revisan logs despues del deploy?
8. ¿Por que no se deben subir secretos a GitHub?

---

# Glosario rapido

| Termino | Definicion |
|---|---|
| Deployment | Proceso de publicar una aplicacion en internet. |
| Frontend | Parte visual que usa el usuario. |
| Backend | Servidor que procesa solicitudes. |
| Variable de entorno | Valor de configuracion definido fuera del codigo. |
| CORS | Mecanismo que controla solicitudes entre dominios. |
| Connection string | Cadena usada para conectar con una base de datos. |
| CI | Integracion continua. |
| CD | Despliegue continuo. |
| Workflow | Proceso automatizado definido en YAML. |

---

# Cierre

El despliegue cloud no consiste solo en presionar un boton. Requiere entender que cada componente tiene una responsabilidad, que las variables conectan los servicios y que la validacion final debe hacerse con evidencias: URL publica, logs, respuesta de API y conexion a la base de datos.
