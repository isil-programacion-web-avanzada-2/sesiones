---
course_id: PWA-ISIL
session_id: S05
module_id: TEMA-05
course_version: "2026"
source_origin: PPT
status: revised-v2
---

# Guía del Estudiante — Tema 05
## Creación de servidores web con Node.js y Express

# 0. Cómo comenzar y cómo usar esta guía

Esta guía trabaja con **ejemplos independientes** para que no pierdas el código de un ejemplo al avanzar al siguiente. Instalarás Express una sola vez en la carpeta raíz y dentro crearás una carpeta para cada `EJxx` y cada `Txx`.

## 0.1 Crea la carpeta principal
Abre una terminal y ejecuta exactamente:

```bash
mkdir servidor-express
cd servidor-express
npm init -y
npm install express
npm install nodemon -D
mkdir ejemplos
mkdir tareas
```

Después abre **la carpeta `servidor-express` completa** en Visual Studio Code. Si tienes disponible el comando `code`, puedes ejecutar:

```bash
code .
```

Si `code .` no funciona, abre Visual Studio Code y usa **Archivo > Abrir carpeta > servidor-express**.

## 0.2 Estructura que irás construyendo

```text
servidor-express/
|-- package.json
|-- node_modules/
|-- ejemplos/
|   |-- EJ01/
|   |   `-- app.js
|   |-- EJ02/
|   |   `-- app.js
|   |-- ...
|   `-- EJ10/
|       |-- app.js
|       `-- productos.json
`-- tareas/
    |-- T01/
    |-- T02/
    `-- ...
```

**Importante:** no escribas código JavaScript dentro de `package.json`. El código de Express se escribe en archivos `.js`, principalmente `app.js`. Cuando un ejemplo necesite otro archivo, la guía indicará su ruta exacta.

## 0.3 Regla para todos los bloques “Escribe ahora”
1. Primero revisa el recuadro **Inicio operativo** del ejemplo.
2. Crea exactamente la carpeta y los archivos indicados.
3. El primer bloque de código se escribe desde la **línea 1** del archivo activo.
4. Los bloques siguientes se escriben **inmediatamente debajo del bloque anterior**, sin borrar lo ya escrito.
5. Si cambia el archivo activo, la guía lo dirá explícitamente.
6. Al final compara tu archivo con **Archivo / artefacto completo al terminar este ejemplo**.
7. Ejecuta siempre desde la carpeta del ejemplo correspondiente.

## 0.4 Cómo crear una carpeta y un archivo en VS Code
En el panel **Explorer** de Visual Studio Code:
- clic derecho sobre `ejemplos` > **New Folder** > escribe `EJ01`;
- clic derecho sobre `EJ01` > **New File** > escribe `app.js`;
- abre `app.js` y recién allí escribe el código indicado.

Para el siguiente ejemplo crea `EJ02`; **no reemplaces el archivo de EJ01**. Así conservarás cada etapa de la clase.


## 1. Propósito de la sesión
En esta clase-laboratorio construirás un servidor web con Node.js y Express desde un proyecto vacío y lo transformarás progresivamente en un backend capaz de recibir solicitudes HTTP, organizar rutas, ejecutar middleware, manejar errores y realizar las cuatro operaciones CRUD sobre un archivo JSON. La prioridad no es copiar un `app.js` terminado, sino entender por qué aparece cada instrucción, qué cambia después de escribirla y cómo comprobar que funciona.

La evidencia final será un CRUD de productos que responde a GET, POST, PUT y DELETE y que puedes probar con Postman, Thunder Client, Insomnia, Hoppscotch o `curl`. La guía contiene todo el código y todos los comandos esenciales; no necesitas abrir otro documento para descubrir pasos de la práctica.

## 2. Resultado observable
### A. Lectura sugerida del docente
Al finalizar la sesión podrás explicar el recorrido de una solicitud desde el cliente hasta Express, distinguir ruta y middleware, capturar parámetros, procesar JSON y relacionar cada método HTTP con una operación CRUD. También podrás leer y modificar un archivo `productos.json` desde Node.js, comprobar cambios en disco y reconocer errores frecuentes de rutas, middleware, IDs y archivos. El resultado de dominio no es únicamente que “el servidor funcione”: debes poder justificar por qué `express.json()` va antes de una ruta que lee `req.body`, por qué un parámetro de URL necesita convertirse antes de compararse con IDs numéricos y por qué `filter()` elimina usando una condición de desigualdad. La sesión termina cuando puedes predecir un resultado, ejecutarlo, interpretarlo y corregir un fallo controlado sin depender de una solución externa.

### B. Desempeños observables
- Crear e iniciar un proyecto Express.
- Construir rutas GET y rutas con parámetros.
- Modularizar rutas mediante `express.Router()`.
- Aplicar middleware y explicar `next()`.
- Manejar un error mediante middleware de cuatro parámetros.
- Implementar Create, Read, Update y Delete sobre JSON.
- Probar endpoints e interpretar códigos de estado.
- Diagnosticar y corregir errores frecuentes.

### C. Criterio de dominio
Dominas la sesión cuando puedes reconstruir el flujo principal sin copiar un archivo final, explicar cada bloque nuevo y demostrar mediante solicitudes HTTP y cambios en los archivos JSON que el comportamiento coincide con tu predicción.

## 3. Antes de iniciar
### Debe saber
- Sintaxis básica de JavaScript: variables, funciones flecha, arreglos y objetos.
- Uso básico de terminal y carpetas.
- Concepto general de cliente, servidor y solicitud HTTP trabajado en el curso.

### Debe tener disponible
- Node.js LTS. Para una instalación nueva en septiembre de 2026 se recomienda Node.js 24 LTS.
- npm, instalado junto con Node.js.
- Visual Studio Code u otro editor.
- Una terminal.
- Navegador y una herramienta de solicitudes HTTP (Postman es la referencia de la PPT; Thunder Client, Insomnia o Hoppscotch son alternativas).

### No se asumirá todavía
- Base de datos relacional o NoSQL.
- Autenticación, JWT o seguridad avanzada.
- ORM.
- Despliegue en nube.
- Arquitecturas avanzadas.

# Preparación del entorno
## Ruta A — Node.js ya está instalado
Ejecuta:

```bash
node -v
npm -v
```

Debes obtener dos versiones y no mensajes como “command not found” o “no se reconoce como un comando”.

## Ruta B — Equipo sin preparar
1. Entra al sitio oficial de Node.js.
2. Descarga la edición LTS.
3. Instálala con las opciones predeterminadas institucionales.
4. Cierra y vuelve a abrir la terminal.
5. Ejecuta `node -v` y `npm -v`.

## Proyecto base
El proyecto base ya fue creado en la sección **0.1**. Express y nodemon se instalan una sola vez en `servidor-express`. Durante los ejemplos trabajarás dentro de `servidor-express/ejemplos/EJxx/`. Para ejecutar puedes usar `node app.js`; si deseas reinicio automático durante desarrollo, desde la carpeta del ejemplo puedes usar `npx nodemon app.js`.

# EJ01 — Crear y levantar el primer servidor Express

## Qué vamos a construir / resolver
Inicializar un servidor Express y comprobar que el proceso queda escuchando en el puerto 3001.

## Qué aprenderás aquí
- require('express')
- express()
- app.listen()
- puerto 3001

## Archivos, recursos o artefactos involucrados
- `app.js`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ01/`  
**Acción inicial:** crea la carpeta `EJ01` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea `app.js` vacío. Este es tu primer archivo de servidor.

Tu estructura para este ejemplo debe verse así:

```text
EJ01/
`-- app.js
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const app = express();

app.listen(3001, () => {
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  console.log('Servidor escuchando en http://localhost:3001');
});
```

### Explicación detallada
Este microbloque completa una parte del comportamiento sin introducir una responsabilidad adicional distinta de la ya explicada; observa cómo se conecta con el bloque anterior.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `require('express')` | Participa en inicializar un servidor express y comprobar que el proceso queda escuchando en el puerto 3001. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `express()` | Participa en inicializar un servidor express y comprobar que el proceso queda escuchando en el puerto 3001. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `app.listen()` | Participa en inicializar un servidor express y comprobar que el proceso queda escuchando en el puerto 3001. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `puerto 3001` | Participa en inicializar un servidor express y comprobar que el proceso queda escuchando en el puerto 3001. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const app = express();

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Desde la raíz `servidor-express`, ejecuta `cd ejemplos/EJ01`.
2. Ejecuta `node app.js`.
3. Verifica que la terminal muestre `Servidor escuchando en http://localhost:3001`.
4. Abre `http://localhost:3001/`; todavía obtendrás 404 porque no existe una ruta `/`.
5. Detén el servidor con `Ctrl+C`.

## Resultado esperado
El proceso inicia y la raíz responde 404 porque todavía no existe una ruta `/`.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Ejecutar `node app.js` fuera de la carpeta correcta o no haber instalado Express.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Entrar a la carpeta del proyecto, ejecutar `npm install` y volver a iniciar el servidor.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T01 — Tarea espejo: Levantar un segundo servidor de práctica
### Dónde realizar la tarea
Crea `servidor-express/tareas/T01/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ01` para poder compararlo después.

### Enunciado
Crea `app.js` con Express y levanta el servidor en el puerto 3001. Cambia únicamente el mensaje de consola a `Servidor de práctica listo en http://localhost:3001`.

### Archivos/artefactos a modificar
- `app.js`

### Restricción
No agregues rutas todavía.

### Pista
Necesitas importar Express, crear `app` y llamar a `app.listen(3001, callback)`.

### Evidencia que debes mostrar
Captura o salida de terminal mostrando el mensaje correcto y una petición a `/` que todavía devuelve 404.

### Cómo saber si está correcta
El proceso queda activo en 3001 y no aparecen errores de módulo.

# EJ02 — Crear rutas GET básicas

## Qué vamos a construir / resolver
Definir endpoints GET y reconocer el papel de `req` y `res` en una ruta Express.

## Qué aprenderás aquí
- app.get()
- ruta/endpoint
- req
- res
- res.send()

## Archivos, recursos o artefactos involucrados
- `app.js`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ02/`  
**Acción inicial:** crea la carpeta `EJ02` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea un `app.js` nuevo para EJ02. No pegues este código dentro de EJ01.

Tu estructura para este ejemplo debe verse así:

```text
EJ02/
`-- app.js
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

`app.get(PATH, HANDLER)` registra una ruta que responde únicamente a solicitudes HTTP GET para ese path.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  res.send('Bienvenido al servidor Express');
});
```

### Explicación detallada
Este microbloque completa una parte del comportamiento sin introducir una responsabilidad adicional distinta de la ya explicada; observa cómo se conecta con el bloque anterior.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 4 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript

app.get('/productos', (req, res) => {
  res.send('Listado de productos');
});
```

### Explicación detallada
`app.get(PATH, HANDLER)` registra una ruta que responde únicamente a solicitudes HTTP GET para ese path.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 5 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

### Explicación detallada
`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `app.get()` | Participa en definir endpoints get y reconocer el papel de `req` y `res` en una ruta express. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `ruta/endpoint` | Participa en definir endpoints get y reconocer el papel de `req` y `res` en una ruta express. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `req` | Participa en definir endpoints get y reconocer el papel de `req` y `res` en una ruta express. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `res` | Participa en definir endpoints get y reconocer el papel de `req` y `res` en una ruta express. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `res.send()` | Participa en definir endpoints get y reconocer el papel de `req` y `res` en una ruta express. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Bienvenido al servidor Express');
});

app.get('/productos', (req, res) => {
  res.send('Listado de productos');
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Ejecuta `cd ejemplos/EJ02` desde la raíz del proyecto.
2. Ejecuta `node app.js`.
3. Prueba `http://localhost:3001/`.
4. Prueba `http://localhost:3001/productos`.
5. Detén con `Ctrl+C`.

## Resultado esperado
`/` responde “Bienvenido al servidor Express” y `/productos` responde “Listado de productos”.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Escribir la ruta después de iniciar otro proceso con el mismo puerto o olvidar reiniciar el servidor.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Detener la instancia anterior, guardar `app.js` y volver a ejecutar; con nodemon basta guardar.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T02 — Tarea espejo: Agregar rutas de información
### Dónde realizar la tarea
Crea `servidor-express/tareas/T02/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ02` para poder compararlo después.

### Enunciado
Crea dos rutas GET: `/saludo` debe responder `Hola desde Express` y `/categorias` debe responder `Listado de categorías`.

### Archivos/artefactos a modificar
- `app.js`

### Restricción
Usa `app.get()` y `res.send()`; no uses Router todavía.

### Pista
Repite el patrón `app.get(PATH, (req, res) => { ... })`.

### Evidencia que debes mostrar
Respuestas HTTP correctas al visitar ambas URLs.

### Cómo saber si está correcta
Ambas rutas responden 200 y el texto exacto solicitado.

# EJ03 — Capturar parámetros en la URL

## Qué vamos a construir / resolver
Recibir un valor dinámico desde la URL mediante `req.params`.

## Qué aprenderás aquí
- :id
- req.params
- req.params.id
- template literal

## Archivos, recursos o artefactos involucrados
- `app.js`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ03/`  
**Acción inicial:** crea la carpeta `EJ03` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea un `app.js` nuevo para EJ03. El parámetro dinámico se implementará aquí.

Tu estructura para este ejemplo debe verse así:

```text
EJ03/
`-- app.js
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const app = express();

app.get('/productos/:id', (req, res) => {
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

`app.get(PATH, HANDLER)` registra una ruta que responde únicamente a solicitudes HTTP GET para ese path.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  const id = req.params.id;
  res.send(`Producto con ID: ${id}`);
});
```

### Explicación detallada
`req.params` reúne los parámetros dinámicos de la URL. `req.params.id` llega inicialmente como texto.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 4 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

### Explicación detallada
`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `:id` | Participa en recibir un valor dinámico desde la url mediante `req.params`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `req.params` | Participa en recibir un valor dinámico desde la url mediante `req.params`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `req.params.id` | Participa en recibir un valor dinámico desde la url mediante `req.params`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `template literal` | Participa en recibir un valor dinámico desde la url mediante `req.params`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const app = express();

app.get('/productos/:id', (req, res) => {
  const id = req.params.id;
  res.send(`Producto con ID: ${id}`);
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Ejecuta `cd ejemplos/EJ03`.
2. Ejecuta `node app.js`.
3. Abre `http://localhost:3001/productos/5`.
4. Debes ver `Producto con ID: 5`.
5. Detén con `Ctrl+C`.

## Resultado esperado
`GET /productos/5` responde `Producto con ID: 5`.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Usar `/productos/id` en lugar de `/productos/:id`, lo que convierte `id` en texto fijo.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Anteponer `:` al nombre del parámetro y leerlo mediante `req.params.id`.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T03 — Tarea espejo: Crear una ruta dinámica de usuarios
### Dónde realizar la tarea
Crea `servidor-express/tareas/T03/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ03` para poder compararlo después.

### Enunciado
Crea `GET /usuarios/:id` y responde `Usuario con ID: X`, donde X proviene de la URL.

### Archivos/artefactos a modificar
- `app.js`

### Restricción
No escribas IDs fijos dentro de la respuesta.

### Pista
El valor dinámico estará en `req.params.id`.

### Evidencia que debes mostrar
Prueba con `/usuarios/8` y `/usuarios/15`; la respuesta debe cambiar.

### Cómo saber si está correcta
El texto refleja exactamente el valor recibido en cada URL.

# EJ04 — Separar rutas con express.Router()

## Qué vamos a construir / resolver
Modularizar las rutas de productos en un archivo independiente y montarlas bajo `/productos`.

## Qué aprenderás aquí
- express.Router()
- module.exports
- require local
- app.use()
- prefijo de rutas

## Archivos, recursos o artefactos involucrados
- `app.js`
- `routes/productos.js`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ04/`  
**Acción inicial:** crea la carpeta `EJ04` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`, `routes/productos.js`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea `app.js`, luego crea la carpeta `routes` y dentro el archivo `productos.js`.

Tu estructura para este ejemplo debe verse así:

```text
EJ04/
|-- app.js
`-- routes/productos.js
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const app = express();
const productosRouter = require('./routes/productos');
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
app.use('/productos', productosRouter);

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
```

### Explicación detallada
`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 4 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});
```

### Explicación detallada
Este microbloque completa una parte del comportamiento sin introducir una responsabilidad adicional distinta de la ya explicada; observa cómo se conecta con el bloque anterior.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Crear el archivo auxiliar `routes/productos.js`
Crea este archivo en la ruta exacta indicada por el Inicio operativo. No lo pegues dentro de `app.js`.

```javascript
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.send('Inicio productos');
});

router.get('/:id', (req, res) => {
  res.send(`Producto ${req.params.id}`);
});

module.exports = router;
```

**Interpretación:** este archivo forma parte del estado que la aplicación leerá o del módulo que `app.js` importará. Su ubicación debe coincidir con la ruta usada en el código.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `express.Router()` | Participa en modularizar las rutas de productos en un archivo independiente y montarlas bajo `/productos`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `module.exports` | Participa en modularizar las rutas de productos en un archivo independiente y montarlas bajo `/productos`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `require local` | Participa en modularizar las rutas de productos en un archivo independiente y montarlas bajo `/productos`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `app.use()` | Participa en modularizar las rutas de productos en un archivo independiente y montarlas bajo `/productos`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `prefijo de rutas` | Participa en modularizar las rutas de productos en un archivo independiente y montarlas bajo `/productos`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const app = express();
const productosRouter = require('./routes/productos');

app.use('/productos', productosRouter);

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Confirma que existen `app.js` y `routes/productos.js`.
2. Ejecuta `cd ejemplos/EJ04` y luego `node app.js`.
3. Prueba `http://localhost:3001/productos`.
4. Prueba `http://localhost:3001/productos/7`.
5. Detén con `Ctrl+C`.

## Resultado esperado
`/productos` responde “Inicio productos” y `/productos/7` responde “Producto 7”.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Importar `./routes/productos` cuando el archivo no existe o no exporta `router`.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Verificar la ruta del archivo y terminar `productos.js` con `module.exports = router`.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T04 — Tarea espejo: Modularizar rutas de usuarios
### Dónde realizar la tarea
Crea `servidor-express/tareas/T04/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ04` para poder compararlo después.

### Enunciado
Crea `routes/usuarios.js` con `GET /` y `GET /:id`, y móntalo en `app.js` con el prefijo `/usuarios`.

### Archivos/artefactos a modificar
- `app.js`
- `routes/usuarios.js`

### Restricción
Las rutas de usuarios no deben quedar definidas directamente en `app.js`.

### Pista
Crea `const router = express.Router()` y exporta el router al final.

### Evidencia que debes mostrar
`/usuarios` y `/usuarios/3` responden correctamente.

### Cómo saber si está correcta
Las dos rutas funcionan y `app.js` solo importa/monta el router.

# EJ05 — Procesar JSON y continuar con next()

## Qué vamos a construir / resolver
Aplicar `express.json()` y un middleware propio antes de llegar a la ruta final.

## Qué aprenderás aquí
- app.use()
- express.json()
- middleware
- next()
- req.url
- req.body

## Archivos, recursos o artefactos involucrados
- `app.js`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ05/`  
**Acción inicial:** crea la carpeta `EJ05` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea un `app.js` nuevo. Aquí registrarás primero los middleware y después la ruta POST.

Tu estructura para este ejemplo debe verse así:

```text
EJ05/
`-- app.js
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const app = express();

app.use(express.json());
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

`express.json()` es middleware incorporado que interpreta cuerpos JSON antes de que una ruta lea `req.body`.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript

app.use((req, res, next) => {
  console.log(`Ruta solicitada: ${req.url}`);
  next();
```

### Explicación detallada
Este `app.use()` registra un middleware propio. `req` contiene la solicitud, `res` la respuesta y `next()` transfiere el control al siguiente middleware o ruta.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 4 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});

app.post('/datos', (req, res) => {
  res.json(req.body);
```

### Explicación detallada
`app.post()` registra una ruta POST; en esta sesión se usa cuando el cliente envía datos que deben procesarse o almacenarse.

`req.body` contiene el objeto JSON enviado por el cliente, siempre que `express.json()` se haya registrado antes.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 5 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
```

### Explicación detallada
`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 6 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});
```

### Explicación detallada
Este microbloque completa una parte del comportamiento sin introducir una responsabilidad adicional distinta de la ya explicada; observa cómo se conecta con el bloque anterior.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `app.use()` | Participa en aplicar `express.json()` y un middleware propio antes de llegar a la ruta final. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `express.json()` | Participa en aplicar `express.json()` y un middleware propio antes de llegar a la ruta final. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `middleware` | Participa en aplicar `express.json()` y un middleware propio antes de llegar a la ruta final. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `next()` | Participa en aplicar `express.json()` y un middleware propio antes de llegar a la ruta final. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `req.url` | Participa en aplicar `express.json()` y un middleware propio antes de llegar a la ruta final. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `req.body` | Participa en aplicar `express.json()` y un middleware propio antes de llegar a la ruta final. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.use((req, res, next) => {
  console.log(`Ruta solicitada: ${req.url}`);
  next();
});

app.post('/datos', (req, res) => {
  res.json(req.body);
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Ejecuta `cd ejemplos/EJ05` y luego `node app.js`.
2. En Postman/Thunder Client envía `POST http://localhost:3001/datos`.
3. Selecciona Body > JSON y envía `{"nombre":"Ana"}`.
4. Comprueba que la respuesta devuelve el mismo JSON y que la terminal registra `/datos`.
5. Detén con `Ctrl+C`.

## Resultado esperado
La consola registra `/datos` y la respuesta devuelve el mismo JSON enviado.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Omitir `next()` en el middleware propio, dejando la solicitud sin llegar a la ruta.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Llamar a `next()` después del registro en consola para ceder el control.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T05 — Tarea espejo: Registrar solicitudes y leer un JSON
### Dónde realizar la tarea
Crea `servidor-express/tareas/T05/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ05` para poder compararlo después.

### Enunciado
Configura `express.json()`, agrega un middleware que muestre `Ruta solicitada: ...` y crea `POST /prueba` que devuelva el JSON recibido.

### Archivos/artefactos a modificar
- `app.js`

### Restricción
El middleware debe ejecutarse antes de la ruta y debe llamar a `next()`.

### Pista
Primero `app.use(express.json())`, luego el middleware propio, después la ruta.

### Evidencia que debes mostrar
La terminal registra `/prueba` y la respuesta coincide con el body enviado.

### Cómo saber si está correcta
No se queda la solicitud pendiente y `req.body` contiene el JSON.

# EJ06 — Manejar errores con middleware de cuatro parámetros

## Qué vamos a construir / resolver
Centralizar una respuesta 500 mediante un middleware `(err, req, res, next)` y validar su funcionamiento con un error controlado.

## Qué aprenderás aquí
- next(error)
- new Error()
- middleware de error
- err.stack
- res.status(500)

## Archivos, recursos o artefactos involucrados
- `app.js`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ06/`  
**Acción inicial:** crea la carpeta `EJ06` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea un `app.js` nuevo. La ruta que genera el error debe quedar antes del middleware de error.

Tu estructura para este ejemplo debe verse así:

```text
EJ06/
`-- app.js
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const app = express();

app.get('/error-demo', (req, res, next) => {
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

`app.get(PATH, HANDLER)` registra una ruta que responde únicamente a solicitudes HTTP GET para ese path.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  next(new Error('Error de prueba controlado'));
});
```

### Explicación detallada
`next(error)` entrega el error a Express para que busque un middleware especial de cuatro parámetros. La ruta no responde directamente: delega el manejo del fallo.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 4 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript

app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('¡Algo salió mal!');
```

### Explicación detallada
El estado 500 representa un error interno controlado por el servidor; aquí se usa para comprobar el middleware de errores.

La firma de cuatro parámetros identifica un middleware de errores. `err` contiene el fallo recibido y `res.status(500)` devuelve una respuesta controlada.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 5 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
```

### Explicación detallada
`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 6 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});
```

### Explicación detallada
Este microbloque completa una parte del comportamiento sin introducir una responsabilidad adicional distinta de la ya explicada; observa cómo se conecta con el bloque anterior.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `next(error)` | Participa en centralizar una respuesta 500 mediante un middleware `(err, req, res, next)` y validar su funcionamiento con un error controlado. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `new Error()` | Participa en centralizar una respuesta 500 mediante un middleware `(err, req, res, next)` y validar su funcionamiento con un error controlado. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `middleware de error` | Participa en centralizar una respuesta 500 mediante un middleware `(err, req, res, next)` y validar su funcionamiento con un error controlado. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `err.stack` | Participa en centralizar una respuesta 500 mediante un middleware `(err, req, res, next)` y validar su funcionamiento con un error controlado. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `res.status(500)` | Participa en centralizar una respuesta 500 mediante un middleware `(err, req, res, next)` y validar su funcionamiento con un error controlado. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const app = express();

app.get('/error-demo', (req, res, next) => {
  next(new Error('Error de prueba controlado'));
});

app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('¡Algo salió mal!');
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Ejecuta `cd ejemplos/EJ06` y luego `node app.js`.
2. Abre `http://localhost:3001/error-demo`.
3. Comprueba estado 500 y el texto `¡Algo salió mal!`.
4. Revisa la traza mostrada en la terminal.
5. Detén con `Ctrl+C`.

## Resultado esperado
`/error-demo` devuelve estado 500 y el texto `¡Algo salió mal!`; la terminal muestra la traza del error.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Colocar el middleware de error antes de las rutas o declarar solo tres parámetros.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Ubicarlo después de las rutas y conservar los cuatro parámetros `(err, req, res, next)`.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T06 — Tarea espejo: Crear un fallo controlado
### Dónde realizar la tarea
Crea `servidor-express/tareas/T06/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ06` para poder compararlo después.

### Enunciado
Crea `GET /fallo-controlado` que envíe un error a `next()` y agrega un middleware de errores que responda 500 con `Error controlado por Express`.

### Archivos/artefactos a modificar
- `app.js`

### Restricción
La respuesta 500 debe provenir del middleware de errores, no directamente de la ruta.

### Pista
La ruta puede usar `next(new Error(...))`.

### Evidencia que debes mostrar
La solicitud devuelve 500 y el texto pedido.

### Cómo saber si está correcta
La traza aparece en consola y el servidor continúa activo después del error.

# EJ07 — CRUD Read: leer productos desde JSON

## Qué vamos a construir / resolver
Leer `productos.json` con `fs.readFileSync()`, convertir su contenido con `JSON.parse()` y responderlo como JSON.

## Qué aprenderás aquí
- require('fs')
- fs.readFileSync()
- JSON.parse()
- res.json()
- productos.json

## Archivos, recursos o artefactos involucrados
- `app.js`
- `productos.json`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ07/`  
**Acción inicial:** crea la carpeta `EJ07` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`, `productos.json`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea `app.js` y `productos.json` en la misma carpeta. `productos.json` no va dentro de `routes`.

Tu estructura para este ejemplo debe verse así:

```text
EJ07/
|-- app.js
`-- productos.json
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const fs = require('fs');
const app = express();
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

`require('fs')` carga el módulo de archivos de Node.js; se utiliza aquí para leer y escribir el archivo JSON de la práctica.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
app.get('/productos', (req, res) => {
  const data = fs.readFileSync('./productos.json');
  res.json(JSON.parse(data));
});
```

### Explicación detallada
`app.get(PATH, HANDLER)` registra una ruta que responde únicamente a solicitudes HTTP GET para ese path.

`fs.readFileSync('./...json')` lee el archivo de manera síncrona. En este laboratorio simple permite concentrarnos en el flujo CRUD.

`JSON.parse()` convierte el contenido JSON leído desde el archivo en valores JavaScript que pueden recorrerse o modificarse.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 4 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

### Explicación detallada
`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Crear el archivo auxiliar `productos.json`
Crea este archivo en la ruta exacta indicada por el Inicio operativo. No lo pegues dentro de `app.js`.

```json
[
  {
    "id": 1,
    "nombre": "Laptop",
    "precio": 2500
  },
  {
    "id": 2,
    "nombre": "Mouse",
    "precio": 100
  }
]
```

**Interpretación:** este archivo forma parte del estado que la aplicación leerá o del módulo que `app.js` importará. Su ubicación debe coincidir con la ruta usada en el código.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `require('fs')` | Participa en leer `productos.json` con `fs.readfilesync()`, convertir su contenido con `json.parse()` y responderlo como json. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `fs.readFileSync()` | Participa en leer `productos.json` con `fs.readfilesync()`, convertir su contenido con `json.parse()` y responderlo como json. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `JSON.parse()` | Participa en leer `productos.json` con `fs.readfilesync()`, convertir su contenido con `json.parse()` y responderlo como json. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `res.json()` | Participa en leer `productos.json` con `fs.readfilesync()`, convertir su contenido con `json.parse()` y responderlo como json. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `productos.json` | Participa en leer `productos.json` con `fs.readfilesync()`, convertir su contenido con `json.parse()` y responderlo como json. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const fs = require('fs');
const app = express();

app.get('/productos', (req, res) => {
  const data = fs.readFileSync('./productos.json');
  res.json(JSON.parse(data));
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Confirma que `app.js` y `productos.json` estén juntos en `ejemplos/EJ07`.
2. Ejecuta `cd ejemplos/EJ07` y luego `node app.js`.
3. Envía `GET http://localhost:3001/productos`.
4. Debes recibir los productos almacenados en el JSON.
5. Detén con `Ctrl+C`.

## Resultado esperado
`GET /productos` devuelve los dos productos definidos en `productos.json`.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Ejecutar el servidor desde otra carpeta y provocar `ENOENT` porque `./productos.json` no se encuentra.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Entrar a la carpeta del ejemplo antes de ejecutar `node app.js` y comprobar que `productos.json` está junto a `app.js`.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T07 — Tarea espejo: Leer un inventario desde otro archivo JSON
### Dónde realizar la tarea
Crea `servidor-express/tareas/T07/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ07` para poder compararlo después.

### Enunciado
Crea `inventario.json` con dos productos y `GET /inventario` que lea el archivo con `fs.readFileSync()`, use `JSON.parse()` y responda con `res.json()`.

### Archivos/artefactos a modificar
- `app.js`
- `inventario.json`

### Restricción
No escribas el arreglo directamente dentro de `app.js`.

### Pista
Repite el ciclo archivo → lectura → parseo → respuesta JSON.

### Evidencia que debes mostrar
La respuesta contiene exactamente los registros de `inventario.json`.

### Cómo saber si está correcta
Modificar un precio en el archivo y repetir GET debe reflejar el cambio.

# EJ08 — CRUD Create: agregar un producto

## Qué vamos a construir / resolver
Recibir un producto por JSON, incorporarlo al arreglo y persistirlo nuevamente en `productos.json`.

## Qué aprenderás aquí
- req.body
- data.push()
- fs.writeFileSync()
- JSON.stringify()
- res.status(201)

## Archivos, recursos o artefactos involucrados
- `app.js`
- `productos.json`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ08/`  
**Acción inicial:** crea la carpeta `EJ08` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`, `productos.json`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea `app.js` y `productos.json` en la misma carpeta. El middleware `express.json()` debe quedar antes de la ruta POST.

Tu estructura para este ejemplo debe verse así:

```text
EJ08/
|-- app.js
`-- productos.json
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const fs = require('fs');
const app = express();
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

`require('fs')` carga el módulo de archivos de Node.js; se utiliza aquí para leer y escribir el archivo JSON de la práctica.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
app.use(express.json());

app.post('/productos', (req, res) => {
  const nuevo = req.body;
```

### Explicación detallada
`express.json()` es middleware incorporado que interpreta cuerpos JSON antes de que una ruta lea `req.body`.

`app.post()` registra una ruta POST; en esta sesión se usa cuando el cliente envía datos que deben procesarse o almacenarse.

`req.body` contiene el objeto JSON enviado por el cliente, siempre que `express.json()` se haya registrado antes.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 4 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  const data = JSON.parse(fs.readFileSync('./productos.json'));
  data.push(nuevo);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
  res.status(201).send('Producto agregado');
```

### Explicación detallada
`fs.readFileSync('./...json')` lee el archivo de manera síncrona. En este laboratorio simple permite concentrarnos en el flujo CRUD.

`JSON.parse()` convierte el contenido JSON leído desde el archivo en valores JavaScript que pueden recorrerse o modificarse.

`push()` agrega el nuevo objeto al final del arreglo existente sin borrar los registros previos.

`fs.writeFileSync()` persiste el arreglo actualizado. `JSON.stringify(..., null, 2)` lo convierte nuevamente a JSON legible.

El estado 201 comunica que la solicitud creó un nuevo recurso correctamente.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 5 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
```

### Explicación detallada
`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 6 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});
```

### Explicación detallada
Este microbloque completa una parte del comportamiento sin introducir una responsabilidad adicional distinta de la ya explicada; observa cómo se conecta con el bloque anterior.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Crear el archivo auxiliar `productos.json`
Crea este archivo en la ruta exacta indicada por el Inicio operativo. No lo pegues dentro de `app.js`.

```json
[
  {
    "id": 1,
    "nombre": "Laptop",
    "precio": 2500
  },
  {
    "id": 2,
    "nombre": "Mouse",
    "precio": 100
  }
]
```

**Interpretación:** este archivo forma parte del estado que la aplicación leerá o del módulo que `app.js` importará. Su ubicación debe coincidir con la ruta usada en el código.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `req.body` | Participa en recibir un producto por json, incorporarlo al arreglo y persistirlo nuevamente en `productos.json`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `data.push()` | Participa en recibir un producto por json, incorporarlo al arreglo y persistirlo nuevamente en `productos.json`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `fs.writeFileSync()` | Participa en recibir un producto por json, incorporarlo al arreglo y persistirlo nuevamente en `productos.json`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `JSON.stringify()` | Participa en recibir un producto por json, incorporarlo al arreglo y persistirlo nuevamente en `productos.json`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `res.status(201)` | Participa en recibir un producto por json, incorporarlo al arreglo y persistirlo nuevamente en `productos.json`. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const fs = require('fs');
const app = express();

app.use(express.json());

app.post('/productos', (req, res) => {
  const nuevo = req.body;
  const data = JSON.parse(fs.readFileSync('./productos.json'));
  data.push(nuevo);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
  res.status(201).send('Producto agregado');
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Ejecuta `cd ejemplos/EJ08` y luego `node app.js`.
2. Envía `POST http://localhost:3001/productos`.
3. Body JSON: `{"id":3,"nombre":"Teclado","precio":80}`.
4. Comprueba estado 201 y `Producto agregado`.
5. Abre `productos.json` y verifica que el producto 3 fue guardado.
6. Detén con `Ctrl+C`.

## Resultado esperado
La respuesta es 201 “Producto agregado” y `productos.json` incluye el nuevo registro.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Olvidar `app.use(express.json())`; entonces `req.body` no contiene el JSON esperado.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Registrar `express.json()` antes de la ruta POST y enviar el body como JSON válido.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T08 — Tarea espejo: Agregar un registro al inventario
### Dónde realizar la tarea
Crea `servidor-express/tareas/T08/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ08` para poder compararlo después.

### Enunciado
Implementa `POST /inventario` para recibir un objeto JSON, agregarlo a `inventario.json` y responder 201 con `Item agregado`.

### Archivos/artefactos a modificar
- `app.js`
- `inventario.json`

### Restricción
Debes leer el contenido existente antes de `push()` y volver a escribir el arreglo completo.

### Pista
Usa `express.json()`, `req.body`, `JSON.parse()`, `push()` y `writeFileSync()`.

### Evidencia que debes mostrar
El archivo agrega el nuevo registro y conserva los anteriores.

### Cómo saber si está correcta
La respuesta es 201 y el JSON queda sintácticamente válido.

# EJ09 — CRUD Update: actualizar por ID

## Qué vamos a construir / resolver
Combinar el ID de la URL con los datos del body para modificar solo el producto coincidente.

## Qué aprenderás aquí
- parseInt()
- req.params.id
- req.body
- Array.map()
- operador spread
- let data

## Archivos, recursos o artefactos involucrados
- `app.js`
- `productos.json`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ09/`  
**Acción inicial:** crea la carpeta `EJ09` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`, `productos.json`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea `app.js` y `productos.json` en la misma carpeta. El endpoint PUT se escribirá en `app.js`.

Tu estructura para este ejemplo debe verse así:

```text
EJ09/
|-- app.js
`-- productos.json
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const fs = require('fs');
const app = express();
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

`require('fs')` carga el módulo de archivos de Node.js; se utiliza aquí para leer y escribir el archivo JSON de la práctica.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
app.use(express.json());

app.put('/productos/:id', (req, res) => {
  const id = parseInt(req.params.id);
```

### Explicación detallada
`express.json()` es middleware incorporado que interpreta cuerpos JSON antes de que una ruta lea `req.body`.

`app.put()` registra la operación de actualización. El `:id` de la URL permite identificar qué producto se modificará.

`req.params` reúne los parámetros dinámicos de la URL. `req.params.id` llega inicialmente como texto.

`parseInt()` convierte el parámetro de ruta a número para compararlo de forma coherente con los IDs numéricos del JSON.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 4 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  const nuevosDatos = req.body;
  let data = JSON.parse(fs.readFileSync('./productos.json'));
  data = data.map(p => p.id === id ? { ...p, ...nuevosDatos } : p);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
```

### Explicación detallada
`req.body` contiene el objeto JSON enviado por el cliente, siempre que `express.json()` se haya registrado antes.

`fs.readFileSync('./...json')` lee el archivo de manera síncrona. En este laboratorio simple permite concentrarnos en el flujo CRUD.

`JSON.parse()` convierte el contenido JSON leído desde el archivo en valores JavaScript que pueden recorrerse o modificarse.

`map()` crea un nuevo arreglo. Para el ID objetivo devuelve una copia combinada `{ ...p, ...nuevosDatos }`; para los demás devuelve el elemento sin cambios.

`fs.writeFileSync()` persiste el arreglo actualizado. `JSON.stringify(..., null, 2)` lo convierte nuevamente a JSON legible.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 5 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  res.send('Producto actualizado');
});
```

### Explicación detallada
Este microbloque completa una parte del comportamiento sin introducir una responsabilidad adicional distinta de la ya explicada; observa cómo se conecta con el bloque anterior.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 6 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

### Explicación detallada
`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Crear el archivo auxiliar `productos.json`
Crea este archivo en la ruta exacta indicada por el Inicio operativo. No lo pegues dentro de `app.js`.

```json
[
  {
    "id": 1,
    "nombre": "Laptop",
    "precio": 2500
  },
  {
    "id": 2,
    "nombre": "Mouse",
    "precio": 100
  }
]
```

**Interpretación:** este archivo forma parte del estado que la aplicación leerá o del módulo que `app.js` importará. Su ubicación debe coincidir con la ruta usada en el código.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `parseInt()` | Participa en combinar el id de la url con los datos del body para modificar solo el producto coincidente. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `req.params.id` | Participa en combinar el id de la url con los datos del body para modificar solo el producto coincidente. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `req.body` | Participa en combinar el id de la url con los datos del body para modificar solo el producto coincidente. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `Array.map()` | Participa en combinar el id de la url con los datos del body para modificar solo el producto coincidente. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `operador spread` | Participa en combinar el id de la url con los datos del body para modificar solo el producto coincidente. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `let data` | Participa en combinar el id de la url con los datos del body para modificar solo el producto coincidente. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const fs = require('fs');
const app = express();

app.use(express.json());

app.put('/productos/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const nuevosDatos = req.body;
  let data = JSON.parse(fs.readFileSync('./productos.json'));
  data = data.map(p => p.id === id ? { ...p, ...nuevosDatos } : p);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
  res.send('Producto actualizado');
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Ejecuta `cd ejemplos/EJ09` y luego `node app.js`.
2. Envía `PUT http://localhost:3001/productos/2`.
3. Body JSON: `{"precio":120}`.
4. Comprueba `Producto actualizado`.
5. Abre `productos.json` y verifica que el producto con id 2 ahora tenga precio 120.
6. Detén con `Ctrl+C`.

## Resultado esperado
La respuesta indica “Producto actualizado” y el producto 2 conserva sus campos previos pero cambia `precio` a 120.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Comparar el ID numérico del JSON con `req.params.id` sin convertirlo; la comparación estricta puede no coincidir.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Convertir el parámetro con `parseInt(req.params.id)` antes de compararlo con `p.id`.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T09 — Tarea espejo: Actualizar un item del inventario
### Dónde realizar la tarea
Crea `servidor-express/tareas/T09/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ09` para poder compararlo después.

### Enunciado
Implementa `PUT /inventario/:id` para actualizar los campos enviados en el body solo en el registro cuyo ID coincide.

### Archivos/artefactos a modificar
- `app.js`
- `inventario.json`

### Restricción
Debe conservar campos no enviados mediante `{ ...item, ...nuevosDatos }`.

### Pista
Convierte el ID a número y usa `map()` con un operador ternario.

### Evidencia que debes mostrar
Actualizar solo `precio` no debe borrar `nombre` ni `id`.

### Cómo saber si está correcta
El archivo cambia únicamente el elemento objetivo y sigue siendo JSON válido.

# EJ10 — CRUD Delete e integración completa

## Qué vamos a construir / resolver
Eliminar un producto por ID con `filter()` y consolidar el CRUD completo para probar GET, POST, PUT y DELETE.

## Qué aprenderás aquí
- app.delete()
- Array.filter()
- !==
- integración CRUD
- pruebas HTTP

## Archivos, recursos o artefactos involucrados
- `app.js`
- `productos.json`

## Inicio operativo — dónde trabajar antes de escribir código

**Carpeta exacta:** `servidor-express/ejemplos/EJ10/`  
**Acción inicial:** crea la carpeta `EJ10` dentro de `ejemplos`.  
**Archivos que debes crear:** `app.js`, `productos.json`.  
**Archivo activo para los microbloques:** `app.js`.  

Crea `app.js` y `productos.json` en la misma carpeta. Este ejemplo integra GET, POST, PUT y DELETE en un solo servidor.

Tu estructura para este ejemplo debe verse así:

```text
EJ10/
|-- app.js
`-- productos.json
```

**Dónde insertar el código:** el primer bloque “Escribe ahora” empieza en la línea 1 de `app.js`. Cada bloque siguiente se agrega inmediatamente debajo del anterior. No pegues estos bloques en `package.json`.

## Paso 1 — Comprender la necesidad
### Problema actual
El comportamiento anterior todavía no resuelve la habilidad específica de este ejemplo.

### Qué necesitamos crear/modificar
El archivo `app.js` y, cuando corresponda, los archivos auxiliares listados arriba.

### Por qué lo necesitamos
Porque Express responde según las rutas y middlewares que registremos; cada microbloque agrega una responsabilidad observable.

### Qué responsabilidad tendrá
Producir exactamente el comportamiento indicado en el objetivo sin introducir componentes ajenos a la sesión.

## Paso 2 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` desde la línea 1
```javascript
const express = require('express');
const fs = require('fs');
const app = express();
```

### Explicación detallada
`require('express')` carga el paquete Express instalado por npm; `express()` crea la aplicación que registrará rutas, middleware y el servidor HTTP.

`require('fs')` carga el módulo de archivos de Node.js; se utiliza aquí para leer y escribir el archivo JSON de la práctica.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 3 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
app.use(express.json());

app.get('/productos', (req, res) => {
  const data = fs.readFileSync('./productos.json');
```

### Explicación detallada
`express.json()` es middleware incorporado que interpreta cuerpos JSON antes de que una ruta lea `req.body`.

`app.get(PATH, HANDLER)` registra una ruta que responde únicamente a solicitudes HTTP GET para ese path.

`fs.readFileSync('./...json')` lee el archivo de manera síncrona. En este laboratorio simple permite concentrarnos en el flujo CRUD.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 4 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  res.json(JSON.parse(data));
});
```

### Explicación detallada
`JSON.parse()` convierte el contenido JSON leído desde el archivo en valores JavaScript que pueden recorrerse o modificarse.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 5 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript

app.post('/productos', (req, res) => {
  const nuevo = req.body;
  const data = JSON.parse(fs.readFileSync('./productos.json'));
```

### Explicación detallada
`app.post()` registra una ruta POST; en esta sesión se usa cuando el cliente envía datos que deben procesarse o almacenarse.

`req.body` contiene el objeto JSON enviado por el cliente, siempre que `express.json()` se haya registrado antes.

`fs.readFileSync('./...json')` lee el archivo de manera síncrona. En este laboratorio simple permite concentrarnos en el flujo CRUD.

`JSON.parse()` convierte el contenido JSON leído desde el archivo en valores JavaScript que pueden recorrerse o modificarse.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 6 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  data.push(nuevo);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
  res.status(201).send('Producto agregado');
});
```

### Explicación detallada
`push()` agrega el nuevo objeto al final del arreglo existente sin borrar los registros previos.

`fs.writeFileSync()` persiste el arreglo actualizado. `JSON.stringify(..., null, 2)` lo convierte nuevamente a JSON legible.

El estado 201 comunica que la solicitud creó un nuevo recurso correctamente.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 7 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript

app.put('/productos/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const nuevosDatos = req.body;
```

### Explicación detallada
`app.put()` registra la operación de actualización. El `:id` de la URL permite identificar qué producto se modificará.

`req.params` reúne los parámetros dinámicos de la URL. `req.params.id` llega inicialmente como texto.

`parseInt()` convierte el parámetro de ruta a número para compararlo de forma coherente con los IDs numéricos del JSON.

`req.body` contiene el objeto JSON enviado por el cliente, siempre que `express.json()` se haya registrado antes.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 8 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  let data = JSON.parse(fs.readFileSync('./productos.json'));
  data = data.map(p => p.id === id ? { ...p, ...nuevosDatos } : p);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
  res.send('Producto actualizado');
```

### Explicación detallada
`fs.readFileSync('./...json')` lee el archivo de manera síncrona. En este laboratorio simple permite concentrarnos en el flujo CRUD.

`JSON.parse()` convierte el contenido JSON leído desde el archivo en valores JavaScript que pueden recorrerse o modificarse.

`map()` crea un nuevo arreglo. Para el ID objetivo devuelve una copia combinada `{ ...p, ...nuevosDatos }`; para los demás devuelve el elemento sin cambios.

`fs.writeFileSync()` persiste el arreglo actualizado. `JSON.stringify(..., null, 2)` lo convierte nuevamente a JSON legible.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 9 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});

app.delete('/productos/:id', (req, res) => {
  const id = parseInt(req.params.id);
```

### Explicación detallada
`app.delete()` registra la operación de eliminación. La ruta recibe un ID y conserva solo los elementos que no coinciden con ese ID.

`req.params` reúne los parámetros dinámicos de la URL. `req.params.id` llega inicialmente como texto.

`parseInt()` convierte el parámetro de ruta a número para compararlo de forma coherente con los IDs numéricos del JSON.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 10 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
  let data = JSON.parse(fs.readFileSync('./productos.json'));
  data = data.filter(p => p.id !== id);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
  res.send('Producto eliminado');
```

### Explicación detallada
`fs.readFileSync('./...json')` lee el archivo de manera síncrona. En este laboratorio simple permite concentrarnos en el flujo CRUD.

`JSON.parse()` convierte el contenido JSON leído desde el archivo en valores JavaScript que pueden recorrerse o modificarse.

`filter()` construye un arreglo nuevo con los elementos que cumplen la condición. Usar `!== id` conserva todos menos el producto eliminado.

`fs.writeFileSync()` persiste el arreglo actualizado. `JSON.stringify(..., null, 2)` lo convierte nuevamente a JSON legible.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 11 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
```

### Explicación detallada
`app.listen(3001, callback)` pone la aplicación a escuchar en el puerto 3001 y ejecuta el callback cuando el servidor se inicia.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Paso 12 — Microbloque
### Estado antes
El archivo aún no contiene esta responsabilidad.

### Escribe ahora en `app.js` inmediatamente debajo del bloque anterior
```javascript
});
```

### Explicación detallada
Este microbloque completa una parte del comportamiento sin introducir una responsabilidad adicional distinta de la ya explicada; observa cómo se conecta con el bloque anterior.

### Elementos nuevos
Revisa los identificadores y métodos que aparecen por primera vez en este bloque; cada uno participa en el flujo solicitud → procesamiento → respuesta.

### Estado después
El servidor ya incorpora esta parte del comportamiento.

### Por qué avanzamos
El siguiente bloque puede apoyarse en esta responsabilidad ya registrada.

## Crear el archivo auxiliar `productos.json`
Crea este archivo en la ruta exacta indicada por el Inicio operativo. No lo pegues dentro de `app.js`.

```json
[
  {
    "id": 1,
    "nombre": "Laptop",
    "precio": 2500
  },
  {
    "id": 2,
    "nombre": "Mouse",
    "precio": 100
  }
]
```

**Interpretación:** este archivo forma parte del estado que la aplicación leerá o del módulo que `app.js` importará. Su ubicación debe coincidir con la ruta usada en el código.

## Auditoría del artefacto construido
| Elemento / fragmento | Qué hace | Por qué existe | Efecto |
|---|---|---|---|
| `app.delete()` | Participa en eliminar un producto por id con `filter()` y consolidar el crud completo para probar get, post, put y delete. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `Array.filter()` | Participa en eliminar un producto por id con `filter()` y consolidar el crud completo para probar get, post, put y delete. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `!==` | Participa en eliminar un producto por id con `filter()` y consolidar el crud completo para probar get, post, put y delete. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `integración CRUD` | Participa en eliminar un producto por id con `filter()` y consolidar el crud completo para probar get, post, put y delete. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |
| `pruebas HTTP` | Participa en eliminar un producto por id con `filter()` y consolidar el crud completo para probar get, post, put y delete. | Existe para materializar la habilidad del ejemplo | Produce un comportamiento observable |

## Archivo / artefacto completo al terminar este ejemplo
```javascript
const express = require('express');
const fs = require('fs');
const app = express();

app.use(express.json());

app.get('/productos', (req, res) => {
  const data = fs.readFileSync('./productos.json');
  res.json(JSON.parse(data));
});

app.post('/productos', (req, res) => {
  const nuevo = req.body;
  const data = JSON.parse(fs.readFileSync('./productos.json'));
  data.push(nuevo);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
  res.status(201).send('Producto agregado');
});

app.put('/productos/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const nuevosDatos = req.body;
  let data = JSON.parse(fs.readFileSync('./productos.json'));
  data = data.map(p => p.id === id ? { ...p, ...nuevosDatos } : p);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
  res.send('Producto actualizado');
});

app.delete('/productos/:id', (req, res) => {
  const id = parseInt(req.params.id);
  let data = JSON.parse(fs.readFileSync('./productos.json'));
  data = data.filter(p => p.id !== id);
  fs.writeFileSync('./productos.json', JSON.stringify(data, null, 2));
  res.send('Producto eliminado');
});

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
```

## Antes de ejecutar o validar: predicción
1. ¿Qué ruta o proceso debería responder?
2. ¿Qué código de estado esperas?
3. ¿Qué cambiará en un archivo JSON, si este ejemplo modifica datos?
4. ¿Qué mensaje aparecerá en la terminal?

## Ejecuta / valida
1. Ejecuta `cd ejemplos/EJ10` y luego `node app.js`.
2. Prueba `GET http://localhost:3001/productos`.
3. Prueba un POST con un producto nuevo.
4. Prueba `PUT http://localhost:3001/productos/2` con un cambio de precio.
5. Prueba `DELETE http://localhost:3001/productos/1`.
6. Vuelve a ejecutar GET y abre `productos.json` para comprobar el estado final.
7. Detén con `Ctrl+C`.

## Resultado esperado
El CRUD permite listar, crear, actualizar y eliminar. Tras DELETE de ID 1, el archivo ya no contiene ese producto.

## Cómo interpretarlo
Si observas el resultado esperado, la ruta/middleware/operación fue registrada en el orden correcto y Express completó el ciclo solicitud-respuesta. Si además se modifica un JSON, abre el archivo y confirma el cambio persistido.

## Variación A
Cambia un valor de prueba (por ejemplo el ID o un campo del JSON) y predice cómo debería cambiar la respuesta sin alterar la estructura del ejemplo.

## Error o caso límite controlado
Usar `filter(p => p.id === id)`; esto conserva únicamente el producto que se pretendía eliminar.

## Por qué ocurre
El error aparece cuando una dependencia, ruta, tipo de dato u orden necesario para este ejemplo no coincide con lo que el código espera.

## Corrección razonada
Para eliminar el coincidente, conservar los demás con `filter(p => p.id !== id)`.

## Qué debes poder explicar con tus palabras
- Qué recibe Express en esta solicitud.
- Qué parte del código decide la respuesta.
- Qué estado cambia y cuál permanece igual.
- Cómo sabes que el resultado es correcto.

## T10 — Tarea espejo: Completar DELETE en un CRUD de inventario
### Dónde realizar la tarea
Crea `servidor-express/tareas/T10/` y realiza allí la tarea. Conserva intacto `ejemplos/EJ10` para poder compararlo después.

### Enunciado
Partiendo de un CRUD de inventario con GET, POST y PUT, agrega `DELETE /inventario/:id` usando `filter()` y guarda el nuevo arreglo.

### Archivos/artefactos a modificar
- `app.js`
- `inventario.json`

### Restricción
No uses `splice()` ni reconstruyas el arreglo manualmente; practica el patrón `filter()`.

### Pista
Conserva los elementos cuyo `item.id !== id`.

### Evidencia que debes mostrar
Después de eliminar ID 1, GET ya no debe devolverlo y los demás registros deben conservarse.

### Cómo saber si está correcta
DELETE responde 200, el archivo se actualiza y las otras operaciones siguen funcionando.

# Cierre de la sesión
En esta práctica construiste un backend de forma incremental. Primero comprobaste que Express puede levantar un servidor aun cuando todavía no existe una ruta; después registraste endpoints GET, capturaste parámetros y separaste rutas mediante `express.Router()`. Sobre ese flujo agregaste middleware para procesar JSON y continuar con `next()`, y comprobaste cómo un middleware de cuatro parámetros centraliza una respuesta de error. Finalmente convertiste `productos.json` en una fuente de datos simple para practicar Read, Create, Update y Delete con los métodos HTTP correspondientes.

El aprendizaje clave es reconocer el ciclo completo: el cliente envía una solicitud, Express aplica middleware, una ruta decide qué operación realizar, el servidor modifica o consulta el estado y finalmente envía una respuesta que debe poder validarse. También corregiste fallos habituales: rutas sin `:id`, `next()` omitido, JSON no procesado, IDs con tipos distintos, rutas de archivos incorrectas y condiciones de `filter()` invertidas.

Antes de la siguiente sesión, repasa la diferencia entre `req.params` y `req.body`, el orden de los middleware y la relación POST/Create, GET/Read, PUT/Update y DELETE/Delete. Si deseas comparar tu resultado con una versión ya materializada, puedes utilizar el laboratorio de FASE 15 como recurso opcional posterior.

Recursos de continuidad: **Lideratec Academy**, https://lideratecacademy.com/blog/ y https://www.youtube.com/@LideratecAcademy.
