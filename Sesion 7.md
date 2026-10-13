---
course_id: ISIL-30690
session_id: S07
module_id: TEMA_07
source_origin: PPT
status: validated-structural
---

# Guía del estudiante — S07 | Autenticación y seguridad web

> **ISIL · Programación Web Avanzada | Lideratec Academy**  
> Código paso a paso: JWT, CORS, bcrypt.js y autorización basada en roles.

## 1. Propósito de la sesión

Construiremos y probaremos una API Express que registra usuarios ficticios, protege contraseñas con hash, autentica mediante JWT, limita lecturas entre orígenes web mediante CORS y verifica permisos por rol. El objetivo es entender **qué archivo crear, por qué existe cada línea importante, qué ejecutar y cómo reconocer el éxito o fallo**. Trabajaremos con una colección temporal en memoria para aislar los mecanismos de seguridad: este laboratorio no reemplaza la persistencia con MongoDB/Mongoose vista antes.

## 2. Resultado observable y lectura de apertura

Una API que guarda contraseñas como hash, emite un JWT firmado tras comprobar credenciales, responde 401 si falta o falla la autenticación, responde 403 si el rol no permite entrar y comunica al navegador los orígenes aceptados mediante cabeceras CORS.

**Lectura docente sugerida.** En la sesión anterior conseguimos que una API atendiera solicitudes y trabajara con datos. Ahora debemos preguntarnos quién puede realizar cada operación. Una API funcional sin control de identidad ni autorización podría exponer recursos que no corresponden a todos. Primero separaremos dos conceptos: autenticar significa comprobar quién presenta una credencial válida, mientras que autorizar significa decidir qué puede hacer esa identidad. Para no almacenar contraseñas en texto plano, aprenderemos a producir y comparar hashes con bcrypt.js. Después emitiremos y verificaremos un JSON Web Token; la firma permite detectar modificaciones, pero no oculta el payload. Integraremos estas comprobaciones como middleware de Express antes de ejecutar una ruta privada. Agregaremos una regla por roles y comprobaremos la diferencia observable entre un usuario y un administrador ficticio. Finalmente, configuraremos CORS para que un navegador distinga qué sitios pueden leer una respuesta; sin confundir esa política del navegador con los permisos de la API. Cada hito se comprobará con códigos HTTP, salidas de consola y pruebas automáticas.

**Desempeños:** construir, ejecutar, contrastar, interpretar, diagnosticar, corregir y justificar una secuencia de autenticación. **Dominio:** poder explicar por qué 401 y 403 son distintos y por qué CORS no reemplaza JWT.

## 3. Antes de iniciar: equipo, herramientas y directorio exacto

**Debes saber:** ejecutar `node` y `npm`, crear archivos con VS Code y reconocer GET, POST y JSON en Express (sesiones previas). **Debes tener:** Node.js 20 o superior, npm, un editor de texto/VS Code y terminal. No hace falta instalar MongoDB, MongoDB Compass, Angular ni Postman para completar este laboratorio; las pruebas se ejecutan con `fetch()` y el probador integrado de Node.

1. Abre VS Code. Crea una carpeta llamada `isil-s07-seguridad-web` y selecciona **Archivo → Abrir carpeta**. En VS Code elige **Terminal → Nuevo terminal** y verifica que estás dentro de esa carpeta (`pwd` en macOS/Linux o `Get-Location` en PowerShell).
2. Ejecuta `node --version` y `npm --version`. Si Node no está instalado, utiliza el instalador LTS compatible con tu sistema desde https://nodejs.org/; abre una terminal nueva y verifica ambos comandos otra vez. No elijas una versión distinta de la institucional si ya funciona.
3. En la terminal de la carpeta, ejecuta exactamente `npm init -y` y luego `npm install express cors bcryptjs jsonwebtoken dotenv`. Esto crea `package.json`, `package-lock.json` y `node_modules/`. No crees ni edites manualmente `node_modules/`.
4. Abre **`package.json` en la RAÍZ** de `isil-s07-seguridad-web` (no en `src/` ni en `ejemplos/`). En esta primera preparación **NO escribas aún los cuatro scripts**: los agregaremos cuando exista el archivo correspondiente. `npm init -y` suele generar un script `test` provisional que solo muestra un mensaje de error. Sustituye únicamente ese objeto por `"scripts": {}`; **conserva** `"dependencies"`, `"name"`, `"version"` y las demás propiedades. Aún no ejecutes `npm start` ni `npm test`: los archivos que usarán aparecerán en EJ03 y EJ07.

**¿Por qué no agregamos todo ahora?** Porque cada script de npm es un **atajo para ejecutar un archivo o comando existente**, no un generador de esos archivos. En esta guía los construiremos primero, configuraremos después el atajo apropiado y finalmente lo ejecutaremos. Cuando uses el laboratorio ya terminado (FASE 15), los cuatro atajos sí estarán configurados desde el principio porque todos sus archivos ya existen.

**Ejemplo del contexto correcto en `package.json`** (las dependencias exactas que generó tu `npm install` deben mantenerse):

```json
{
  "name": "isil-s07-seguridad-web",
  "version": "1.0.0",
  "scripts": {},
  "dependencies": {
    "bcryptjs": "^3.0.0",
    "cors": "^2.8.5",
    "dotenv": "^16.0.0",
    "express": "^5.0.0",
    "jsonwebtoken": "^9.0.0"
  }
}
```

**Atención:** este JSON es **ilustrativo de la estructura**, no una orden para sustituir todas las versiones: `npm install` puede registrar versiones distintas compatibles. La configuración real debe conservar las versiones instaladas. En JSON no se admiten comentarios ni comas finales y solo puede haber una propiedad `"scripts"`.

5. En el explorador izquierdo, crea carpetas `ejemplos`, `src`, `src/middleware`, `pruebas` y `tareas`. Para crearlas, usa clic derecho → **Nueva carpeta**. Luego crea archivos mediante clic derecho → **Nuevo archivo**.
6. En la raíz, crea `.gitignore` con lo siguiente:

```text
node_modules/
.env
*.log
```

7. En la raíz crea `.env.example` y `.env` con estas tres claves (inicialmente vacías):

```dotenv
JWT_SECRET=
DEMO_ADMIN_PASSWORD=
LAB_PASSWORD=
```

8. En terminal ejecuta `node -e "console.log(require('node:crypto').randomBytes(48).toString('hex'))"`. Copia el valor generado al lado de `JWT_SECRET=` **solo en `.env`**. En ese archivo agrega dos contraseñas inventadas para esta práctica, distintas de cualquier contraseña real. Nunca las compartas ni subas `.env` a un repositorio.

**Verificación inicial:** `node -e "require('express'); require('bcryptjs'); require('jsonwebtoken'); require('cors'); console.log('dependencias OK')"` debe imprimir `dependencias OK`. Si aparece `Cannot find module`, vuelve a ejecutar `npm install` en la carpeta correcta. Si `npm` no encuentra el registro, comprueba internet/proxy institucional.

### Estructura que construirás

```text
isil-s07-seguridad-web/
├── .env                 (secreto local; no compartir)
├── .env.example         (sin valores)
├── .gitignore
├── package.json
├── ejemplos/
│   ├── EJ01_bcrypt.js
│   └── EJ02_jwt.js
├── src/
│   ├── server.js
│   ├── crear-app.js
│   ├── cors-config.js
│   └── middleware/
│       ├── autenticacion.js
│       └── roles.js
├── pruebas/
│   └── EJ07_flujo.test.js
└── tareas/             (actividades espejo al final de cada ejemplo)
```

Construiremos estas partes progresivamente; las carpetas pueden estar vacías al inicio. Para cada EJxx usa el archivo con el nombre exacto mostrado. No copies código de otro paquete: todo el código esencial está a continuación.


## 3.1 Cómo leer `package.json` y ejecutar comandos sin perder el orden

**Distingue cuatro cosas que cumplen funciones diferentes.** `package.json` es el archivo de configuración ubicado en la raíz; `scripts` es un objeto dentro de él; `node` es el programa que ejecuta JavaScript; `npm` lee los scripts y facilita lanzarlos desde la terminal. Un script **NO instala dependencias, no crea automáticamente archivos y no obliga a ejecutar los otros scripts**.

Cuando escribes `npm run ej01`, npm busca la clave `ej01` dentro de `scripts` y ejecuta literalmente `node ejemplos/EJ01_bcrypt.js` tomando como referencia la raíz del proyecto. Podrías escribir este comando largo directamente, pero el alias deja una instrucción común para toda la clase. Cuando usas `npm start` o `npm test`, npm utiliza los alias especiales `start` y `test` (sin necesidad de escribir `run`).

**Secuencia global de la sesión, de principio a fin:**

| Momento | Qué debes terminar ANTES | Qué añades a `scripts` | Qué ejecutas y qué observas |
|---|---|---|---|
| Preparación | `npm init -y`, `npm install`, `.env`, carpetas | `"scripts": {}` | `npm run` muestra los scripts disponibles; aún no hay ejercicios listos. |
| EJ01 | Crear y guardar `ejemplos/EJ01_bcrypt.js` | `"ej01": "node ejemplos/EJ01_bcrypt.js"` | `npm run ej01` → tres líneas de verificación con `true`, `true`, `false`. |
| EJ02 | Crear y guardar `ejemplos/EJ02_jwt.js` | `"ej02": "node ejemplos/EJ02_jwt.js"` | `npm run ej02` → firma válida y token manipulado rechazado. |
| EJ03 | Crear completos `src/server.js` y `src/crear-app.js` | `"start": "node src/server.js"` | `npm start` → servidor escuchando en `http://localhost:3001`; **se queda encendido**. |
| EJ04–EJ06 | Guardar middleware y nuevas rutas en `src/` | Nada nuevo: se reutiliza `start` | Presiona **Ctrl+C** para detener, luego `npm start` para cargar cambios; prueba 401/200, 403/200 y CORS. |
| EJ07 | Crear y guardar `pruebas/EJ07_flujo.test.js` | `"test": "node --test pruebas/EJ07_flujo.test.js"` | `npm test` → pruebas H1–H4 y resumen PASS/FAIL; **no requiere servidor manual**. |

**Dónde se escribe cada cosa.** El texto con `"ej01": ...` se escribe en el archivo `package.json`, **dentro del objeto `scripts`**. En cambio, `npm run ej01` se escribe en la **terminal de VS Code**, no en un archivo JavaScript ni dentro del JSON. El código fuente se escribe en los archivos `.js` indicados en cada ejemplo. **Guarda con Ctrl+S** antes de ejecutar.

**Cómo saber dónde estás:** en la terminal situada en la carpeta principal ejecuta `npm run` para mostrar los nombres de scripts. El directorio debe contener `package.json`; puedes comprobarlo con `ls` (macOS/Linux) o `dir` (PowerShell). `node --version` comprueba Node, pero **no** comprueba que estés en la carpeta correcta.

**Cómo interpretar cada ejecución:** los scripts `ej01` y `ej02` imprimen resultados y terminan; `start` inicia una API que sigue esperando peticiones hasta presionar Ctrl+C; `test` ejecuta las pruebas de Node, arranca internamente una instancia temporal en un puerto libre y la cierra al terminar. `npm test` no es la misma operación que `npm start`.

> **Regla de oro:** `crear y guardar archivo → agregar su script → ejecutar en terminal → observar salida → explicar → continuar al siguiente EJ`. No avances si el nombre del archivo aún no existe o si un script anterior falló.

### 3.2 Cómo editar el objeto `scripts` sin romper el JSON

Un `package.json` tiene **una única** pareja de llaves exteriores `{ ... }`. `scripts` está al mismo nivel que `dependencies`, nunca dentro de `dependencies`. Por ejemplo, cuando ya existen EJ01 y EJ02, la sección que editas queda así:

```json
"scripts": {
  "ej01": "node ejemplos/EJ01_bcrypt.js",
  "ej02": "node ejemplos/EJ02_jwt.js"
}
```

El ejemplo anterior es **solo el fragmento** que va dentro del `package.json` original; no debe guardarse como archivo JSON aislado. La coma entre `ej01` y `ej02` separa dos propiedades; **no** coloques una coma después del último script. Cuando haya otra propiedad después del objeto `scripts`, sí necesitarás una coma **después de la llave de cierre de `scripts`**. Si VS Code marca error JSON, revisa ambas posiciones antes de ejecutar.

**Comprueba la sintaxis del archivo completo** desde la terminal:

```bash
node -e "const p=require('./package.json'); console.log('Scripts configurados:', Object.keys(p.scripts))"
```

El resultado mostrará `Scripts configurados: []` al comienzo; luego irá incluyendo `ej01`, `ej02`, `start` y `test`. Esta comprobación solo lee JSON: **no ejecuta** ninguno de los scripts.


---

# EJ01 — Hash de contraseña con bcrypt.js

**Objetivo observable:** Los resultados de compare deben ser true y false sin guardar la clave en texto plano.

**Problema actual y por qué se necesita:** Un login necesita verificar contraseñas sin almacenarlas en claro.

**Archivos involucrados:** `ejemplos/EJ01_bcrypt.js`. **Punto de partida:** Carpeta creada, `.env` con LAB_PASSWORD, dependencias instaladas

**Habilidad nueva:** hash con salt y comparación asíncrona. **Tarea espejo:** T01.


### Microbloque 1 — Cargar configuración y biblioteca

**Antes:** Archivo vacío **Por qué:** Necesitamos leer configuración local y utilizar bcrypt.js.

**En `ejemplos/EJ01_bcrypt.js`, escribe o añade:**

```javascript
require('dotenv').config();
const bcrypt = require('bcryptjs');
```

**Explicación:** dotenv.config() carga las variables de `.env` en `process.env`; require() importa el paquete. `bcrypt` ofrece hash() y compare().

**Elementos nuevos:** dotenv, require, bcrypt

**Después:** Podemos tomar la contraseña ficticia sin escribirla en el código.


### Microbloque 2 — Comprobar el dato de entrada

**Antes:** Tenemos imports **Por qué:** No debemos procesar un valor ausente.

**En `ejemplos/EJ01_bcrypt.js`, escribe o añade:**

```javascript
async function main() {
  const password = process.env.LAB_PASSWORD;
  if (!password) throw new Error('Configura LAB_PASSWORD en .env (solo valor ficticio)');
```

**Explicación:** `async` habilita `await`; `password` representa la entrada temporal en memoria, no un valor guardado; la condición detiene la prueba si falta configuración.

**Elementos nuevos:** async, await, process.env, Error

**Después:** Si falta LAB_PASSWORD, la ejecución fallará con mensaje claro.


### Microbloque 3 — Transformar y contrastar

**Antes:** Existe password temporal **Por qué:** Necesitamos un hash y dos comprobaciones independientes.

**En `ejemplos/EJ01_bcrypt.js`, escribe o añade:**

```javascript
const hash = await bcrypt.hash(password, 10);
  const correcta = await bcrypt.compare(password, hash);
  const incorrecta = await bcrypt.compare('otra-clave-ficticia', hash);
```

**Explicación:** `hash()` crea un hash con coste 10 y salt gestionado por bcrypt; `compare()` verifica una contraseña candidata frente al hash. La segunda candidata es distinta para probar fallo. Nunca se descifra el hash.

**Elementos nuevos:** hash, correcta, incorrecta

**Después:** Dos comprobaciones tienen valores true y false.


### Microbloque 4 — Mostrar solo resultados y terminar

**Antes:** Existen dos booleanos **Por qué:** No debemos imprimir la contraseña.

**En `ejemplos/EJ01_bcrypt.js`, escribe o añade:**

```javascript
console.log('Hash generado:', hash.startsWith('$2'));
  console.log('Contraseña correcta:', correcta);
  console.log('Contraseña diferente:', incorrecta);
}
main().catch(error => { console.error(error.message); process.exitCode = 1; });
```

**Explicación:** La consola muestra validaciones, no el secreto. `main().catch()` informa fallos sin continuar ocultamente; `process.exitCode=1` marca error al sistema.

**Elementos nuevos:** console.log, startsWith, catch, exitCode

**Después:** Queda un script ejecutable.


## Archivo(s) completo(s) al terminar este ejemplo

### `ejemplos/EJ01_bcrypt.js`

```javascript
require('dotenv').config();
const bcrypt = require('bcryptjs');

async function main() {
  const password = process.env.LAB_PASSWORD;
  if (!password) throw new Error('Configura LAB_PASSWORD en .env (solo valor ficticio)');
  const hash = await bcrypt.hash(password, 10);
  const correcta = await bcrypt.compare(password, hash);
  const incorrecta = await bcrypt.compare('otra-clave-ficticia', hash);
  console.log('Hash generado:', hash.startsWith('$2'));
  console.log('Contraseña correcta:', correcta);
  console.log('Contraseña diferente:', incorrecta);
}

main().catch(error => { console.error(error.message); process.exitCode = 1; });
```


### Paso final de EJ01 — crear el primer script y ejecutarlo

1. Comprueba que `ejemplos/EJ01_bcrypt.js` **ya tiene todas las líneas del ejemplo** y está guardado.
2. Abre `package.json` desde el explorador de VS Code y localiza `"scripts": {}`.
3. **Dentro de esas llaves**, escribe exactamente:

```json
"scripts": {
  "ej01": "node ejemplos/EJ01_bcrypt.js"
}
```

**Por qué:** `ej01` es el nombre de nuestro atajo; `node` llama al intérprete JavaScript; `ejemplos/` indica la carpeta; `EJ01_bcrypt.js` es el archivo que acabas de escribir. No está ejecutando Express ni levantando un servidor: solo verifica el proceso de hashing y comparación.

4. Guarda `package.json`, abre la terminal **en la raíz** y ejecuta `npm run ej01`.
5. Interpreta la salida: `Hash generado: true` demuestra que se obtuvo un hash con el formato esperado, `Contraseña correcta: true` verifica la coincidencia y `Contraseña diferente: false` confirma que otra candidata es rechazada. `npm run ej01` termina; puedes escribir enseguida el siguiente comando.
6. **No ejecutes todavía `npm run ej02`, `npm start` ni `npm test`:** todavía no has creado los archivos que necesitan.


## Auditoría antes de ejecutar

**Qué cambia y qué queda:** El texto sensible está solo en memoria; se almacena/contrasta el hash, no la clave. Identifica cada import, variable de entorno, condición, respuesta y ruta vistos arriba; si no puedes explicar alguno, revísalo antes de avanzar.

## Antes de ejecutar: predicción

1. ¿El primer compare devolverá true? 2. ¿La clave distinta devolverá true o false?

## Ejecuta y valida

Ejecuta `npm run ej01` en la terminal de la carpeta raíz.

**Resultado esperado:** `Hash generado: true`, `Contraseña correcta: true`, `Contraseña diferente: false`.

**Interpretación:** El hash se compara; no se recupera la contraseña original.

## Variación razonada

Cambia solo LAB_PASSWORD en .env por otra clave ficticia. El hash cambia, pero los booleanos mantienen su significado.

## Error o caso límite controlado

Deja LAB_PASSWORD vacío durante una ejecución controlada.

**Por qué ocurre:** No hay contraseña de entrada disponible.

**Corrección:** Restituye un valor ficticio en .env y repite.

**Explica con tus palabras:** qué información entra, qué valida el servidor, qué cambia en memoria/cabeceras y por qué el resultado es correcto.

## T01 — Tarea espejo de EJ01

**Enunciado:** Modificar un script para verificar una clave ficticia correcta y otra incorrecta.

**Archivo a crear/editar:** `tareas/T01.js` y, si necesitas consultar el original, `ejemplos/EJ01_bcrypt.js`. Crea el archivo `tareas/T01.js` con clic derecho en la carpeta y escribe una variante propia de lo aprendido.

**Restricción:** sin contraseñas reales, sin publicar tokens, sin dar rol `admin` por el cuerpo del registro. **Pista:** primero reproduce el flujo del ejemplo con una entrada diferente; después agrega el caso negativo.

**Evidencia:** Dos salidas booleanas diferentes; no mostrar el valor original.

**Cómo validar:** `node tareas/T01.js` desde la raíz del proyecto, con las dependencias y `.env` ya preparados. Compara el resultado con la predicción y explica por qué funciona.


---

# EJ02 — Generar y verificar un JWT firmado

**Objetivo observable:** Observar identidad verificada y rechazo de token modificado.

**Problema actual y por qué se necesita:** Después de autenticar, una API puede devolver una credencial firmada que acompaña futuras peticiones.

**Archivos involucrados:** `ejemplos/EJ02_jwt.js`. **Punto de partida:** JWT_SECRET local generado; no copiar valores a código

**Habilidad nueva:** sign y verify con caducidad. **Tarea espejo:** T02.


### Microbloque 1 — Importar librerías y validar secreto

**Antes:** Existe .env **Por qué:** El emisor y verificador necesitan la misma clave local.

**En `ejemplos/EJ02_jwt.js`, escribe o añade:**

```javascript
require('dotenv').config();
const jwt = require('jsonwebtoken');
const secreto = process.env.JWT_SECRET;
if (!secreto || secreto.length < 48) throw new Error('Configura JWT_SECRET (mínimo 48 caracteres)');
```

**Explicación:** `jwt` es la biblioteca de firma/verificación. No uses una cadena fija como `mi_secreto` en el código; el guard evita continuar sin secreto suficientemente largo para la práctica.

**Elementos nuevos:** jsonwebtoken, secreto, 48 caracteres

**Después:** La clave queda fuera del repositorio.


### Microbloque 2 — Crear token mínimo

**Antes:** La clave es válida **Por qué:** El payload debe contener solo identificación y rol necesarios.

**En `ejemplos/EJ02_jwt.js`, escribe o añade:**

```javascript
const token = jwt.sign({ id: 10, rol: 'usuario' }, secreto, {
  expiresIn: '1h', algorithm: 'HS256'
});
```

**Explicación:** `sign()` firma datos con HS256. `id` y `rol` son claims de demostración. `expiresIn` limita vigencia. El payload es visible si alguien obtiene el JWT; no agregues contraseña.

**Elementos nuevos:** payload, HS256, expiresIn

**Después:** Existe JWT temporal en memoria.


### Microbloque 3 — Verificar firma y caducidad

**Antes:** Token firmado **Por qué:** La recepción de texto no basta: la firma debe validarse.

**En `ejemplos/EJ02_jwt.js`, escribe o añade:**

```javascript
const usuario = jwt.verify(token, secreto, { algorithms: ['HS256'] });
console.log('Token validado:', usuario.id, usuario.rol);
```

**Explicación:** `verify()` valida firma y vencimiento. `algorithms` limita el algoritmo esperado; `usuario` es el payload verificado. Mostramos solo id y rol.

**Elementos nuevos:** verify, algorithms, usuario

**Después:** Se puede confiar en esos claims para esta prueba.


### Microbloque 4 — Rechazar manipulación

**Antes:** Token válido probado **Por qué:** Necesitamos observar un caso no válido.

**En `ejemplos/EJ02_jwt.js`, escribe o añade:**

```javascript
try {
  jwt.verify(`${token}alterado`, secreto, { algorithms: ['HS256'] });
} catch (error) {
  console.log('Token alterado: rechazado');
}
```

**Explicación:** Se altera el texto transmitido; `verify()` detecta que no corresponde a una firma válida. `try/catch` captura el error esperado de esa prueba.

**Elementos nuevos:** try, catch, template literal

**Después:** Queda demostrado el rechazo.


## Archivo(s) completo(s) al terminar este ejemplo

### `ejemplos/EJ02_jwt.js`

```javascript
require('dotenv').config();
const jwt = require('jsonwebtoken');

const secreto = process.env.JWT_SECRET;
if (!secreto || secreto.length < 48) throw new Error('Configura JWT_SECRET (mínimo 48 caracteres)');
const token = jwt.sign({ id: 10, rol: 'usuario' }, secreto, {
  expiresIn: '1h', algorithm: 'HS256'
});
const usuario = jwt.verify(token, secreto, { algorithms: ['HS256'] });
console.log('Token validado:', usuario.id, usuario.rol);
try {
  jwt.verify(`${token}alterado`, secreto, { algorithms: ['HS256'] });
} catch (error) {
  console.log('Token alterado: rechazado');
}
```


### Paso final de EJ02 — añadir el segundo script sin borrar el primero

1. Guarda completo `ejemplos/EJ02_jwt.js`. Abre `package.json`, localiza el script `ej01` existente y **añade otra propiedad** separada por coma:

```json
"scripts": {
  "ej01": "node ejemplos/EJ01_bcrypt.js",
  "ej02": "node ejemplos/EJ02_jwt.js"
}
```

2. **No borres `ej01`** ni `dependencies`. `ej02` es un atajo para que Node ejecute el archivo `EJ02_jwt.js`; necesita `JWT_SECRET` definido en `.env`, pero no requiere Express iniciado.
3. Desde la raíz ejecuta primero `npm run` y comprueba que aparecen ambos nombres. Luego ejecuta `npm run ej02`.
4. Observa que la firma verifica un token que hemos creado, y que una modificación produce rechazo. **No publiques el token** ni su secreto. Como este es otro script de consola, **termina solo**.
5. Si aparece `Missing script: "ej02"`, revisa el nombre de la clave en `scripts`. Si aparece `Cannot find module ... EJ02_jwt.js`, revisa la ruta y que el archivo existe; son problemas diferentes.


## Auditoría antes de ejecutar

**Qué cambia y qué queda:** Se verifica autenticidad, no confidencialidad. Identifica cada import, variable de entorno, condición, respuesta y ruta vistos arriba; si no puedes explicar alguno, revísalo antes de avanzar.

## Antes de ejecutar: predicción

1. ¿Se muestra el JWT? 2. ¿La alteración debe aceptarse?

## Ejecuta y valida

Ejecuta `npm run ej02`.

**Resultado esperado:** `Token validado: 10 usuario` y `Token alterado: rechazado`.

**Interpretación:** Una firma detecta manipulación; no cifra el payload.

## Variación razonada

Prueba `expiresIn: "15m"` y explica la diferencia de política de duración (no necesitas esperar quince minutos).

## Error o caso límite controlado

Borra JWT_SECRET temporalmente de .env.

**Por qué ocurre:** La firma no tiene clave y no debe intentarse.

**Corrección:** Regenera/copia un secreto local; no uses una clave del PPT.

**Explica con tus palabras:** qué información entra, qué valida el servidor, qué cambia en memoria/cabeceras y por qué el resultado es correcto.

## T02 — Tarea espejo de EJ02

**Enunciado:** Emitir un JWT ficticio de 15 minutos para id 25; verificarlo y rechazar uno manipulado.

**Archivo a crear/editar:** `tareas/T02.js` y, si necesitas consultar el original, `ejemplos/EJ02_jwt.js`. Crea el archivo `tareas/T02.js` con clic derecho en la carpeta y escribe una variante propia de lo aprendido.

**Restricción:** sin contraseñas reales, sin publicar tokens, sin dar rol `admin` por el cuerpo del registro. **Pista:** primero reproduce el flujo del ejemplo con una entrada diferente; después agrega el caso negativo.

**Evidencia:** Claims válidos y rechazo de token manipulado, sin imprimir JWT.

**Cómo validar:** `node tareas/T02.js` desde la raíz del proyecto, con las dependencias y `.env` ya preparados. Compara el resultado con la predicción y explica por qué funciona.


---

# EJ03 — API de registro y login con Express

**Objetivo observable:** Crear cuenta con hash, recuperar acceso con contraseña correcta y emitir JWT.

**Problema actual y por qué se necesita:** Pasamos de scripts aislados a endpoints HTTP que autentican una identidad registrada.

**Archivos involucrados:** `src/server.js y src/crear-app.js`. **Punto de partida:** Tenemos npm, .env y conceptos de Express previos

**Habilidad nueva:** dos operaciones HTTP y memoria temporal. **Tarea espejo:** T03.


### Microbloque 1 — Servidor de arranque

**Antes:** El servidor no existe **Por qué:** Separar ejecución de construcción del app permite probar sin abrir puerto fijo.

**En `src/server.js`, escribe o añade:**

```javascript
require('dotenv').config();
const { crearApp } = require('./crear-app');
```

**Explicación:** `./crear-app` es un módulo que crearás a continuación; la desestructuración importa su función exportada. Se carga .env antes de construir la API.

**Elementos nuevos:** crearApp, import relativo

**Después:** Podemos construirlo desde un módulo separado.


### Microbloque 2 — Inicializar y escuchar

**Antes:** Módulo aún no creado **Por qué:** Express debe comenzar a atender peticiones cuando estén definidas las rutas.

**En `src/server.js`, escribe o añade:**

```javascript
async function iniciar() {
  const app = await crearApp();
  const puerto = Number(process.env.PORT || 3001);
  app.listen(puerto, () => console.log(`API de laboratorio: http://localhost:${puerto}`));
}
iniciar().catch(error => { console.error(error.message); process.exitCode = 1; });
```

**Explicación:** La función crea app, elige puerto 3001 si no existe PORT, escucha e informa la URL. En la práctica no deberías iniciar múltiples ejemplos a la vez porque compartirían puerto.

**Elementos nuevos:** listen, PORT, async iniciar

**Después:** El punto de entrada queda preparado.


### Microbloque 3 — Dependencias de API

**Antes:** Archivo vacío **Por qué:** Express maneja rutas, bcrypt guarda hash y JWT crea tokens.

**En `src/crear-app.js`, escribe o añade:**

```javascript
const express = require('express');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
```

**Explicación:** Estas bibliotecas ya fueron instaladas y usadas: Express expone `app`, bcrypt protege contraseñas y jsonwebtoken firma identidad.

**Elementos nuevos:** express, app

**Después:** Listo para construir aplicación.


### Microbloque 4 — Estado inicial y clave

**Antes:** No hay servidor configurado **Por qué:** Necesitamos validar secreto, permitir JSON y mantener usuarios temporales.

**En `src/crear-app.js`, escribe o añade:**

```javascript
async function crearApp() {
  const secreto = process.env.JWT_SECRET;
  if (!secreto || secreto.length < 48) throw new Error('JWT_SECRET faltante o demasiado corto');
  const app = express();
  app.use(express.json());
  const usuarios = [];
  let siguienteId = 1;
```

**Explicación:** `express.json()` interpreta cuerpos JSON. `usuarios` es un arreglo efímero con registros ficticios y `siguienteId` genera IDs nuevos. El orden importa: parseo antes de rutas, rutas antes de return.

**Elementos nuevos:** usuarios, siguienteId

**Después:** Puede recibir JSON y alojar datos durante este proceso.


### Microbloque 5 — Crear cuenta administrativa de laboratorio

**Antes:** Lista vacía **Por qué:** La ruta pública de registro jamás debe asignar admin; para demostración un admin ficticio nace desde variable local.

**En `src/crear-app.js`, escribe o añade:**

```javascript
if (process.env.DEMO_ADMIN_PASSWORD) {
  usuarios.push({
    id: siguienteId++, email: 'admin@ejemplo.test', rol: 'admin',
    hash: await bcrypt.hash(process.env.DEMO_ADMIN_PASSWORD, 10)
  });
}
```

**Explicación:** Solo si existe DEMO_ADMIN_PASSWORD se inserta cuenta de práctica con hash. `push` agrega elemento; `siguienteId++` entrega el ID y después incrementa. No existe endpoint público para volverse admin.

**Elementos nuevos:** DEMO_ADMIN_PASSWORD, admin, push

**Después:** Hay un admin ficticio opcional de prueba.


### Microbloque 6 — Ruta pública y datos de registro

**Antes:** JSON disponible **Por qué:** Queremos comprobar conectividad e impedir datos incompletos.

**En `src/crear-app.js`, escribe o añade:**

```javascript
app.get('/publico', (req, res) => res.json({ mensaje: 'Ruta pública disponible' }));
app.post('/registro', async (req, res) => {
  const { email, password } = req.body || {};
  if (typeof email !== 'string' || !email.includes('@') ||
      typeof password !== 'string' || password.length < 8) {
    return res.status(400).json({ mensaje: 'Datos de registro inválidos' });
  }
```

**Explicación:** `GET /publico` no requiere login. `POST /registro` toma dos campos del JSON y rechaza tipo/formato básico inválido con 400. Es validación mínima de laboratorio, no un validador de producción.

**Elementos nuevos:** req.body, email, password, 400

**Después:** El servidor distingue cuerpo correcto de uno incompleto.


### Microbloque 7 — Evitar duplicado y guardar hash

**Antes:** Datos validados **Por qué:** No se debe crear el mismo correo dos veces ni almacenar password.

**En `src/crear-app.js`, escribe o añade:**

```javascript
if (usuarios.some(u => u.email === email)) {
  return res.status(409).json({ mensaje: 'El correo ya existe' });
}
const usuario = {
  id: siguienteId++, email, rol: 'usuario',
  hash: await bcrypt.hash(password, 10)
};
usuarios.push(usuario);
return res.status(201).json({ id: usuario.id, email: usuario.email, rol: usuario.rol });
});
```

**Explicación:** `some` detecta correo ya presente, 409 es conflicto. `rol` siempre es `usuario` aunque el cliente envíe otro. Se guarda `hash`, nunca `password`, y la respuesta 201 omite el hash.

**Elementos nuevos:** some, 409, 201

**Después:** Registro guarda hash y devuelve metadatos no sensibles.


### Microbloque 8 — Login y emisión condicionada

**Antes:** Usuario registrado **Por qué:** Solo quien presenta contraseña verificada recibe token.

**En `src/crear-app.js`, escribe o añade:**

```javascript
app.post('/login', async (req, res) => {
  const { email, password } = req.body || {};
  const usuario = usuarios.find(u => u.email === email);
  if (!usuario || typeof password !== 'string' ||
      !(await bcrypt.compare(password, usuario.hash))) {
    return res.status(401).json({ mensaje: 'Credenciales no válidas' });
  }
```

**Explicación:** `find` localiza la cuenta; `compare` contrasta texto temporal con hash almacenado. Si la cuenta o clave fallan, 401 no revela cuál de ambas falló.

**Elementos nuevos:** find, 401

**Después:** Un usuario inválido no recibe token.


### Microbloque 9 — Firmar token y exportar app

**Antes:** Credenciales verificadas **Por qué:** Completar respuesta del login sin exponer el secreto.

**En `src/crear-app.js`, escribe o añade:**

```javascript
const token = jwt.sign({ id: usuario.id, rol: usuario.rol }, secreto, {
  expiresIn: '1h', algorithm: 'HS256'
});
return res.json({ token, rol: usuario.rol });
});
return app;
}
module.exports = { crearApp };
```

**Explicación:** Se firma solo id/rol. `module.exports` permite a server.js importar crearApp. El JWT aparece **solo** en la respuesta al cliente; no lo copies a un documento público.

**Elementos nuevos:** token, module.exports

**Después:** La API está lista para iniciar.


## Archivo(s) completo(s) al terminar este ejemplo

### `src/server.js`

```javascript
require('dotenv').config();
const { crearApp } = require('./crear-app');

async function iniciar() {
  const app = await crearApp();
  const puerto = Number(process.env.PORT || 3001);
  app.listen(puerto, () => console.log(`API de laboratorio: http://localhost:${puerto}`));
}
iniciar().catch(error => { console.error(error.message); process.exitCode = 1; });
```
### `src/crear-app.js`

```javascript
const express = require('express');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');

async function crearApp() {
  const secreto = process.env.JWT_SECRET;
  if (!secreto || secreto.length < 48) throw new Error('JWT_SECRET faltante o demasiado corto');
  const app = express();
  app.use(express.json());
  const usuarios = [];
  let siguienteId = 1;

  if (process.env.DEMO_ADMIN_PASSWORD) {
    usuarios.push({
      id: siguienteId++, email: 'admin@ejemplo.test', rol: 'admin',
      hash: await bcrypt.hash(process.env.DEMO_ADMIN_PASSWORD, 10)
    });
  }

  app.get('/publico', (req, res) => res.json({ mensaje: 'Ruta pública disponible' }));

  app.post('/registro', async (req, res) => {
    const { email, password } = req.body || {};
    if (typeof email !== 'string' || !email.includes('@') ||
        typeof password !== 'string' || password.length < 8) {
      return res.status(400).json({ mensaje: 'Datos de registro inválidos' });
    }
    if (usuarios.some(u => u.email === email)) {
      return res.status(409).json({ mensaje: 'El correo ya existe' });
    }
    const usuario = {
      id: siguienteId++, email, rol: 'usuario',
      hash: await bcrypt.hash(password, 10)
    };
    usuarios.push(usuario);
    return res.status(201).json({ id: usuario.id, email: usuario.email, rol: usuario.rol });
  });

  app.post('/login', async (req, res) => {
    const { email, password } = req.body || {};
    const usuario = usuarios.find(u => u.email === email);
    if (!usuario || typeof password !== 'string' ||
        !(await bcrypt.compare(password, usuario.hash))) {
      return res.status(401).json({ mensaje: 'Credenciales no válidas' });
    }
    const token = jwt.sign({ id: usuario.id, rol: usuario.rol }, secreto, {
      expiresIn: '1h', algorithm: 'HS256'
    });
    return res.json({ token, rol: usuario.rol });
  });

  return app;
}

module.exports = { crearApp };
```


### Paso final de EJ03 — incorporar `start` y levantar por primera vez Express

1. **Antes de arrancar**, comprueba que estén guardados **los dos archivos** `src/server.js` y `src/crear-app.js`; `server.js` importa a `crear-app.js`, por eso ninguno debe faltar.
2. En `package.json`, conserva los dos scripts anteriores y agrega `start` dentro del mismo objeto:

```json
"scripts": {
  "ej01": "node ejemplos/EJ01_bcrypt.js",
  "ej02": "node ejemplos/EJ02_jwt.js",
  "start": "node src/server.js"
}
```

3. Guarda, ubícate en la raíz del proyecto y ejecuta `npm start` (o `npm run start`, ambos usan el mismo script). npm traduce esa línea a `node src/server.js`: Node ejecuta `iniciar()`, `crearApp()` construye Express, `app.listen(...)` abre el puerto 3001 y la terminal queda ocupada porque el servidor **permanece escuchando**.
4. Debe mostrarse `API de laboratorio: http://localhost:3001`. Esto **no significa** que ya se haya probado el login; solo confirma el arranque del proceso.
5. Sin cerrar esta terminal, abre **Terminal → Nuevo terminal** en VS Code, confirma que estás en la misma carpeta y ejecuta:

```bash
node -e "fetch('http://localhost:3001/publico').then(r=>r.json()).then(console.log)"
```

6. **Qué ocurre:** el segundo proceso Node hace una petición GET a la API que se encuentra encendida en el primer terminal. La respuesta muestra el mensaje de la ruta pública; si el servidor no está activo obtendrás un error de conexión.
7. Para detener el servidor, regresa al primer terminal y pulsa **Ctrl+C**. Al modificar archivos `src/*.js` en EJ04, EJ05 o EJ06, necesitarás ejecutar otra vez `npm start`: nuestro laboratorio **no incorpora reinicio automático** (no usamos nodemon).


## Auditoría antes de ejecutar

**Qué cambia y qué queda:** El servidor conserva usuarios mientras está encendido, pero no los persiste al reiniciar. Identifica cada import, variable de entorno, condición, respuesta y ruta vistos arriba; si no puedes explicar alguno, revísalo antes de avanzar.

## Antes de ejecutar: predicción

1. ¿Qué devuelve GET /publico? 2. ¿Cuál estado de POST /registro exitoso? 3. ¿El usuario puede registrarse como admin?

## Ejecuta y valida

Ejecuta `npm start` y deja esta terminal abierta. En **otra terminal** del mismo proyecto ejecuta `node -e "fetch('http://localhost:3001/publico').then(r=>r.json()).then(console.log)"`. Para probar POST puedes usar `fetch()` en la consola Node con un cuerpo JSON y contraseña ficticia; EJ07 dará una prueba automatizada reproducible.

**Resultado esperado:** `Ruta pública disponible`; registros válidos 201; login correcto devuelve token; login incorrecto 401.

**Interpretación:** El arreglo `usuarios` desaparece al apagar servidor; el cliente no asigna roles.

## Variación razonada

Envía registro sin email: debe dar 400; intenta registrar mismo correo: 409.

## Error o caso límite controlado

Intenta ejecutar `npm start` sin JWT_SECRET o con puerto ocupado.

**Por qué ocurre:** Falta entorno o existe un servidor escuchando.

**Corrección:** Restituye JWT_SECRET o detén el proceso anterior con Ctrl+C; reinicia.

**Explica con tus palabras:** qué información entra, qué valida el servidor, qué cambia en memoria/cabeceras y por qué el resultado es correcto.

## T03 — Tarea espejo de EJ03

**Enunciado:** Enviar registro solicitando rol admin y demostrar que el servidor crea usuario; repetir email y observar 409.

**Archivo a crear/editar:** `tareas/T03.js` y, si necesitas consultar el original, `src/crear-app.js`. Crea el archivo `tareas/T03.js` con clic derecho en la carpeta y escribe una variante propia de lo aprendido.

**Restricción:** sin contraseñas reales, sin publicar tokens, sin dar rol `admin` por el cuerpo del registro. **Pista:** primero reproduce el flujo del ejemplo con una entrada diferente; después agrega el caso negativo.

**Evidencia:** 201 con rol usuario, sin hash, y 409 para correo duplicado.

**Cómo validar:** `node tareas/T03.js` desde la raíz del proyecto, con las dependencias y `.env` ya preparados. Compara el resultado con la predicción y explica por qué funciona.



### Prueba manual completa del registro y login (EJ03)

Mantén `npm start` abierto. En otra terminal ejecuta `node` para abrir su consola interactiva. Escribe **estas instrucciones una por una**; la contraseña de esta prueba se genera durante la ejecución y no queda escrita en los archivos:

```javascript
const { randomBytes } = require('node:crypto');
const base = 'http://localhost:3001';
const cuenta = {
  email: 'alumno_s07@ejemplo.test',
  password: randomBytes(18).toString('base64url')
};
const registro = await fetch(`${base}/registro`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ ...cuenta, rol: 'admin' })
});
console.log(registro.status, await registro.json());
const ingreso = await fetch(`${base}/login`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(cuenta)
});
console.log('Estado de login:', ingreso.status);
const respuesta = await ingreso.json();
const tokenDeClase = respuesta.token;
console.log('Token recibido:', Boolean(tokenDeClase));
```

**Qué comprobar:** el registro devuelve `201` con `rol: "usuario"` aunque la petición haya declarado `"admin"`; el login devuelve `200` y el indicador del token será `true`. No imprimas ni copies el token completo. Si obtienes `409`, probablemente registraste ese correo en el mismo proceso; cambia el email ficticio o reinicia el servidor (su memoria se vacía). Conserva abierta esta consola si deseas reutilizar `tokenDeClase` en EJ04 y EJ05. La próxima comprobación usa esa misma variable:

```javascript
const sinCredencial = await fetch(`${base}/perfil`);
console.log('Sin token:', sinCredencial.status);
// Ejecuta las dos líneas siguientes solo después de construir EJ04.
const conCredencial = await fetch(`${base}/perfil`, {
  headers: { Authorization: `Bearer ${tokenDeClase}` }
});
console.log('Con Bearer:', conCredencial.status);
```

**Atención:** si detienes `npm start` y vuelves a iniciarlo, el servidor vuelve a crear su arreglo temporal y el usuario anterior deja de existir. El JWT anterior, sin embargo, aún puede conservar su firma durante una hora; en esta práctica, `/perfil` confía en los claims firmados y no consulta si el usuario continúa registrado. Esto constituye una limitación explícita del laboratorio en memoria, no un modelo completo de revocación de sesiones.


---

# EJ04 — Middleware Bearer y /perfil

**Objetivo observable:** El endpoint perfil debe responder 401 sin token y 200 con token válido.

**Problema actual y por qué se necesita:** Un login exitoso no protege automáticamente las rutas; hay que verificar el encabezado en cada acceso.

**Archivos involucrados:** `src/middleware/autenticacion.js y src/crear-app.js`. **Punto de partida:** EJ03 funcionando

**Habilidad nueva:** middleware next() y req.usuario. **Tarea espejo:** T04.


### Microbloque 1 — Importar JWT y leer Authorization

**Antes:** Archivo nuevo **Por qué:** Un middleware debe extraer la credencial enviada por el cliente.

**En `src/middleware/autenticacion.js`, escribe o añade:**

```javascript
const jwt = require('jsonwebtoken');
function verificarToken(req, res, next) {
  const encabezado = req.headers.authorization || '';
  const partes = encabezado.split(' ');
```

**Explicación:** `authorization` es cabecera HTTP. El formato esperado son dos partes: `Bearer` y token; `split` los separa. `next` delega al siguiente middleware/ruta si procede.

**Elementos nuevos:** encabezado, partes, next

**Después:** Tenemos los componentes del encabezado.


### Microbloque 2 — Fallar ante formato incorrecto

**Antes:** Cabecera interpretada **Por qué:** Un token ausente no puede considerarse autenticado.

**En `src/middleware/autenticacion.js`, escribe o añade:**

```javascript
if (partes.length !== 2 || partes[0] !== 'Bearer' || !partes[1]) {
  return res.status(401).json({ mensaje: 'Se requiere Bearer token' });
}
```

**Explicación:** La condición verifica longitud, esquema y contenido. `return` impide continuar al handler; 401 es un problema de autenticación.

**Elementos nuevos:** Bearer, 401

**Después:** Solicitudes sin credencial terminan aquí.


### Microbloque 3 — Validar firma y conservar claims

**Antes:** Formateo válido **Por qué:** Necesitamos verificar el JWT antes de entregar acceso.

**En `src/middleware/autenticacion.js`, escribe o añade:**

```javascript
try {
  req.usuario = jwt.verify(partes[1], process.env.JWT_SECRET, {
    algorithms: ['HS256']
  });
  return next();
} catch (error) {
  return res.status(401).json({ mensaje: 'Token inválido o vencido' });
}
}
module.exports = { verificarToken };
```

**Explicación:** Un token válido deja identidad comprobada en req.usuario y llama next. Si la firma o caducidad fallan, 401. A diferencia de 403, aquí no hay identidad autenticada aceptada.

**Elementos nuevos:** req.usuario, next, catch

**Después:** Middleware puede proteger rutas.


### Microbloque 4 — Conectar middleware antes del handler

**Antes:** API EJ03 **Por qué:** Es necesario usarlo explícitamente en la ruta.

**En `src/crear-app.js`, escribe o añade:**

```javascript
const { verificarToken } = require('./middleware/autenticacion');
// Dentro de crearApp(), justo antes de `return app;`:
app.get('/perfil', verificarToken, (req, res) => {
  res.json({ id: req.usuario.id, rol: req.usuario.rol });
});
```

**Explicación:** Agrega el `require` junto a los otros imports; coloca app.get antes de return app. El orden importa: primero verificarToken, después el callback de perfil. Solo se devuelven id y rol.

**Elementos nuevos:** /perfil, orden middleware

**Después:** La ruta privada deja de ser pública.


## Archivo(s) completo(s) al terminar este ejemplo

### `src/middleware/autenticacion.js`

```javascript
const jwt = require('jsonwebtoken');

function verificarToken(req, res, next) {
  const encabezado = req.headers.authorization || '';
  const partes = encabezado.split(' ');
  if (partes.length !== 2 || partes[0] !== 'Bearer' || !partes[1]) {
    return res.status(401).json({ mensaje: 'Se requiere Bearer token' });
  }
  try {
    req.usuario = jwt.verify(partes[1], process.env.JWT_SECRET, {
      algorithms: ['HS256']
    });
    return next();
  } catch (error) {
    return res.status(401).json({ mensaje: 'Token inválido o vencido' });
  }
}
module.exports = { verificarToken };
```
### `src/crear-app.js`

```javascript
const express = require('express');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
const { verificarToken } = require('./middleware/autenticacion');

async function crearApp() {
  const secreto = process.env.JWT_SECRET;
  if (!secreto || secreto.length < 48) throw new Error('JWT_SECRET faltante o demasiado corto');
  const app = express();
  app.use(express.json());
  const usuarios = [];
  let siguienteId = 1;

  if (process.env.DEMO_ADMIN_PASSWORD) {
    usuarios.push({
      id: siguienteId++, email: 'admin@ejemplo.test', rol: 'admin',
      hash: await bcrypt.hash(process.env.DEMO_ADMIN_PASSWORD, 10)
    });
  }

  app.get('/publico', (req, res) => res.json({ mensaje: 'Ruta pública disponible' }));

  app.post('/registro', async (req, res) => {
    const { email, password } = req.body || {};
    if (typeof email !== 'string' || !email.includes('@') ||
        typeof password !== 'string' || password.length < 8) {
      return res.status(400).json({ mensaje: 'Datos de registro inválidos' });
    }
    if (usuarios.some(u => u.email === email)) {
      return res.status(409).json({ mensaje: 'El correo ya existe' });
    }
    const usuario = {
      id: siguienteId++, email, rol: 'usuario',
      hash: await bcrypt.hash(password, 10)
    };
    usuarios.push(usuario);
    return res.status(201).json({ id: usuario.id, email: usuario.email, rol: usuario.rol });
  });

  app.post('/login', async (req, res) => {
    const { email, password } = req.body || {};
    const usuario = usuarios.find(u => u.email === email);
    if (!usuario || typeof password !== 'string' ||
        !(await bcrypt.compare(password, usuario.hash))) {
      return res.status(401).json({ mensaje: 'Credenciales no válidas' });
    }
    const token = jwt.sign({ id: usuario.id, rol: usuario.rol }, secreto, {
      expiresIn: '1h', algorithm: 'HS256'
    });
    return res.json({ token, rol: usuario.rol });
  });

  app.get('/perfil', verificarToken, (req, res) => {
    res.json({ id: req.usuario.id, rol: req.usuario.rol });
  });

  return app;
}

module.exports = { crearApp };
```


## Auditoría antes de ejecutar

**Qué cambia y qué queda:** Un login produce un JWT, pero /perfil exige verificarlo en la cabecera. Identifica cada import, variable de entorno, condición, respuesta y ruta vistos arriba; si no puedes explicar alguno, revísalo antes de avanzar.

## Antes de ejecutar: predicción

1. ¿Qué status sin cabecera? 2. ¿Qué status con token válido?

## Ejecuta y valida

**EJ04 reutiliza `start`; no agregues un nuevo script.** Comprueba que `src/middleware/autenticacion.js` esté guardado y que `src/crear-app.js` tenga el middleware conectado. Ve a la terminal del servidor, pulsa **Ctrl+C** y vuelve a ejecutar `npm start`. Abre una **segunda terminal** para la petición. Usa consola/cliente HTTP con `Authorization: Bearer TOKEN_DE_TU_LOGIN` sin copiarlo al repositorio. Más adelante `npm test` ejecutará ambos casos automáticamente.

**Resultado esperado:** 401 sin Bearer; 200 con Bearer válido y JSON {id,rol}.

**Interpretación:** `next()` solo ocurre tras verificación; un token no equivale a cualquier texto.

## Variación razonada

Cambia Bearer por Token: debe recibir 401.

## Error o caso límite controlado

Usar token expirado o alterado.

**Por qué ocurre:** jwt.verify falla o el formato no coincide.

**Corrección:** Vuelve a iniciar sesión y envía el encabezado correcto.

**Explica con tus palabras:** qué información entra, qué valida el servidor, qué cambia en memoria/cabeceras y por qué el resultado es correcto.

## T04 — Tarea espejo de EJ04

**Enunciado:** Solicitar /perfil sin credencial y luego con Bearer token válido.

**Archivo a crear/editar:** `tareas/T04.js` y, si necesitas consultar el original, `src/middleware/autenticacion.js + src/crear-app.js`. Crea el archivo `tareas/T04.js` con clic derecho en la carpeta y escribe una variante propia de lo aprendido.

**Restricción:** sin contraseñas reales, sin publicar tokens, sin dar rol `admin` por el cuerpo del registro. **Pista:** primero reproduce el flujo del ejemplo con una entrada diferente; después agrega el caso negativo.

**Evidencia:** 401 sin token; 200 con token; no incluye contraseña.

**Cómo validar:** `node tareas/T04.js` desde la raíz del proyecto, con las dependencias y `.env` ya preparados. Compara el resultado con la predicción y explica por qué funciona.


---

# EJ05 — Autorización por roles

**Objetivo observable:** Usuario autenticado obtiene 403 en /admin/dashboard y admin obtiene 200.

**Problema actual y por qué se necesita:** Autenticación acredita identidad, pero una ruta administrativa exige permiso adicional.

**Archivos involucrados:** `src/middleware/roles.js y src/crear-app.js`. **Punto de partida:** EJ04 funcionando; DEMO_ADMIN_PASSWORD ficticio configurado

**Habilidad nueva:** middleware de autorización y 403. **Tarea espejo:** T05.


### Microbloque 1 — Crear una fábrica de middleware

**Antes:** Archivo vacío **Por qué:** Una ruta necesita declarar qué roles acepta.

**En `src/middleware/roles.js`, escribe o añade:**

```javascript
function permitirRoles(...rolesPermitidos) {
  return (req, res, next) => {
```

**Explicación:** `...rolesPermitidos` captura argumentos como `admin`; la función retorna otro middleware que examinará la solicitud.

**Elementos nuevos:** rest ...rolesPermitidos, closure

**Después:** La ruta podrá suministrar roles permitidos.


### Microbloque 2 — Probar autorización

**Antes:** Roles suministrados **Por qué:** El middleware debe negar usuario sin rol adecuado.

**En `src/middleware/roles.js`, escribe o añade:**

```javascript
if (!req.usuario || !rolesPermitidos.includes(req.usuario.rol)) {
  return res.status(403).json({ mensaje: 'Rol insuficiente' });
}
return next();
```

**Explicación:** La identidad previamente verificada está en req.usuario; `includes` verifica membresía; 403 es permiso insuficiente, distinto del 401 sin autenticación.

**Elementos nuevos:** includes, 403

**Después:** Solo identidad con rol suficiente puede continuar.


### Microbloque 3 — Cerrar módulo y montar ruta

**Antes:** Middleware completo **Por qué:** La ruta requiere autenticación y después autorización.

**En `src/crear-app.js`, escribe o añade:**

```javascript
// En roles.js cierra las funciones y exporta:
  };
}
module.exports = { permitirRoles };
// En crear-app.js importa junto a los otros require:
const { permitirRoles } = require('./middleware/roles');
```

**Explicación:** Los cierres pertenecen a la función retornada y a permitirRoles. El módulo exporta el middleware para utilizarlo en la ruta.

**Elementos nuevos:** module.exports, permitirRoles

**Después:** Se puede importar en app.


### Microbloque 4 — Crear la ruta administrativa

**Antes:** Existe /perfil privado **Por qué:** No basta con permitirRoles sin autenticar primero.

**En `src/crear-app.js`, escribe o añade:**

```javascript
// Dentro de crearApp(), antes de `return app;`:
app.get('/admin/dashboard', verificarToken, permitirRoles('admin'), (req, res) => {
  res.json({ mensaje: 'Panel administrativo permitido' });
});
```

**Explicación:** Orden obligatorio: verificarToken añade req.usuario; permitirRoles decide si admin está permitido; al final se ejecuta el handler. El admin demo se sembró en EJ03 y no puede crearse con /registro.

**Elementos nuevos:** /admin/dashboard

**Después:** Tenemos ruta con doble control.


## Archivo(s) completo(s) al terminar este ejemplo

### `src/middleware/roles.js`

```javascript
function permitirRoles(...rolesPermitidos) {
  return (req, res, next) => {
    if (!req.usuario || !rolesPermitidos.includes(req.usuario.rol)) {
      return res.status(403).json({ mensaje: 'Rol insuficiente' });
    }
    return next();
  };
}
module.exports = { permitirRoles };
```
### `src/crear-app.js`

```javascript
const express = require('express');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
const { verificarToken } = require('./middleware/autenticacion');
const { permitirRoles } = require('./middleware/roles');

async function crearApp() {
  const secreto = process.env.JWT_SECRET;
  if (!secreto || secreto.length < 48) throw new Error('JWT_SECRET faltante o demasiado corto');
  const app = express();
  app.use(express.json());
  const usuarios = [];
  let siguienteId = 1;

  if (process.env.DEMO_ADMIN_PASSWORD) {
    usuarios.push({
      id: siguienteId++, email: 'admin@ejemplo.test', rol: 'admin',
      hash: await bcrypt.hash(process.env.DEMO_ADMIN_PASSWORD, 10)
    });
  }

  app.get('/publico', (req, res) => res.json({ mensaje: 'Ruta pública disponible' }));

  app.post('/registro', async (req, res) => {
    const { email, password } = req.body || {};
    if (typeof email !== 'string' || !email.includes('@') ||
        typeof password !== 'string' || password.length < 8) {
      return res.status(400).json({ mensaje: 'Datos de registro inválidos' });
    }
    if (usuarios.some(u => u.email === email)) {
      return res.status(409).json({ mensaje: 'El correo ya existe' });
    }
    const usuario = {
      id: siguienteId++, email, rol: 'usuario',
      hash: await bcrypt.hash(password, 10)
    };
    usuarios.push(usuario);
    return res.status(201).json({ id: usuario.id, email: usuario.email, rol: usuario.rol });
  });

  app.post('/login', async (req, res) => {
    const { email, password } = req.body || {};
    const usuario = usuarios.find(u => u.email === email);
    if (!usuario || typeof password !== 'string' ||
        !(await bcrypt.compare(password, usuario.hash))) {
      return res.status(401).json({ mensaje: 'Credenciales no válidas' });
    }
    const token = jwt.sign({ id: usuario.id, rol: usuario.rol }, secreto, {
      expiresIn: '1h', algorithm: 'HS256'
    });
    return res.json({ token, rol: usuario.rol });
  });

  app.get('/perfil', verificarToken, (req, res) => {
    res.json({ id: req.usuario.id, rol: req.usuario.rol });
  });

  app.get('/admin/dashboard', verificarToken, permitirRoles('admin'), (req, res) => {
    res.json({ mensaje: 'Panel administrativo permitido' });
  });

  return app;
}

module.exports = { crearApp };
```


## Auditoría antes de ejecutar

**Qué cambia y qué queda:** Se diferencia identidad verificada de permiso para una operación. Identifica cada import, variable de entorno, condición, respuesta y ruta vistos arriba; si no puedes explicar alguno, revísalo antes de avanzar.

## Antes de ejecutar: predicción

1. ¿Qué ocurre sin token? 2. ¿Con token usuario? 3. ¿Con token admin?

## Ejecuta y valida

**EJ05 reutiliza `start` (no uses `npm run ej05`, ya que no existe ese script).** Guarda `src/middleware/roles.js` y sus cambios en `src/crear-app.js`; detén la instancia anterior con **Ctrl+C** y ejecuta de nuevo `npm start`. En otra terminal inicia sesión con un usuario de práctica, luego con admin@ejemplo.test y la contraseña ficticia del `.env`. Envía ambos JWT a `/admin/dashboard` sin escribirlos en archivos; `node tareas/T05.js` automatizará la comparación.

**Resultado esperado:** 401 sin token, 403 con usuario, 200 con admin.

**Interpretación:** La autorización depende de claims verificados por servidor y política en ruta.

## Variación razonada

Intenta /registro incluyendo `rol:"admin"`: debe seguir devolviendo `usuario`.

## Error o caso límite controlado

Invertir el orden de `permitirRoles` y `verificarToken`.

**Por qué ocurre:** req.usuario no estaría poblado cuando se consulta el rol.

**Corrección:** Restituye autenticación primero y permisos después.

**Explica con tus palabras:** qué información entra, qué valida el servidor, qué cambia en memoria/cabeceras y por qué el resultado es correcto.

## T05 — Tarea espejo de EJ05

**Enunciado:** Comprobar que usuario obtiene 403 en dashboard y cuenta admin ficticia obtiene 200.

**Archivo a crear/editar:** `tareas/T05.js` y, si necesitas consultar el original, `src/middleware/roles.js + src/crear-app.js`. Crea el archivo `tareas/T05.js` con clic derecho en la carpeta y escribe una variante propia de lo aprendido.

**Restricción:** sin contraseñas reales, sin publicar tokens, sin dar rol `admin` por el cuerpo del registro. **Pista:** primero reproduce el flujo del ejemplo con una entrada diferente; después agrega el caso negativo.

**Evidencia:** 403 para usuario y 200 para administrador de práctica.

**Cómo validar:** `node tareas/T05.js` desde la raíz del proyecto, con las dependencias y `.env` ya preparados. Compara el resultado con la predicción y explica por qué funciona.


---

# EJ06 — Configurar CORS con lista de orígenes

**Objetivo observable:** Mostrar cabecera Access-Control-Allow-Origin para localhost:4200 y ausencia para origen ajeno.

**Problema actual y por qué se necesita:** Un frontend en otro origen necesita autorización de lectura en el navegador; no debemos confundirla con permisos de cuenta.

**Archivos involucrados:** `src/cors-config.js y src/crear-app.js`. **Punto de partida:** EJ05 funcionando

**Habilidad nueva:** Origin, preflight OPTIONS y orden de middleware. **Tarea espejo:** T06.


### Microbloque 1 — Definir orígenes de frontend permitidos

**Antes:** Archivo vacío **Por qué:** Necesitamos una lista explícita de orígenes de navegador.

**En `src/cors-config.js`, escribe o añade:**

```javascript
const origenes = ['http://localhost:4200'];
const corsOpciones = {
  origin: (origen, callback) => {
```

**Explicación:** Un origen incluye protocolo, dominio y puerto. `localhost:4200` representa cliente Angular típico; aquí no se requiere Angular instalado. El callback decide si se envía cabecera.

**Elementos nuevos:** Origin, corsOpciones, callback

**Después:** Existe política de lectura en construcción.


### Microbloque 2 — Aprobar o no anunciar lectura

**Antes:** Hay lista **Por qué:** Clientes CLI pueden no enviar Origin; el navegador sí lo envía en solicitudes cruzadas.

**En `src/cors-config.js`, escribe o añade:**

```javascript
if (!origen || origenes.includes(origen)) return callback(null, true);
return callback(null, false);
},
```

**Explicación:** `!origen` admite clientes de consola. Orígenes de la lista obtienen cabecera; los demás no. Eso **no impide** que una herramienta de servidor haga solicitudes; el navegador decide si el script puede leer la respuesta.

**Elementos nuevos:** origen, callback(null, false)

**Después:** Solo origen permitido recibe autorización CORS.


### Microbloque 3 — Métodos, cabeceras y exportación

**Antes:** Política origen definida **Por qué:** Para preflight debemos anunciar métodos y cabeceras admitidos.

**En `src/cors-config.js`, escribe o añade:**

```javascript
methods: ['GET', 'POST'],
allowedHeaders: ['Content-Type', 'Authorization']
};
module.exports = { corsOpciones };
```

**Explicación:** El navegador puede enviar `OPTIONS` de comprobación previa cuando usa JSON o Authorization. `cors` maneja esas respuestas; no necesitamos crear ruta OPTIONS manual para este caso.

**Elementos nuevos:** allowedHeaders, OPTIONS, preflight

**Después:** Configuración lista para Express.


### Microbloque 4 — Registrar CORS antes de las rutas

**Antes:** API con roles **Por qué:** El middleware debe actuar antes de que respondan los endpoints.

**En `src/crear-app.js`, escribe o añade:**

```javascript
// Agrega junto al resto de imports:
const cors = require('cors');
const { corsOpciones } = require('./cors-config');
// Justo después de `const app = express();`, antes de express.json():
app.use(cors(corsOpciones));
```

**Explicación:** La biblioteca coloca cabeceras de acceso según la función origin. No agregues credentials:true: el ejemplo envía Bearer manual y no necesita cookies.

**Elementos nuevos:** app.use(cors(...))

**Después:** Las cabeceras se evalúan al comenzar cada solicitud.


## Archivo(s) completo(s) al terminar este ejemplo

### `src/cors-config.js`

```javascript
const origenes = ['http://localhost:4200'];

const corsOpciones = {
  origin: (origen, callback) => {
    // Sin Origin: permite pruebas desde terminal; no equivale a autorización.
    if (!origen || origenes.includes(origen)) return callback(null, true);
    // No anuncia permiso CORS a otros orígenes: el navegador no podrá leer.
    return callback(null, false);
  },
  methods: ['GET', 'POST'],
  allowedHeaders: ['Content-Type', 'Authorization']
};
module.exports = { corsOpciones };
```
### `src/crear-app.js`

```javascript
const express = require('express');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
const { verificarToken } = require('./middleware/autenticacion');
const { permitirRoles } = require('./middleware/roles');
const cors = require('cors');
const { corsOpciones } = require('./cors-config');

async function crearApp() {
  const secreto = process.env.JWT_SECRET;
  if (!secreto || secreto.length < 48) throw new Error('JWT_SECRET faltante o demasiado corto');
  const app = express();
  app.use(cors(corsOpciones));
  app.use(express.json());
  const usuarios = [];
  let siguienteId = 1;

  if (process.env.DEMO_ADMIN_PASSWORD) {
    usuarios.push({
      id: siguienteId++, email: 'admin@ejemplo.test', rol: 'admin',
      hash: await bcrypt.hash(process.env.DEMO_ADMIN_PASSWORD, 10)
    });
  }

  app.get('/publico', (req, res) => res.json({ mensaje: 'Ruta pública disponible' }));

  app.post('/registro', async (req, res) => {
    const { email, password } = req.body || {};
    if (typeof email !== 'string' || !email.includes('@') ||
        typeof password !== 'string' || password.length < 8) {
      return res.status(400).json({ mensaje: 'Datos de registro inválidos' });
    }
    if (usuarios.some(u => u.email === email)) {
      return res.status(409).json({ mensaje: 'El correo ya existe' });
    }
    const usuario = {
      id: siguienteId++, email, rol: 'usuario',
      hash: await bcrypt.hash(password, 10)
    };
    usuarios.push(usuario);
    return res.status(201).json({ id: usuario.id, email: usuario.email, rol: usuario.rol });
  });

  app.post('/login', async (req, res) => {
    const { email, password } = req.body || {};
    const usuario = usuarios.find(u => u.email === email);
    if (!usuario || typeof password !== 'string' ||
        !(await bcrypt.compare(password, usuario.hash))) {
      return res.status(401).json({ mensaje: 'Credenciales no válidas' });
    }
    const token = jwt.sign({ id: usuario.id, rol: usuario.rol }, secreto, {
      expiresIn: '1h', algorithm: 'HS256'
    });
    return res.json({ token, rol: usuario.rol });
  });

  app.get('/perfil', verificarToken, (req, res) => {
    res.json({ id: req.usuario.id, rol: req.usuario.rol });
  });

  app.get('/admin/dashboard', verificarToken, permitirRoles('admin'), (req, res) => {
    res.json({ mensaje: 'Panel administrativo permitido' });
  });

  return app;
}

module.exports = { crearApp };
```


## Auditoría antes de ejecutar

**Qué cambia y qué queda:** CORS comunica permiso de lectura al navegador; la API sigue autenticando y autorizando cada ruta. Identifica cada import, variable de entorno, condición, respuesta y ruta vistos arriba; si no puedes explicar alguno, revísalo antes de avanzar.

## Antes de ejecutar: predicción

1. ¿El origen permitido obtiene Access-Control-Allow-Origin? 2. ¿Cuál es la diferencia con 401?

## Ejecuta y valida

**EJ06 tampoco requiere un nuevo script.** Guarda `src/cors-config.js` y `src/crear-app.js`; pulsa **Ctrl+C** en la terminal antigua y ejecuta `npm start` otra vez. Abre **otra terminal** en la carpeta raíz y ejecuta `node` para iniciar su consola interactiva. Escribe estas líneas una por una:

```javascript
const respuesta = await fetch('http://localhost:3001/publico', {
  headers: { Origin: 'http://localhost:4200' }
});
console.log(respuesta.status);
console.log(respuesta.headers.get('access-control-allow-origin'));
```

Obtendrás `200` y `http://localhost:4200`. Vuelve a probar con `Origin: 'http://origen-ajeno.test'` y compara la cabecera. Escribe `.exit` para salir de Node.

**Resultado esperado:** HTTP 200 en ambas peticiones de consola; solo el origen permitido obtiene cabecera CORS. Preflight permitido devuelve OPTIONS 204.

**Interpretación:** El cliente Node NO aplica restricciones CORS del navegador; se inspeccionan cabeceras, no se simula bloqueo de red.

## Variación razonada

Agrega `http://localhost:5173` a la lista si tu frontend usa ese puerto, explica por qué.

## Error o caso límite controlado

Esperar 403 de Express solamente por Origin ajeno.

**Por qué ocurre:** CORS no es una regla de autorización de servidor.

**Corrección:** Mantén verificarToken y permitirRoles para proteger recursos; usa CORS solo para controlar lectura desde navegador.

**Explica con tus palabras:** qué información entra, qué valida el servidor, qué cambia en memoria/cabeceras y por qué el resultado es correcto.

## T06 — Tarea espejo de EJ06

**Enunciado:** Simular origen permitido, uno ajeno y preflight OPTIONS, comparando cabeceras.

**Archivo a crear/editar:** `tareas/T06.js` y, si necesitas consultar el original, `src/cors-config.js`. Crea el archivo `tareas/T06.js` con clic derecho en la carpeta y escribe una variante propia de lo aprendido.

**Restricción:** sin contraseñas reales, sin publicar tokens, sin dar rol `admin` por el cuerpo del registro. **Pista:** primero reproduce el flujo del ejemplo con una entrada diferente; después agrega el caso negativo.

**Evidencia:** Access-Control-Allow-Origin solo en origen permitido; OPTIONS 204.

**Cómo validar:** `node tareas/T06.js` desde la raíz del proyecto, con las dependencias y `.env` ya preparados. Compara el resultado con la predicción y explica por qué funciona.


---

# EJ07 — Validación integral reproducible

**Objetivo observable:** Automatizar 201, 200, 401, 403, 204 y cabeceras CORS sin reutilizar claves reales.

**Problema actual y por qué se necesita:** Debemos comprobar que los mecanismos trabajan juntos y que cada fallo tiene una causa identificable.

**Archivos involucrados:** `pruebas/EJ07_flujo.test.js`. **Punto de partida:** Archivos EJ01–EJ06 creados, .env válido

**Habilidad nueva:** node:test, fetch y asserts de HTTP. **Tarea espejo:** T07.


### Microbloque 1 — Imports de pruebas y datos ficticios

**Antes:** Directorio pruebas creado **Por qué:** Necesitamos comparar lo esperado con lo recibido sin claves publicadas.

**En `pruebas/EJ07_flujo.test.js`, escribe o añade:**

```javascript
require('dotenv').config();
const { randomBytes } = require('node:crypto');
const { test, before, after } = require('node:test');
const assert = require('node:assert/strict');
const { crearApp } = require('../src/crear-app');
```

**Explicación:** node:test y assert vienen incluidos en Node. `randomBytes` genera datos ficticios únicos para la prueba y evita hardcodear contraseñas; el import relativo apunta a crear-app.js.

**Elementos nuevos:** node:test, assert, randomBytes

**Después:** Tenemos herramientas de prueba.


### Microbloque 2 — Inicialización en puerto libre

**Antes:** App importada **Por qué:** La prueba necesita una API aislada sin colisión con npm start.

**En `pruebas/EJ07_flujo.test.js`, escribe o añade:**

```javascript
let servidor, base, tokenUsuario;
const email = `alumno_${randomBytes(5).toString('hex')}@ejemplo.test`;
const password = randomBytes(18).toString('base64url');
```

**Explicación:** El email y password son aleatorios cada ejecución. `tokenUsuario` se asignará tras login. `base` se obtiene de puerto aleatorio, sin fijar 3001.

**Elementos nuevos:** base, tokenUsuario

**Después:** Datos listos sin credenciales personales.


### Microbloque 3 — Arranque y cierre de prueba

**Antes:** Estado de servidor no inicializado **Por qué:** Un test debe liberar el puerto al terminar.

**En `pruebas/EJ07_flujo.test.js`, escribe o añade:**

```javascript
before(async () => {
  const app = await crearApp();
  servidor = app.listen(0);
  await new Promise(resolve => servidor.once('listening', resolve));
  base = `http://127.0.0.1:${servidor.address().port}`;
});
```

**Explicación:** `before` inicia una vez; escuchar en puerto 0 asigna puerto libre; se espera evento listening para poder ejecutar fetch.

**Elementos nuevos:** before, listen(0), Promise

**Después:** Servidor de pruebas listo.


### Microbloque 4 — Cerrar correctamente

**Antes:** Servidor abierto **Por qué:** No queremos procesos en segundo plano después del test.

**En `pruebas/EJ07_flujo.test.js`, escribe o añade:**

```javascript
after(async () => {
  if (servidor) await new Promise((resolve, reject) =>
    servidor.close(err => err ? reject(err) : resolve()));
});
```

**Explicación:** `after` cierra la aplicación para que Node finalice; un error de cierre rechaza la promesa.

**Elementos nuevos:** after, server.close

**Después:** Pruebas pueden acabar limpiamente.


### Microbloque 5 — Registro y política de roles

**Antes:** App disponible **Por qué:** La primera prueba confirma entrada pública y que el servidor asigna el rol.

**En `pruebas/EJ07_flujo.test.js`, escribe o añade:**

```javascript
test('EJ07 H1: ruta pública y registro con rol asignado por servidor', async () => {
  const publica = await fetch(`${base}/publico`);
  assert.equal(publica.status, 200);
```

**Explicación:** `test` agrupa comprobaciones. La ruta pública debe responder 200 antes de crear usuario. En el archivo completo añadirás el POST /registro y las demás afirmaciones.

**Elementos nuevos:** test, assert.equal

**Después:** Inicio del primer hito.


### Microbloque 6 — Probar cabeceras de autorización

**Antes:** tokenUsuario proviene del login **Por qué:** Después de login se debe rechazar sin JWT y aceptar Bearer válido.

**En `pruebas/EJ07_flujo.test.js`, escribe o añade:**

```javascript
const sinToken = await fetch(`${base}/perfil`);
assert.equal(sinToken.status, 401);
const perfil = await fetch(`${base}/perfil`, {
  headers: { Authorization: `Bearer ${tokenUsuario}` }
});
assert.equal(perfil.status, 200);
```

**Explicación:** Este microbloque se ubica **dentro** del test H2 después de recibir tokenUsuario (ver archivo completo). Sin Bearer hay 401; con token válido se obtiene perfil. El orden es imprescindible.

**Elementos nuevos:** Authorization, 401/200

**Después:** Validación de autenticación completada.


### Microbloque 7 — Diferenciar rol y CORS

**Antes:** Usuario comprobado **Por qué:** Las pruebas deben detectar autorización y política del navegador separadamente.

**En `pruebas/EJ07_flujo.test.js`, escribe o añade:**

```javascript
const denegado = await fetch(`${base}/admin/dashboard`, {
  headers: { Authorization: `Bearer ${tokenUsuario}` }
});
assert.equal(denegado.status, 403);
```

**Explicación:** Este bloque pertenece a H3 en el archivo completo; luego H4 comprueba headers CORS y preflight. Un usuario autenticado pero no administrador obtiene 403.

**Elementos nuevos:** 403

**Después:** Autorización probada sin elevar rol.


**Importante:** completa ahora el contenido entero de `pruebas/EJ07_flujo.test.js` tal como aparece abajo. Los microbloques anteriores muestran los conceptos nuevos; el archivo consolidado incluye H1–H4, el login previo a `tokenUsuario`, el cierre de cada `test` y las cabeceras preflight. Revisa cada aserción y ubícala en el flujo correcto antes de ejecutar.


## Archivo(s) completo(s) al terminar este ejemplo

### `pruebas/EJ07_flujo.test.js`

```javascript
require('dotenv').config();
const { randomBytes } = require('node:crypto');
const { test, before, after } = require('node:test');
const assert = require('node:assert/strict');
const { crearApp } = require('../src/crear-app');

let servidor, base, tokenUsuario;
const email = `alumno_${randomBytes(5).toString('hex')}@ejemplo.test`;
const password = randomBytes(18).toString('base64url');

before(async () => {
  const app = await crearApp();
  servidor = app.listen(0);
  await new Promise(resolve => servidor.once('listening', resolve));
  base = `http://127.0.0.1:${servidor.address().port}`;
});
after(async () => {
  if (servidor) await new Promise((resolve, reject) => servidor.close(err => err ? reject(err) : resolve()));
});

test('EJ07 H1: ruta pública y registro con rol asignado por servidor', async () => {
  const publica = await fetch(`${base}/publico`);
  assert.equal(publica.status, 200);
  const registro = await fetch(`${base}/registro`, {
    method: 'POST', headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password, rol: 'admin' })
  });
  assert.equal(registro.status, 201);
  const datos = await registro.json();
  assert.equal(datos.rol, 'usuario');
  assert.equal('hash' in datos, false);
});

test('EJ07 H2: login y ruta privada', async () => {
  const login = await fetch(`${base}/login`, {
    method: 'POST', headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password })
  });
  assert.equal(login.status, 200);
  tokenUsuario = (await login.json()).token;
  assert.ok(tokenUsuario);
  const sinToken = await fetch(`${base}/perfil`);
  assert.equal(sinToken.status, 401);
  const perfil = await fetch(`${base}/perfil`, { headers: { Authorization: `Bearer ${tokenUsuario}` } });
  assert.equal(perfil.status, 200);
  assert.equal((await perfil.json()).rol, 'usuario');
});

test('EJ07 H3: autorización y token inválido', async () => {
  const sinRol = await fetch(`${base}/admin/dashboard`, {
    headers: { Authorization: `Bearer ${tokenUsuario}` }
  });
  assert.equal(sinRol.status, 403);
  const alterado = await fetch(`${base}/perfil`, {
    headers: { Authorization: `Bearer ${tokenUsuario}alterado` }
  });
  assert.equal(alterado.status, 401);
});

test('EJ07 H4: CORS permitidos, no permitidos y preflight', async () => {
  const permitido = await fetch(`${base}/publico`, { headers: { Origin: 'http://localhost:4200' } });
  assert.equal(permitido.headers.get('access-control-allow-origin'), 'http://localhost:4200');
  const otro = await fetch(`${base}/publico`, { headers: { Origin: 'http://otro.ejemplo.test' } });
  assert.equal(otro.headers.get('access-control-allow-origin'), null);
  const previo = await fetch(`${base}/login`, {
    method: 'OPTIONS', headers: {
      Origin: 'http://localhost:4200',
      'Access-Control-Request-Method': 'POST',
      'Access-Control-Request-Headers': 'Content-Type'
    }
  });
  assert.equal(previo.status, 204);
  assert.equal(previo.headers.get('access-control-allow-origin'), 'http://localhost:4200');
});
```


### Paso final de EJ07 — añadir `test` y ejecutar las pruebas automatizadas

1. Guarda completamente `pruebas/EJ07_flujo.test.js`. A diferencia de `ej01` y `ej02`, este archivo utiliza `node:test` y `assert` para verificar varias solicitudes HTTP.
2. Abre `package.json` y agrega `test` sin perder las tres entradas anteriores:

```json
"scripts": {
  "ej01": "node ejemplos/EJ01_bcrypt.js",
  "ej02": "node ejemplos/EJ02_jwt.js",
  "start": "node src/server.js",
  "test": "node --test pruebas/EJ07_flujo.test.js"
}
```

3. **Qué significa cada parte:** `test` es el nombre convencional reconocido por `npm test`; `node` es el intérprete; `--test` activa el ejecutor de pruebas **integrado en Node.js**; `pruebas/EJ07_flujo.test.js` es el archivo de pruebas y no una ruta HTTP. **No hace falta instalar Jest ni Mocha**.
4. Si dejaste una API iniciada con `npm start`, puedes detenerla con **Ctrl+C** para concentrarte en la salida de pruebas. El test llama a `crearApp()` y utiliza `app.listen(0)` para abrir un puerto libre **solo durante la prueba**; al terminar, lo cierra. No necesitas ejecutar `npm start` antes de `npm test`.
5. En la raíz ejecuta `npm test`. Se esperan cuatro pruebas H1, H2, H3 y H4 aprobadas y un resumen sin fallos. Si una falla, revisa primero el nombre del caso, el `assert.equal` que falló y el status/cabecera que devuelve la API.
6. **Diferencia final:** `npm start` arranca un servicio para probar manualmente; `npm test` comprueba automáticamente el comportamiento y termina por sí solo.


## Auditoría antes de ejecutar

**Qué cambia y qué queda:** Se ejecutan los hitos en orden: registro, login, ruta privada, permiso y CORS. Identifica cada import, variable de entorno, condición, respuesta y ruta vistos arriba; si no puedes explicar alguno, revísalo antes de avanzar.

## Antes de ejecutar: predicción

1. ¿Qué status si se solicita rol admin al registrarse? 2. ¿Cuándo es 403 y cuándo 401?

## Ejecuta y valida

Si la API sigue abierta, detén `npm start` con **Ctrl+C**. Comprueba que el script `test` ya se añadió al `package.json` (instrucciones justo antes de esta sección) y ejecuta `npm test` desde la raíz. Node arranca y cierra la API dentro del proceso de prueba.

**Resultado esperado:** 4 subpruebas con estado OK/PASS (H1–H4).

**Interpretación:** El conjunto valida éxito y fallo HTTP, no prueba resistencia completa a ataques ni sustituye pruebas de producción.

## Variación razonada

En H4 sustituye localhost:4200 por un origen ajeno para observar cómo cambia la cabecera esperada.

## Error o caso límite controlado

Falta `.env` o DEMO_ADMIN_PASSWORD.

**Por qué ocurre:** Sin JWT_SECRET falla crearApp; admin no se utiliza directamente en EJ07 pero lo necesitarás en T05.

**Corrección:** Verifica las tres claves de `.env` y que nunca estén en .env.example.

**Explica con tus palabras:** qué información entra, qué valida el servidor, qué cambia en memoria/cabeceras y por qué el resultado es correcto.

## T07 — Tarea espejo de EJ07

**Enunciado:** Automatizar registro, duplicado, login fallido, login correcto, perfil, rol insuficiente y CORS.

**Archivo a crear/editar:** `tareas/T07.js` y, si necesitas consultar el original, `pruebas/EJ07_flujo.test.js`. Crea el archivo `tareas/T07.js` con clic derecho en la carpeta y escribe una variante propia de lo aprendido.

**Restricción:** sin contraseñas reales, sin publicar tokens, sin dar rol `admin` por el cuerpo del registro. **Pista:** primero reproduce el flujo del ejemplo con una entrada diferente; después agrega el caso negativo.

**Evidencia:** Pruebas 201,409,401,200,403 y origen sin autorización CORS.

**Cómo validar:** `node tareas/T07.js` desde la raíz del proyecto, con las dependencias y `.env` ya preparados. Compara el resultado con la predicción y explica por qué funciona.


---

## 4. Matriz rápida para comprobar el recorrido

| EJ | Archivo clave | Hito esperado | Tarea |
|---|---|---|---|
| EJ01 | ejemplos/EJ01_bcrypt.js | true/false bcrypt | T01 |
| EJ02 | ejemplos/EJ02_jwt.js | firma válida y alteración rechazada | T02 |
| EJ03 | src/crear-app.js + src/server.js | POST /registro 201; /login 200/401 | T03 |
| EJ04 | src/middleware/autenticacion.js | /perfil 401/200 | T04 |
| EJ05 | src/middleware/roles.js | /admin/dashboard 403/200 | T05 |
| EJ06 | src/cors-config.js | cabecera CORS selectiva | T06 |
| EJ07 | pruebas/EJ07_flujo.test.js | batería automática H1–H4 | T07 |

## 5. Qué entregar

Un archivo comprimido de tu carpeta de trabajo **sin** `node_modules`, `.env`, contraseñas, secretos ni tokens; un README breve indicando los comandos; resultados de `npm test` y las siete tareas T01–T07 resueltas. Puedes adjuntar capturas de estados HTTP evitando que aparezca el token completo. Evalúa primero seguridad y explicación, después formato.

## 6. Cierre de sesión y conexión formativa

En este laboratorio has transformado un servidor Express que simplemente recibía solicitudes en una API capaz de identificar, autorizar y dar respuestas diferenciadas. El recorrido comenzó con bcrypt.js: ahora sabes que una contraseña de acceso debe compararse con un hash, no buscarse en una tabla como texto plano ni recuperarse a partir del hash. Después construiste un JWT firmado con identidad mínima y una duración limitada, y verificaste su firma antes de confiar en los claims. La firma no sirve para esconder datos; por eso el token no contiene contraseñas. Al agregar el registro y el login observaste que la cuenta se guarda en memoria solo para estudiar los controles: al reiniciar el proceso desaparece, a diferencia de una base de datos persistente.

También comprobaste dos decisiones distintas del backend. Si el cliente no presenta un Bearer token válido, la ruta privada responde 401. Si la identidad ya está autenticada pero no cumple el rol requerido, la ruta administrativa responde 403. Finalmente configuraste CORS para declarar desde qué origen puede leer la respuesta un script en navegador. Las herramientas de consola pueden seguir enviando peticiones; por tanto, CORS jamás sustituye a la autenticación y autorización.

Repasa el orden de middlewares, el valor de `next()`, la caducidad de JWT y la diferencia entre `hash` y contraseña original. Conserva las pruebas positivas y negativas: son la evidencia de que tu programa funciona por razones verificables. En las próximas prácticas podrás recuperar la persistencia con MongoDB/Mongoose sin confundir las capas de seguridad. Para profundizar consulta el blog de Lideratec Academy: https://lideratecacademy.com/blog/ y su canal educativo: https://www.youtube.com/@LideratecAcademy. La versión materializada FASE 15 es únicamente un apoyo opcional para comparar tu trabajo terminado.


## 7. `package.json` final explicado y guía de diagnóstico de scripts

Al terminar EJ07, todos los archivos ya existen y el bloque completo queda así. **Solo se muestra `scripts`**, situado dentro del mismo `package.json` que ya contiene tus dependencias:

```json
"scripts": {
  "ej01": "node ejemplos/EJ01_bcrypt.js",
  "ej02": "node ejemplos/EJ02_jwt.js",
  "start": "node src/server.js",
  "test": "node --test pruebas/EJ07_flujo.test.js"
}
```

**Tabla de interpretación de errores:**

| Mensaje o síntoma | Significa | Qué revisar, en este orden |
|---|---|---|
| `Missing script: "ej01"` | npm no encuentra ese alias | Abre `package.json`; revisa `scripts`, nombre exacto, guarda y ejecuta `npm run`. |
| `Cannot find module .../EJ01_bcrypt.js` | La ruta del script apunta a un archivo inexistente | Revisa nombre, mayúsculas, ubicación `ejemplos/` y si guardaste el `.js`. |
| `Cannot find module 'bcryptjs'` o `'express'` | Faltan librerías instaladas en esta carpeta | Desde la raíz, ejecuta `npm install`; asegúrate de abrir la carpeta correcta. |
| `Unexpected token` / error al leer `package.json` | El JSON está mal formado | Revisa comas entre scripts, llaves y ausencia de comentarios o comas finales. |
| `Configura JWT_SECRET` o `LAB_PASSWORD` | Falta configuración local del ejercicio | Revisa `.env` en la raíz; no copies valores sensibles a capturas o entregas. |
| `EADDRINUSE` | Ya se está usando el puerto 3001 | Detén servidor anterior con **Ctrl+C** y vuelve a ejecutar `npm start`. |
| `fetch failed` al pedir `/publico` | No existe servidor activo en ese puerto | Ejecuta `npm start` en una terminal, sin cerrarla; consulta desde otra. |
| `npm test` falla en H1–H4 | Algún comportamiento es distinto al esperado | Lee el nombre del test y compara status/cabeceras; no cambies una prueba para ocultar el fallo. |

**Autocomprobación de cierre:** explica por qué ejecutar `npm run ej01` no abre un puerto, por qué `npm start` queda funcionando y por qué `npm test` puede trabajar sin servidor manual. Comprueba que los cuatro scripts aparecen con `npm run` y que `node -e "require('./package.json'); console.log('JSON válido')"` imprime `JSON válido`.
