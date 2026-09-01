# Lideratec Academy

## PROGRAMACIÓN WEB AVANZADA

### Sesión 01 - Fundamentos de JavaScript moderno y TypeScript

**Práctica guiada:** Del JavaScript ES6+ a un proyecto TypeScript organizado y compilable  
**Proyecto académico:** Lideratec Academy  
**Elaborado por el docente**

---

## 1. Propósito de la práctica

En esta sesión prepararás un entorno local para ejecutar JavaScript y compilar TypeScript. Después trabajarás progresivamente con `let`, `const`, funciones flecha, template literals, destructuring, promesas, `async/await`, tipos de datos de TypeScript, interfaces, type aliases, clases, módulos, namespaces y `tsconfig.json`.

La práctica está diseñada para que no solo copies código: en cada bloque tendrás que ejecutar, observar, modificar y comprobar resultados.

## 2. Resultado de aprendizaje observable

Al finalizar la sesión podrás:

1. Diferenciar el uso de `var`, `let` y `const` en ejemplos ejecutables.
2. Aplicar funciones flecha, template literals y destructuring en JavaScript moderno.
3. Interpretar y ejecutar operaciones asincrónicas con promesas y `async/await`.
4. Definir variables, arrays, tuplas, union types, enums, generics, interfaces y type aliases en TypeScript.
5. Construir clases con modificadores `public`, `private` y `protected`, además de herencia.
6. Organizar código TypeScript mediante módulos y namespaces.
7. Configurar `tsconfig.json`, compilar archivos `.ts` y ejecutar el JavaScript generado.

## 3. Duración sugerida

**180 minutos**, incluyendo una pausa breve y los puntos de control.

## 4. Conocimientos previos mínimos

- Crear carpetas y archivos.
- Abrir una terminal o consola.
- Reconocer variables, funciones y objetos en JavaScript.
- Ejecutar instrucciones siguiendo una secuencia.

No se requiere experiencia previa con TypeScript.

## 5. Herramientas y recursos

| Componente | Función en la práctica | ¿Debe instalarse? | Orden |
|---|---|---:|---:|
| Node.js LTS | Ejecutar JavaScript fuera del navegador y disponer de npm | Sí | 1 |
| npm | Gestionar la dependencia de TypeScript | Se instala con Node.js | 2 |
| TypeScript | Comprobar tipos y compilar `.ts` a `.js` | Sí, dentro del proyecto | 3 |
| Visual Studio Code | Editar archivos y abrir una terminal integrada | Recomendado | 4 |

### Enlaces oficiales

- Node.js: https://nodejs.org/en/download
- TypeScript: https://www.typescriptlang.org/download/
- Visual Studio Code: https://code.visualstudio.com/

> **Referencia de laboratorio:** se recomienda utilizar la versión LTS que muestre la página oficial de Node.js. El número exacto de revisión puede cambiar con el tiempo.

---

# PARTE I - PREPARACIÓN COMPLETA DEL ENTORNO

## 6. ¿Qué componente hace qué?

### Node.js

Node.js permite ejecutar JavaScript desde la terminal. En esta sesión lo utilizarás para ejecutar los archivos `.js` que escribas y los archivos `.js` que TypeScript genere después de compilar.

### npm

npm se instala junto con Node.js. Lo utilizarás para instalar TypeScript dentro del proyecto.

### TypeScript

TypeScript extiende JavaScript con sintaxis de tipos. Los archivos `.ts` se comprueban y se compilan para producir JavaScript ejecutable.

### Visual Studio Code

Es el editor recomendado para crear la estructura de carpetas, escribir código y utilizar una terminal integrada. Si tu laboratorio utiliza otro editor autorizado, puedes mantener los mismos archivos y comandos.

---

## 7. Descargar e instalar Node.js

### Paso 1. Abrir la página oficial

**Objetivo:** localizar la distribución correcta de Node.js.

**Dónde hacerlo:** navegador web.

**Acción:**

1. Abre https://nodejs.org/en/download
2. Localiza la versión identificada como **LTS**.
3. Selecciona la opción adecuada para tu sistema operativo y arquitectura.
4. En Windows, utiliza el instalador precompilado cuando esté disponible.

**Qué debería ocurrir:** se descargará un instalador de Node.js en la carpeta de descargas del sistema.

**Cómo comprobarlo:** revisa que el archivo descargado provenga del dominio `nodejs.org`.

**Si aparece un problema:** si la página muestra varias versiones, elige **LTS** para esta práctica y evita una versión marcada como EOL.

### Paso 2. Ejecutar el instalador

**Objetivo:** instalar Node.js y npm.

**Dónde hacerlo:** carpeta de Descargas de Windows.

**Acción:**

1. Abre la carpeta **Descargas**.
2. Identifica el instalador de Node.js que acabas de descargar.
3. Ejecuta el instalador.
4. Si Windows solicita permisos para realizar cambios, acepta únicamente si el instalador proviene del sitio oficial.
5. Mantén los componentes predeterminados de Node.js y npm.
6. No cambies rutas o componentes del laboratorio salvo indicación institucional.
7. Completa la instalación.
8. Si el sistema solicita reiniciar, guarda tu trabajo y reinicia antes de continuar.

**Qué debería ocurrir:** Node.js quedará disponible desde una nueva terminal.

---

## 8. Verificar Node.js y npm

### Paso 3. Abrir una terminal nueva

**Objetivo:** comprobar que la instalación quedó registrada correctamente.

**Dónde hacerlo:** PowerShell, Símbolo del sistema o terminal integrada de Visual Studio Code.

**Qué debes escribir:**

```bash
node -v
npm -v
```

**Qué debería ocurrir:** cada comando debe mostrar un número de versión.

**Cómo comprobarlo:** si ambos comandos responden con una versión, el entorno base está listo.

**Si aparece un problema:**

- Si aparece "node no se reconoce" o un mensaje equivalente, cierra todas las terminales y abre una nueva.
- Si continúa el error, reinicia Windows y vuelve a ejecutar los comandos.
- Si todavía falla, revisa la instalación antes de continuar.

---

## 9. Instalar Visual Studio Code

Si ya tienes un editor autorizado, puedes continuar con él. Si no lo tienes:

1. Abre https://code.visualstudio.com/
2. Selecciona la descarga correspondiente a tu sistema operativo.
3. Ejecuta el instalador descargado.
4. Mantén la configuración predeterminada del laboratorio.
5. Abre Visual Studio Code al finalizar.

**Comprobación:** debes poder abrir una carpeta, crear archivos y abrir una terminal desde el editor.

---

## 10. Crear el proyecto de la sesión

### Paso 4. Crear la carpeta principal

**Objetivo:** disponer de una estructura única para JavaScript y TypeScript.

**Dónde hacerlo:** terminal.

```bash
mkdir tema01-js-ts
cd tema01-js-ts
```

**Qué debería ocurrir:** la terminal debe quedar ubicada dentro de `tema01-js-ts`.

### Paso 5. Inicializar npm

```bash
npm init -y
```

Nota: en caso salga error de bloqueo por politicas de window:
"PowerShell está bloqueando el script npm.ps1 por la política de ejecución de Windows:"
En la misma terminal de VS Code, ejecuta:
```bash
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

**Qué debería ocurrir:** se creará el archivo `package.json`.

**Cómo comprobarlo:** abre la carpeta en Visual Studio Code y localiza `package.json`.

### Paso 6. Instalar TypeScript dentro del proyecto

```bash
npm install typescript --save-dev
```

**Qué debería ocurrir:** se crearán `node_modules`, `package-lock.json` y una dependencia de desarrollo de TypeScript en `package.json`.

**Cómo comprobarlo:**

```bash
npx tsc --version
```

Debe mostrarse una versión de TypeScript.

### Paso 7. Crear `tsconfig.json`

```bash
npx tsc --init
```

**Qué debería ocurrir:** aparecerá el archivo `tsconfig.json` en la raíz del proyecto.

---

## 11. Configurar la estructura `src` y `dist`

### Paso 8. Crear carpetas

```bash
mkdir js
mkdir src
```

Ahora crea manualmente dentro de `src` estas carpetas:

```text
src/
  models/
  services/
```

La carpeta `dist` será generada por TypeScript al compilar.

### Paso 9. Ajustar `tsconfig.json`

Reemplaza el contenido principal de `tsconfig.json` por esta configuración de laboratorio:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "CommonJS",
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*.ts"]
}
```

### ¿Qué controla esta configuración?

- `rootDir`: indica dónde está el código TypeScript fuente.
- `outDir`: indica dónde se guardará el JavaScript compilado.
- `strict`: activa comprobaciones estrictas de tipos.
- `target`: establece una versión moderna de JavaScript como salida.
- `module`: permite que el ejemplo de `import` y `export` pueda ejecutarse con Node.js en esta práctica.

### Punto de control del entorno

- [ ] Instalé Node.js LTS.
- [ ] `node -v` muestra una versión.
- [ ] `npm -v` muestra una versión.
- [ ] Creé `tema01-js-ts`.
- [ ] Ejecuté `npm init -y`.
- [ ] Instalé TypeScript dentro del proyecto.
- [ ] `npx tsc --version` funciona.
- [ ] Existe `tsconfig.json`.
- [ ] Existen las carpetas `js`, `src/models` y `src/services`.

No continúes si `npx tsc --version` produce un error.

---

# PARTE II - JAVASCRIPT MODERNO ES6+

## Bloque 1. `var`, `let` y `const`

**Objetivo:** diferenciar alcance, reasignación y uso recomendado.

**Concepto trabajado:** `var`, `let`, `const`.

**Explicación breve:** `var` tiene alcance de función o global; `let` y `const` respetan el alcance de bloque. `let` admite reasignación y `const` no permite reasignar la referencia declarada.

### Ejemplo guiado

Crea el archivo `js/01-variables.js`:

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

### Paso a paso

1. `total` se declara con `let` porque cambia durante el recorrido.
2. `i` se declara con `let` porque cambia en cada iteración.
3. `precio` se declara con `const` porque no necesita reasignarse dentro de esa vuelta.
4. `carrito` se declara con `const` porque la referencia al array no se reasigna.

### Ejecutar

```bash
node js/01-variables.js
```

**Resultado esperado:**

```text
Total a pagar: S/ 2950
```

### Actividad para desarrollar

Agrega un cuarto producto con nombre `Monitor` y precio `900`. Ejecuta otra vez y registra el nuevo total.

**Espacio para responder:**

- ¿Qué variable sí necesita reasignación?
- ¿Qué declaracion usarías para una configuración que no cambiará?
- ¿Por qué se evita `var` en este ejemplo?

**Error frecuente:** intentar reasignar una variable declarada con `const`.

**Mini reto:** crea una constante `IGV` con el valor `0.18` y calcula solo el monto del IGV del total.

---

## Bloque 2. Funciones flecha y template literals

**Objetivo:** transformar funciones breves y generar mensajes dinámicos.

### Ejemplo guiado

Crea `js/02-funciones-template.js`:

```javascript
const calcularDescuento = (precio, porcentaje) => {
  return precio * porcentaje;
};

const producto = "Laptop";
const precio = 2500;
const descuento = calcularDescuento(precio, 0.10);

console.log(`${producto}: precio S/ ${precio}, descuento S/ ${descuento}`);
```

### Paso a paso

1. `calcularDescuento` es una función flecha.
2. Los backticks permiten construir una cadena con `${...}`.
3. La expresión `${descuento}` inserta el valor calculado.

### Ejecutar

```bash
node js/02-funciones-template.js
```

**Resultado esperado:**

```text
Laptop: precio S/ 2500, descuento S/ 250
```

### Actividad para desarrollar

Modifica el descuento a `15%` y agrega una variable `precioFinal`.

**Pregunta de comprobación:** ¿qué ventaja observas al usar template literals frente a concatenar muchas cadenas con `+`?

---

## Bloque 3. Destructuring

**Objetivo:** extraer propiedades de objetos sin repetir continuamente `objeto.propiedad`.

Crea `js/03-destructuring.js`:

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

**Resultado esperado:**

```text
Producto: Smartphone
Precio: S/ 1200
Marca: Samsung
```

### Actividad para desarrollar

Extrae también `garantia` usando destructuring y muéstrala en consola.

### Error frecuente

Confundir la ruta de una propiedad anidada. `marca` no está directamente dentro de `producto`; está dentro de `detalles`.

---

## Bloque 4. Promesas y `async/await`

**Objetivo:** interpretar una operación asincrónica y manejar éxito o error.

### Ejemplo 4A - Promesa

Crea `js/04-promesas.js`:

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

Ejecuta:

```bash
node js/04-promesas.js
```

### Qué debes observar

1. Primero aparece el mensaje de consulta.
2. Hay una espera aproximada de un segundo.
3. La promesa se resuelve si el usuario existe.
4. `finally` se ejecuta al terminar.

### Ejemplo 4B - La misma operación con `async/await`

Crea `js/05-async-await.js`:

```javascript
function obtenerUsuario(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const usuarios = {
        1: { nombre: "María", edad: 25 },
        2: { nombre: "Claudia", edad: 28 }
      };

      const usuario = usuarios[id];
      usuario ? resolve(usuario) : reject(new Error("Usuario no encontrado"));
    }, 1000);
  });
}

async function mostrarUsuario(id) {
  try {
    const usuario = await obtenerUsuario(id);
    console.log(`Usuario: ${usuario.nombre}, edad: ${usuario.edad}`);
  } catch (error) {
    console.error("Error:", error.message);
  }
}

mostrarUsuario(2);
```

Ejecuta:

```bash
node js/05-async-await.js
```

### Actividad para desarrollar

Cambia `mostrarUsuario(2)` por `mostrarUsuario(99)`.

**Responde:**

- ¿Qué bloque se ejecuta cuando el usuario no existe?
- ¿Qué palabra clave espera el resultado de la promesa?
- ¿Qué ventaja de lectura encuentras respecto a encadenar varios `.then()`?

### Punto de control JavaScript

- [ ] Ejecuté los cinco archivos JavaScript.
- [ ] Puedo explicar cuándo usar `let` y cuándo usar `const`.
- [ ] Usé una función flecha.
- [ ] Usé template literals.
- [ ] Extraje datos mediante destructuring.
- [ ] Provocé una resolución y un rechazo de una promesa.
- [ ] Probé `async/await` con `try/catch`.

---

# PARTE III - TYPESCRIPT Y TIPADO ESTÁTICO

## Bloque 5. Primer archivo TypeScript y tipos básicos

**Objetivo:** comprobar que TypeScript detecta tipos antes de ejecutar el JavaScript generado.

Crea `src/tipos.ts`:

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

### Compilar

Desde la raíz del proyecto:

```bash
npx tsc
```

**Qué debería ocurrir:** se creará la carpeta `dist` y dentro aparecerá `tipos.js`.

### Ejecutar

```bash
node dist/tipos.js
```

### Prueba de tipado

Cambia temporalmente:

```typescript
let edad: number = 30;
```

por:

```typescript
let edad: number = "treinta";
```

Luego ejecuta:

```bash
npx tsc
```

**Qué debes observar:** TypeScript debe reportar un error de tipo.

**Antes de continuar:** restaura `edad` a un número y vuelve a compilar sin errores.

---

## Bloque 6. `unknown`, union types, enum y generic

**Objetivo:** reconocer distintos mecanismos de tipado presentes en la sesión.

### Actividad guiada con `unknown`

Agrega en `src/tipos.ts`:

```typescript
if (typeof desconocido === "string") {
  console.log(desconocido.toUpperCase());
}
```

**Qué debes observar:** el valor `unknown` se valida antes de utilizar un método propio de `string`.

### Actividad para desarrollar

1. Permite que una variable `codigo` acepte `number` o `string`.
2. Asigna primero `500` y luego `"WEB-500"`.
3. Crea un enum llamado `Nivel` con `Basico`, `Intermedio` y `Avanzado`.
4. Invoca `identidad<boolean>(true)` y muestra el resultado.

**Resultado esperado:** `npx tsc` debe compilar sin errores.

---

## Bloque 7. Interfaces y type aliases

**Objetivo:** definir la forma esperada de un objeto y crear un nombre para un tipo combinado.

Crea `src/models/Usuario.ts`:

```typescript
export interface Usuario {
  id: number;
  nombre: string;
  activo: boolean;
}

export type Identificador = number | string;
```

Ahora crea `src/usuarios-demo.ts`:

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

Compila y ejecuta:

```bash
npx tsc
node dist/usuarios-demo.js
```

### Actividad para desarrollar

Crea `u3` con tus propios datos ficticios y comprueba que la estructura respeta la interfaz.

### Prueba de error controlado

Elimina temporalmente `activo` de `u3` y compila. Observa el error. Después restaura la propiedad.

**Pregunta de comprobación:** ¿qué problema ayuda a detectar la interfaz antes de ejecutar el programa?

---

## Bloque 8. Clases, modificadores y herencia

**Objetivo:** construir objetos a partir de clases y diferenciar `private`, `public` y `protected`.

Crea `src/models/Personaje.ts`:

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

Crea `src/clases-demo.ts`:

```typescript
import { Guerrero } from "./models/Personaje";

const goku = new Guerrero("Goku", 99, 9000);

console.log(goku.presentarse());
console.log(goku.atacar());
console.log(`Nivel público: ${goku.nivel}`);
```

Compila y ejecuta:

```bash
npx tsc
node dist/clases-demo.js
```

**Resultado esperado:** se muestran la presentación, el ataque y el nivel.

### Actividad para desarrollar

Crea otro `Guerrero` con valores diferentes y muestra sus resultados.

### Error frecuente

Intentar acceder desde fuera a `nombre` o `poder`. `nombre` es `private`; `poder` es `protected` y puede utilizarse desde una subclase como `Guerrero`, pero no directamente desde el objeto externo.

---

# PARTE IV - MÓDULOS, NAMESPACES Y `tsconfig.json`

## Bloque 9. Módulos con `export` e `import`

**Objetivo:** separar responsabilidades en archivos TypeScript reutilizables.

Crea `src/models/Producto.ts`:

```typescript
export interface Producto {
  id: number;
  nombre: string;
  precio: number;
}
```

Crea `src/services/CarritoService.ts`:

```typescript
import { Producto } from "../models/Producto";

export function calcularTotal(productos: Producto[]): number {
  return productos.reduce((total, producto) => total + producto.precio, 0);
}
```

Crea `src/main.ts`:

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

Compila:

```bash
npx tsc
```

Ejecuta:

```bash
node dist/main.js
```

**Resultado esperado:**

```text
Total del carrito: S/ 2950
```

### Qué debes interpretar

- `Producto.ts` define una estructura exportable.
- `CarritoService.ts` exporta una función.
- `main.ts` importa ambos elementos para utilizarlos.
- Cada archivo `.ts` que contiene `import` o `export` funciona como módulo.

### Actividad para desarrollar

Agrega un producto al carrito y verifica el nuevo total.

---

## Bloque 10. Namespace

**Objetivo:** reconocer un contenedor lógico que agrupa elementos relacionados.

Crea `src/namespace-demo.ts`:

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

Compila y ejecuta:

```bash
npx tsc
node dist/namespace-demo.js
```

**Resultado esperado:**

```text
Hola, Ángel
Adiós, Ángel
```

### Pregunta de comprobación

¿Qué diferencia práctica observas entre llamar `Utilidades.saludar(...)` y utilizar una función importada desde otro archivo?

Escribe tu explicación con tus propias palabras.

---

# PARTE V - ACTIVIDAD INTEGRADORA

## 12. Construir un mini sistema de productos

**Objetivo:** integrar tipos, interfaces, módulos, función flecha, template literal y compilación.

### Requisitos

Debes reutilizar la estructura ya creada y realizar estos cambios:

1. En `Producto.ts`, agrega la propiedad `activo: boolean`.
2. Actualiza los objetos de `main.ts` para incluir `activo`.
3. Crea una función exportada `obtenerActivos(productos)` dentro de `CarritoService.ts`.
4. La función debe devolver solo los productos activos.
5. Usa una función flecha dentro de la operación de filtrado.
6. En `main.ts`, muestra cada producto activo mediante template literals.
7. Mantén el cálculo del total.
8. Compila con `npx tsc`.
9. Ejecuta `node dist/main.js`.

### Código inicial para la función

Completa únicamente la parte indicada:

```typescript
export function obtenerActivos(productos: Producto[]): Producto[] {
  return productos.filter((producto) => {
    // Completa la condición
    return /* condición */;
  });
}
```

### Debes comprobar

- La compilación termina sin errores.
- Solo se muestran productos con `activo: true`.
- El total sigue calculándose correctamente.
- `dist` contiene los archivos JavaScript generados.

### Espacio para responder

1. ¿Qué error detectaría TypeScript si olvidas agregar `activo` a uno de los productos?
2. ¿Qué archivo define la estructura de `Producto`?
3. ¿Qué archivo contiene la lógica para calcular el total?
4. ¿Por qué `main.ts` no necesita conocer cómo está implementado internamente `calcularTotal`?
5. ¿Qué función cumple `outDir` en `tsconfig.json`?

---

# PARTE VI - ERRORES FRECUENTES Y SOLUCIONES

## 13. Tabla de diagnóstico

| Problema observado | Causa probable | Qué revisar | Solución recomendada |
|---|---|---|---|
| `node` no se reconoce | Node.js no quedó disponible en la terminal actual | `node -v` | Cierra la terminal, abre una nueva y vuelve a comprobar; si persiste, revisa la instalación |
| `npm` no se reconoce | Instalación incompleta o terminal antigua | `npm -v` | Reabre la terminal o reinstala Node.js desde el sitio oficial |
| `npx tsc` falla | TypeScript no está instalado en el proyecto | `package.json` y `node_modules` | Ejecuta `npm install typescript --save-dev` dentro de la carpeta correcta |
| No aparece `dist` | La compilación no terminó o `outDir` está mal configurado | Mensajes de `npx tsc` y `tsconfig.json` | Corrige los errores de compilación y revisa `outDir` |
| TypeScript marca incompatibilidad de tipo | Se asignó un valor de tipo diferente al declarado | Línea indicada por el compilador | Ajusta el dato o el tipo de acuerdo con el objetivo del código |
| `Cannot find module` al compilar | Ruta de `import` incorrecta o archivo faltante | Rutas relativas | Verifica nombre de carpetas, archivo y uso de `../` o `./` |
| Se intenta usar una propiedad `private` desde fuera | El modificador de acceso impide acceso externo | Declaración de la clase | Accede mediante un método público si corresponde |
| `await` produce un error de uso | Se utilizó fuera del contexto esperado | Función que contiene `await` | Verifica que la lógica esté dentro de una función `async` |

---

# PARTE VII - COMPROBACIÓN Y EVIDENCIAS

## 14. Preguntas de comprobación

Responde con una o dos frases por pregunta.

1. ¿En qué se diferencian `let` y `const`?
2. ¿Para qué sirven los template literals?
3. ¿Qué problema simplifica el destructuring?
4. ¿Cuáles son los tres estados conceptuales de una promesa?
5. ¿Qué hace `await` dentro de una función `async`?
6. ¿Qué ventaja aporta el tipado estático de TypeScript?
7. ¿Cuándo utilizarías un union type?
8. ¿Qué define una interfaz?
9. ¿Qué diferencia existe entre `private` y `protected`?
10. ¿Para qué sirven `export` e `import`?
11. ¿Qué función cumple un namespace?
12. ¿Qué hacen `rootDir`, `outDir` y `strict` en `tsconfig.json`?

## 15. Evidencias que debes preparar

Prepara una carpeta con la siguiente estructura:

```text
Tema01_ApellidoNombre/
  tema01-js-ts/
  evidencias/
    01-versiones.png
    02-js-ejecutado.png
    03-error-tipado.png
    04-compilacion-correcta.png
    05-integracion-final.png
    respuestas.md
```

### Evidencias mínimas

1. **01-versiones.png:** terminal mostrando `node -v`, `npm -v` y `npx tsc --version`.
2. **02-js-ejecutado.png:** uno de los ejemplos ES6+ ejecutándose correctamente.
3. **03-error-tipado.png:** error controlado producido por una asignación incompatible y luego corregido.
4. **04-compilacion-correcta.png:** `npx tsc` ejecutado sin errores y carpeta `dist` visible.
5. **05-integracion-final.png:** salida de la actividad integradora.
6. **respuestas.md:** respuestas a las preguntas de comprobación.

> No incluyas contraseñas, tokens ni datos personales sensibles en las capturas.

---

## 16. Verificación final

- [ ] Comprendo el objetivo de la práctica.
- [ ] Preparé el entorno correcto.
- [ ] Ejecuté la comprobación inicial.
- [ ] Desarrollé los ejemplos guiados de JavaScript ES6+.
- [ ] Compilé y ejecuté código TypeScript.
- [ ] Probé tipos básicos y avanzados incluidos en la sesión.
- [ ] Utilicé interfaz y type alias.
- [ ] Construí y ejecuté una clase con herencia.
- [ ] Organicé código con módulos.
- [ ] Ejecuté el ejemplo de namespace.
- [ ] Validé `rootDir`, `outDir` y `strict`.
- [ ] Completé la actividad integradora.
- [ ] Revisé los errores frecuentes.
- [ ] Preparé las evidencias solicitadas.

---

# 17. Cierre académico

En esta práctica pasaste de ejecutar JavaScript moderno a trabajar con un proyecto TypeScript estructurado. Utilizaste características de ES6+, manejaste asincronía, incorporaste tipado estático, definiste interfaces y clases, y finalmente organizaste archivos mediante módulos y un namespace.

Antes de la siguiente sesión, repasa especialmente:

- cuándo utilizar `let` y `const`;
- la diferencia entre una promesa y su consumo con `async/await`;
- el propósito de los tipos en TypeScript;
- la función de interfaces, clases y modificadores de acceso;
- el recorrido `src -> npx tsc -> dist`;
- el uso de `export` e `import`.

Evita avanzar si tu proyecto todavía presenta errores de compilación. La carpeta `dist` debe ser el resultado de un proyecto TypeScript que compile correctamente.

## Recursos de refuerzo

- Blog: https://lideratecacademy.com/
- Canal YouTube: https://www.youtube.com/@LideratecAcademy
- TypeScript: https://www.typescriptlang.org/
- Node.js: https://nodejs.org/
