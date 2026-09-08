# GUÍA DEL ESTUDIANTE

## Programación Web Avanzada - Tema 02

**Sesión:** Uso de TypeScript en desarrollo web  
**Práctica guiada:** Componentes, servicios, genéricos, Webpack, TSConfig y depuración  
**Modalidad sugerida:** laboratorio guiado  
**Duración sugerida:** 230 minutos, incluida una pausa breve  
**Fecha de verificación técnica:** 08/09/2026

---

## 1. ¿Qué aprenderás en esta sesión?

Al finalizar podrás:

1. explicar por qué TypeScript mejora la mantenibilidad y detección temprana de errores en proyectos web;
2. construir componentes simples con clases e interfaces;
3. crear componentes genéricos con `<T>` y restricciones con `extends`;
4. separar acceso a datos mediante un servicio genérico con `async/await`;
5. crear desde cero un proyecto TypeScript con `tsconfig.json`;
6. integrar TypeScript con Webpack usando `ts-loader`;
7. generar `bundle.js` y mapas de código fuente;
8. interpretar errores de compilación, rutas y configuración;
9. organizar el código en módulos con `export` e `import`;
10. validar que el proyecto compila y que el navegador ejecuta el resultado correcto.

## 2. Mapa general de la práctica

```text
CARPETA DEL PROYECTO
        |
        +--> package.json            -> dependencias y scripts
        +--> tsconfig.json           -> reglas del compilador TypeScript
        +--> webpack.config.cjs      -> reglas del empaquetador
        +--> index.html              -> página que carga dist/bundle.js
        |
        +--> src/
        |     +--> index.ts          -> punto de entrada
        |     +--> components/       -> componentes reutilizables
        |     +--> services/         -> acceso a datos
        |     +--> models/           -> interfaces de datos
        |     +--> utils/            -> funciones reutilizables
        |
        +--> dist/
              +--> bundle.js         -> salida de Webpack
              +--> bundle.js.map     -> mapa para depuración
```

La práctica seguirá exactamente ese recorrido. Cada vez que aparezca código nuevo se indicará **qué archivo crear, en qué carpeta, por qué existe y cómo comprobarlo**.

## 3. Herramientas recomendadas para esta práctica

> Importante: "la versión más nueva" no siempre es la versión correcta para una clase. En esta sesión se conserva el enfoque del material: Webpack + `ts-loader`.

| Herramienta | Estado al 08/09/2026 | Recomendación para clase | Motivo |
|---|---|---|---|
| Node.js | v24 LTS; v26 Current | **Node.js 24 LTS** | LTS es más apropiada para laboratorio estable. |
| TypeScript | 7.0.2 estable | **TypeScript 6.0.3** | `ts-loader` presenta incompatibilidad conocida con TypeScript 7; la práctica del curso usa `ts-loader`. |
| Webpack | 5.109.2 | **Webpack 5.109.2** | Rama estable de Webpack 5, compatible con el enfoque de la sesión. |
| webpack-cli | 7.2.3 | **webpack-cli 7.2.3** | CLI actual para ejecutar Webpack. |
| ts-loader | 9.6.2 | **ts-loader 9.6.2** | Loader vigente de TypeScript para Webpack 5; se usará con TypeScript 6.0.3. |
| Visual Studio Code | 1.134 | **VS Code 1.134 o actualización estable equivalente** | Editor recomendado para la práctica y depuración. |

### Recursos oficiales

- Node.js: https://nodejs.org/en/download
- Estado de versiones Node.js: https://nodejs.org/en/about/previous-releases
- TypeScript: https://www.typescriptlang.org/
- Releases TypeScript: https://github.com/microsoft/TypeScript/releases
- Guía oficial Webpack + TypeScript: https://webpack.js.org/guides/typescript/
- ts-loader: https://github.com/TypeStrong/ts-loader
- Visual Studio Code: https://code.visualstudio.com/download

### ¿Por qué no usaremos TypeScript 7.0.2 en esta práctica?

TypeScript 7 es estable y representa la evolución actual del compilador. Sin embargo, esta clase enseña explícitamente el flujo **TypeScript -> ts-loader -> Webpack**. Existe una incompatibilidad reportada entre `ts-loader` y TypeScript 7. Por eso, para que todos los estudiantes reproduzcan el mismo laboratorio sin depender de soluciones experimentales, se fija **TypeScript 6.0.3** únicamente para esta sesión. El concepto académico no cambia.

---

## 4. Antes de comenzar: valida tu equipo

### 4.1 Abre una terminal

En Windows puedes usar la terminal integrada de VS Code, PowerShell o Windows Terminal.

### 4.2 Comprueba Node.js

**Dónde ejecutarlo:** en cualquier carpeta.

```bash
node -v
```

**Qué buscamos:** una salida que empiece con `v24.` si instalaste la versión recomendada.

Ejemplo:

```text
v24.x.x
```

### 4.3 Comprueba npm

```bash
npm -v
```

**Por qué:** npm instalará las dependencias locales del proyecto.

### Si `node` o `npm` no se reconoce

1. Cierra y vuelve a abrir VS Code después de instalar Node.js.
2. Vuelve a ejecutar `node -v`.
3. Si sigue fallando, comprueba que Node.js esté instalado desde el sitio oficial.
4. No continúes con `npm install` hasta que `node -v` funcione.

---

## 5. Preparación del proyecto desde cero

### 5.1 Crea la carpeta principal

**Nombre exacto recomendado:**

```text
typescript-webpack-clase
```

Puedes crearla desde el explorador de archivos o con terminal:

```bash
mkdir typescript-webpack-clase
cd typescript-webpack-clase
```

**Qué hace cada comando:**

| Parte | Significado |
|---|---|
| `mkdir` | crea una carpeta |
| `typescript-webpack-clase` | nombre de la carpeta |
| `cd` | cambia la ubicación actual de la terminal |

**Validación:** la terminal debe quedar ubicada dentro de `typescript-webpack-clase`.

### 5.2 Abre la carpeta en VS Code

Si el comando `code` está disponible:

```bash
code .
```

Si no está disponible, abre VS Code y usa **Archivo > Abrir carpeta**.

**Por qué:** a partir de este momento todos los archivos deben quedar dentro de la misma raíz.

### 5.3 Crea `package.json`

**Dónde:** terminal situada en la raíz `typescript-webpack-clase`.

```bash
npm init -y
```

**Por qué:** `package.json` registra el proyecto, sus dependencias y scripts.

**Resultado esperado:** aparece el archivo:

```text
package.json
```

**Comprueba:** en el Explorador de VS Code debes verlo al mismo nivel que la carpeta `src` que crearás después.

### 5.4 Instala las dependencias del laboratorio

**Dónde:** raíz del proyecto.

```bash
npm install --save-dev typescript webpack webpack-cli ts-loader
```

### ¿Qué significa?

| Parte | Significado |
|---|---|
| `npm install` | instala paquetes en el proyecto |
| `--save-dev` | los registra como dependencias de desarrollo |
| `typescript` | compilador fijado para compatibilidad del laboratorio |
| `webpack` | empaquetador del proyecto |
| `webpack-cli` | interfaz de línea de comandos para Webpack |
| `ts-loader` | puente entre TypeScript y Webpack |

**Resultado esperado:** se crean `node_modules/` y `package-lock.json`, y `package.json` incorpora `devDependencies`.

**Error frecuente:** ejecutar el comando en otra carpeta.  
**Cómo detectarlo:** `package.json` no aparece donde esperabas.  
**Corrección:** usa `cd` para volver a la raíz correcta antes de instalar.

### 5.5 Verifica las versiones instaladas localmente

```bash
npx tsc -v
```

Esperado:

```text
Version 6.0.3
```

Luego:

```bash
npx webpack -v
```

La salida debe identificar Webpack y webpack-cli instalados localmente.

**Por qué usamos `npx`:** fuerza el uso de la herramienta instalada dentro del proyecto, evitando depender de instalaciones globales distintas entre estudiantes.

---

## 6. Crea la estructura de carpetas y archivos

En VS Code crea exactamente esta estructura:

```text
typescript-webpack-clase/
|
+-- package.json
+-- package-lock.json
+-- tsconfig.json
+-- webpack.config.cjs
+-- index.html
+-- src/
|   +-- index.ts
|   +-- components/
|   |   +-- Header.ts
|   |   +-- Footer.ts
|   |   +-- Button.ts
|   |   +-- ListComponent.ts
|   |   +-- DataList.ts
|   +-- models/
|   |   +-- User.ts
|   +-- services/
|   |   +-- ApiService.ts
|   +-- utils/
|       +-- math.ts
+-- dist/                 # Webpack la creará al compilar
```

### Cómo crear un archivo en VS Code

1. En el panel **Explorador**, selecciona la carpeta correcta.
2. Pulsa el icono **Nuevo archivo**.
3. Escribe el nombre exacto, incluida la extensión.
4. Presiona Enter.
5. Antes de pegar código, verifica en la ruta superior del editor que estás en el archivo correcto.

> Esta comprobación evita un error muy común: pegar una clase en `index.ts` cuando debía ir en `components/`, o crear `webpack.config.cjs` dentro de `src/`.

---

## 7. Configura TypeScript: archivo `tsconfig.json`

### 7.1 ¿Qué es?

`tsconfig.json` le indica al compilador cómo interpretar el código TypeScript del proyecto.

### 7.2 ¿Dónde se crea?

En la raíz:

```text
typescript-webpack-clase/tsconfig.json
```

No debe ir dentro de `src/`.

### 7.3 Escribe este contenido

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "strict": true,
    "sourceMap": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules", "dist"]
}
```

### 7.4 ¿Por qué cada opción?

| Opción | Función en esta práctica |
|---|---|
| `target: ES2022` | genera JavaScript moderno compatible con navegadores actuales |
| `module: ESNext` | conserva módulos para que Webpack pueda analizarlos |
| `moduleResolution: Bundler` | resuelve imports pensando en un bundler moderno |
| `strict: true` | activa validaciones estrictas de tipos |
| `sourceMap: true` | genera información para depurar TypeScript desde el bundle |
| `esModuleInterop: true` | mejora compatibilidad con ciertos módulos JavaScript |
| `forceConsistentCasingInFileNames` | detecta diferencias de mayúsculas/minúsculas en rutas |
| `skipLibCheck: true` | evita revisar tipos internos de dependencias durante este laboratorio |
| `include` | limita el código propio a `src/**/*.ts` |
| `exclude` | evita analizar `node_modules` y `dist` |

### 7.5 Validación inicial

Ejecuta:

```bash
npx tsc --noEmit
```

Al inicio, si `src/index.ts` está vacío, no debería aparecer un error relevante.

**Por qué `--noEmit`:** queremos usar `tsc` aquí solo como verificador de tipos; la salida final la generará Webpack.

---

## 8. Configura Webpack: archivo `webpack.config.cjs`

### 8.1 ¿Por qué usamos `.cjs`?

El material original usa `webpack.config.js` con `require`. En proyectos modernos puede existir configuración ESM en `package.json`; usar `.cjs` deja explícito que este archivo de configuración utiliza CommonJS y evita ambigüedad en clase.

### 8.2 ¿Dónde se crea?

```text
typescript-webpack-clase/webpack.config.cjs
```

### 8.3 Código completo

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  mode: "development",
  devtool: "source-map",
  module: {
    rules: [
      {
        test: /\.ts$/,
        use: "ts-loader",
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: [".ts", ".js"],
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
};
```

### 8.4 Lee el flujo, no memorices la sintaxis

```text
src/index.ts
    |
    v
regla test: /\.ts$/
    |
    v
ts-loader
    |
    v
TypeScript 6.0.3
    |
    v
Webpack
    |
    v
dist/bundle.js
```

### 8.5 Validación de la configuración

```bash
npx webpack configtest --config webpack.config.cjs
npx webpack configtest webpack.config.cjs
```

**Resultado esperado:** un mensaje indicando que la configuración es válida.

**Si falla con “Cannot find module ts-loader”:** revisa que instalaste dependencias en la misma carpeta que contiene `package.json`.

---

## 9. Crea la página `index.html`

### 9.1 ¿Dónde?

En la raíz:

```text
typescript-webpack-clase/index.html
```

### 9.2 Contenido

```html
<!doctype html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>TypeScript + Webpack</title>
</head>
<body>
  <main id="app"></main>
  <script src="./dist/bundle.js" defer></script>
</body>
</html>
```

### ¿Por qué existe este archivo?

Webpack crea JavaScript, pero necesitamos una página HTML que lo cargue. En esta sesión no agregaremos plugins adicionales; el foco es TypeScript, componentes, servicios, TSConfig y Webpack.

---

## 10. Ejemplo 1. Primer componente con una clase `Header`
### 1. ¿Qué aprenderemos con este ejemplo?

Crear una unidad reutilizable con estado (`title`) y comportamiento (`render`).
### 2. Problema o situación

La página necesita un encabezado. En lugar de escribir HTML directamente en `index.ts`, encapsularemos esa responsabilidad en una clase.
### 3. Antes de comenzar

Debes tener creados `src/components/Header.ts` y `src/index.ts`, además de la configuración de TypeScript y Webpack.
### 4. Paso 1 - crea la clase Header

**Archivo:** `src/components/Header.ts`  
**Qué hacemos:** define una clase exportable que reciba un título  
**Por qué lo hacemos:** separar la responsabilidad de construir el encabezado

```typescript
export class Header {
  constructor(public title: string) {}

  render(): string {
    return `<h1>${this.title}</h1>`;
  }
}
```

**Qué significa lo importante:** `public title: string` crea y tipa la propiedad; `render(): string` promete devolver texto HTML.

**Resultado esperado:** el archivo compila, pero aún no se verá nada porque todavía no lo importamos.

**Cómo comprobarlo:** ejecuta `npx tsc --noEmit`; no debe haber errores.

### 5. Paso 2 - usa el componente desde el punto de entrada

**Archivo:** `src/index.ts`  
**Qué hacemos:** importa Header, localiza `#app` e inserta el resultado de `render()`  
**Por qué lo hacemos:** `index.ts` coordina los módulos, mientras Header conserva su propia responsabilidad

```typescript
import { Header } from "./components/Header";

const app = document.querySelector<HTMLElement>("#app");

if (!app) {
  throw new Error("No se encontró el elemento #app");
}

const header = new Header("Programación Web Avanzada");
app.innerHTML = header.render();
```

**Qué significa lo importante:** `querySelector<HTMLElement>` tipa el elemento; la comprobación `if (!app)` evita usar un valor `null`.

**Resultado esperado:** al compilar y abrir `index.html`, aparece el título “Programación Web Avanzada”.

**Cómo comprobarlo:** ejecuta `npx webpack --config webpack.config.cjs` y abre `index.html`.

### Flujo completo

```text
Header.ts --export--> index.ts --Webpack--> bundle.js --navegador--> <h1>
```

### Modificación con los estudiantes

Cambia el título a `TypeScript en componentes` y vuelve a compilar. Luego cambia el tipo del constructor a `number` solo para observar cómo TypeScript detecta la llamada incorrecta antes de ejecutar; después restaura `string`.

**Antes de ejecutar, predice:** ¿qué cambiará y qué debería permanecer igual?

### Error frecuente o caso alternativo

Si escribes `new Header(2026)` con el constructor tipado como `string`, `npx tsc --noEmit` debe marcar incompatibilidad de tipos. Corrige usando texto o restableciendo el tipo esperado.

### Preguntas de comprobación

1. ¿Qué responsabilidad tiene `Header.ts`?
2. ¿Por qué `index.ts` necesita importar la clase?
3. ¿Qué ventaja aporta tipar `title` como `string`?

### Idea clave

Un componente no es solo HTML: agrupa una responsabilidad concreta y ofrece una interfaz de uso clara.

### Conexión con el siguiente ejemplo

Ahora definiremos un contrato común para componentes mediante una interfaz.

---

## 11. Ejemplo 2. Interfaces y componentes `Footer` y `Button`
### 1. ¿Qué aprenderemos con este ejemplo?

Usar una interfaz como contrato y observar encapsulación mediante una propiedad privada.
### 2. Problema o situación

Queremos que varios componentes puedan garantizar que implementan un método `render()`.
### 3. Antes de comenzar

Crea `src/components/Footer.ts` y `src/components/Button.ts`. Mantén `Header.ts` del ejemplo anterior.
### 4. Paso 1 - define el contrato y Footer

**Archivo:** `src/components/Footer.ts`  
**Qué hacemos:** declara `Component` y haz que Footer lo implemente  
**Por qué lo hacemos:** la interfaz garantiza una estructura mínima común

```typescript
export interface Component {
  render(): string;
}

export class Footer implements Component {
  render(): string {
    return "<footer>Aprende a tu manera en ISIL</footer>";
  }
}
```

**Qué significa lo importante:** `implements Component` obliga a Footer a ofrecer `render(): string`.

**Resultado esperado:** Footer satisface el contrato.

**Cómo comprobarlo:** elimina temporalmente `render()` y ejecuta `npx tsc --noEmit`; observa el error y luego restaura el método.

### 5. Paso 2 - crea Button con encapsulación

**Archivo:** `src/components/Button.ts`  
**Qué hacemos:** define una etiqueta privada y métodos `onClick` y `render`  
**Por qué lo hacemos:** demostrar que el estado interno puede quedar encapsulado

```typescript
export class Button {
  constructor(private label: string) {}

  onClick(): void {
    console.log(`Presionado: ${this.label}`);
  }

  render(): string {
    return `<button id="saveBtn">${this.label}</button>`;
  }
}
```

**Qué significa lo importante:** `private label` solo se usa dentro de la clase; `void` indica que `onClick` no retorna un valor.

**Resultado esperado:** la clase puede renderizar un botón y registrar una acción.

**Cómo comprobarlo:** ejecuta la comprobación de tipos.

### 6. Paso 3 - integra ambos en index.ts

**Archivo:** `src/index.ts`  
**Qué hacemos:** importa Footer y Button y agrega su HTML  
**Por qué lo hacemos:** demostrar composición de unidades reutilizables

```typescript
import { Header } from "./components/Header";
import { Footer } from "./components/Footer";
import { Button } from "./components/Button";

const app = document.querySelector<HTMLElement>("#app");
if (!app) throw new Error("No se encontró #app");

const header = new Header("Programación Web Avanzada");
const button = new Button("Guardar");
const footer = new Footer();

app.innerHTML = `
  ${header.render()}
  ${button.render()}
  ${footer.render()}
`;

document.querySelector("#saveBtn")?.addEventListener("click", () => {
  button.onClick();
});
```

**Qué significa lo importante:** el operador `?.` ejecuta `addEventListener` solo si el botón existe.

**Resultado esperado:** la página muestra encabezado, botón y pie; al pulsar Guardar aparece un mensaje en consola.

**Cómo comprobarlo:** compila, abre DevTools > Console y pulsa el botón.

### Flujo completo

```text
interfaces/clases -> render() -> index.ts -> DOM -> evento click -> onClick()
```

### Modificación con los estudiantes

Cambia el texto del botón a `Enviar` y predice qué partes visuales y de consola cambiarán. Después agrega otro botón con otra etiqueta reutilizando la misma clase.

**Antes de ejecutar, predice:** ¿qué cambiará y qué debería permanecer igual?

### Error frecuente o caso alternativo

Si intentas leer `button.label` desde `index.ts`, TypeScript debe rechazarlo porque `label` es `private`. No elimines `private` para “arreglarlo”; usa un método público si realmente necesitas exponer información.

### Preguntas de comprobación

1. ¿Qué garantiza `implements Component`?
2. ¿Qué problema evita `private`?
3. ¿Por qué `onClick` retorna `void`?

### Idea clave

Las interfaces definen contratos; las clases implementan comportamiento y pueden ocultar estado interno.

### Conexión con el siguiente ejemplo

El siguiente paso será reutilizar una misma estructura con diferentes tipos mediante genéricos.

---

## 12. Ejemplo 3. Componente genérico `ListComponent<T>`
### 1. ¿Qué aprenderemos con este ejemplo?

Comprender cómo `<T>` permite reutilizar una clase conservando seguridad de tipos.
### 2. Problema o situación

Necesitamos mostrar listas de frutas y números sin crear dos clases casi idénticas.
### 3. Antes de comenzar

Crea `src/components/ListComponent.ts`.
### 4. Paso 1 - crea la clase genérica

**Archivo:** `src/components/ListComponent.ts`  
**Qué hacemos:** declara `ListComponent<T>` y almacena `T[]`  
**Por qué lo hacemos:** hacer que el tipo concreto se defina al usar la clase

```typescript
export class ListComponent<T> {
  constructor(private items: T[]) {}

  render(): string {
    return `
      <ul>
        ${this.items.map((item) => `<li>${String(item)}</li>`).join("")}
      </ul>
    `;
  }
}
```

**Qué significa lo importante:** `T` es un parámetro de tipo; `T[]` significa “arreglo de ese mismo tipo”. Se usa `String(item)` para convertir de forma explícita al contenido que irá al HTML.

**Resultado esperado:** una sola clase acepta distintas colecciones tipadas.

**Cómo comprobarlo:** `npx tsc --noEmit`.

### 5. Paso 2 - usa el genérico con dos tipos

**Archivo:** `src/index.ts`  
**Qué hacemos:** crea una lista de `string` y otra de `number`  
**Por qué lo hacemos:** comprobar que la implementación se reutiliza sin perder información de tipos

```typescript
import { ListComponent } from "./components/ListComponent";

const frutas = new ListComponent<string>(["Manzana", "Pera", "Mango"]);
const numeros = new ListComponent<number>([1, 2, 3, 4, 5]);

app.innerHTML += `
  <h2>Frutas</h2>
  ${frutas.render()}
  <h2>Números</h2>
  ${numeros.render()}
`;
```

**Qué significa lo importante:** `<string>` fija T como string; `<number>` fija T como number.

**Resultado esperado:** la misma clase genera dos listas con tipos diferentes.

**Cómo comprobarlo:** compila y verifica ambas listas en el navegador.

### Flujo completo

```text
ListComponent<T> + T[] -> instancia<string> / instancia<number> -> render común
```

### Modificación con los estudiantes

Prueba `new ListComponent<number>([1, 2, "3"])`. Antes de ejecutar, predice si el error será de compilación o de navegador. Después corrígelo.

**Antes de ejecutar, predice:** ¿qué cambiará y qué debería permanecer igual?

### Error frecuente o caso alternativo

Mezclar `string` dentro de `ListComponent<number>` produce un error de tipos. El objetivo del genérico es precisamente evitar que una estructura reusable pierda consistencia.

### Preguntas de comprobación

1. ¿Qué representa T?
2. ¿Cuándo se decide el tipo concreto?
3. ¿Qué ventaja tendría sobre usar `any[]`?

### Idea clave

Los genéricos aportan flexibilidad sin renunciar a validación estática.

### Conexión con el siguiente ejemplo

Ahora añadiremos una restricción para trabajar con objetos que obligatoriamente tengan `id` y `nombre`.

---

## 13. Ejemplo 4. Genéricos con restricción `T extends Identificable`
### 1. ¿Qué aprenderemos con este ejemplo?

Aplicar un contrato mínimo a un tipo genérico y trabajar con objetos complejos.
### 2. Problema o situación

Una lista de usuarios, productos o tareas puede variar en propiedades, pero necesitamos garantizar que cada elemento tenga `id` y `nombre`.
### 3. Antes de comenzar

Crea `src/components/DataList.ts`.
### 4. Paso 1 - define la restricción

**Archivo:** `src/components/DataList.ts`  
**Qué hacemos:** crea `Identificable` y limita T con `extends`  
**Por qué lo hacemos:** permitir tipos flexibles pero con propiedades mínimas seguras

```typescript
export interface Identificable {
  id: number;
  nombre: string;
}

export class DataList<T extends Identificable> {
  constructor(private datos: T[]) {}

  render(): string {
    return this.datos
      .map((item) => `<p>ID: ${item.id} - Nombre: ${item.nombre}</p>`)
      .join("");
  }
}
```

**Qué significa lo importante:** `T extends Identificable` no significa herencia de clase; en este contexto obliga al tipo a ser compatible con ese contrato estructural.

**Resultado esperado:** dentro de DataList, TypeScript permite usar `id` y `nombre` con seguridad.

**Cómo comprobarlo:** `npx tsc --noEmit`.

### 5. Paso 2 - crea datos complejos y renderiza

**Archivo:** `src/index.ts`  
**Qué hacemos:** construye objetos con propiedades adicionales  
**Por qué lo hacemos:** demostrar que el contrato mínimo no impide extender el objeto

```typescript
import { DataList } from "./components/DataList";

const usuarios = new DataList([
  { id: 1, nombre: "Felix", rol: "Admin" },
  { id: 2, nombre: "Yeisy", rol: "Editor" },
]);

app.innerHTML += `
  <h2>Usuarios</h2>
  ${usuarios.render()}
`;
```

**Qué significa lo importante:** TypeScript infiere un tipo que incluye `id`, `nombre` y `rol`; DataList solo exige las dos primeras.

**Resultado esperado:** la página muestra ID y nombre de cada usuario.

**Cómo comprobarlo:** compila y observa dos registros.

### Flujo completo

```text
objetos concretos -> cumplen Identificable -> DataList<T extends Identificable> -> render seguro
```

### Modificación con los estudiantes

Quita `nombre` del segundo objeto y predice el error. Luego agrégalo nuevamente. Después crea una lista de productos con `id`, `nombre` y `precio` usando la misma clase.

**Antes de ejecutar, predice:** ¿qué cambiará y qué debería permanecer igual?

### Error frecuente o caso alternativo

El material contiene comillas tipográficas alrededor de un nombre en un fragmento. En código deben usarse comillas válidas (`"` o `'`). Además, si falta `nombre`, la restricción genérica impide crear la instancia.

### Preguntas de comprobación

1. ¿Qué garantiza `extends Identificable`?
2. ¿Puede el objeto tener propiedades adicionales?
3. ¿Qué diferencia existe entre esta solución y `any`?

### Idea clave

Una restricción genérica define el mínimo necesario para operar con seguridad y conserva extensibilidad.

### Conexión con el siguiente ejemplo

A continuación usaremos genéricos en un servicio asíncrono que obtiene datos desde una API.

---

## 14. Ejemplo 5. Servicio genérico `ApiService` con `async/await`
### 1. ¿Qué aprenderemos con este ejemplo?

Separar la comunicación HTTP de la interfaz y tipar la respuesta de una API.
### 2. Problema o situación

La interfaz necesita usuarios remotos. En lugar de poner `fetch` dentro del componente, centralizaremos la petición en un servicio.
### 3. Antes de comenzar

Crea `src/services/ApiService.ts` y `src/models/User.ts`. Para ejecutar la petición necesitas conexión a Internet.
### 4. Paso 1 - crea el servicio

**Archivo:** `src/services/ApiService.ts`  
**Qué hacemos:** define un método GET genérico  
**Por qué lo hacemos:** reutilizar una sola clase para distintos tipos de respuesta

```typescript
export class ApiService {
  async get<T>(url: string): Promise<T> {
    const response = await fetch(url);

    if (!response.ok) {
      throw new Error(`Error HTTP: ${response.status}`);
    }

    return (await response.json()) as T;
  }
}
```

**Qué significa lo importante:** `get<T>` recibe el tipo esperado; `Promise<T>` expresa que el valor llegará de forma asíncrona; `response.ok` valida el estado HTTP.

**Resultado esperado:** el servicio queda listo, pero aún no se ejecuta ninguna petición.

**Cómo comprobarlo:** ejecuta la comprobación de tipos.

### 5. Paso 2 - define el modelo de datos

**Archivo:** `src/models/User.ts`  
**Qué hacemos:** crea una interfaz con las propiedades que usaremos  
**Por qué lo hacemos:** dar forma tipada a la respuesta consumida por la aplicación

```typescript
export interface User {
  id: number;
  name: string;
  email: string;
}
```

**Qué significa lo importante:** la interfaz no crea datos en runtime; describe la estructura esperada durante el desarrollo.

**Resultado esperado:** podremos solicitar `User[]` al servicio.

**Cómo comprobarlo:** no debe generar errores.

### 6. Paso 3 - consume el servicio desde index.ts

**Archivo:** `src/index.ts`  
**Qué hacemos:** crea una función asíncrona y renderiza solo `id` y `name` aunque la API entregue más campos  
**Por qué lo hacemos:** demostrar que una API puede retornar objetos completos y la interfaz decide qué propiedades utiliza

```typescript
import { ApiService } from "./services/ApiService";
import type { User } from "./models/User";

const api = new ApiService();

async function mostrarUsuarios(): Promise<void> {
  const contenedor = document.createElement("section");
  contenedor.innerHTML = "<h2>Usuarios desde API</h2><p>Cargando...</p>";
  app.appendChild(contenedor);

  try {
    const users = await api.get<User[]>(
      "https://jsonplaceholder.typicode.com/users"
    );

    contenedor.innerHTML = `
      <h2>Usuarios desde API</h2>
      ${users
        .slice(0, 3)
        .map((u) => `<p>${u.id}. ${u.name}</p>`)
        .join("")}
    `;
  } catch (error) {
    const message = error instanceof Error ? error.message : "Error desconocido";
    contenedor.innerHTML = `<p>No se pudieron cargar usuarios: ${message}</p>`;
  }
}

void mostrarUsuarios();
```

**Qué significa lo importante:** el servicio recibe todos los datos JSON, pero el render usa únicamente `id` y `name`; el tipado no obliga a mostrar todas las propiedades.

**Resultado esperado:** se muestran tres usuarios de JSONPlaceholder o un mensaje controlado si la red falla.

**Cómo comprobarlo:** abre la consola y la pestaña Network del navegador para observar la solicitud.

### Flujo completo

```text
index.ts -> ApiService.get<User[]>() -> fetch -> respuesta JSON -> User[] tipado -> render id + name
```

### Modificación con los estudiantes

Cambia `.slice(0, 3)` por `.slice(0, 5)`. Después cambia el endpoint temporalmente a una URL inexistente y observa el manejo de error; restaura la URL correcta al terminar.

**Antes de ejecutar, predice:** ¿qué cambiará y qué debería permanecer igual?

### Error frecuente o caso alternativo

Si la red institucional bloquea el dominio, no concluyas que TypeScript está mal configurado. Distingue error de compilación de error de red. El `catch` debe mostrar un mensaje sin romper toda la página.

### Preguntas de comprobación

1. ¿Qué responsabilidad tiene ApiService?
2. ¿Por qué el método es genérico?
3. ¿Por qué la API puede traer más propiedades de las que mostramos?
4. ¿Qué diferencia hay entre un error HTTP y un error de tipos?

### Idea clave

Un servicio separa la comunicación externa de la presentación y el tipo genérico documenta la forma que espera el consumidor.

### Conexión con el siguiente ejemplo

Terminaremos organizando una función en un módulo independiente y revisando source maps.

---

## 15. Ejemplo 6. Módulos, `export`/`import` y depuración con source maps
### 1. ¿Qué aprenderemos con este ejemplo?

Organizar responsabilidades en módulos y localizar el código TypeScript original durante la depuración.
### 2. Problema o situación

Queremos mover una función matemática fuera de `index.ts` y comprobar que Webpack mantiene la relación entre el bundle y los fuentes TypeScript.
### 3. Antes de comenzar

Crea `src/utils/math.ts`. Verifica que `sourceMap: true` esté en `tsconfig.json` y `devtool: "source-map"` en Webpack.
### 4. Paso 1 - crea la función exportada

**Archivo:** `src/utils/math.ts`  
**Qué hacemos:** exporta una función `suma` tipada  
**Por qué lo hacemos:** evitar duplicación y separar una utilidad reutilizable

```typescript
export function suma(a: number, b: number): number {
  return a + b;
}
```

**Qué significa lo importante:** los parámetros y el retorno son `number`; `export` permite usar la función desde otro módulo.

**Resultado esperado:** la función existe pero no se ejecuta hasta que sea importada.

**Cómo comprobarlo:** `npx tsc --noEmit`.

### 5. Paso 2 - importa con el mismo nombre

**Archivo:** `src/index.ts`  
**Qué hacemos:** importa `suma` y muestra el resultado  
**Por qué lo hacemos:** demostrar que el nombre exportado e importado deben coincidir

```typescript
import { suma } from "./utils/math";

console.log("3 + 2 =", suma(3, 2));
```

**Qué significa lo importante:** la diapositiva del material mezcla `suma` con una llamada `sum`; aquí se corrige la inconsistencia operativa conservando el concepto de módulos.

**Resultado esperado:** la consola muestra `3 + 2 = 5`.

**Cómo comprobarlo:** compila, abre la consola del navegador y verifica el valor.

### 6. Paso 3 - genera el bundle y mapas

**Archivo:** `terminal en la raíz`  
**Qué hacemos:** ejecuta Webpack  
**Por qué lo hacemos:** producir el archivo que el navegador entiende y su source map para depurar

```bash
npx webpack --config webpack.config.cjs
```

**Qué significa lo importante:** Webpack parte de `src/index.ts`, aplica `ts-loader`, resuelve imports y escribe `dist/bundle.js`.

**Resultado esperado:** aparecen `dist/bundle.js` y `dist/bundle.js.map`.

**Cómo comprobarlo:** abre `dist/` en VS Code y confirma ambos archivos.

### Flujo completo

```text
math.ts --export--> index.ts --ts-loader--> Webpack --> bundle.js + bundle.js.map --> DevTools Sources
```

### Modificación con los estudiantes

Cambia `suma(3, 2)` por `suma(10, 7)`, predice la salida y recompila. Después intenta importar `{ sum }` para provocar un error de exportación; corrige volviendo a `{ suma }`.

**Antes de ejecutar, predice:** ¿qué cambiará y qué debería permanecer igual?

### Error frecuente o caso alternativo

Usar `sum(3, 2)` cuando solo existe `suma` produce un error. También puede aparecer error si la ruta del import usa mayúsculas diferentes a la carpeta real. Corrige nombre y ruta, no desactives la comprobación.

### Preguntas de comprobación

1. ¿Qué diferencia hay entre exportar e importar?
2. ¿Qué archivos produce Webpack?
3. ¿Para qué sirve `bundle.js.map`?
4. ¿Por qué la configuración de depuración del material que apunta a `dist/index.js` no coincide con esta práctica?

### Idea clave

Los módulos organizan responsabilidades y los source maps permiten relacionar el código empaquetado con los archivos TypeScript originales.

### Conexión con el siguiente ejemplo

Con todos los bloques listos, integraremos la aplicación y validaremos el proyecto completo.

---

## 16. Scripts recomendados en `package.json`

Abre `package.json` y sustituye la sección `scripts` por:

```json
"scripts": {
  "typecheck": "tsc --noEmit",
  "build": "webpack --config webpack.config.cjs --mode development",
  "build:prod": "webpack --config webpack.config.cjs --mode production"
}
```

A partir de ahora podrás ejecutar:

```bash
npm run typecheck
npm run build
npm run build:prod
```

### ¿Por qué es mejor que recordar comandos largos?

Los scripts documentan el flujo del proyecto y todos los estudiantes ejecutan la misma orden.

---

## 17. Ejemplo integrador: validación completa

### Objetivo

Comprobar que todos los archivos trabajan como un sistema y no como ejemplos aislados.

### Paso 1 - Comprueba tipos

```bash
npm run typecheck
```

**Esperado:** termina sin errores.

### Paso 2 - Construye el proyecto

```bash
npm run build
```

**Esperado:** Webpack informa que compiló correctamente y `dist/bundle.js` tiene fecha reciente.

### Paso 3 - Abre `index.html`

Debes observar, según los ejemplos que mantuviste activos:

- encabezado;
- botón;
- pie;
- listas genéricas;
- usuarios complejos;
- usuarios obtenidos desde API, si existe conectividad.

### Paso 4 - Abre DevTools

1. Presiona F12 o abre las herramientas de desarrollador.
2. Revisa **Console**.
3. Pulsa el botón Guardar.
4. Debes ver el mensaje del componente `Button`.
5. Revisa **Sources**; con source maps activos el navegador puede mostrar fuentes TypeScript relacionados con el bundle.

### Paso 5 - Haz una modificación controlada

Cambia un título o una colección. Antes de compilar escribe tu predicción en una línea:

```text
Predicción: ______________________________________________
```

Luego ejecuta `npm run build`, recarga y compara.

---

## 18. Tabla de errores frecuentes

| Síntoma | Causa probable | Cómo comprobar | Corrección |
|---|---|---|---|
| `node` no se reconoce | Node no instalado o terminal antigua | `node -v` | instala Node LTS y reinicia terminal |
| `Cannot find module 'ts-loader'` | dependencias no instaladas en esa raíz | revisa `node_modules` y `package.json` | ejecuta `npm install` en la raíz correcta |
| `Module not found` | ruta de import incorrecta | compara carpeta y mayúsculas | corrige ruta relativa |
| no aparece `dist/bundle.js` | Webpack no ejecutó o falló | lee la salida de terminal | corrige el primer error reportado y vuelve a compilar |
| TypeScript marca tipo incompatible | dato no cumple contrato | ejecuta `npm run typecheck` | corrige el dato o la definición real, no uses `any` para ocultarlo |
| navegador sigue mostrando versión anterior | bundle no recompilado o caché | revisa hora de `bundle.js` | `npm run build` y recarga |
| API no carga | red, CORS o endpoint | pestaña Network | distingue red de compilación; prueba conectividad |
| importas `sum` pero exportaste `suma` | nombre inconsistente | revisa `math.ts` | usa el mismo identificador |
| launch apunta a `dist/index.js` | configuración no coincide con Webpack | revisa `output.filename` | depura el bundle/browser; en esta práctica la salida es `dist/bundle.js` |
| TypeScript 7 falla con ts-loader | incompatibilidad actual del loader | revisa versiones | usa TypeScript 6.0.3 en este laboratorio |

---

## 19. Actividades espejo

### Actividad A - Producto genérico

Crea un arreglo con objetos que contengan:

```text
id
nombre
precio
```

Usa `DataList` sin modificar su contrato mínimo. Explica por qué `precio` puede existir aunque `DataList` no lo use.

### Actividad B - Botón reutilizable

Crea un segundo `Button` con etiqueta `Cancelar`. Haz que su clic invoque `onClick()` sobre su propia instancia.

### Actividad C - Servicio tipado

Define una interfaz `Post` con `id` y `title` y utiliza el mismo `ApiService` para consultar:

```text
https://jsonplaceholder.typicode.com/posts?_limit=3
```

Muestra únicamente `id` y `title`.

### Actividad D - Error controlado

En `ListComponent<number>` agrega temporalmente un string. Antes de ejecutar responde:

1. ¿Fallará `typecheck`, Webpack o el navegador?
2. ¿Por qué TypeScript puede detectarlo antes?
3. ¿Cuál es la corrección correcta?

Restaura el código válido al finalizar.

---

## 20. Evidencias sugeridas

Conserva como evidencia:

1. captura de la estructura de carpetas;
2. salida de `npm run typecheck` sin errores;
3. salida de `npm run build` correcta;
4. captura del navegador con componentes renderizados;
5. captura de consola del botón;
6. fragmento de un componente genérico;
7. evidencia de una modificación y su resultado.

---

## 21. Checklist final

- [ ] Sé identificar la raíz del proyecto.
- [ ] Sé qué función tiene `package.json`.
- [ ] Sé por qué usamos TypeScript 6.0.3 en este laboratorio.
- [ ] Puedo explicar `strict` y `sourceMap`.
- [ ] Sé qué hace `entry` en Webpack.
- [ ] Sé qué función cumple `ts-loader`.
- [ ] Puedo explicar por qué `bundle.js` aparece en `dist/`.
- [ ] Puedo crear una clase con propiedades tipadas.
- [ ] Puedo usar una interfaz como contrato.
- [ ] Puedo explicar `<T>`.
- [ ] Puedo explicar `T extends Identificable`.
- [ ] Puedo consumir un servicio con `async/await`.
- [ ] Puedo diferenciar error de tipo, error de ruta y error de red.
- [ ] Puedo importar y exportar módulos.
- [ ] Puedo ejecutar `npm run typecheck` y `npm run build`.

---

## 22. Mapa final de la sesión

```text
TYPESCRIPT
  |
  +--> tipado estricto
  +--> clases e interfaces
  +--> genéricos
  +--> servicios async
  +--> módulos
          |
          v
       TSCONFIG
          |
          v
       TS-LOADER
          |
          v
       WEBPACK
          |
          v
     dist/bundle.js
          |
          v
      NAVEGADOR
          |
          v
      SOURCE MAPS
```

## 23. Cierre

En esta sesión no solo escribiste sintaxis TypeScript. Construiste un flujo completo de proyecto: configuraste el compilador, organizaste componentes y servicios, aplicaste genéricos, empaquetaste módulos con Webpack y verificaste la salida en el navegador.

**Debes repasar especialmente:**

- diferencia entre una interfaz y una clase;
- propósito de los genéricos;
- separación componente/servicio;
- responsabilidad de `tsconfig.json` frente a `webpack.config.cjs`;
- interpretación de errores antes de intentar “corregirlos” con `any`.

**Conexión con la siguiente sesión:** estos fundamentos permiten integrar TypeScript de forma más natural en frameworks modernos, manteniendo contratos de datos, componentes y servicios.

**Blog:** https://lideratecacademy.com/  
**Canal YouTube:** https://www.youtube.com/@LideratecAcademy
