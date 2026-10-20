# SESIÓN 08 · FASE 13 · GUÍA DEL ESTUDIANTE (V4)
## Mini proyecto integrador: «Inventario Seguro»

**Curso:** Programación Web Avanzada · **Institución:** ISIL  
**Duración total de clase:** 120 minutos · **Modalidad:** presencial, práctica guiada  
**Naturaleza:** nueva actividad integradora de repaso (sesiones 05: Express/CRUD y 07: JWT/seguridad).

> **Dos temas, un único proyecto, pruebas en terminal en cada etapa.**  
> **Tema 1:** API REST y operaciones CRUD con Node.js + Express.  
> **Tema 2:** autenticación con JWT y protección de rutas (bcrypt como apoyo al login).  
> Angular, MongoDB, CORS y autorización por roles **no forman parte de esta práctica de 120 minutos**.

## 1. ¿Qué construiremos y para qué?

Una tienda de útiles escolares tiene un inventario que todos pueden **consultar**; únicamente quien inicia sesión puede **crear, editar o eliminar** productos. Construirás UNA sola API y la mejorarás en pequeños pasos: primero responderá consultas, después modificará productos y finalmente pedirá un token para modificar datos.

**Al finalizar podrás demostrar:** `GET /api/productos` → **200**; `POST /api/productos` sin token → **401**; `POST /api/login` correcto → **200 y token**; `POST /api/productos` con token → **201**; `PUT` → **200**; `DELETE` → **204**. Si los datos son inválidos → **400**; si el ID no existe → **404**.

### Mapa del resultado final

```text
CLIENTE HTTP (Thunder Client / Postman)
            │
            ├── GET /api/productos ────────────────► lista JSON (200)
            │
            ├── POST /api/login ──► bcrypt ──► JWT ─► token (200)
            │
            └── POST /api/productos + Bearer token
                       │
                       ▼
               middleware verificarToken
                       │ válido
                       ▼
                Express actualiza lista ───────────► creado (201)
```

**Regla para toda la clase:** utiliza **Terminal A** para mantener el servidor (`npm start`) y **Terminal B** para las verificaciones (`node -e ...`). Cada vez que modifiques código: **Ctrl+C** en Terminal A, luego `npm start` en Terminal A. Nunca escribas un comando de prueba en la terminal donde el servidor sigue ejecutándose.

**Importante:** los productos se guardan en un arreglo en memoria. **Desaparecen los cambios al reiniciar el servidor**. Usaremos esta decisión para concentrarnos en los dos temas de la sesión, no para simular una base de datos.

## 2. Cronograma de una sola clase

| Minutos | Actividad | Evidencia |
|---|---|---|
| 00–08 (8) | Presentación del caso y predicción de respuestas | Diferenciar 200, 201 y 401 |
| 08–20 (12) | Preparar carpeta, npm, archivos y `scripts` | Proyecto configurado |
| 20–53 (33) | **Tema 1:** lista, creación, edición y eliminación | CRUD funcionando sin seguridad todavía |
| 53–83 (30) | **Tema 2:** bcrypt, login, JWT, middleware | Token emitido y cambios protegidos |
| 83–99 (16) | Comprobación completa y tarea espejo | Respuestas HTTP y evidencia |
| 99–115 (16) | Evaluación formativa de los dos temas | Respuestas explicadas |
| 115–120 (5) | Retroalimentación y cierre | Conclusión técnica |
| **Total** | | **120 minutos** |

## 3. Preparación exacta desde una carpeta vacía (min. 08–20)

### PASO 0. Abre el proyecto y la terminal

1. En tu equipo crea una carpeta llamada **`inventario-seguro-s08`**.
2. Abre **Visual Studio Code → Archivo → Abrir carpeta** y selecciona esa carpeta.
3. Abre **Terminal → Nueva terminal**. La terminal debe mostrar esa carpeta como directorio actual.
4. Comprueba Node.js (recomendado Node.js 22 o superior):

```bash
node -v
npm -v
```

**¿Por qué?** `node` ejecuta JavaScript en el servidor; `npm` administra paquetes y comandos del proyecto. Si una orden no se reconoce, corrige la instalación antes de continuar.

**¿Se prueba aquí? SÍ, solo el entorno.** Los dos comandos anteriores deben devolver números de versión. **Todavía NO ejecutes `npm start`**, porque `src/app.js` y `src/server.js` aún no existen.

### PASO 1. Inicializa e instala dependencias: son dos operaciones distintas

**Escribe y pulsa Enter:**

```bash
npm init -y
```

- **Qué hace:** crea el archivo `package.json` en la raíz.
- **Por qué existe:** registra el nombre del proyecto, sus dependencias y sus comandos.
- **Qué NO hace:** no instala Express ni inicia el servidor.
- **Verificación:** aparece `package.json` en el explorador de VS Code.

**Ahora ejecuta:**

```bash
npm install express bcryptjs jsonwebtoken dotenv
```

- `express`: recibe solicitudes HTTP y define rutas CRUD.
- `bcryptjs`: permite comparar una contraseña con un hash, sin almacenarla como texto plano en una aplicación real.
- `jsonwebtoken`: crea y verifica tokens JWT firmados.
- `dotenv`: permite leer valores de un archivo `.env`.
- **Qué cambia:** npm crea `node_modules/` y actualiza `dependencies` de `package.json`.
- **Verificación:** aparecen `node_modules/` y `package-lock.json`.

**¿Se prueba ahora? SÍ, instalaciones (no HTTP todavía).** En la misma terminal ejecuta:

```bash
npm ls --depth=0
```

**Esperado:** la lista incluye `express`, `bcryptjs`, `jsonwebtoken` y `dotenv`. Si sale `missing`, revisa que estés en la carpeta correcta y vuelve a ejecutar `npm install`. **No ejecutes el servidor todavía.**

### PASO 2. Crea carpetas y archivos en el lugar correcto

En VS Code, haz clic derecho sobre la raíz → **Nueva carpeta** → `src`. Dentro crea `app.js` y `server.js` (vacíos por ahora). En la raíz crea **solo** `.gitignore`; el archivo `.env` se generará con Node.js en el siguiente subpaso. La estructura esperada es:

```text
inventario-seguro-s08/
├── .env                 ← lo genera Node.js, NO lo escribimos a mano
├── .gitignore           ← evita publicar credenciales locales
├── package.json         ← lo creó npm
├── package-lock.json    ← lo creó npm
├── node_modules/        ← npm lo instaló
└── src/
    ├── app.js           ← rutas, productos y JWT (lo completaremos)
    └── server.js        ← inicia el servidor (lo completaremos)
```

**Primero abre `.gitignore` y escribe exactamente:**

```gitignore
node_modules/
.env
```

#### PASO 2A. Genera la clave JWT desde la terminal (NO la inventes ni la copies de Internet)

**¿Por qué?** `JWT_SECRET` firma el token y sirve para verificar que no fue modificado. La clave debe ser impredecible y distinta para cada proyecto. `node:crypto` es un módulo integrado de Node.js; **no se instala con npm**. `randomBytes(32)` genera **32 bytes aleatorios** y `.toString('hex')` los representa mediante **64 caracteres hexadecimales**.

**Terminal (en la raíz del proyecto, donde está `package.json`):** copia la línea completa y pulsa Enter:

```bash
node -e "const fs=require('node:fs'); const crypto=require('node:crypto'); const secret=crypto.randomBytes(32).toString('hex'); fs.writeFileSync('.env', ['PORT=3000','JWT_SECRET='+secret].join('\n')+'\n',{flag:'wx'}); console.log('OK: archivo .env creado con clave aleatoria; el secreto no se muestra.');"
```

**¿Qué cambió?** Se creó `.env` con dos líneas: `PORT=3000` y `JWT_SECRET=<valor aleatorio>`. La salida muestra solo `OK`, **no la clave**. La opción `{flag:'wx'}` evita sobrescribir por accidente un `.env` que ya exista. Si aparece `EEXIST`, **ya existe** `.env`: no lo reemplaces para continuar las pruebas. No compartas su contenido ni lo subas a Git.

**¿Se prueba aquí? SÍ, sin mostrar el secreto.** Ejecuta:

```bash
node -e "const fs=require('node:fs'); const s=fs.readFileSync('.env','utf8'); const v=s.match(/^JWT_SECRET=([0-9a-f]+)$/m); console.log('Clave válida (64 caracteres hex):', Boolean(v && v[1].length===64)); console.log('Puerto definido:', /^PORT=3000$/m.test(s));"
```

**Esperado:** `Clave válida (64 caracteres hex): true` y `Puerto definido: true`. Si la salida es `false`, revisa que ejecutaste el comando en la carpeta del proyecto. **Todavía no se inicia Express**: faltan las rutas.

> **Importante:** hay **dos elementos distintos**: `JWT_SECRET` es una clave privada del servidor, que no se envía al cliente; el **token JWT** se generará más adelante al iniciar sesión y sí se enviará al cliente.

**Relación con sesiones anteriores:** se retoma JWT de la sesión 07; la generación criptográfica desde la terminal refuerza la gestión de secretos. El valor exacto es único para cada estudiante.

### PASO 3. Configura los scripts de `package.json`: configuración no es ejecución

En la **terminal situada en la raíz**, ejecuta en este orden:

```bash
npm pkg set "scripts.start=node src/server.js"
npm pkg set "scripts.test=node --test test/flujo.test.js"
npm pkg get scripts
```

**¿Por qué cada línea?**

1. `npm pkg set "scripts.start=..."` registra que **`npm start`** ejecutará `src/server.js`. Todavía **no** lo ejecuta.
2. `npm pkg set "scripts.test=..."` registra que **`npm test`** ejecutará el archivo de prueba **solo cuando ese archivo exista**. Lo incluimos en el paquete técnico para validar después de la clase; no tienes que escribirlo durante los 120 minutos.
3. `npm pkg get scripts` solo **muestra** los aliases creados. No arranca ni prueba nada.

En `package.json` debe existir un objeto parecido a este (puede contener también otras propiedades):

```json
"scripts": {
  "start": "node src/server.js",
  "test": "node --test test/flujo.test.js"
}
```

**No reemplaces todo `package.json` por ese fragmento:** es solo el objeto `scripts` dentro del JSON completo que creó npm.

**¿Se prueba aquí? SÍ, la configuración, NO el funcionamiento del servidor.** Ejecuta `npm pkg get scripts` (ya indicado) y comprueba los nombres `start` y `test`. **No ejecutes `npm test` todavía**, porque el archivo de pruebas se explica al final de esta guía y en el proyecto resuelto.

## 4. Tema 1: construye un CRUD real con Express (min. 20–53)

### PASO 4. `src/server.js`: un archivo que pone el servidor a escuchar

Abre **`src/server.js`** (que todavía está vacío) y escribe:

```js
require('dotenv').config();
const app = require('./app');

const puerto = Number(process.env.PORT || 3000);
app.listen(puerto, () => {
  console.log(`API disponible en http://localhost:${puerto}`);
});
```

**Lectura causal, línea por línea:**

- `require('dotenv').config()` lee `.env` y coloca sus valores en `process.env`; debe ocurrir antes de consultar `PORT`.
- `require('./app')` trae nuestra aplicación Express desde el archivo `app.js` que construiremos a continuación.
- `process.env.PORT || 3000` utiliza el puerto del entorno o `3000` como valor alternativo; `Number()` lo transforma en número.
- `app.listen(...)` mantiene el servidor disponible para recibir peticiones.
- `console.log(...)` indica en qué dirección puedes comprobarlo.

**Predicción:** si ejecutaras `npm start` ahora, ¿qué ocurriría? Todavía falta que `app.js` exporte la aplicación: espera a terminar el paso siguiente.

**¿Se prueba aquí? SÍ, solo sintaxis.** En la terminal:

```bash
node --check src/server.js
```

**Esperado:** no aparece ningún mensaje y el comando termina sin error. **No uses `npm start` todavía**. Un archivo puede tener sintaxis válida y fallar al arrancar si su dependencia `./app` aún no existe o no exporta Express.

### PASO 5. `src/app.js`: primer resultado verificable, GET

Abre **`src/app.js`** y escribe TODO este bloque inicial (todavía no es el archivo final):

```js
const express = require('express');
const app = express();
app.use(express.json());

const productos = [
  { id: 1, nombre: 'Cuaderno', precio: 12 },
  { id: 2, nombre: 'Lapicero', precio: 3 }
];
let siguienteId = 3;

app.get('/api/productos', (req, res) => {
  res.json(productos);
});

module.exports = app;
```

**¿Por qué y qué cambia?**

- `require('express')` importa la biblioteca que instalaste.
- `express()` crea la aplicación donde registraremos rutas.
- `app.use(express.json())` lee cuerpos JSON de solicitudes; sin él `POST` y `PUT` no podrán acceder correctamente a los datos enviados.
- `productos` es nuestro estado inicial, con **dos** elementos; sus IDs son 1 y 2.
- `siguienteId = 3` será el identificador del próximo producto; así evitamos reutilizar ID 1 o 2.
- `app.get('/api/productos', ...)` responde a una consulta HTTP GET a esa ruta.
- `res.json(productos)` transforma el arreglo en una respuesta JSON con código **200**.
- `module.exports = app` permite que `server.js` importe **esa misma** aplicación; sin exportarla, `app.listen` no funcionaría.

**Ahora sí, desde la raíz, ejecuta:**

```bash
npm start
```

Deja esa terminal abierta. En el navegador visita **`http://localhost:3000/api/productos`**. Deberías ver:

```json
[
  { "id": 1, "nombre": "Cuaderno", "precio": 12 },
  { "id": 2, "nombre": "Lapicero", "precio": 3 }
]
```

**¿Se prueba aquí? SÍ, primero sintaxis y después HTTP.** En **Terminal B** (sin detener el servidor de Terminal A):

```bash
node --check src/app.js
node -e "fetch('http://localhost:3000/api/productos').then(async r=>console.log('HTTP',r.status,await r.text())).catch(console.error)"
```

**Esperado:** sin errores de sintaxis y `HTTP 200` con los **dos productos**. También puedes comprobarlo desde el navegador. Si aparece `ECONNREFUSED`, el servidor no está encendido: vuelve a **Terminal A** y ejecuta `npm start`.

**Estado observable:** antes no había respuesta HTTP; ahora un GET muestra dos productos. Si cambias el código, detén el proceso de **Terminal A** con **Ctrl+C**, ejecuta de nuevo `npm start`, y vuelve a probar desde **Terminal B** (no se activa recarga automática).

### PASO 6. Valida las entradas antes de modificar la lista

En **`src/app.js`**, busca exactamente esta línea:

```js
let siguienteId = 3;
```

**Inmediatamente DEBAJO** inserta esta función, dejando `app.get(...)` después:

```js
function datosValidos(nombre, precio) {
  return typeof nombre === 'string' && nombre.trim().length > 0 &&
    typeof precio === 'number' && Number.isFinite(precio) && precio >= 0;
}
```

**¿Por qué existe?** Antes de crear o modificar productos debemos comprobar que el nombre sea texto no vacío y que el precio sea un número finito no negativo. `typeof` comprueba tipos; `trim()` elimina espacios; `Number.isFinite` evita `Infinity` y `NaN`; `&&` exige que todo sea verdadero. Una entrada incorrecta no debe cambiar `productos`.

**¿Se prueba aquí? SÍ, solo sintaxis; el comportamiento se probará con POST.** En Terminal B:

```bash
node --check src/app.js
```

**Esperado:** termina sin mensajes ni errores. La función existe, pero aún ninguna ruta la llama: por eso **no corresponde** probar un POST hasta crear la ruta en el paso 7.

### PASO 7. Agrega POST: crear un producto

**Lugar exacto:** en `src/app.js`, coloca el bloque siguiente **DESPUÉS del cierre de `app.get('/api/productos', ...)` y ANTES de `module.exports = app;`**:

```js
app.post('/api/productos', (req, res) => {
  const { nombre, precio } = req.body ?? {};
  if (!datosValidos(nombre, precio)) {
    return res.status(400).json({ error: 'Nombre o precio inválido' });
  }
  const nuevo = { id: siguienteId++, nombre: nombre.trim(), precio };
  productos.push(nuevo);
  return res.status(201).json(nuevo);
});
```

**Lee las instrucciones:** `app.post` escucha POST; `req.body` contiene el JSON recibido; `?? {}` evita un error si no llega cuerpo; `datosValidos` detiene entradas incorrectas y responde **400**; `siguienteId++` usa ID 3 y luego incrementa el contador a 4; `push` agrega el objeto al arreglo; **201** indica creación exitosa.

**Prueba en Thunder Client o Postman:** método `POST`, URL `http://localhost:3000/api/productos`, pestaña Body → JSON, escribe:

```json
{ "nombre": "Regla", "precio": 4 }
```

Asegúrate de que el encabezado sea `Content-Type: application/json`. Detén y reinicia el servidor con **Ctrl+C → `npm start`**, después envía la solicitud. **Resultado esperado:** `201` y `{ "id": 3, "nombre": "Regla", "precio": 4 }`. Después ejecuta GET: ahora hay **tres** productos. Una vez terminadas las pruebas, pasa al siguiente bloque. **Si repites POST varias veces, los siguientes IDs cambiarán**.

**¿Se prueba aquí? SÍ, también desde la terminal (sin depender de Postman).** Con `npm start` corriendo en Terminal A, escribe en Terminal B:

```bash
node --check src/app.js
node -e "fetch('http://localhost:3000/api/productos',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({nombre:'Regla',precio:4})}).then(async r=>console.log('HTTP',r.status,await r.text())).catch(console.error)"
node -e "fetch('http://localhost:3000/api/productos').then(async r=>console.log('HTTP',r.status,await r.text())).catch(console.error)"
```

**Esperado:** `HTTP 201` con el nuevo producto y luego `HTTP 200` con tres productos. **Si sale 404**, quizá pegaste la ruta POST **después** de `module.exports` o no reiniciaste el servidor; revisa la ubicación y reinicia Terminal A.

### PASO 8. Agrega PUT y DELETE para completar el CRUD

**Lugar exacto:** en `src/app.js`, pega estos dos bloques **después de `app.post('/api/productos', ...)` y antes de `module.exports = app;`**:

```js
app.put('/api/productos/:id', (req, res) => {
  const producto = productos.find((p) => p.id === Number(req.params.id));
  if (!producto) {
    return res.status(404).json({ error: 'Producto no encontrado' });
  }
  const { nombre, precio } = req.body ?? {};
  if (!datosValidos(nombre, precio)) {
    return res.status(400).json({ error: 'Nombre o precio inválido' });
  }
  producto.nombre = nombre.trim();
  producto.precio = precio;
  return res.json(producto);
});

app.delete('/api/productos/:id', (req, res) => {
  const indice = productos.findIndex((p) => p.id === Number(req.params.id));
  if (indice === -1) {
    return res.status(404).json({ error: 'Producto no encontrado' });
  }
  productos.splice(indice, 1);
  return res.status(204).send();
});
```

**¿Por qué se usan estas instrucciones?**

- `:id` en la ruta es un parámetro; `req.params.id` es texto y `Number(...)` lo convierte en número para compararlo con el `id` almacenado.
- `find()` busca el producto existente; si no aparece, el recurso no existe y devolvemos **404**.
- `producto.nombre = ...` y `producto.precio = ...` **modifican el objeto ya almacenado**; PUT exige enviar los dos campos completos.
- `findIndex()` obtiene la posición numérica del arreglo; `-1` significa «no encontrado».
- `splice(indice, 1)` elimina un elemento en esa posición. `204` confirma la operación y **no devuelve cuerpo**.

**Prueba ahora, mientras TODAVÍA no hay JWT:** reinicia el servidor. Realiza POST de `Regla` (ID 3), luego `PUT http://localhost:3000/api/productos/3` con `{"nombre":"Regla grande","precio":7}`: debe responder **200**. Finalmente `DELETE` a `/api/productos/3`: debe responder **204**. GET vuelve a mostrar solo los dos productos iniciales.

**¿Se prueba aquí? SÍ, en terminal y con un orden concreto.** Reinicia el servidor en Terminal A con **Ctrl+C → `npm start`** para que vuelva a tener solo dos productos. En Terminal B ejecuta **en este orden** (cada línea por separado):

**1. Crear Regla (POST; 201):**

```bash
node -e "fetch('http://localhost:3000/api/productos',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({nombre:'Regla',precio:4})}).then(async r=>console.log('HTTP',r.status,await r.text())).catch(console.error)"
```

**2. Editar ID 3 (PUT; 200):**

```bash
node -e "fetch('http://localhost:3000/api/productos/3',{method:'PUT',headers:{'Content-Type':'application/json'},body:JSON.stringify({nombre:'Regla grande',precio:7})}).then(async r=>console.log('HTTP',r.status,await r.text())).catch(console.error)"
```

**3. Eliminar ID 3 (DELETE; 204):**

```bash
node -e "fetch('http://localhost:3000/api/productos/3',{method:'DELETE'}).then(async r=>console.log('HTTP',r.status,'cuerpo:',JSON.stringify(await r.text()))).catch(console.error)"
```

**4. Verificar lista final (GET; 200):**

```bash
node -e "fetch('http://localhost:3000/api/productos').then(async r=>console.log('HTTP',r.status,await r.text())).catch(console.error)"
```

**Esperado en ese orden:** `201`, `200`, `204` sin cuerpo (`""`), `200` con dos productos. Si el PUT devuelve **404**, no se creó el ID 3: reinicia y comienza desde el POST. Estas pruebas son **válidas solamente antes de agregar `verificarToken`**.

> **Alto de seguridad:** en este momento cualquiera podría modificar el inventario. Está permitido **solo como etapa temporal en tu computador de laboratorio**. En el siguiente tema convertiremos esas tres rutas en operaciones protegidas. No expongas esta etapa a Internet.

## 5. Tema 2: agrega autenticación y protege las rutas (min. 53–83)

### PASO 9. Prepara la clave y el usuario de demostración

En **`src/app.js`**, justo **debajo** de `const express = require('express');`, agrega:

```js
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
require('dotenv').config();

if (!process.env.JWT_SECRET || process.env.JWT_SECRET.length < 32) {
  throw new Error('Configura JWT_SECRET con al menos 32 caracteres en .env');
}
```

**Para qué sirve cada instrucción:** `bcrypt` compara una contraseña ingresada con su hash; `jwt` firma y verifica tokens; `dotenv.config()` carga `JWT_SECRET` desde `.env`; el `if` impide arrancar sin una clave mínima para el ejercicio. **Un JWT está firmado, no cifrado**: no introduzcas contraseñas dentro de su contenido.

Ahora ubica `let siguienteId = 3;` y agrega **debajo de esa línea, ANTES de `function datosValidos(...)`:**

```js
// Cuenta exclusivamente de demostración: nunca usar credenciales fijas en producción.
const usuarioDemo = {
  correo: 'docente@demo.test',
  hash: bcrypt.hashSync('Clase2026!', 10)
};
```

**¿Por qué hay un hash y no una contraseña almacenada?** `hashSync(clave, 10)` calcula al iniciar la aplicación una representación verificable de la contraseña de prueba. El `10` es el factor de trabajo (coste de bcrypt), **no el número de caracteres**. Cuando el usuario inicie sesión comprobaremos la contraseña con `compareSync`; **no** compararemos cadenas de contraseñas ni intentaremos descifrar el hash. En proyectos reales el hash se almacena en una base de datos y no se crean cuentas con claves fijas en el código. Aquí sirve únicamente para entender el flujo.

**¿Se prueba aquí? SÍ, clave generada + sintaxis, sin revelar el secreto.** En Terminal B:

```bash
node --check src/app.js
node -e "require('dotenv').config(); console.log('JWT_SECRET cargado:', typeof process.env.JWT_SECRET==='string' && process.env.JWT_SECRET.length===64)"
```

**Esperado:** no hay errores de sintaxis y `JWT_SECRET cargado: true`. Si aparece el error de clave al reiniciar el servidor, verifica que `.env` esté en la **raíz**, no dentro de `src/`. Reinicia Terminal A antes de continuar.

### PASO 10. Crea el middleware que verifica la identidad

**Lugar exacto:** en `src/app.js`, inmediatamente **DESPUÉS del cierre de `function datosValidos(...)` y ANTES de `app.get('/api/productos', ...)`**, escribe:

```js
function verificarToken(req, res, next) {
  const cabecera = req.headers.authorization || '';
  const [tipo, token] = cabecera.split(' ');
  if (tipo !== 'Bearer' || !token) {
    return res.status(401).json({ error: 'Token requerido' });
  }
  try {
    req.usuario = jwt.verify(token, process.env.JWT_SECRET);
    next();
  } catch {
    return res.status(401).json({ error: 'Token inválido o expirado' });
  }
}
```

**Cómo actúa:** `req` contiene la petición; `res` permite responder; `next` pasa el control a la ruta **solo si la verificación tiene éxito**. La cabecera debe tener la forma `Authorization: Bearer TOKEN`. `split(' ')` separa la palabra `Bearer` del token; `jwt.verify` comprueba firma y caducidad; `req.usuario` guarda los datos verificados para posibles rutas posteriores. **Sin token o con token incorrecto responde 401**, y `next()` no se ejecuta.

**Predicción:** si una ruta usa `verificarToken` y llega sin cabecera Authorization, ¿se ejecuta la función que agrega el producto? **No**: termina antes de `push()`.

**¿Se prueba aquí? SÍ, solo sintaxis.** En Terminal B ejecuta:

```bash
node --check src/app.js
```

**Esperado:** sin errores. **Aún NO se producirá 401 en POST** porque el middleware ya existe, pero **todavía no está conectado a ninguna ruta**. Ese cambio se comprobará en el paso 12.

### PASO 11. Crea la ruta de inicio de sesión

**Lugar exacto:** en `src/app.js`, pega lo siguiente **DESPUÉS de `verificarToken` y ANTES de `app.get('/api/productos', ...)`**:

```js
app.post('/api/login', (req, res) => {
  const { correo, clave } = req.body ?? {};
  if (correo !== usuarioDemo.correo ||
      typeof clave !== 'string' ||
      !bcrypt.compareSync(clave, usuarioDemo.hash)) {
    return res.status(401).json({ error: 'Credenciales incorrectas' });
  }
  const token = jwt.sign(
    { id: 1, correo: usuarioDemo.correo },
    process.env.JWT_SECRET,
    { expiresIn: '1h' }
  );
  return res.json({ token });
});
```

**Qué cambia paso a paso:** `req.body` recibe `correo` y `clave`; la condición detiene usuarios y claves incorrectos; `compareSync` comprueba si la contraseña coincide con el hash; `jwt.sign` **genera un token firmado** con ID y correo y vencimiento de una hora; `res.json({token})` lo entrega al cliente. **La contraseña no va en el JWT**. En este ejemplo usarás `docente@demo.test` / `Clase2026!` exclusivamente de manera local.

**¿Se prueba aquí? SÍ, login desde Terminal B.** Reinicia Terminal A (`Ctrl+C`, después `npm start`) y ejecuta:

```bash
node -e "fetch('http://localhost:3000/api/login',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({correo:'docente@demo.test',clave:'Clase2026!'})}).then(async r=>{const j=await r.json();console.log('HTTP',r.status,'JWT generado:',Boolean(j.token));}).catch(console.error)"
```

**Esperado:** `HTTP 200 JWT generado: true`. El comando **no imprime el token completo**, solo indica si existe. Si devuelve `HTTP 401`, revisa el correo, la contraseña de demostración y que no haya espacios extra en el JSON. El JWT se genera en **cada login**, no cuando se crea `.env`.

### PASO 12. Conecta el middleware a las rutas que modifican datos

Ahora **NO** crees nuevas rutas: modifica la firma de **las tres rutas que ya existen** en `src/app.js`.

| Busca la línea existente | Reemplázala exactamente por |
|---|---|
| `app.post('/api/productos', (req, res) => {` | `app.post('/api/productos', verificarToken, (req, res) => {` |
| `app.put('/api/productos/:id', (req, res) => {` | `app.put('/api/productos/:id', verificarToken, (req, res) => {` |
| `app.delete('/api/productos/:id', (req, res) => {` | `app.delete('/api/productos/:id', verificarToken, (req, res) => {` |

**¿Por qué este cambio tan pequeño es suficiente?** Express ejecuta los argumentos de cada ruta en orden: **primero `verificarToken`**, después la función CRUD. Si no hay JWT válido, el middleware responde y la operación no se ejecuta. **No agregues `verificarToken` al GET**, porque los productos deben seguir siendo públicos. **No lo agregues al login**, porque el usuario todavía no tiene token.

**Resultado final de la arquitectura:** una sola aplicación, un mismo arreglo `productos`, un único login y tres operaciones protegidas. No necesitas crear seis proyectos distintos.

**¿Se prueba aquí? SÍ, con y sin token.** Reinicia Terminal A (`Ctrl+C`, después `npm start`), luego ejecuta desde Terminal B:

**1. Sin token (debe bloquear POST):**

```bash
node -e "fetch('http://localhost:3000/api/productos',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({nombre:'Regla',precio:4})}).then(async r=>console.log('HTTP',r.status,await r.text())).catch(console.error)"
```

**Esperado:** `HTTP 401`, y **no** se agrega ningún producto.

**2. Con token (login + POST en una sola instrucción, sin copiar JWT):**

```bash
node -e "(async()=>{const base='http://localhost:3000';const login=await fetch(base+'/api/login',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({correo:'docente@demo.test',clave:'Clase2026!'})});const {token}=await login.json();if(!token)throw Error('Login fallido');const r=await fetch(base+'/api/productos',{method:'POST',headers:{'Content-Type':'application/json',Authorization:'Bearer '+token},body:JSON.stringify({nombre:'Regla',precio:4})});console.log('HTTP',r.status,await r.text());})().catch(console.error)"
```

**Esperado:** `HTTP 201` y un producto con ID 3. Esta prueba demuestra que **el servidor firmó un JWT**, el cliente lo devolvió como `Authorization: Bearer ...` y el middleware permitió la escritura. **No pegues `JWT_SECRET` en la cabecera**: se envía el token, nunca el secreto.

## 6. Prueba completa e integración (min. 83–99)

**Detén el servidor con Ctrl+C y ejecuta `npm start` otra vez en Terminal A.** Abre Thunder Client o Postman si lo deseas; **todas las etapas también tienen su comando en Terminal B**. Usa el mismo puerto que aparece en la terminal. Todas las peticiones con cuerpo necesitan `Content-Type: application/json`. Reiniciar devuelve el arreglo a los dos productos iniciales.

| Orden | Solicitud | Qué escribir / hacer | Esperado |
|---|---|---|---|
| 1 | `GET /api/productos` | Sin token | **200**, dos productos |
| 2 | `POST /api/productos` | JSON `{"nombre":"Regla","precio":4}` sin token | **401**, no crea nada |
| 3 | `POST /api/login` | Correo y clave del usuario demo (paso 11) | **200**, devuelve token |
| 4 | `POST /api/productos` | JSON de Regla, header `Authorization: Bearer TU_TOKEN` | **201**, crea ID 3 |
| 5 | `PUT /api/productos/3` | Token y JSON `{"nombre":"Regla grande","precio":7}` | **200**, modifica ID 3 |
| 6 | `DELETE /api/productos/3` | Token, sin cuerpo | **204**, sin cuerpo |
| 7 | `GET /api/productos` | Sin token | **200**, otra vez dos productos |

**Prueba integradora totalmente por terminal:** desde Terminal B, con `npm start` abierto en Terminal A, ejecuta **un único comando**. Este comando hace GET, POST sin token, login, POST con token, PUT, DELETE y GET final, y muestra sus códigos sin mostrar el JWT:

```bash
node -e "(async()=>{const b='http://localhost:3000';const j={'Content-Type':'application/json'};const check=async(label,url,opt)=>{const r=await fetch(b+url,opt);console.log(label,'HTTP',r.status);return r;};await check('GET inicial','/api/productos');await check('POST sin token','/api/productos',{method:'POST',headers:j,body:JSON.stringify({nombre:'Regla',precio:4})});const login=await check('LOGIN','/api/login',{method:'POST',headers:j,body:JSON.stringify({correo:'docente@demo.test',clave:'Clase2026!'})});const {token}=await login.json();if(!token)throw Error('Sin token');const h={...j,Authorization:'Bearer '+token};const creado=await check('POST con token','/api/productos',{method:'POST',headers:h,body:JSON.stringify({nombre:'Regla',precio:4})});const {id}=await creado.json();await check('PUT','/api/productos/'+id,{method:'PUT',headers:h,body:JSON.stringify({nombre:'Regla grande',precio:7})});await check('DELETE','/api/productos/'+id,{method:'DELETE',headers:h});await check('GET final','/api/productos');})().catch(console.error)"
```

**Esperado, línea por línea:** `GET inicial HTTP 200`; `POST sin token HTTP 401`; `LOGIN HTTP 200`; `POST con token HTTP 201`; `PUT HTTP 200`; `DELETE HTTP 204`; `GET final HTTP 200`. Si obtienes un código inesperado, vuelve al paso donde se implementa esa ruta, corrige y **reinicia Terminal A**.

**Cómo usar el token sin confundirlo:** en la respuesta del login copia **solo el valor** de `token`, sin las comillas del JSON. En la petición de creación el encabezado debe ser `Authorization` y su valor `Bearer eyJ...` (con un espacio después de `Bearer`). El token concreto cambia entre ejecuciones: **no copies un token del ejemplo o de otro estudiante**.

### Registro de evidencia

En el README de tu práctica registra cuatro capturas: **GET 200**, **POST sin token 401**, **login 200 con token (oculta el token en la captura compartida)** y **POST con token 201**. Explica en una frase qué cambió entre 401 y 201. **Nunca publiques `.env`, contraseñas reales ni tokens activos.**

### Tarea espejo (individual, 6 minutos)

**T01 — Consulta por ID:** sin copiar una solución ya hecha, agrega la ruta pública `GET /api/productos/:id` en `src/app.js`, **entre el GET que devuelve todos los productos y el POST protegido**. Debe devolver un producto existente con código **200**, o `{ "error": "Producto no encontrado" }` con **404** si no existe. **Pista:** ya utilizaste `productos.find(...)` y `Number(req.params.id)` en PUT. **Criterio de aceptación:** `GET /api/productos/1` devuelve «Cuaderno» y `GET /api/productos/999` devuelve 404; no debe pedir JWT.

**¿Se prueba T01 desde terminal? SÍ.** Reinicia Terminal A después de incorporar la ruta y ejecuta en Terminal B:

```bash
node -e "Promise.all(['/api/productos/1','/api/productos/999'].map(async p=>{const r=await fetch('http://localhost:3000'+p);console.log(p,'HTTP',r.status,await r.text());})).catch(console.error)"
```

**Esperado:** ID 1 responde `HTTP 200` con «Cuaderno» y ID 999 responde `HTTP 404`. **No hace falta iniciar sesión**. Si 404 aparece para ambos, comprueba que la ruta `/api/productos/:id` esté declarada y que reiniciaste el servidor.

**Reflexiona:** ¿por qué un GET público puede usar `find`, mientras un POST protegido requiere `verificarToken`? Escribe la respuesta sin describir solo el código.

## 7. Evaluación y cierre (min. 99–120)

Del minuto **99 al 115** responde una evaluación formativa de **dos temas**. Justifica al menos una respuesta de HTTP y una de JWT. Del minuto **115 al 120** compara tus resultados con las respuestas esperadas y registra una dificultad que debas repasar.

**Checklist de salida:**

- [ ] Generé `JWT_SECRET` desde la terminal con `node:crypto` y comprobé su existencia **sin imprimir el valor**.
- [ ] En cada paso distinguí **prueba de configuración**, **prueba de sintaxis** y **prueba HTTP**.
- [ ] Sé identificar `package.json`, `src/app.js`, `src/server.js` y `.env`.
- [ ] Ejecuté `npm start` solo después de crear los archivos necesarios.
- [ ] Puedo explicar qué cambia en el arreglo después de POST, PUT y DELETE.
- [ ] Puedo explicar por qué GET no exige token y POST sí.
- [ ] Comprobé `401` sin JWT y `201` con JWT válido.
- [ ] Sé distinguir el hash de bcrypt de la firma de un JWT.

## 8. Solución completa para comprobar tu avance (no sustituye los pasos)

Si te equivocas al pegar un bloque, utiliza el **siguiente código final** para **comparar**, no para omitir la práctica. Debe coincidir con `FASE_15_Proyecto_Integrador/src/app.js` del paquete acompañante. **No hace falta escribir un tercer archivo ni cambiar el orden de la clase.**

```js
const express = require('express');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
require('dotenv').config();

if (!process.env.JWT_SECRET || process.env.JWT_SECRET.length < 32) {
  throw new Error('Configura JWT_SECRET con al menos 32 caracteres en .env');
}

const app = express();
app.use(express.json());

const productos = [
  { id: 1, nombre: 'Cuaderno', precio: 12 },
  { id: 2, nombre: 'Lapicero', precio: 3 }
];
let siguienteId = 3;

// Cuenta exclusivamente de demostración: nunca usar credenciales fijas en producción.
const usuarioDemo = {
  correo: 'docente@demo.test',
  hash: bcrypt.hashSync('Clase2026!', 10)
};

function datosValidos(nombre, precio) {
  return typeof nombre === 'string' && nombre.trim().length > 0 &&
    typeof precio === 'number' && Number.isFinite(precio) && precio >= 0;
}

function verificarToken(req, res, next) {
  const cabecera = req.headers.authorization || '';
  const [tipo, token] = cabecera.split(' ');
  if (tipo !== 'Bearer' || !token) {
    return res.status(401).json({ error: 'Token requerido' });
  }
  try {
    req.usuario = jwt.verify(token, process.env.JWT_SECRET);
    next();
  } catch {
    return res.status(401).json({ error: 'Token inválido o expirado' });
  }
}

app.post('/api/login', (req, res) => {
  const { correo, clave } = req.body ?? {};
  if (correo !== usuarioDemo.correo ||
      typeof clave !== 'string' ||
      !bcrypt.compareSync(clave, usuarioDemo.hash)) {
    return res.status(401).json({ error: 'Credenciales incorrectas' });
  }
  const token = jwt.sign(
    { id: 1, correo: usuarioDemo.correo },
    process.env.JWT_SECRET,
    { expiresIn: '1h' }
  );
  return res.json({ token });
});

app.get('/api/productos', (req, res) => {
  res.json(productos);
});

app.post('/api/productos', verificarToken, (req, res) => {
  const { nombre, precio } = req.body ?? {};
  if (!datosValidos(nombre, precio)) {
    return res.status(400).json({ error: 'Nombre o precio inválido' });
  }
  const nuevo = { id: siguienteId++, nombre: nombre.trim(), precio };
  productos.push(nuevo);
  return res.status(201).json(nuevo);
});

app.put('/api/productos/:id', verificarToken, (req, res) => {
  const producto = productos.find((p) => p.id === Number(req.params.id));
  if (!producto) {
    return res.status(404).json({ error: 'Producto no encontrado' });
  }
  const { nombre, precio } = req.body ?? {};
  if (!datosValidos(nombre, precio)) {
    return res.status(400).json({ error: 'Nombre o precio inválido' });
  }
  producto.nombre = nombre.trim();
  producto.precio = precio;
  return res.json(producto);
});

app.delete('/api/productos/:id', verificarToken, (req, res) => {
  const indice = productos.findIndex((p) => p.id === Number(req.params.id));
  if (indice === -1) {
    return res.status(404).json({ error: 'Producto no encontrado' });
  }
  productos.splice(indice, 1);
  return res.status(204).send();
});

module.exports = app;
```

**Comprobación automatizada opcional (fuera de los 120 minutos):** si estás trabajando desde cero y quieres ejecutar `npm test`, crea la carpeta `test` y dentro el archivo `flujo.test.js`, con el código exacto del **ANEXO A** al final de esta guía (también está incluido en el ZIP técnico). Ejecuta en una terminal desde la raíz:

```bash
node --check test/flujo.test.js
npm test
```

**Esperado:** `pass 1`, `fail 0` (si las dependencias y la clave están configuradas). **No es necesario** tener `npm start` abierto: las pruebas crean un servidor temporal en otro puerto. A diferencia de la versión anterior, estas pruebas leen **tu clave aleatoria de `.env`** y no colocan una clave fija en el código.

### Si algo falla: diagnóstico breve

| Mensaje o síntoma | Causa frecuente | Qué hacer |
|---|---|---|
| `Cannot find module express` | Falta instalación | `npm install` en la raíz |
| `Cannot find module ./app` | `src/app.js` ausente o mal nombrado | Revisa ruta y extensión |
| `EADDRINUSE` | Puerto 3000 ocupado | Detén el servidor anterior con Ctrl+C |
| `JWT_SECRET` no configurada | Falta `.env` o clave corta | Repite PASO 2A si no existe `.env`; no sobrescribas secretos existentes |
| `POST` responde 401 incluso con login exitoso | No se envió `Bearer TOKEN` en la solicitud de productos | Revisa la cabecera Authorization |
| `POST` responde 400 | Body inválido o sin `Content-Type: application/json` | Verifica nombre y precio numérico |
| `DELETE` responde 204 sin JSON | Es el comportamiento correcto | Revisa con GET que desapareció |
| `GET` muestra 2 tras reiniciar | Arreglo en memoria | Comportamiento esperado; no hay MongoDB |


## ANEXO A — Prueba automática opcional: `test/flujo.test.js`

**Cuándo:** fuera de la clase de 120 minutos, si deseas automatizar la validación de toda la aplicación. **Dónde:** crea la carpeta `test/` en la raíz y el archivo `test/flujo.test.js`; copia **exactamente** el siguiente código. El mismo archivo existe en la FASE 15 para comprobar que la guía y el laboratorio están sincronizados. **Por qué:** `npm test` comprueba respuestas HTTP de manera repetible, sin navegador y sin copiar el JWT a mano.

```js
const { test } = require('node:test');
const assert = require('node:assert/strict');
require('dotenv').config(); // Usa el secreto generado en .env por el estudiante
const app = require('../src/app');

test('inventario seguro: consulta, autenticación y CRUD', async () => {
  const servidor = app.listen(0);
  await new Promise((resolve) => servidor.once('listening', resolve));
  const base = `http://127.0.0.1:${servidor.address().port}`;
  try {
    const lista = await fetch(`${base}/api/productos`);
    assert.equal(lista.status, 200);
    assert.equal((await lista.json()).length, 2);

    const sinToken = await fetch(`${base}/api/productos`, {
      method: 'POST', headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ nombre: 'Regla', precio: 4 })
    });
    assert.equal(sinToken.status, 401);

    const malLogin = await fetch(`${base}/api/login`, {
      method: 'POST', headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ correo: 'docente@demo.test', clave: 'incorrecta' })
    });
    assert.equal(malLogin.status, 401);

    const login = await fetch(`${base}/api/login`, {
      method: 'POST', headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ correo: 'docente@demo.test', clave: 'Clase2026!' })
    });
    assert.equal(login.status, 200);
    const { token } = await login.json();
    assert.ok(token);

    const cabeceras = {
      'Content-Type': 'application/json', Authorization: `Bearer ${token}`
    };
    const invalido = await fetch(`${base}/api/productos`, {
      method: 'POST', headers: cabeceras,
      body: JSON.stringify({ nombre: '', precio: -2 })
    });
    assert.equal(invalido.status, 400);

    const crear = await fetch(`${base}/api/productos`, {
      method: 'POST', headers: cabeceras,
      body: JSON.stringify({ nombre: 'Regla', precio: 4 })
    });
    assert.equal(crear.status, 201);
    const nuevo = await crear.json();
    assert.equal(nuevo.id, 3);

    const editar = await fetch(`${base}/api/productos/${nuevo.id}`, {
      method: 'PUT', headers: cabeceras,
      body: JSON.stringify({ nombre: 'Regla grande', precio: 7 })
    });
    assert.equal(editar.status, 200);
    assert.equal((await editar.json()).precio, 7);

    const eliminar = await fetch(`${base}/api/productos/${nuevo.id}`, {
      method: 'DELETE', headers: cabeceras
    });
    assert.equal(eliminar.status, 204);

    const final = await fetch(`${base}/api/productos`);
    assert.equal((await final.json()).length, 2);
  } finally {
    await new Promise((resolve, reject) => {
      servidor.close((err) => err ? reject(err) : resolve());
    });
  }
});
```

**Para ejecutar:** primero confirma que existe `.env` con su clave aleatoria y que ya completaste la aplicación; luego ejecuta en la raíz:

```bash
node --check test/flujo.test.js
npm test
```

**Esperado:** `pass 1` y `fail 0`. La prueba usa un puerto temporal: **no requiere `npm start`**. Si falla por dependencias, ejecuta `npm install`. Si falla porque `JWT_SECRET` no existe, vuelve al PASO 2A. No reproduzcas un token ni un secreto real en capturas.
