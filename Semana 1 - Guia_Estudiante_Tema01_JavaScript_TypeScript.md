# Lideratec Academy

## PROGRAMACIÓN WEB AVANZADA

### Sesión 01 - Fundamentos de JavaScript moderno y TypeScript

**Práctica guiada:** Del JavaScript ES6+ a un proyecto TypeScript organizado y compilable  
**Proyecto académico:** Lideratec Academy  
**Elaborado por el docente**

---

# 1. Propósito de la práctica

En esta sesión prepararás un entorno local para ejecutar JavaScript y compilar TypeScript. Después trabajarás progresivamente con `let`, `const`, funciones flecha, template literals, destructuring, promesas, `async/await`, tipos de datos de TypeScript, interfaces, type aliases, clases, módulos, namespaces y `tsconfig.json`.

La práctica está diseñada para que no ejecutes comandos de memoria. En cada etapa debes comprender:

1. **qué comando vas a ejecutar;**
2. **en qué carpeta debes ejecutarlo;**
3. **por qué es necesario;**
4. **qué archivo, carpeta o estado modifica;**
5. **qué resultado debes observar;**
6. **cómo saber si funcionó correctamente.**

> La meta no es copiar una secuencia de comandos. La meta es comprender el flujo completo: preparar el entorno, escribir código, compilarlo cuando corresponda y ejecutar el resultado.

---

# 2. Resultado de aprendizaje observable

Al finalizar la sesión podrás:

1. Diferenciar el uso de `var`, `let` y `const` en ejemplos ejecutables.
2. Aplicar funciones flecha, template literals y destructuring en JavaScript moderno.
3. Interpretar y ejecutar operaciones asincrónicas con promesas y `async/await`.
4. Definir variables, arrays, tuplas, union types, enums, generics, interfaces y type aliases en TypeScript.
5. Construir clases con modificadores `public`, `private` y `protected`, además de herencia.
6. Organizar código TypeScript mediante módulos y namespaces.
7. Configurar `tsconfig.json`, compilar archivos `.ts` y ejecutar el JavaScript generado.
8. Explicar para qué sirve cada comando principal utilizado durante la práctica.

---

# 3. Duración sugerida

**180 minutos**, incluyendo una pausa breve y los puntos de control.

---

# 4. Conocimientos previos mínimos

- Crear carpetas y archivos.
- Abrir una terminal o consola.
- Reconocer variables, funciones y objetos en JavaScript.
- Ejecutar instrucciones siguiendo una secuencia.

No se requiere experiencia previa con TypeScript.

---

# 5. Herramientas y recursos

| Componente | Función en la práctica | ¿Debe instalarse? | Orden |
|---|---|---:|---:|
| Node.js LTS | Ejecutar JavaScript fuera del navegador y disponer de npm | Sí | 1 |
| npm | Gestionar el proyecto y sus dependencias | Se instala con Node.js | 2 |
| TypeScript | Comprobar tipos y compilar `.ts` a `.js` | Sí, dentro del proyecto | 3 |
| Visual Studio Code | Editar archivos y utilizar una terminal integrada | Recomendado | 4 |

## Enlaces oficiales

- Node.js: https://nodejs.org/en/download
- TypeScript: https://www.typescriptlang.org/download/
- Visual Studio Code: https://code.visualstudio.com/

> **Referencia de laboratorio:** utiliza la versión marcada como **LTS** en el sitio oficial de Node.js. La numeración concreta cambia con el tiempo.

---

# 6. Antes de comenzar: cómo leer los comandos

Durante la práctica aparecerán comandos como estos:

```bash
node -v
npm -v
npm init -y
npm install typescript --save-dev
npx tsc
```

No representan la misma clase de acción.

| Elemento | Qué representa |
|---|---|
| `node` | Ejecuta JavaScript o consulta información de Node.js |
| `npm` | Administra un proyecto Node y sus dependencias |
| `npx` | Ejecuta una herramienta instalada dentro del proyecto |
| `tsc` | Compilador de TypeScript |
| `mkdir` | Crea una carpeta |
| `cd` | Cambia la carpeta actual de la terminal |
| `-v` / `--version` | Solicita información de versión |

## 6.1. Idea clave: la terminal siempre está ubicada en una carpeta

Un error muy frecuente consiste en escribir un comando correcto desde una carpeta incorrecta.

Por ejemplo:

```bash
npm init -y
```

crea `package.json` **en la carpeta donde se encuentre la terminal en ese momento**.

Por eso, antes de ejecutar comandos del proyecto, debes saber dónde estás.

### En PowerShell

```powershell
Get-Location
```

### En Símbolo del sistema

```bat
cd
```

Ambos comandos permiten comprobar la ubicación actual.

---

# PARTE I - PREPARACIÓN COMPLETA DEL ENTORNO

# 7. ¿Qué componente hace qué?

## 7.1. Node.js

Node.js permite ejecutar JavaScript desde una terminal.

En esta sesión se utilizará para dos situaciones:

1. ejecutar directamente los ejemplos `.js`;
2. ejecutar los `.js` que TypeScript genere dentro de `dist`.

Ejemplo:

```bash
node js/01-variables.js
```

Aquí Node.js recibe como entrada un archivo JavaScript y lo ejecuta.

---

## 7.2. npm

npm se instala junto con Node.js.

En esta práctica se utilizará para:

- crear el archivo `package.json`;
- instalar TypeScript dentro del proyecto;
- registrar TypeScript como dependencia de desarrollo.

npm **no es el compilador de TypeScript**. Su función aquí es administrar el proyecto y sus paquetes.

---

## 7.3. TypeScript

TypeScript permite escribir JavaScript con información de tipos.

El compilador `tsc` realizará dos tareas importantes:

1. comprobar errores de tipos;
2. generar JavaScript a partir de los archivos `.ts`.

El flujo será:

```text
src/tipos.ts
     |
     | npx tsc
     v
dist/tipos.js
     |
     | node dist/tipos.js
     v
resultado en consola
```

---

## 7.4. Visual Studio Code

Visual Studio Code será utilizado para:

- abrir la carpeta del proyecto;
- crear archivos;
- editar código;
- visualizar la estructura de carpetas;
- abrir una terminal integrada.

---

# 8. Descargar e instalar Node.js

## Paso 1. Abrir la página oficial

**Objetivo:** localizar una versión estable de Node.js.

**Dónde hacerlo:** navegador web.

### Acción

1. Abre https://nodejs.org/en/download
2. Localiza la versión identificada como **LTS**.
3. Selecciona la opción adecuada para tu sistema operativo.
4. En Windows, utiliza el instalador correspondiente a tu arquitectura.

### ¿Por qué utilizamos LTS?

LTS significa *Long Term Support*. Para una práctica académica interesa una versión estable y con soporte, no una versión experimental o fuera de mantenimiento.

### Qué debería ocurrir

El instalador será descargado en la carpeta de descargas de tu sistema.

### Cómo comprobarlo

Verifica que el archivo provenga del dominio oficial:

```text
nodejs.org
```

---

## Paso 2. Ejecutar el instalador

**Objetivo:** instalar Node.js y npm.

### Acción

1. Abre la carpeta **Descargas**.
2. Ubica el instalador descargado.
3. Ejecuta el instalador.
4. Si Windows solicita autorización para realizar cambios, comprueba que el instalador sea el descargado desde el sitio oficial.
5. Mantén los componentes predeterminados de Node.js y npm.
6. Completa la instalación.
7. Si el instalador solicita reiniciar el equipo, guarda tu trabajo y reinicia.

### Qué debería ocurrir

Al terminar, Windows deberá poder localizar los comandos:

```text
node
npm
```

desde una terminal nueva.

---

# 9. Verificar Node.js y npm

Abre una **terminal nueva** después de instalar Node.js.

Esto es importante porque una terminal abierta antes de la instalación puede conservar una configuración anterior de las variables del sistema.

---

## Paso 3. Comprobar Node.js

Ejecuta:

```bash
node -v
```

### ¿Qué hace este comando?

- `node` llama al ejecutable de Node.js.
- `-v` solicita únicamente su versión.
- No crea archivos.
- No instala nada.
- No ejecuta todavía tu proyecto.

### ¿Por qué lo ejecutamos?

Porque antes de escribir código debemos comprobar que la terminal puede encontrar Node.js.

Si este comando falla, más adelante también fallará:

```bash
node archivo.js
```

### Resultado esperado

Algo similar a:

```text
vXX.XX.X
```

El número exacto puede variar.

---

## Paso 3.1. Comprobar npm

Ejecuta:

```bash
npm -v
```

### ¿Qué hace?

Consulta la versión de npm instalada junto con Node.js.

### ¿Por qué lo necesitamos?

Más adelante utilizaremos npm para:

```text
crear package.json
instalar TypeScript
registrar dependencias
```

### Resultado esperado

```text
XX.X.X
```

---

## Punto de control

| Comando | Resultado correcto |
|---|---|
| `node -v` | muestra una versión |
| `npm -v` | muestra una versión |

No continúes si alguno de los dos comandos no funciona.

---

# 10. Instalar Visual Studio Code

Si ya dispones de un editor autorizado, puedes utilizarlo.

Si no:

1. Abre https://code.visualstudio.com/
2. Descarga la versión correspondiente a tu sistema operativo.
3. Ejecuta el instalador.
4. Mantén la configuración predeterminada del laboratorio.
5. Abre Visual Studio Code.

## Comprobación

Debes poder:

- abrir una carpeta;
- crear un archivo;
- abrir una terminal.

---

# 11. Crear el proyecto de la sesión

Esta sección es fundamental. A partir de aquí, la **ubicación de la terminal** importa.

---

## Paso 4. Crear la carpeta principal

Ubícate primero en la carpeta donde deseas guardar la práctica.

Ejecuta:

```bash
mkdir tema01-js-ts
```

### ¿Qué significa?

`mkdir` proviene de *make directory*.

El comando crea una carpeta llamada:

```text
tema01-js-ts
```

### ¿Qué modifica?

Crea una carpeta física en el disco.

### ¿Qué NO hace?

No entra automáticamente dentro de la carpeta.

---

## Paso 4.1. Entrar en la carpeta

Ejecuta:

```bash
cd tema01-js-ts
```

### ¿Qué significa `cd`?

`cd` significa *change directory*.

La terminal cambia su ubicación actual hacia:

```text
tema01-js-ts
```

### ¿Por qué es necesario?

Los comandos posteriores crearán archivos en la ubicación actual.

Si ejecutaras:

```bash
npm init -y
```

fuera de `tema01-js-ts`, `package.json` se crearía en otra carpeta.

### Cómo comprobarlo

PowerShell:

```powershell
Get-Location
```

Símbolo del sistema:

```bat
cd
```

La ruta mostrada debe terminar en:

```text
tema01-js-ts
```

---

# 12. Inicializar npm

Desde la raíz de `tema01-js-ts`, ejecuta:

```bash
npm init -y
```

## ¿Qué significa cada parte?

| Parte | Significado |
|---|---|
| `npm` | ejecuta el gestor de paquetes |
| `init` | inicializa un proyecto npm |
| `-y` | acepta valores iniciales predeterminados |

## ¿Por qué ejecutamos este comando?

Porque necesitamos que la carpeta sea reconocida como un proyecto administrado por npm.

El archivo principal que aparecerá será:

```text
package.json
```

## ¿Qué es `package.json`?

Es el archivo que describe el proyecto.

Puede almacenar, entre otros datos:

- nombre;
- versión;
- scripts;
- dependencias;
- dependencias de desarrollo.

## Resultado esperado

```text
tema01-js-ts/
  package.json
```

## Cómo comprobarlo

En Visual Studio Code debe aparecer `package.json` en la raíz.

Ábrelo. Deberás observar una estructura JSON.

---

# 13. Si PowerShell bloquea `npm.ps1`

En Windows puede aparecer un mensaje parecido a:

```text
npm.ps1 cannot be loaded because running scripts is disabled
```

Este mensaje no significa necesariamente que npm esté mal instalado.

Significa que PowerShell está aplicando una política de ejecución a scripts.

---

## 13.1. Primero diagnostica

Ejecuta:

```powershell
Get-ExecutionPolicy
```

### ¿Qué hace?

Muestra la política efectiva de la sesión.

No modifica la configuración.

Luego puedes ejecutar:

```powershell
Get-ExecutionPolicy -List
```

### ¿Qué hace?

Muestra las políticas configuradas para distintos ámbitos.

Tampoco modifica nada.

---

## 13.2. Alternativa recomendada en equipos institucionales

Si PowerShell bloquea `npm.ps1`, puedes abrir una terminal de **Símbolo del sistema / Command Prompt** en Visual Studio Code y volver a ejecutar:

```bat
npm -v
npm init -y
```

### ¿Por qué esta alternativa es útil?

Porque el bloqueo corresponde a la política de PowerShell.

Cambiar de terminal permite continuar sin modificar una política de seguridad del equipo.

---

## 13.3. Alternativa temporal en PowerShell

Utilízala únicamente si el docente o la institución lo autoriza.

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
```

### ¿Qué hace?

- `Set-ExecutionPolicy`: solicita modificar la política de ejecución.
- `RemoteSigned`: permite scripts locales y aplica requisitos adicionales a determinados scripts descargados.
- `-Scope Process`: limita el cambio a **la terminal actual**.

### ¿Por qué se prefiere `Process` en una práctica?

Porque el cambio no queda configurado permanentemente para el usuario.

Cuando se cierre esa sesión de PowerShell, el ámbito `Process` deja de aplicarse.

Después vuelve a comprobar:

```powershell
npm -v
```

> No modifiques políticas de equipo institucionales ni utilices cambios de alcance `LocalMachine` sin autorización.

---

# 14. Instalar TypeScript dentro del proyecto

Comprueba primero que sigues en:

```text
tema01-js-ts
```

Luego ejecuta:

```bash
npm install typescript --save-dev
```

## ¿Qué significa cada parte?

| Parte | Función |
|---|---|
| `npm` | ejecuta el gestor de paquetes |
| `install` | descarga e instala un paquete |
| `typescript` | paquete que deseamos instalar |
| `--save-dev` | registra el paquete como dependencia de desarrollo |

## ¿Por qué TypeScript se instala dentro del proyecto?

Porque así cada proyecto puede trabajar con su propia versión de TypeScript.

Esto evita depender de una instalación global distinta en cada computadora.

## ¿Qué modifica este comando?

Después de ejecutarlo aparecerán normalmente:

```text
tema01-js-ts/
  node_modules/
  package-lock.json
  package.json
```

Y `package.json` incorporará una sección similar a:

```json
"devDependencies": {
  "typescript": "..."
}
```

## ¿Qué significa cada nuevo elemento?

### `node_modules/`

Contiene los paquetes instalados físicamente.

No debes editar manualmente esta carpeta.

### `package-lock.json`

Registra las versiones concretas que npm resolvió para el proyecto.

### `devDependencies`

Indica herramientas que se utilizan para desarrollar o compilar el proyecto.

TypeScript aparece aquí porque lo necesitamos durante el desarrollo.

---

# 15. Verificar TypeScript

Ejecuta:

```bash
npx tsc --version
```

## ¿Por qué utilizamos `npx`?

TypeScript fue instalado dentro del proyecto.

`npx` permite ejecutar la herramienta local sin depender de una instalación global.

## ¿Qué significa `tsc`?

`tsc` significa:

```text
TypeScript Compiler
```

## ¿Qué hace `--version`?

Solo consulta la versión.

No compila todavía ningún archivo.

## Resultado esperado

```text
Version X.X.X
```

---

# 16. Crear `tsconfig.json`

Ejecuta:

```bash
npx tsc --init
```

## ¿Qué hace?

El compilador TypeScript crea un archivo:

```text
tsconfig.json
```

## ¿Compila el proyecto?

No.

`--init` solamente crea una configuración inicial.

## ¿Por qué necesitamos este archivo?

Porque `tsconfig.json` permitirá indicar:

- dónde estará el código TypeScript;
- dónde se generará JavaScript;
- qué nivel de validación utilizar;
- qué versión de JavaScript producir;
- cómo se manejarán los módulos.

## Diferencia importante

```text
npm init -y
    -> crea package.json

npx tsc --init
    -> crea tsconfig.json
```

No son equivalentes.

---

# 17. Crear la estructura de carpetas

Desde la raíz:

```bash
mkdir js
mkdir src
```

## ¿Qué hace cada comando?

```bash
mkdir js
```

crea la carpeta para ejemplos JavaScript.

```bash
mkdir src
```

crea la carpeta para archivos TypeScript fuente.

Después crea:

```text
src/
  models/
  services/
```

En Windows puedes hacerlo con:

```bat
mkdir src\models
mkdir src\services
```

## ¿Por qué todavía no creamos `dist`?

Porque queremos que `dist` aparezca como **resultado de la compilación**.

La estructura esperada antes de compilar será:

```text
tema01-js-ts/
  js/
  node_modules/
  src/
    models/
    services/
  package-lock.json
  package.json
  tsconfig.json
```

---

# 18. Configurar `tsconfig.json`

Abre `tsconfig.json` y utiliza esta configuración de laboratorio:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "CommonJS",
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true
  },
  "include": ["src/**/*.ts"]
}
```

## ¿Qué controla cada propiedad?

### `target`

```json
"target": "ES2022"
```

Indica la versión de JavaScript que TypeScript utilizará como salida.

---

### `module`

```json
"module": "CommonJS"
```

Controla cómo se representarán los módulos en el JavaScript generado.

En esta práctica nos permite utilizar:

```text
import
export
```

en TypeScript y posteriormente ejecutar el resultado con Node.js.

---

### `rootDir`

```json
"rootDir": "./src"
```

Indica:

> “Mi código TypeScript fuente está dentro de `src`”.

---

### `outDir`

```json
"outDir": "./dist"
```

Indica:

> “El JavaScript compilado debe guardarse dentro de `dist`”.

---

### `strict`

```json
"strict": true
```

Activa validaciones estrictas de tipos.

Es importante para que el estudiante pueda observar errores antes de ejecutar el programa.

---

### `include`

```json
"include": ["src/**/*.ts"]
```

Indica que se deben considerar los archivos `.ts` ubicados en `src` y sus subcarpetas.

---

## Punto de control del entorno

- [ ] `node -v` funciona.
- [ ] `npm -v` funciona.
- [ ] Estoy ubicado dentro de `tema01-js-ts`.
- [ ] Existe `package.json`.
- [ ] Existe `node_modules`.
- [ ] Existe `package-lock.json`.
- [ ] `npx tsc --version` funciona.
- [ ] Existe `tsconfig.json`.
- [ ] Existen `js`, `src/models` y `src/services`.
- [ ] Comprendo que `dist` aparecerá al compilar.

---

# PARTE II - JAVASCRIPT MODERNO ES6+

# Bloque 1. `var`, `let` y `const`

**Objetivo:** diferenciar alcance, reasignación y uso recomendado.

Crea:

```text
js/01-variables.js
```

con:

```javascript
function calcularTotal(productos) {
  let total = 0;

  for (let i = 0; i < productos.length; i++) {
    const precio = productos[i].precio;
    total += precio;
  }

  return total;
}

const carrito = [
  { nombre: "Laptop", precio: 2500 },
  { nombre: "Mouse", precio: 150 },
  { nombre: "Teclado", precio: 300 }
];

console.log(`Total a pagar: S/ ${calcularTotal(carrito)}`);
```

## ¿Por qué se utiliza cada declaración?

- `let total`: cambia mientras se recorren los productos.
- `let i`: cambia en cada iteración.
- `const precio`: no necesita reasignarse durante esa vuelta.
- `const carrito`: no se reasigna la referencia del array.

## Ejecutar

Desde la raíz:

```bash
node js/01-variables.js
```

## ¿Qué hace este comando?

`node` recibe la ruta de un archivo JavaScript y lo ejecuta.

Aquí no utilizamos `npx tsc` porque el archivo ya es `.js`.

## Resultado esperado

```text
Total a pagar: S/ 2950
```

## Actividad

Agrega:

```javascript
{ nombre: "Monitor", precio: 900 }
```

y vuelve a ejecutar el mismo comando.

### ¿Por qué hay que volver a ejecutar?

Porque modificar el archivo no ejecuta automáticamente el programa.

Node.js debe leer nuevamente el archivo actualizado.

---

# Bloque 2. Funciones flecha y template literals

Crea:

```text
js/02-funciones-template.js
```

```javascript
const calcularDescuento = (precio, porcentaje) => {
  return precio * porcentaje;
};

const producto = "Laptop";
const precio = 2500;
const descuento = calcularDescuento(precio, 0.10);

console.log(`${producto}: precio S/ ${precio}, descuento S/ ${descuento}`);
```

Ejecuta:

```bash
node js/02-funciones-template.js
```

## ¿Por qué utilizamos nuevamente `node`?

Porque seguimos ejecutando JavaScript directamente.

El comando significa:

```text
node + ruta_del_archivo
```

Resultado esperado:

```text
Laptop: precio S/ 2500, descuento S/ 250
```

---

# Bloque 3. Destructuring

Crea:

```text
js/03-destructuring.js
```

```javascript
const producto = {
  id: 101,
  nombre: "Smartphone",
  precio: 1200,
  detalles: {
    marca: "Samsung",
    garantia: "1 año"
  }
};

const {
  nombre,
  precio,
  detalles: { marca }
} = producto;

console.log(`Producto: ${nombre}`);
console.log(`Precio: S/ ${precio}`);
console.log(`Marca: ${marca}`);
```

Ejecuta:

```bash
node js/03-destructuring.js
```

## ¿Qué estamos validando?

Que la ruta utilizada en el destructuring coincida con la estructura real del objeto.

Antes de ejecutar:

1. guarda el archivo;
2. comprueba que estás en la raíz del proyecto;
3. ejecuta el comando.

---


# Bloque 4. Promesas y `async/await`

## Ejemplo 4A - Comprender una Promesa paso a paso

**Objetivo:** comprender cómo JavaScript representa una operación cuyo resultado no se obtiene inmediatamente y cómo podemos reaccionar cuando dicha operación termina correctamente o produce un error.

Antes de escribir código, debemos comprender el problema que queremos representar.

Imagina que una aplicación necesita buscar un usuario en una base de datos o consultar información desde un servicio externo.

El resultado normalmente **no está disponible de manera inmediata**.

La aplicación realiza una solicitud, espera una respuesta y después pueden ocurrir dos situaciones:

```text
Operación asincrónica
        |
        v
     Promise
        |
   +----+----+
   |         |
   v         v
 Éxito      Error
resolve    reject
   |         |
   v         v
.then()   .catch()
```

Una `Promise` representa precisamente ese resultado que estará disponible posteriormente.

---

## 4A.1. ¿Qué es una Promise?

Una `Promise` es un objeto que representa el resultado futuro de una operación.

Por ejemplo, una operación puede necesitar:

* consultar una API;
* consultar una base de datos;
* leer información;
* esperar que termine otro proceso;
* realizar una tarea que no produce un resultado inmediatamente.

Cuando creamos una promesa, todavía no necesariamente conocemos su resultado.

Por ello, una promesa puede encontrarse en tres estados.

| Estado      | Significado                          |
| ----------- | ------------------------------------ |
| `pending`   | La operación todavía no ha terminado |
| `fulfilled` | La operación terminó correctamente   |
| `rejected`  | La operación terminó con un error    |

Inicialmente una promesa se encuentra en:

```text
pending
```

Después puede pasar a:

```text
fulfilled
```

si la operación termina correctamente mediante:

```javascript
resolve(...)
```

o puede pasar a:

```text
rejected
```

si ocurre un problema mediante:

```javascript
reject(...)
```

Visualmente:

```text
                Promise
                   |
                   v
                pending
                   |
             +-----+-----+
             |           |
             v           v
          resolve      reject
             |           |
             v           v
         fulfilled    rejected
```

---

## 4A.2. Crear el archivo

Dentro de la carpeta `js` crea el archivo:

```text
js/04-promesas.js
```

La estructura del proyecto debería verse aproximadamente así:

```text
tema01-js-ts/
  js/
    01-variables.js
    02-funciones-template.js
    03-destructuring.js
    04-promesas.js
```

---

## 4A.3. Código completo del ejemplo

Escribe dentro de `js/04-promesas.js`:

```javascript
function obtenerUsuario(id) {
  return new Promise((resolve, reject) => {
    console.log("Consultando usuarios...");

    setTimeout(() => {
      const usuarios = {
        1: { nombre: "María", edad: 25 },
        2: { nombre: "Claudia", edad: 28 }
      };

      const usuario = usuarios[id];

      if (usuario) {
        resolve(usuario);
      } else {
        reject(new Error("Usuario no encontrado"));
      }
    }, 1000);
  });
}

obtenerUsuario(1)
  .then((usuario) => {
    console.log(`Usuario: ${usuario.nombre}, edad: ${usuario.edad}`);
  })
  .catch((error) => {
    console.error("Error:", error.message);
  })
  .finally(() => {
    console.log("Consulta finalizada");
  });
```

Antes de ejecutar el programa, vamos a comprender qué realiza cada parte.

---

## 4A.4. Paso 1 - Crear la función `obtenerUsuario`

Observa:

```javascript
function obtenerUsuario(id) {
```

Creamos una función llamada:

```text
obtenerUsuario
```

La función recibe un parámetro:

```text
id
```

Este identificador será utilizado para buscar un usuario.

Por ejemplo:

```javascript
obtenerUsuario(1);
```

significa:

> Buscar el usuario cuyo identificador es `1`.

Mientras que:

```javascript
obtenerUsuario(2);
```

significa:

> Buscar el usuario cuyo identificador es `2`.

Si utilizamos:

```javascript
obtenerUsuario(99);
```

intentaremos encontrar un usuario cuyo identificador es `99`.

En nuestro ejemplo ese usuario no existe.

---

## 4A.5. Paso 2 - Crear y devolver una Promise

Dentro de la función encontramos:

```javascript
return new Promise((resolve, reject) => {
```

Esta línea es fundamental.

La función `obtenerUsuario()` no devuelve inmediatamente un usuario.

Devuelve una:

```text
Promise
```

Podemos interpretarla como:

> La operación todavía está trabajando, pero posteriormente entregará un resultado o un error.

La promesa recibe dos funciones importantes:

```javascript
resolve
reject
```

---

### ¿Qué hace `resolve`?

`resolve` se utiliza cuando la operación termina correctamente.

Por ejemplo:

```javascript
resolve(usuario);
```

significa:

> La operación terminó correctamente y el resultado obtenido es `usuario`.

Conceptualmente:

```text
Promise
   |
   v
pending
   |
   v
resolve(usuario)
   |
   v
fulfilled
```

---

### ¿Qué hace `reject`?

`reject` se utiliza cuando la operación no puede completarse correctamente.

Por ejemplo:

```javascript
reject(new Error("Usuario no encontrado"));
```

significa:

> La operación terminó con un error.

Conceptualmente:

```text
Promise
   |
   v
pending
   |
   v
reject(error)
   |
   v
rejected
```

---

## 4A.6. Paso 3 - Informar que comienza la operación

Observa:

```javascript
console.log("Consultando usuarios...");
```

Este mensaje se ejecuta inmediatamente cuando comienza la función.

Por eso será uno de los primeros mensajes que veremos en consola:

```text
Consultando usuarios...
```

En una aplicación real, un mensaje similar podría representar que el sistema está:

* consultando una API;
* buscando información;
* consultando una base de datos;
* esperando la respuesta de otro servicio.

---

## 4A.7. Paso 4 - Simular una operación que demora

Observa:

```javascript
setTimeout(() => {
```

y posteriormente:

```javascript
}, 1000);
```

En este ejemplo utilizamos `setTimeout` para **simular que obtener la información requiere cierto tiempo**.

El número:

```text
1000
```

representa:

```text
1000 milisegundos
```

que equivalen aproximadamente a:

```text
1 segundo
```

Por lo tanto, nuestro ejemplo simula este comportamiento:

```text
Solicitar usuario
       |
       v
esperar aproximadamente
     1 segundo
       |
       v
obtener resultado
```

> `setTimeout` no representa por sí mismo una consulta a una base de datos. Lo utilizamos únicamente para simular una operación que tarda en responder.

---

## 4A.8. Paso 5 - Simular información disponible

Dentro de `setTimeout` encontramos:

```javascript
const usuarios = {
  1: { nombre: "María", edad: 25 },
  2: { nombre: "Claudia", edad: 28 }
};
```

Estamos utilizando un objeto JavaScript para representar datos disponibles.

En nuestro ejemplo tenemos:

```text
ID 1 -> María
ID 2 -> Claudia
```

No existe:

```text
ID 99
```

En una aplicación real estos datos podrían provenir de otro origen.

---

## 4A.9. Paso 6 - Buscar el usuario

La siguiente instrucción es:

```javascript
const usuario = usuarios[id];
```

El valor de `id` dependerá de cómo llamemos a la función.

Si ejecutamos:

```javascript
obtenerUsuario(1);
```

entonces:

```text
id = 1
```

Por lo tanto JavaScript buscará:

```javascript
usuarios[1]
```

y encontrará:

```javascript
{
  nombre: "María",
  edad: 25
}
```

La variable:

```javascript
usuario
```

contendrá ese objeto.

---

## 4A.10. Paso 7 - Decidir si la operación terminó correctamente

Ahora encontramos:

```javascript
if (usuario) {
  resolve(usuario);
} else {
  reject(new Error("Usuario no encontrado"));
}
```

Aquí existen dos posibles caminos.

---

### Camino 1 - El usuario existe

Si encontramos el usuario:

```javascript
resolve(usuario);
```

La promesa termina correctamente.

Conceptualmente:

```text
pending
   |
usuario encontrado
   |
   v
resolve(usuario)
   |
   v
fulfilled
```

Por ejemplo, si buscamos:

```javascript
obtenerUsuario(1);
```

obtendremos:

```javascript
{
  nombre: "María",
  edad: 25
}
```

---

### Camino 2 - El usuario no existe

Si no encontramos el usuario:

```javascript
reject(new Error("Usuario no encontrado"));
```

La promesa termina con un error.

Conceptualmente:

```text
pending
   |
usuario no encontrado
   |
   v
reject(error)
   |
   v
rejected
```

---

# 4A.11. Consumir la Promise

Hasta este momento hemos creado una función que devuelve una promesa.

Ahora necesitamos utilizar su resultado.

Observa:

```javascript
obtenerUsuario(1)
```

Estamos solicitando:

```text
Usuario con ID = 1
```

Como `obtenerUsuario()` devuelve una promesa, podemos indicar qué debe hacer el programa:

* cuando la operación tenga éxito;
* cuando ocurra un error;
* cuando la operación termine.

Para ello utilizamos:

```text
.then()
.catch()
.finally()
```

---

## 4A.12. ¿Qué hace `.then()`?

Observa:

```javascript
.then((usuario) => {
  console.log(`Usuario: ${usuario.nombre}, edad: ${usuario.edad}`);
})
```

`.then()` se ejecuta cuando la promesa termina correctamente.

Eso ocurre cuando en nuestra función se ejecuta:

```javascript
resolve(usuario);
```

Existe una relación directa:

```text
resolve(usuario)
       |
       v
    .then()
```

El valor enviado mediante:

```javascript
resolve(usuario);
```

es recibido por:

```javascript
.then((usuario) => {
```

Por eso dentro del `.then()` podemos escribir:

```javascript
usuario.nombre
usuario.edad
```

En nuestro ejemplo los valores serán:

```text
usuario.nombre = María
usuario.edad = 25
```

y la consola mostrará:

```text
Usuario: María, edad: 25
```

---

## 4A.13. ¿Qué hace `.catch()`?

Observa:

```javascript
.catch((error) => {
  console.error("Error:", error.message);
})
```

`.catch()` se ejecuta cuando la promesa termina con un error.

Esto ocurre cuando utilizamos:

```javascript
reject(...)
```

Existe esta relación:

```text
reject(error)
      |
      v
   .catch()
```

Por ejemplo:

```javascript
reject(new Error("Usuario no encontrado"));
```

produce un objeto de error.

Ese objeto llega a:

```javascript
.catch((error) => {
```

y mediante:

```javascript
error.message
```

podemos obtener el mensaje:

```text
Usuario no encontrado
```

La consola mostrará:

```text
Error: Usuario no encontrado
```

---

## 4A.14. ¿Qué hace `.finally()`?

Finalmente encontramos:

```javascript
.finally(() => {
  console.log("Consulta finalizada");
});
```

`finally()` se ejecuta cuando la operación termina.

Se ejecutará tanto si ocurrió:

```text
resolve
```

como si ocurrió:

```text
reject
```

Podemos interpretarlo así:

```text
              Promise
                 |
         +-------+-------+
         |               |
         v               v
      resolve          reject
         |               |
         v               v
      .then()          .catch()
         |               |
         +-------+-------+
                 |
                 v
             .finally()
```

En nuestro ejemplo mostrará:

```text
Consulta finalizada
```

---

# 4A.15. Ejecutar el caso exitoso

Ahora sí ejecutaremos el programa.

Comprueba primero que la terminal se encuentre dentro de:

```text
tema01-js-ts
```

Puedes verificarlo antes de continuar.

Luego ejecuta:

```bash
node js/04-promesas.js
```

---

## ¿Qué significa este comando?

Tenemos dos partes:

```text
node
```

y:

```text
js/04-promesas.js
```

### `node`

Solicita a Node.js ejecutar un archivo JavaScript.

### `js/04-promesas.js`

Es la ruta del archivo que queremos ejecutar.

Por lo tanto:

```bash
node js/04-promesas.js
```

significa:

> Node.js, ejecuta el archivo `04-promesas.js` que se encuentra dentro de la carpeta `js`.

---

# 4A.16. Resultado esperado

Actualmente nuestro código contiene:

```javascript
obtenerUsuario(1)
```

El usuario con ID `1` sí existe.

Por ello, primero debería aparecer:

```text
Consultando usuarios...
```

Después de aproximadamente un segundo:

```text
Usuario: María, edad: 25
Consulta finalizada
```

La salida completa será aproximadamente:

```text
Consultando usuarios...
Usuario: María, edad: 25
Consulta finalizada
```

---

# 4A.17. Comprender el orden de ejecución

No memorices solamente la salida.

Relaciona cada mensaje con la instrucción que lo produjo.

---

## Momento 1 - Comienza la operación

Se ejecuta:

```javascript
console.log("Consultando usuarios...");
```

Aparece:

```text
Consultando usuarios...
```

En este momento podemos entender conceptualmente que la promesa se encuentra:

```text
pending
```

---

## Momento 2 - La operación está esperando

`setTimeout` está simulando una operación que tarda aproximadamente un segundo.

Conceptualmente:

```text
Consultando usuarios...
        |
        v
Promise pendiente
        |
        v
espera aproximada
        |
        v
continúa la operación
```

---

## Momento 3 - Se busca el usuario

Se ejecuta:

```javascript
const usuario = usuarios[id];
```

Como hemos llamado:

```javascript
obtenerUsuario(1)
```

entonces:

```text
id = 1
```

El usuario existe.

Por ello se ejecuta:

```javascript
resolve(usuario);
```

La promesa termina correctamente.

---

## Momento 4 - Se ejecuta `.then()`

Como ocurrió:

```javascript
resolve(usuario);
```

se ejecuta:

```javascript
.then(...)
```

y aparece:

```text
Usuario: María, edad: 25
```

---

## Momento 5 - Se ejecuta `.finally()`

Después se ejecuta:

```javascript
.finally(...)
```

y aparece:

```text
Consulta finalizada
```

---

# 4A.18. Flujo completo del caso exitoso

```text
obtenerUsuario(1)
        |
        v
new Promise(...)
        |
        v
pending
        |
        v
"Consultando usuarios..."
        |
        v
espera aproximada
de 1 segundo
        |
        v
buscar usuarios[1]
        |
        v
usuario encontrado
        |
        v
resolve(usuario)
        |
        v
fulfilled
        |
        v
.then(usuario)
        |
        v
"Usuario: María, edad: 25"
        |
        v
.finally()
        |
        v
"Consulta finalizada"
```

---

# 4A.19. Probar ahora el camino de error

Para comprender completamente una promesa debemos probar también qué sucede cuando la operación falla.

Busca al final del archivo:

```javascript
obtenerUsuario(1)
```

y cámbialo por:

```javascript
obtenerUsuario(99)
```

¿Por qué utilizamos `99`?

Porque dentro de nuestro objeto solo tenemos:

```text
1 -> María
2 -> Claudia
```

Por lo tanto:

```text
99
```

no existe.

Guarda el archivo.

Vuelve a ejecutar:

```bash
node js/04-promesas.js
```

---

## Resultado esperado

Primero aparecerá:

```text
Consultando usuarios...
```

Después de aproximadamente un segundo:

```text
Error: Usuario no encontrado
Consulta finalizada
```

---

# 4A.20. ¿Qué ocurrió internamente?

Cuando ejecutamos:

```javascript
obtenerUsuario(99)
```

JavaScript intenta encontrar:

```javascript
usuarios[99]
```

Como no existe, la condición:

```javascript
if (usuario)
```

no se cumple.

Entonces se ejecuta:

```javascript
reject(new Error("Usuario no encontrado"));
```

Eso provoca que se ejecute:

```javascript
.catch(...)
```

y no:

```javascript
.then(...)
```

El flujo será:

```text
obtenerUsuario(99)
        |
        v
Promise
        |
        v
pending
        |
        v
buscar usuarios[99]
        |
        v
no existe
        |
        v
reject(error)
        |
        v
rejected
        |
        v
.catch(error)
        |
        v
"Error: Usuario no encontrado"
        |
        v
.finally()
        |
        v
"Consulta finalizada"
```

---

# 4A.21. Comparar éxito y error

| Situación             | Acción dentro de la Promise | Método que procesa el resultado |
| --------------------- | --------------------------- | ------------------------------- |
| Usuario encontrado    | `resolve(usuario)`          | `.then()`                       |
| Usuario no encontrado | `reject(error)`             | `.catch()`                      |
| La operación termina  | éxito o error               | `.finally()`                    |

Podemos resumirlo así:

```text
                 Promise
                    |
                    v
                 pending
                    |
          +---------+---------+
          |                   |
          v                   v
       resolve             reject
          |                   |
          v                   v
      fulfilled            rejected
          |                   |
          v                   v
       .then()             .catch()
          |                   |
          +---------+---------+
                    |
                    v
                .finally()
```

---

# 4A.22. Actividad de comprobación

Realiza las siguientes pruebas.

---

## Prueba 1

Utiliza:

```javascript
obtenerUsuario(1)
```

Ejecuta:

```bash
node js/04-promesas.js
```

Registra:

* nombre obtenido;
* edad obtenida;
* si se ejecutó `.then()` o `.catch()`.

---

## Prueba 2

Utiliza:

```javascript
obtenerUsuario(2)
```

Ejecuta nuevamente:

```bash
node js/04-promesas.js
```

Registra el resultado.

---

## Prueba 3

Utiliza:

```javascript
obtenerUsuario(99)
```

Ejecuta:

```bash
node js/04-promesas.js
```

Identifica:

* qué mensaje aparece;
* qué método procesa el error;
* si `finally()` continúa ejecutándose.

---

# 4A.23. Mini reto

Agrega un nuevo usuario:

```javascript
3: { nombre: "Carlos", edad: 31 }
```

El objeto debería quedar similar a:

```javascript
const usuarios = {
  1: { nombre: "María", edad: 25 },
  2: { nombre: "Claudia", edad: 28 },
  3: { nombre: "Carlos", edad: 31 }
};
```

Después ejecuta:

```javascript
obtenerUsuario(3)
```

y comprueba el resultado.

---

# 4A.24. Preguntas de comprobación

Responde con tus propias palabras:

1. ¿Qué representa una `Promise`?
2. ¿En qué estado se encuentra inicialmente una promesa?
3. ¿Qué significa el estado `fulfilled`?
4. ¿Qué significa el estado `rejected`?
5. ¿Cuándo utilizamos `resolve()`?
6. ¿Cuándo utilizamos `reject()`?
7. ¿Qué relación existe entre `resolve()` y `.then()`?
8. ¿Qué relación existe entre `reject()` y `.catch()`?
9. ¿Por qué `.finally()` se ejecuta tanto cuando existe éxito como cuando ocurre un error?
10. ¿Para qué se utiliza `setTimeout` específicamente en este ejemplo?
11. ¿Qué diferencia observaste entre ejecutar `obtenerUsuario(1)` y `obtenerUsuario(99)`?
12. ¿Por qué decimos que el resultado de esta operación no está disponible inmediatamente?

---

# 4A.25. Punto de control

Antes de continuar con `async/await`, debes poder explicar este flujo sin revisar el código:

```text
new Promise(...)
      |
      v
   pending
      |
 +----+----+
 |         |
 v         v
resolve   reject
 |         |
 v         v
.then()  .catch()
   \       /
    \     /
     v   v
   .finally()
```

Debes comprender que:

* `Promise` representa un resultado que estará disponible posteriormente;
* `pending` significa que la operación todavía no termina;
* `resolve()` indica que la operación terminó correctamente;
* `reject()` indica que la operación terminó con un error;
* `.then()` procesa el resultado exitoso;
* `.catch()` procesa el error;
* `.finally()` se ejecuta al finalizar cualquiera de los dos caminos.

Con esta base podemos estudiar ahora `async/await`, que ofrece una forma diferente y normalmente más legible de consumir una promesa.

---

# PARTE III - TYPESCRIPT Y TIPADO ESTÁTICO

# Bloque 5. Primer archivo TypeScript

Crea:

```text
src/tipos.ts
```

```typescript
let nombre: string = "Yuraima";
let edad: number = 30;
let activo: boolean = true;

let dato: any = "ISIL";
dato = 42;

let desconocido: unknown = "hola";

let numeros: number[] = [1, 2, 3];
let personaje: [string, number] = ["Krillin", 5];

let id: number | string;
id = 101;
id = "A23";

enum Estado {
  Activo,
  Inactivo,
  Pendiente
}

const estadoActual: Estado = Estado.Activo;

function identidad<T>(valor: T): T {
  return valor;
}

console.log(nombre, edad, activo);
console.log(dato, desconocido);
console.log(numeros, personaje, id, estadoActual);
console.log(identidad<string>("Saiyan"));
console.log(identidad<number>(9000));
```

---

## Paso 1. Compilar

Desde la raíz:

```bash
npx tsc
```

## ¿Qué ocurre exactamente?

Cuando ejecutas `npx tsc`:

1. `npx` localiza el compilador TypeScript instalado en el proyecto.
2. `tsc` busca `tsconfig.json`.
3. Lee `include`.
4. Encuentra los `.ts` dentro de `src`.
5. Comprueba los tipos.
6. Transforma TypeScript en JavaScript.
7. Guarda el resultado dentro de `dist`.

## Resultado esperado

Después de compilar:

```text
dist/
  tipos.js
```

## ¿Por qué no ejecutamos directamente `src/tipos.ts`?

Porque el objetivo de esta práctica es aprender el proceso:

```text
TypeScript fuente
      |
      | npx tsc
      v
JavaScript compilado
      |
      | node
      v
ejecución
```

---

## Paso 2. Ejecutar el resultado compilado

```bash
node dist/tipos.js
```

## ¿Qué hace?

Node.js ejecuta el JavaScript generado por TypeScript.

Por tanto:

```text
npx tsc
```

y:

```text
node dist/tipos.js
```

cumplen funciones diferentes.

| Comando | Función |
|---|---|
| `npx tsc` | comprobar y compilar |
| `node dist/tipos.js` | ejecutar |

---

# 19. Provocar un error de tipos de forma controlada

Cambia:

```typescript
let edad: number = 30;
```

por:

```typescript
let edad: number = "treinta";
```

Ejecuta nuevamente:

```bash
npx tsc
```

## ¿Por qué repetimos el comando?

Porque modificaste el código y necesitamos que TypeScript vuelva a comprobarlo.

## Qué debes observar

El compilador debe indicar que:

```text
string
```

no es compatible con:

```text
number
```

## ¿Qué demuestra este ejercicio?

Que TypeScript puede detectar un problema antes de ejecutar la aplicación.

Corrige nuevamente:

```typescript
let edad: number = 30;
```

y recompila:

```bash
npx tsc
```

No continúes hasta que la compilación quede limpia.

---

# Bloque 6. `unknown`, union types, enum y generic

Agrega:

```typescript
if (typeof desconocido === "string") {
  console.log(desconocido.toUpperCase());
}
```

Después:

```bash
npx tsc
```

y:

```bash
node dist/tipos.js
```

## ¿Por qué siempre se respeta este orden?

Porque el archivo fuente está en `src`.

Después de modificar un `.ts`:

```text
modificar
   |
guardar
   |
npx tsc
   |
node dist/...
```

Si omites la compilación, podrías ejecutar un `.js` anterior.

---

# Bloque 7. Interfaces y type aliases

Crea:

```text
src/models/Usuario.ts
```

```typescript
export interface Usuario {
  id: number;
  nombre: string;
  activo: boolean;
}

export type Identificador = number | string;
```

Crea:

```text
src/usuarios-demo.ts
```

```typescript
import { Usuario, Identificador } from "./models/Usuario";

const u1: Usuario = {
  id: 1,
  nombre: "Henry",
  activo: true
};

const u2: Usuario = {
  id: 2,
  nombre: "Zulay",
  activo: false
};

const codigo: Identificador = "USR-001";

console.log(u1);
console.log(u2);
console.log(`Código: ${codigo}`);
```

Compila:

```bash
npx tsc
```

Luego ejecuta:

```bash
node dist/usuarios-demo.js
```

## ¿Por qué son dos comandos?

### `npx tsc`

Comprueba que los objetos respeten la interfaz y genera JavaScript.

### `node dist/usuarios-demo.js`

Ejecuta el archivo ya compilado.

> Si modificas `usuarios-demo.ts` y ejecutas únicamente `node dist/usuarios-demo.js`, podrías ejecutar una versión anterior.

---

# Bloque 8. Clases, modificadores y herencia

Crea:

```text
src/models/Personaje.ts
```

```typescript
export class Personaje {
  private nombre: string;
  public nivel: number;
  protected poder: number;

  constructor(nombre: string, nivel: number, poder: number) {
    this.nombre = nombre;
    this.nivel = nivel;
    this.poder = poder;
  }

  public presentarse(): string {
    return `Soy ${this.nombre}, nivel ${this.nivel}`;
  }
}

export class Guerrero extends Personaje {
  public atacar(): string {
    return `Ataque con poder ${this.poder}`;
  }
}
```

Crea:

```text
src/clases-demo.ts
```

```typescript
import { Guerrero } from "./models/Personaje";

const goku = new Guerrero("Goku", 99, 9000);

console.log(goku.presentarse());
console.log(goku.atacar());
console.log(`Nivel público: ${goku.nivel}`);
```

Ejecuta:

```bash
npx tsc
```

y después:

```bash
node dist/clases-demo.js
```

## Qué valida cada paso

`npx tsc` comprueba:

- tipos;
- accesos;
- imports;
- sintaxis TypeScript.

`node` comprueba:

- comportamiento del JavaScript generado.

---

# PARTE IV - MÓDULOS, NAMESPACES Y `tsconfig.json`

# Bloque 9. Módulos con `export` e `import`

Crea:

```text
src/models/Producto.ts
```

```typescript
export interface Producto {
  id: number;
  nombre: string;
  precio: number;
}
```

Crea:

```text
src/services/CarritoService.ts
```

```typescript
import { Producto } from "../models/Producto";

export function calcularTotal(productos: Producto[]): number {
  return productos.reduce((total, producto) => total + producto.precio, 0);
}
```

Crea:

```text
src/main.ts
```

```typescript
import { Producto } from "./models/Producto";
import { calcularTotal } from "./services/CarritoService";

const carrito: Producto[] = [
  { id: 1, nombre: "Laptop", precio: 2500 },
  { id: 2, nombre: "Mouse", precio: 150 },
  { id: 3, nombre: "Teclado", precio: 300 }
];

console.log(`Total del carrito: S/ ${calcularTotal(carrito)}`);
```

---

## Compilar el proyecto completo

```bash
npx tsc
```

## ¿Qué cambia ahora?

TypeScript debe analizar varios archivos relacionados:

```text
main.ts
  |
  +--> models/Producto.ts
  |
  +--> services/CarritoService.ts
```

El compilador sigue los imports y genera la estructura correspondiente en `dist`.

---

## Ejecutar el punto principal

```bash
node dist/main.js
```

## ¿Por qué `main.js`?

Porque `main.ts` es el archivo que reúne los módulos del ejemplo.

Después de compilar, el archivo ejecutable equivalente queda en:

```text
dist/main.js
```

Resultado esperado:

```text
Total del carrito: S/ 2950
```

---

# Bloque 10. Namespace

Crea:

```text
src/namespace-demo.ts
```

```typescript
namespace Utilidades {
  export function saludar(nombre: string): string {
    return `Hola, ${nombre}`;
  }

  export function despedir(nombre: string): string {
    return `Adiós, ${nombre}`;
  }
}

console.log(Utilidades.saludar("Ángel"));
console.log(Utilidades.despedir("Ángel"));
```

Compila:

```bash
npx tsc
```

Ejecuta:

```bash
node dist/namespace-demo.js
```

## ¿Por qué el orden no cambia?

Porque:

```text
namespace-demo.ts
```

es TypeScript fuente.

Node.js ejecutará:

```text
namespace-demo.js
```

después de la compilación.

---

# PARTE V - ACTIVIDAD INTEGRADORA

# 20. Construir un mini sistema de productos

## Objetivo

Integrar:

- interfaces;
- módulos;
- función flecha;
- template literals;
- compilación TypeScript.

## Requisitos

1. En `Producto.ts`, agrega:

```typescript
activo: boolean;
```

2. Actualiza todos los productos de `main.ts`.
3. Crea una función:

```typescript
export function obtenerActivos(productos: Producto[]): Producto[] {
  return productos.filter((producto) => {
    return /* condición */;
  });
}
```

4. Muestra solo los productos activos.
5. Mantén el cálculo total.

---

## Compilar después de modificar

```bash
npx tsc
```

### ¿Por qué?

Porque agregaste una propiedad obligatoria a la interfaz.

TypeScript debe comprobar que todos los objetos cumplan ahora el nuevo contrato.

---

## Ejecutar después de una compilación correcta

```bash
node dist/main.js
```

### ¿Por qué no antes?

Porque `dist/main.js` debe corresponder con la versión actual del código TypeScript.

---

# PARTE VI - ERRORES FRECUENTES Y DIAGNÓSTICO

# 21. Tabla de diagnóstico

| Problema | Causa probable | Qué revisar | Solución inicial |
|---|---|---|---|
| `node` no se reconoce | Node.js no está disponible en la terminal | `node -v` | cierra y abre la terminal; revisa la instalación |
| `npm` no se reconoce | npm no está disponible | `npm -v` | revisa Node.js o abre una terminal nueva |
| PowerShell bloquea `npm.ps1` | política de ejecución | `Get-ExecutionPolicy -List` | usa Command Prompt o una alternativa autorizada |
| `package.json` aparece en otra carpeta | ejecutaste `npm init` desde una ubicación incorrecta | ruta de la terminal | entra primero con `cd tema01-js-ts` |
| `npx tsc` falla | falta TypeScript o estás fuera del proyecto | `package.json`, `node_modules`, ubicación | vuelve a la raíz y verifica instalación |
| no aparece `dist` | la compilación falla o `outDir` es incorrecto | errores de `npx tsc` y `tsconfig.json` | corrige los errores y recompila |
| TypeScript reporta incompatibilidad | el valor no coincide con el tipo | línea reportada | corrige tipo o dato |
| `Cannot find module` | ruta de import incorrecta | `./` y `../` | revisa carpetas y nombres |
| ejecutas un resultado antiguo | modificaste `.ts` pero no recompilaste | fecha/código de `dist` | ejecuta `npx tsc` nuevamente |
| `await` da error | se usa fuera del contexto esperado | función contenedora | revisa que corresponda a una función `async` |

---

# 22. Mapa final de comandos

Antes de terminar, debes poder explicar esta tabla.

| Comando | Qué hace | ¿Modifica archivos? | Por qué se usa |
|---|---|---:|---|
| `node -v` | consulta versión de Node.js | No | verificar instalación |
| `npm -v` | consulta versión de npm | No | verificar npm |
| `mkdir tema01-js-ts` | crea la carpeta | Sí | aislar el proyecto |
| `cd tema01-js-ts` | cambia ubicación | No | trabajar en la carpeta correcta |
| `npm init -y` | inicializa npm | Sí | crear `package.json` |
| `npm install typescript --save-dev` | instala TypeScript | Sí | disponer del compilador local |
| `npx tsc --version` | consulta `tsc` local | No | validar instalación |
| `npx tsc --init` | crea configuración TypeScript | Sí | generar `tsconfig.json` |
| `node js/archivo.js` | ejecuta JavaScript | No | probar ejemplos ES6+ |
| `npx tsc` | comprueba y compila TypeScript | Sí | generar/actualizar `dist` |
| `node dist/archivo.js` | ejecuta JavaScript compilado | No | comprobar el resultado |

---

# PARTE VII - COMPROBACIÓN Y EVIDENCIAS

# 23. Preguntas de comprobación

Responde con una o dos frases.

1. ¿Qué diferencia existe entre `node` y `npm`?
2. ¿Por qué `npm init -y` se ejecuta dentro de la carpeta del proyecto?
3. ¿Qué crea `npm init -y`?
4. ¿Qué modifica `npm install typescript --save-dev`?
5. ¿Por qué se utiliza `npx tsc` en lugar de depender de un `tsc` global?
6. ¿Qué diferencia existe entre `npx tsc --init` y `npx tsc`?
7. ¿Qué función cumple `src`?
8. ¿Qué función cumple `dist`?
9. ¿Por qué debemos recompilar después de modificar un `.ts`?
10. ¿Qué hace `node dist/main.js`?
11. ¿En qué se diferencian `let` y `const`?
12. ¿Para qué sirven los template literals?
13. ¿Qué problema simplifica destructuring?
14. ¿Qué hace `await`?
15. ¿Qué ventaja aporta el tipado estático?
16. ¿Qué define una interfaz?
17. ¿Qué diferencia existe entre `private` y `protected`?
18. ¿Para qué sirven `export` e `import`?
19. ¿Qué función cumple un namespace?
20. ¿Qué hacen `rootDir`, `outDir` y `strict`?

---

# 24. Evidencias de entrega

Prepara:

```text
Tema01_ApellidoNombre/
  tema01-js-ts/
  evidencias/
    01-versiones.png
    02-estructura-proyecto.png
    03-js-ejecutado.png
    04-error-tipado.png
    05-compilacion-correcta.png
    06-integracion-final.png
    respuestas.md
```

## Evidencias mínimas

### `01-versiones.png`

Debe mostrar:

```bash
node -v
npm -v
npx tsc --version
```

### `02-estructura-proyecto.png`

Debe mostrar:

```text
js
src
node_modules
package.json
package-lock.json
tsconfig.json
```

### `03-js-ejecutado.png`

Un ejemplo ES6+ funcionando.

### `04-error-tipado.png`

Error controlado de TypeScript.

### `05-compilacion-correcta.png`

`npx tsc` ejecutado sin errores y `dist` visible.

### `06-integracion-final.png`

Resultado de la actividad integradora.

### `respuestas.md`

Respuestas de la sección de comprobación.

---

# 25. Verificación final

- [ ] Sé qué hace `node`.
- [ ] Sé qué hace `npm`.
- [ ] Sé por qué debo comprobar la carpeta actual.
- [ ] Comprendo `mkdir` y `cd`.
- [ ] Comprendo por qué existe `package.json`.
- [ ] Comprendo qué ocurre al instalar TypeScript.
- [ ] Comprendo para qué sirve `npx`.
- [ ] Comprendo para qué sirve `tsc`.
- [ ] Comprendo la diferencia entre `tsc --init` y `tsc`.
- [ ] Comprendo el flujo `src -> tsc -> dist`.
- [ ] Ejecuté JavaScript ES6+.
- [ ] Compilé TypeScript.
- [ ] Provocé y corregí un error de tipos.
- [ ] Utilicé interfaces y clases.
- [ ] Organicé código mediante módulos.
- [ ] Probé un namespace.
- [ ] Completé la actividad integradora.
- [ ] Preparé las evidencias.

---

# 26. Cierre académico

En esta práctica pasaste de ejecutar JavaScript moderno a trabajar con un proyecto TypeScript estructurado.

La idea principal que debes conservar no es una lista aislada de comandos, sino este flujo:

```text
1. Verifico las herramientas
   node -v
   npm -v

2. Creo y ubico el proyecto
   mkdir
   cd

3. Inicializo npm
   npm init -y

4. Instalo TypeScript
   npm install typescript --save-dev

5. Creo la configuración
   npx tsc --init

6. Escribo TypeScript
   src/*.ts

7. Compruebo y compilo
   npx tsc

8. Ejecuto el resultado
   node dist/*.js
```

Si puedes explicar **qué hace, por qué se ejecuta y qué produce cada paso**, entonces ya no estás copiando comandos: estás comprendiendo el flujo de trabajo.

## Recursos de refuerzo

- Blog: https://lideratecacademy.com/
- Canal YouTube: https://www.youtube.com/@LideratecAcademy
- TypeScript: https://www.typescriptlang.org/
- Node.js: https://nodejs.org/
