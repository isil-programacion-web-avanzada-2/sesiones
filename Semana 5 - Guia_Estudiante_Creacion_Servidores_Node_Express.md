# Guía del estudiante - Creación de servidores web con Node.js y Express

**Curso:** Programación Web Avanzada  
**Sesión:** Creación de servidores web con Node.js y Express  
**Duración sugerida:** 2 horas académicas

---

## Control previo de la sesión

**Conceptos trabajados en esta guía:** Node.js, Express, npm, versión LTS, `app.js`, `app.listen`, rutas, `app.get`, parámetros en rutas, `express.Router`, middleware, `express.json()`, `next()`, manejo de errores, CRUD, GET, POST, PUT, DELETE, `productos.json`, `fs.readFileSync`, `fs.writeFileSync`, `JSON.parse`, `JSON.stringify` y Postman.

**Regla de trabajo:** esta práctica usa únicamente los conceptos de la sesión. La actualización técnica aplicada es operativa: usar Node.js LTS vigente y escribir correctamente los comandos `node -v` y `npm -v`.

---

## Propósito de la práctica

Construir paso a paso un servidor web básico con Node.js y Express, crear rutas, usar middleware, implementar operaciones CRUD sobre un archivo `productos.json` y validar el resultado mediante solicitudes HTTP.

---

## Resultado de aprendizaje observable

Al finalizar la práctica, podrás **construir y validar** un servidor Express básico que responda rutas HTTP, procese datos JSON y ejecute operaciones CRUD sobre un archivo de productos.

---

## Requisitos previos mínimos

Antes de iniciar, verifica que tienes:

- Node.js instalado en versión LTS vigente.
- npm disponible.
- Visual Studio Code o un editor equivalente.
- Terminal o consola.
- Postman, Thunder Client, Insomnia o una herramienta similar para probar solicitudes HTTP.

Verifica Node.js y npm:

~~~bash
node -v
npm -v
~~~

**Importante:** usa guion normal `-`, no guion largo tipográfico.

---

## Indicaciones generales

1. Trabaja en una carpeta limpia para esta sesión.
2. Copia los ejemplos guiados antes de resolver las actividades.
3. Guarda cada archivo antes de probarlo.
4. Ejecuta el servidor desde la carpeta del proyecto.
5. Prueba cada ruta antes de continuar.
6. Registra tus respuestas en los espacios indicados.
7. No avances al CRUD si el servidor básico aún no responde.

---

# Desarrollo paso a paso

## Bloque 1 - Comprender el flujo del servidor Express

**Objetivo del bloque:**  
Identificar el recorrido de una solicitud HTTP dentro de un servidor Express.

**Concepto trabajado:**  
Node.js, Express, solicitud HTTP, servidor, respuesta HTTP.

**Explicación breve:**  
Node.js permite ejecutar JavaScript en el servidor. Express facilita la creación de servidores web mediante rutas, middleware y respuestas HTTP.

**Ejemplo guiado:**

~~~text
Cliente / Postman
      |
      v
Solicitud HTTP
      |
      v
Servidor Express
      |
      v
Ruta o middleware
      |
      v
Respuesta HTTP
~~~

**Qué debes observar:**  
El servidor no aparece aislado. Forma parte de un flujo donde un cliente solicita y Express responde.

**Actividad para ti:**  
Dibuja el mismo flujo y agrega dónde se ubicaría `productos.json`.

**Instrucciones:**

1. Copia el flujo base.
2. Agrega un bloque llamado `productos.json`.
3. Conecta `Servidor Express` con `productos.json`.
4. Explica brevemente qué representa esa conexión.

**Espacio para responder:**

- ¿Qué elemento envía la solicitud?
- ¿Qué elemento procesa la solicitud?
- ¿Qué elemento almacena los productos?
- ¿Qué elemento recibe la respuesta?

**Resultado esperado:**  
Debes representar un flujo cliente-servidor con una fuente de datos simple.

**Error común a evitar:**  
Confundir Node.js con Express. Node.js es el entorno; Express es el framework.

**Mini reto:**  
Agrega en el flujo las palabras `GET`, `POST`, `PUT` y `DELETE`.

---

## Bloque 2 - Crear el proyecto Express

**Objetivo del bloque:**  
Crear la carpeta del proyecto, inicializar npm e instalar Express.

**Concepto trabajado:**  
npm, `npm init -y`, `npm install express`.

**Explicación breve:**  
npm permite gestionar dependencias. Express se instala dentro del proyecto para construir el servidor.

**Ejemplo guiado:**

~~~bash
mkdir servidor-express
cd servidor-express
npm init -y
npm install express
~~~

**Qué debes observar:**  
Después de ejecutar los comandos, deben aparecer archivos y carpetas del proyecto.

**Actividad para ti:**  
Verifica la estructura creada.

**Instrucciones:**

1. Ejecuta los comandos en la terminal.
2. Abre la carpeta en tu editor.
3. Ubica `package.json`.
4. Ubica `node_modules`.

**Espacio para responder:**

- ¿Qué archivo crea `npm init -y`?
- ¿Qué dependencia se instala con `npm install express`?
- ¿En qué carpeta ejecutaste los comandos?
- ¿Qué ocurrió si ejecutaste el comando fuera de la carpeta correcta?

**Resultado esperado:**  
El proyecto debe contener `package.json`, `package-lock.json` y `node_modules`.

**Error común a evitar:**  
Ejecutar `npm install express` fuera de la carpeta del proyecto.

**Mini reto:**  
Abre `package.json` y ubica la sección donde aparece Express.

---

## Bloque 3 - Crear el servidor base con app.js

**Objetivo del bloque:**  
Levantar un servidor Express mínimo.

**Concepto trabajado:**  
`app.js`, `require('express')`, `express()`, `app.listen`.

**Explicación breve:**  
El archivo `app.js` será el punto de entrada del servidor. Allí se importa Express, se crea la aplicación y se define el puerto.

**Ejemplo guiado:**

Crea el archivo:

~~~text
app.js
~~~

Agrega este código:

~~~js
const express = require('express');
const app = express();

app.listen(3001, () => {
  console.log('Servidor escuchando en http://localhost:3001');
});
~~~

Ejecuta:

~~~bash
node app.js
~~~

**Qué debes observar:**  
La terminal debe mostrar el mensaje de servidor activo.

**Actividad para ti:**  
Ejecuta el servidor y abre el navegador en el puerto indicado.

**Instrucciones:**

1. Guarda `app.js`.
2. Ejecuta `node app.js`.
3. Abre el navegador.
4. Ingresa a `http://localhost:3001`.

**Espacio para responder:**

- ¿Qué mensaje aparece en la terminal?
- ¿Qué aparece en el navegador?
- ¿El servidor está activo?
- ¿Existe una ruta definida para `/`?

**Resultado esperado:**  
El servidor debe quedar escuchando en el puerto `3001`.

**Error común a evitar:**  
Pensar que `Cannot GET /` significa que Express falló. Puede significar que falta la ruta raíz.

**Mini reto:**  
Cambia el mensaje del `console.log` y vuelve a ejecutar el servidor.

---

## Bloque 4 - Crear rutas básicas

**Objetivo del bloque:**  
Definir rutas GET y enviar respuestas simples desde Express.

**Concepto trabajado:**  
Rutas, `app.get`, `req`, `res`.

**Explicación breve:**  
Una ruta indica cómo responde el servidor cuando recibe una solicitud en una URL específica.

**Ejemplo guiado:**

Agrega estas rutas antes de `app.listen`:

~~~js
app.get('/', (req, res) => {
  res.send('Bienvenido al servidor Express');
});

app.get('/productos', (req, res) => {
  res.send('Listado de productos');
});
~~~

**Qué debes observar:**  
Cada ruta responde con un mensaje diferente.

**Actividad para ti:**  
Prueba las dos rutas en el navegador o en Postman.

**Instrucciones:**

1. Guarda el archivo.
2. Reinicia el servidor si no usas Nodemon.
3. Prueba `http://localhost:3001`.
4. Prueba `http://localhost:3001/productos`.

**Espacio para responder:**

- ¿Qué responde la ruta `/`?
- ¿Qué responde la ruta `/productos`?
- ¿Qué función cumple `res.send()`?
- ¿Qué representa `req`?

**Resultado esperado:**  
Debes obtener respuestas diferentes según la URL solicitada.

**Error común a evitar:**  
Escribir una ruta diferente en el navegador, por ejemplo `/producto` en lugar de `/productos`.

**Mini reto:**  
Crea una ruta `/estado` que responda `Servidor activo`.

---

## Bloque 5 - Capturar parámetros en rutas

**Objetivo del bloque:**  
Usar parámetros dinámicos en URLs.

**Concepto trabajado:**  
`/productos/:id`, `req.params.id`.

**Explicación breve:**  
Los parámetros permiten capturar valores desde la URL. Express usa `:id` para indicar que una parte de la ruta es dinámica.

**Ejemplo guiado:**

~~~js
app.get('/productos/:id', (req, res) => {
  const id = req.params.id;
  res.send(`Producto con ID: ${id}`);
});
~~~

**Qué debes observar:**  
El valor escrito en la URL aparece en la respuesta.

**Actividad para ti:**  
Prueba la misma ruta con tres IDs distintos.

**Instrucciones:**

1. Guarda el archivo.
2. Reinicia el servidor si corresponde.
3. Prueba `/productos/1`.
4. Prueba `/productos/5`.
5. Prueba `/productos/20`.

**Espacio para responder:**

- ¿Qué parte de la URL cambia?
- ¿Qué valor captura `req.params.id`?
- ¿Por qué no conviene crear una ruta diferente para cada producto?
- ¿Qué ocurre si escribes `/productos/id`?

**Resultado esperado:**  
La respuesta debe mostrar el ID enviado en la URL.

**Error común a evitar:**  
Definir la ruta como `/productos/id` en vez de `/productos/:id`.

**Mini reto:**  
Modifica el mensaje para que diga: `Consultando producto número X`.

---

## Bloque 6 - Agregar middleware

**Objetivo del bloque:**  
Procesar solicitudes antes de que lleguen a la ruta final.

**Concepto trabajado:**  
Middleware, `express.json()`, `next()`.

**Explicación breve:**  
Un middleware se ejecuta entre la solicitud y la respuesta. Puede procesar JSON, registrar información o preparar la solicitud antes de continuar.

**Ejemplo guiado:**

Agrega antes de las rutas:

~~~js
app.use(express.json());

app.use((req, res, next) => {
  console.log(`Ruta solicitada: ${req.url}`);
  next();
});
~~~

**Qué debes observar:**  
Cada vez que pruebas una ruta, la terminal debe registrar la URL solicitada.

**Actividad para ti:**  
Prueba dos rutas y observa la terminal.

**Instrucciones:**

1. Guarda el archivo.
2. Reinicia el servidor si corresponde.
3. Ingresa a `/productos`.
4. Ingresa a `/productos/2`.
5. Observa la terminal.

**Espacio para responder:**

- ¿Qué imprime la terminal?
- ¿Para qué sirve `express.json()`?
- ¿Qué pasaría si se omite `next()`?
- ¿En qué parte del flujo se ejecuta el middleware?

**Resultado esperado:**  
La terminal muestra las rutas solicitadas.

**Error común a evitar:**  
Olvidar `next()` en un middleware personalizado.

**Mini reto:**  
Modifica el middleware para mostrar también el método HTTP.

---

## Bloque 7 - Crear productos.json y leer productos

**Objetivo del bloque:**  
Leer datos desde un archivo JSON y enviarlos como respuesta.

**Concepto trabajado:**  
CRUD - Read, `productos.json`, `fs.readFileSync`, `JSON.parse`, `res.json`.

**Explicación breve:**  
Para practicar CRUD sin una base de datos externa, se usará un archivo `productos.json` como fuente de datos simple.

**Ejemplo guiado:**

Crea el archivo:

~~~text
productos.json
~~~

Agrega este contenido:

~~~json
[
  { "id": 1, "nombre": "Laptop", "precio": 2500 },
  { "id": 2, "nombre": "Mouse", "precio": 100 }
]
~~~

En `app.js`, importa `fs`:

~~~js
const fs = require('fs');
~~~

Crea la ruta de lectura:

~~~js
app.get('/productos', (req, res) => {
  const data = fs.readFileSync('./productos.json');
  res.json(JSON.parse(data));
});
~~~

**Qué debes observar:**  
La ruta `/productos` debe responder con un arreglo JSON.

**Actividad para ti:**  
Prueba `GET /productos` desde Postman.

**Instrucciones:**

1. Guarda `productos.json`.
2. Guarda `app.js`.
3. Ejecuta el servidor.
4. Envía una solicitud GET a `/productos`.

**Espacio para responder:**

- ¿Qué datos devuelve el servidor?
- ¿Qué función lee el archivo?
- ¿Qué función convierte el texto del archivo en JSON?
- ¿Qué función envía la respuesta como JSON?

**Resultado esperado:**  
Debes recibir una lista de productos.

**Error común a evitar:**  
Escribir mal la ruta del archivo `./productos.json`.

**Mini reto:**  
Agrega manualmente un tercer producto al archivo y vuelve a probar la ruta.

---

## Bloque 8 - Crear un producto con POST

**Objetivo del bloque:**  
Recibir datos JSON desde una solicitud y guardarlos en `productos.json`.

**Concepto trabajado:**  
CRUD - Create, POST, `req.body`, `fs.writeFileSync`, `JSON.stringify`.

**Explicación breve:**  
POST se usa para crear un nuevo registro. Express recibe el producto desde `req.body`, lo agrega al arreglo y guarda el archivo actualizado.

**Ejemplo guiado:**

~~~js
app.post('/productos', (req, res) => {
  const nuevo = req.body;
  const data = JSON.parse(fs.readFileSync('./productos.json'));

  data.push(nuevo);

  fs.writeFileSync('./productos.json', JSON.stringify(data));
  res.status(201).send('Producto agregado');
});
~~~

En Postman, envía:

~~~json
{
  "id": 3,
  "nombre": "Teclado",
  "precio": 150
}
~~~

**Qué debes observar:**  
Después de enviar la solicitud POST, el archivo debe incluir el nuevo producto.

**Actividad para ti:**  
Crea un producto diferente.

**Instrucciones:**

1. En Postman selecciona método POST.
2. Usa la ruta `/productos`.
3. En Body selecciona raw y JSON.
4. Envía un producto nuevo con `id`, `nombre` y `precio`.
5. Verifica el resultado con GET.

**Espacio para responder:**

- ¿Qué método HTTP usaste?
- ¿Qué datos enviaste en el cuerpo?
- ¿Qué respuesta devolvió el servidor?
- ¿El producto aparece luego en GET `/productos`?

**Resultado esperado:**  
El servidor agrega el producto y responde con estado de creación.

**Error común a evitar:**  
Enviar el cuerpo sin formato JSON o sin activar `express.json()`.

**Mini reto:**  
Agrega un producto con otro precio y verifica que quede guardado.

---

## Bloque 9 - Actualizar y eliminar productos

**Objetivo del bloque:**  
Modificar y eliminar productos usando ID desde la URL.

**Concepto trabajado:**  
CRUD - Update y Delete, PUT, DELETE, `req.params.id`, `map()`, `filter()`.

**Explicación breve:**  
PUT actualiza datos existentes. DELETE elimina un registro. Ambas operaciones usan el ID enviado en la URL.

**Ejemplo guiado:**

Ruta PUT:

~~~js
app.put('/productos/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const nuevosDatos = req.body;
  let data = JSON.parse(fs.readFileSync('./productos.json'));

  data = data.map(p => p.id === id ? { ...p, ...nuevosDatos } : p);

  fs.writeFileSync('./productos.json', JSON.stringify(data));
  res.send('Producto actualizado');
});
~~~

Ruta DELETE:

~~~js
app.delete('/productos/:id', (req, res) => {
  const id = parseInt(req.params.id);
  let data = JSON.parse(fs.readFileSync('./productos.json'));

  data = data.filter(p => p.id !== id);

  fs.writeFileSync('./productos.json', JSON.stringify(data));
  res.send('Producto eliminado');
});
~~~

**Qué debes observar:**  
PUT modifica el producto indicado. DELETE lo elimina del arreglo.

**Actividad para ti:**  
Actualiza un producto y luego elimina otro.

**Instrucciones:**

1. Selecciona método PUT.
2. Usa `/productos/3` o el ID que agregaste.
3. Envía un JSON con un nuevo precio.
4. Verifica con GET.
5. Selecciona método DELETE.
6. Usa el ID del producto que deseas eliminar.
7. Verifica nuevamente con GET.

**Espacio para responder:**

- ¿Qué producto actualizaste?
- ¿Qué campo modificaste?
- ¿Qué producto eliminaste?
- ¿Qué cambió en el resultado de GET `/productos`?

**Resultado esperado:**  
Un producto cambia sus datos y otro deja de aparecer en la lista.

**Error común a evitar:**  
No convertir `req.params.id` a número antes de compararlo con `p.id`.

**Mini reto:**  
Actualiza solo el campo `nombre` de un producto sin cambiar su precio.

---

# Actividades guiadas para resolver

## Actividad 1 - Reconocimiento

**Objetivo:**  
Identificar las partes básicas del servidor.

**Código base:**

~~~js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Bienvenido');
});

app.listen(3001, () => {
  console.log('Servidor activo');
});
~~~

**Qué modificar:**  
Agrega una ruta `/estado`.

**Qué observar:**  
El servidor debe responder un mensaje cuando visites `/estado`.

**Qué responder:**

- ¿Qué método HTTP usaste?
- ¿Qué endpoint agregaste?
- ¿Qué respuesta enviaste?
- ¿Dónde colocaste la ruta?

**Resultado esperado:**  
La ruta `/estado` responde correctamente.

**Criterio de validación rápida:**  
El navegador o Postman muestra el mensaje esperado.

---

## Actividad 2 - Modificación guiada

**Objetivo:**  
Trabajar con parámetros en rutas.

**Código base:**

~~~js
app.get('/productos/:id', (req, res) => {
  const id = req.params.id;
  res.send(`Producto con ID: ${id}`);
});
~~~

**Qué modificar:**  
Cambia la respuesta para mostrar un mensaje más descriptivo.

**Qué observar:**  
El ID debe seguir apareciendo en la respuesta.

**Qué responder:**

- ¿Qué parte de la URL se captura?
- ¿Qué variable guarda el ID?
- ¿Qué mensaje devuelve tu ruta?
- ¿Qué valores probaste?

**Resultado esperado:**  
La ruta captura valores dinámicos desde la URL.

**Criterio de validación rápida:**  
Al cambiar el número en la URL, cambia el número de la respuesta.

---

## Actividad 3 - Aplicación

**Objetivo:**  
Crear un producto usando POST.

**Código base:**

~~~js
app.post('/productos', (req, res) => {
  const nuevo = req.body;
  const data = JSON.parse(fs.readFileSync('./productos.json'));

  data.push(nuevo);

  fs.writeFileSync('./productos.json', JSON.stringify(data));
  res.status(201).send('Producto agregado');
});
~~~

**Qué modificar:**  
Envía un producto distinto desde Postman.

**Qué observar:**  
El producto debe aparecer luego al consultar GET `/productos`.

**Qué responder:**

- ¿Qué JSON enviaste?
- ¿Qué respuesta devolvió el servidor?
- ¿Qué cambió en `productos.json`?
- ¿Qué ruta usaste para validar?

**Resultado esperado:**  
El nuevo producto queda guardado.

**Criterio de validación rápida:**  
GET `/productos` muestra el producto agregado.

---

## Actividad 4 - Integración

**Objetivo:**  
Validar el CRUD completo.

**Caso completo:**  
Tienes un archivo `productos.json` con productos. Debes probar lectura, creación, actualización y eliminación.

**Qué modificar:**  
Agrega un producto, actualiza su precio y luego elimínalo.

**Qué observar:**  
Cada operación debe modificar el resultado de GET `/productos`.

**Qué responder:**

- ¿Qué producto agregaste?
- ¿Qué precio actualizaste?
- ¿Qué ID eliminaste?
- ¿Qué operación confirmó que el producto ya no existe?
- ¿Qué método HTTP corresponde a cada paso?

**Resultado esperado:**  
El ciclo CRUD se completa correctamente.

**Criterio de validación rápida:**  
El servidor responde correctamente a GET, POST, PUT y DELETE.

---

# Preguntas de validación

1. ¿Qué diferencia hay entre Node.js y Express?
2. ¿Qué función cumple `app.listen`?
3. ¿Qué significa `Cannot GET /`?
4. ¿Para qué sirve `express.json()`?
5. ¿Qué hace `next()`?
6. ¿Qué método HTTP se usa para leer productos?
7. ¿Qué método HTTP se usa para crear productos?
8. ¿Qué método HTTP se usa para actualizar productos?
9. ¿Qué método HTTP se usa para eliminar productos?
10. ¿Por qué se usa Postman en esta práctica?

---

# Errores comunes a evitar

- Ejecutar comandos fuera de la carpeta del proyecto.
- Copiar guiones incorrectos en `node -v` o `npm -v`.
- Olvidar guardar `app.js`.
- Olvidar reiniciar el servidor si no se usa Nodemon.
- Escribir `/productos/id` en lugar de `/productos/:id`.
- Omitir `express.json()` antes de usar `req.body`.
- Olvidar `next()` en un middleware.
- Enviar JSON inválido desde Postman.
- Confundir GET, POST, PUT y DELETE.
- Escribir mal la ruta de `productos.json`.

---

# Checklist final de aprendizaje

Marca cada afirmación cuando puedas cumplirla:

- [ ] Puedo identificar qué es Node.js.
- [ ] Puedo diferenciar Node.js de Express.
- [ ] Puedo explicar para qué sirve npm.
- [ ] Puedo crear un proyecto Express desde terminal.
- [ ] Puedo construir un servidor básico con `app.js`.
- [ ] Puedo crear rutas con `app.get`.
- [ ] Puedo usar parámetros como `/productos/:id`.
- [ ] Puedo explicar qué hace un middleware.
- [ ] Puedo usar `express.json()` para leer JSON.
- [ ] Puedo relacionar GET, POST, PUT y DELETE con CRUD.
- [ ] Puedo leer datos desde `productos.json`.
- [ ] Puedo crear, actualizar y eliminar productos.
- [ ] Puedo validar rutas con Postman.

---

# Cierre de la práctica

En esta sesión construiste un servidor Express desde cero, definiste rutas, agregaste middleware, trabajaste con parámetros y aplicaste un CRUD básico usando `productos.json`. El objetivo principal es que puedas explicar el flujo completo: solicitud HTTP, middleware, ruta, operación sobre datos y respuesta del servidor.

---

# Referencias de refuerzo

Blog: https://lideratecacademy.com/  
Canal YouTube: https://www.youtube.com/@LideratecAcademy
