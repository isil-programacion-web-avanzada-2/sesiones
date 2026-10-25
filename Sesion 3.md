---
course_id: ISIL-PWA
session_id: S03
module_id: TEMA-03
course_version: 2026
source_origin: PPT
status: corrected
---

# Guía del Estudiante - Clase laboratorio S03
## Introducción a Angular: CLI, componentes, data binding, directivas, servicios e inyección de dependencias

**Lideratec Academy / Programación Web Avanzada**

> **Cómo usar esta guía:** aquí construirás la aplicación desde cero. No necesitas abrir FASE 15 para saber qué código escribir. FASE 15 es un recurso opcional: sirve para validar tu resultado o para comenzar con el proyecto ya preparado si el docente decide ahorrar tiempo de digitación.

# 1. Propósito de la sesión

Durante la práctica construirás una pequeña aplicación de catálogo en Angular. Empezarás verificando el entorno y crearás el proyecto con Angular CLI. Después escribirás componentes, conectarás propiedades TypeScript con la plantilla, responderás a eventos, usarás control de flujo, aplicarás directivas de atributo y separarás los datos en un servicio inyectable. El cierre integra esas piezas en un catálogo con filtro de productos activos.

El objetivo no es copiar bloques terminados. En cada ejemplo escribirás una parte pequeña, comprenderás por qué existe, predecirás el resultado, ejecutarás y comprobarás qué cambió.

# 2. Resultado observable

## Lectura sugerida del docente

Al finalizar esta sesión podrás crear y levantar un proyecto Angular moderno, reconocer la responsabilidad de un componente standalone y construir una interfaz a partir de varios componentes pequeños. También podrás explicar cómo fluye un dato desde TypeScript hacia el HTML mediante interpolación y property binding, y cómo una acción del usuario regresa al componente mediante event binding. Utilizarás `@if` y `@for` para controlar qué se renderiza, aplicarás `NgClass` y `NgStyle` para modificar la presentación a partir del estado y separarás los datos en un servicio que Angular entrega por inyección de dependencias.

La evidencia de dominio no será solamente que la página “funcione”. Deberás poder señalar qué archivo contiene cada responsabilidad, explicar qué línea cambia el estado, predecir qué ocurrirá antes de ejecutar y justificar por qué la interfaz cambia después de una acción.

## Desempeños observables

- Crear y ejecutar un workspace Angular.
- Generar y componer componentes standalone.
- Renderizar propiedades mediante interpolación.
- Aplicar property binding y event binding.
- Utilizar `@if` y `@for` con `track`.
- Aplicar `NgClass` y `NgStyle`.
- Crear e inyectar un servicio.
- Integrar las técnicas anteriores en un catálogo funcional.

## Criterio de dominio

Dominas la sesión cuando puedes reconstruir el flujo principal sin copiar el proyecto listo, explicar los bindings y la inyección de dependencias, modificar al menos una condición y comprobar el resultado de forma razonada.

# 3. Antes de iniciar

**Debes saber:** TypeScript básico: variables, arreglos, objetos, clases y métodos.

**Debes tener disponible:** Node.js compatible con Angular 20, npm, Visual Studio Code o un editor equivalente y una terminal.

**No necesitas:** descargar FASE 15, una API externa, una base de datos, routing ni formularios reactivos.

# 4. Herramientas y recursos

## Node.js y npm
Node.js ejecuta Angular CLI y las herramientas de compilación. npm instala las dependencias declaradas por el proyecto.

Verifica:

```bash
node -v
npm -v
```

## Angular CLI 20.3
Para garantizar que el proyecto de esta guía use la misma línea del laboratorio preparado, utilizaremos Angular CLI 20.3.0 mediante `npx`. No es obligatorio tener esa versión instalada globalmente.

## Visual Studio Code
Se utilizará para abrir la carpeta del proyecto, crear archivos y escribir TypeScript, HTML y SCSS.

# 5. Ruta de trabajo

```text
VERIFICAR NODE Y NPM
        ↓
CREAR PROYECTO ANGULAR
        ↓
EJ02 COMPONENTE
        ↓
EJ03 INTERPOLACIÓN
        ↓
EJ04 BINDING
        ↓
EJ05 @if / @for
        ↓
EJ06 NgClass / NgStyle
        ↓
EJ07 SERVICIO + DI
        ↓
EJ08 INTEGRADOR
        ↓
TAREAS ESPEJO
```

# 6. Preparación y verificación del entorno

## Ruta A - el equipo ya está preparado

1. Abre una terminal.
2. Ejecuta `node -v` y confirma que Node responde.
3. Ejecuta `npm -v` y confirma que npm responde.
4. Crea una carpeta de trabajo para la sesión.
5. Continúa con EJ01.

## Ruta B - el equipo todavía no está preparado

1. Instala una versión de Node.js compatible con Angular 20 siguiendo la distribución institucional o el sitio oficial.
2. Cierra y vuelve a abrir la terminal.
3. Ejecuta `node -v` y `npm -v`.
4. Si alguno no responde, corrige primero la instalación o la variable PATH.
5. Cuando ambos comandos funcionen, continúa con EJ01.

# 7. Desarrollo del laboratorio mediante ejemplos resueltos

**Regla de trabajo:** no abras FASE 15 durante el desarrollo normal. Escribe lo indicado aquí. Si al final necesitas validar, el proyecto listo de FASE 15 contiene estos mismos archivos fuente.


# EJ01 - Crear y ejecutar el workspace Angular

## Qué vamos a resolver
Partir de una carpeta sin proyecto y obtener una aplicación Angular que compile y se ejecute en el navegador.

## Paso 1 - verificar herramientas
En la terminal escribe:

```bash
node -v
npm -v
```

**Qué significa:** el primer comando comprueba el runtime; el segundo, el gestor de paquetes. Si alguno falla, todavía no conviene crear el proyecto.

## Paso 2 - crear el proyecto
Ubícate en la carpeta donde guardarás la práctica y escribe:

```bash
npx @angular/cli@20.3.0 new angular-s03-lab --standalone --style=scss --routing=false --ssr=false --skip-tests --package-manager=npm --defaults
```

### Lectura pedagógica del comando
- `npx @angular/cli@20.3.0`: usa Angular CLI 20.3.0 para esta creación.
- `new angular-s03-lab`: crea un nuevo workspace y una aplicación con ese nombre.
- `--standalone`: usa componentes standalone.
- `--style=scss`: configura SCSS.
- `--routing=false`: no agrega routing porque no es objetivo de esta sesión.
- `--ssr=false`: no agrega renderizado del lado servidor.
- `--skip-tests`: evita archivos de prueba para reducir ruido en la práctica.
- `--package-manager=npm`: utiliza npm.
- `--defaults`: evita preguntas interactivas que ya resolvimos con las banderas.

## Paso 3 - entrar al proyecto

```bash
cd angular-s03-lab
```

Ahora abre esa carpeta en Visual Studio Code. Si tienes el comando `code` habilitado:

```bash
code .
```

## Paso 4 - normalizar el punto de entrada
Abre `src/main.ts`, selecciona su contenido y escribe exactamente:

```ts
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent).catch(err => console.error(err));
```

### Por qué está escrito así
`bootstrapApplication(AppComponent)` le indica a Angular qué componente inicia la aplicación. `catch(...)` permite mostrar en consola un error de arranque si ocurre.

## Paso 5 - escribir los estilos globales de la práctica
Abre `src/styles.scss` y reemplaza su contenido por:

```scss
body {
  font-family: Arial, Helvetica, sans-serif;
  margin: 0;
  background: #f4f7fb;
  color: #162033;
}

main {
  max-width: 1100px;
  margin: auto;
  padding: 1.5rem;
}

.lab-section {
  background: white;
  margin: 1rem 0;
  padding: 1rem 1.25rem;
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.06);
}

button {
  padding: 0.55rem 0.9rem;
  border: 0;
  border-radius: 8px;
  cursor: pointer;
}

button:disabled {
  cursor: not-allowed;
  opacity: 0.55;
}

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 1rem;
}

.card {
  border: 1px solid #d9e2ef;
  border-radius: 10px;
  padding: 1rem;
}

.destacado {
  border-width: 2px;
}

.agotado {
  opacity: 0.6;
}

.tag {
  font-size: 0.8rem;
  padding: 0.15rem 0.45rem;
  border-radius: 999px;
  background: #e9eef7;
}
```

### Qué debes comprender
Estos estilos no contienen lógica Angular. Preparan una apariencia legible para los ejemplos. Algunas clases (`grid`, `card`, `destacado`, `agotado`) se usarán más adelante.

## Paso 6 - iniciar la aplicación

```bash
npm start
```

Si el script `start` no estuviera disponible en un proyecto creado de forma distinta, el equivalente es:

```bash
npx ng serve
```

## Antes de ejecutar: predicción
¿Qué proceso permanecerá activo: `node -v` o `npm start`? ¿Por qué?

## Resultado esperado
La terminal muestra una URL local y el navegador carga la aplicación.

## Error controlado
Detén el servidor con `Ctrl+C` e intenta recargar la página. El código sigue en disco, pero el servidor de desarrollo ya no está atendiendo la URL.

## T01 - tarea espejo
Crea otro workspace llamado `angular-s03-prueba` con las mismas decisiones técnicas y valida que pueda levantarse con `ng serve` o `npm start`.

## Criterio de validación
Puedes explicar qué creó `ng new`, entrar a la carpeta y levantar la aplicación sin usar un proyecto descargado.


# EJ02 - Crear y componer un componente standalone

## Qué vamos a resolver
Crear un encabezado reutilizable y conectarlo con el componente raíz.

## Paso 1 - generar el componente
Con el servidor detenido o desde otra terminal ubicada en `angular-s03-lab`, escribe:

```bash
npx ng generate component components/header --standalone --skip-tests --style=scss
```

Angular crea la carpeta `src/app/components/header`.

## Paso 2 - escribir la clase del componente
Abre `src/app/components/header/header.ts`.

Primero deja la importación:

```ts
import { Component } from '@angular/core';
```

**Qué significa:** `Component` es el decorador que convierte una clase TypeScript en una pieza administrada por Angular.

Ahora escribe el decorador:

```ts
@Component({
  selector: 'app-header',
  standalone: true,
  templateUrl: './header.html',
  styleUrl: './header.scss'
})
```

### Explicación línea por línea
- `selector`: nombre de la etiqueta que otro template utilizará para insertar el componente.
- `standalone: true`: el componente declara sus dependencias sin depender de un `NgModule`.
- `templateUrl`: indica dónde está su HTML.
- `styleUrl`: indica dónde están sus estilos.

Finalmente escribe:

```ts
export class Header {}
```

El archivo completo debe quedar así:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-header',
  standalone: true,
  templateUrl: './header.html',
  styleUrl: './header.scss'
})
export class Header {}
```

## Paso 3 - escribir el HTML
Abre `header.component.html` y escribe:

```html
<header class="header">
  <h1>Angular - Sesión 03</h1>
  <p>Componentes, binding, directivas y servicios</p>
</header>
```

**Qué hace:** este HTML pertenece exclusivamente al encabezado. Todavía es estático; la interpolación se trabajará en EJ03.

## Paso 4 - escribir el SCSS
Abre `header.component.scss` y escribe:

```scss
.header {
  padding: 1.25rem;
  border-radius: 12px;
  background: #13213c;
  color: white;
}

.header p {
  margin-bottom: 0;
}
```

## Paso 5 - importar el componente en AppComponent
Abre `src/app/app.component.ts`. Para este momento de la práctica escribe:

```ts
import { Component } from '@angular/core';
import { Header } from './components/header/header';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    Header
  ],
  templateUrl: './app.html'
})
export class App {}
```

### Lectura pedagógica
El import de TypeScript hace disponible la clase en el archivo. El arreglo `imports` de `@Component` hace disponible el componente dentro de la plantilla de `AppComponent`. Son dos responsabilidades distintas.

## Paso 6 - usar el selector
Abre `src/app/app.html` y escribe:

```html
<main>
  <app-header />
</main>
```

`<app-header />` coincide con `selector: 'app-header'`.

## Antes de ejecutar: predicción
¿Qué pasaría si mantienes `<app-header />` pero quitas `Header` del arreglo `imports`?

## Ejecuta

```bash
npm start
```

Debes ver el encabezado.

## Variante
Cambia temporalmente `Angular - Sesión 03` por `Mi primer componente` y observa el hot reload. Luego restaura el texto original.

## Error controlado
Quita `Header` de `imports`, guarda y observa el error. Después restáuralo.

## T02 - tarea espejo
Crea el archivo `src/app/tareas/footer/footer.ts` y parte de este código:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-footer',
  standalone: true,
  template: `<!-- TODO T02: agrega el texto solicitado -->`
})
export class FooterComponent {}
```

Completa el template para mostrar `Programación Web Avanzada - Sesión 03`, impórtalo temporalmente en `AppComponent` y usa `<app-footer />` al final.

## Criterio de validación
El footer aparece por su propio selector y el contenido pertenece al `FooterComponent`, no al componente raíz.


# EJ03 - Interpolación: TypeScript hacia HTML

## Qué vamos a resolver
Guardar datos en la clase y renderizarlos en la plantilla con `{{ ... }}`.

## Paso 1 - generar el componente

```bash
npx ng generate component components/interpolacion --standalone --skip-tests --style=none
```

## Paso 2 - escribir la clase
Abre `interpolacion.ts`.

Escribe primero la estructura del componente:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-interpolacion',
  standalone: true,
  templateUrl: './interpolacion.html'
})
export class Interpolacion {
}
```

Ahora, **dentro de la clase**, agrega una propiedad a la vez:

```ts
titulo = 'Catálogo académico';
```

`título` representa un dato del estado del componente.

Luego:

```ts
descripcion = 'Datos renderizados desde TypeScript';
```

Y finalmente:

```ts
totalProductos = 3;
```

El archivo completo queda:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-interpolacion',
  standalone: true,
  templateUrl: './interpolacion.html'
})
export class Interpolacion {
  titulo = 'Catálogo académico';
  descripcion = 'Datos renderizados desde TypeScript';
  totalProductos = 3;
}
```

## Paso 3 - escribir la plantilla
Abre `interpolacion.html` y escribe la primera expresión:

```html
<h2>{{ titulo }}</h2>
```

Angular evalúa `titulo` en la instancia del componente y coloca su valor como texto.

Agrega:

```html
<p>{{ descripcion }}</p>
```

Y después:

```html
<p>Total inicial: {{ totalProductos }}</p>
```

El archivo completo queda:

```html
<section>
  <h2>{{ titulo }}</h2>
  <p>{{ descripcion }}</p>
  <p>Total inicial: {{ totalProductos }}</p>
</section>
```

## Paso 4 - integrar EJ03 en la raíz
Reemplaza `app.component.ts` por el estado acumulado:

```ts
import { Component } from '@angular/core';
import { Header } from './components/header/header';
import { Interpolacion } from './components/interpolacion/interpolacion';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    Header,
    Interpolacion
  ],
  templateUrl: './app.html'
})
export class App {}
```

Y `app.html` por:

```html
<main>
  <app-header />

  <section class="lab-section">
    <h2>EJ03 - Interpolación</h2>
    <app-interpolacion />
  </section>
</main>
```

## Mapa mental
```text
propiedad TypeScript
        ↓
{ expresión }
        ↓
texto renderizado en HTML
```

## Antes de ejecutar: predicción
Si cambias `totalProductos = 3` por `totalProductos = 8`, ¿qué archivo HTML necesitas modificar? Respuesta esperada: ninguno.

## Ejecuta y comprueba
Guarda. Debes ver título, descripción y total.

## Variante
Cambia `totalProductos` a `8`, observa el resultado y vuelve a `3`.

## Error controlado
Escribe temporalmente `{{ totalProducto }}` en el HTML. Observa que la plantilla intenta acceder a una propiedad que no existe. Corrige el nombre.

## T03 - tarea espejo
Crea `src/app/tareas/interpolacion/t03-interpolacion.component.ts` con:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-t03-interpolacion',
  standalone: true,
  template: `<p><!-- TODO T03: usa interpolación --></p>`
})
export class T03InterpolacionComponent {
  /* TODO: declara subtitulo, docente y sesion */
}
```

Declara `subtitulo`, `docente` y `sesion`, y muestra los tres con interpolación.

## Criterio de validación
Los valores están definidos en TypeScript y el template solo los renderiza.


# EJ04 - Property binding + event binding

## Qué vamos a resolver
Enviar estado desde TypeScript hacia propiedades del DOM y responder a un clic cambiando ese estado.

## Paso 1 - generar el componente

```bash
npx ng generate component components/binding --standalone --skip-tests --style=none
```

## Paso 2 - escribir el estado inicial
Abre `binding.ts` y escribe la estructura:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-binding',
  standalone: true,
  templateUrl: './binding.html'
})
export class Binding {
}
```

Dentro de la clase escribe:

```ts
productoNombre = 'Laptop';
seleccionado = false;
```

### Por qué existen estas variables
- `productoNombre` será el valor que verá el `<input>`.
- `seleccionado` representa el estado de la interacción. Empieza en `false` porque el usuario todavía no ha hecho clic.

## Paso 3 - escribir el método que cambia el estado
Debajo de las propiedades agrega:

```ts
seleccionar(): void {
  this.seleccionado = true;
}
```

### Lectura pedagógica del código
`seleccionar()` no busca el botón con JavaScript ni cambia el DOM manualmente. Solo modifica `seleccionado`. Angular detecta el nuevo estado y vuelve a evaluar los bindings de la plantilla.

El TypeScript completo queda:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-binding',
  standalone: true,
  templateUrl: './binding.html'
})
export class Binding {
  productoNombre = 'Laptop';
  seleccionado = false;

  seleccionar(): void {
    this.seleccionado = true;
  }
}
```

## Paso 4 - escribir el input con property binding
Abre `binding.component.html` y comienza:

```html
<section>
  <input [value]="productoNombre" readonly>
</section>
```

Los corchetes en `[value]` indican que Angular debe evaluar `productoNombre` y asignar el resultado a la propiedad `value` del elemento.

## Paso 5 - agregar el botón y el evento
Dentro de `<section>` agrega:

```html
<button [disabled]="seleccionado" (click)="seleccionar()">
  Seleccionar
</button>
```

### Explicación
- `[disabled]="seleccionado"`: cuando `seleccionado` sea `true`, el botón quedará deshabilitado.
- `(click)="seleccionar()"`: cuando el usuario haga clic, Angular llama al método.

## Paso 6 - mostrar un resultado condicionado
Agrega:

```html
@if (seleccionado) {
  <p>Producto seleccionado: {{ productoNombre }}</p>
}
```

El HTML completo queda:

```html
<section>
  <input [value]="productoNombre" readonly>
  <button [disabled]="seleccionado" (click)="seleccionar()">
    Seleccionar
  </button>

  @if (seleccionado) {
    <p>Producto seleccionado: {{ productoNombre }}</p>
  }
</section>
```

## Mapa mental del flujo
```text
seleccionado = false
        ↓
botón habilitado
        ↓
usuario hace clic
        ↓
(click) ejecuta seleccionar()
        ↓
seleccionado = true
        ↓
[disabled] deshabilita + @if muestra mensaje
```

## Paso 7 - integrar EJ04
`app.component.ts`:

```ts
import { Component } from '@angular/core';
import { HeaderComponent } from './ejemplos/ej02-header/header.component';
import { InterpolacionComponent } from './ejemplos/ej03-interpolacion/interpolacion.component';
import { BindingComponent } from './ejemplos/ej04-binding/binding.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    HeaderComponent,
    InterpolacionComponent,
    BindingComponent
  ],
  templateUrl: './app.component.html'
})
export class AppComponent {}
```

`app.component.html`:

```html
<main>
  <app-header />

  <section class="lab-section">
    <h2>EJ03 - Interpolación</h2>
    <app-interpolacion />
  </section>

  <section class="lab-section">
    <h2>EJ04 - Binding</h2>
    <app-binding />
  </section>
</main>
```

## Antes de ejecutar: predicción
Después del primer clic, ¿podrás hacer clic otra vez inmediatamente? Justifica con el valor de `seleccionado`.

## Ejecuta y compara
Pulsa **Seleccionar**. El mensaje aparece y el botón queda deshabilitado.

## Variante
Cambia temporalmente `seleccionado = true` y recarga. Observa que el estado inicial ya muestra el resultado y el botón nace deshabilitado. Luego restaura `false`.

## Error controlado
Cambia `[disabled]="seleccionado"` por `disabled="seleccionado"`. Explica por qué un atributo literal no representa el mismo binding booleano. Luego restaura los corchetes.

## T04 - tarea espejo
Crea `src/app/tareas/T04-binding/t04-binding.component.ts` con:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-t04-binding',
  standalone: true,
  template: `<button>Activar producto</button><p>Estado pendiente</p>`
})
export class T04BindingComponent {
  activo = false;
  /* TODO: implementar activar() y bindings en template */
}
```

Implementa `activar()` y modifica el template para que el clic cambie `activo` a `true`, muestre el estado y deshabilite el botón.

## Criterio de validación
Puedes señalar la línea que recibe el evento, la línea que cambia el estado y el binding que refleja ese cambio.


# EJ05 - Control flow moderno con `@if` y `@for`

## Qué vamos a resolver
Renderizar una lista de productos y controlar qué ocurre si la lista está vacía.

## Paso 1 - crear el modelo Producto
En el explorador de VS Code crea la carpeta:

```text
src/app/modelos
```

Dentro crea `producto.model.ts` y escribe primero:

```ts
export interface Producto {
}
```

`interface` describe la forma que debe tener un producto. Ahora agrega las propiedades una por una:

```ts
id: number;
nombre: string;
precio: number;
activo: boolean;
stock: number;
```

El archivo completo debe quedar:

```ts
export interface Producto {
  id: number;
  nombre: string;
  precio: number;
  activo: boolean;
  stock: number;
}
```

### Qué aporta el modelo
Si un objeto declarado como `Producto` omite una propiedad o usa un tipo incompatible, TypeScript puede advertirlo durante el desarrollo.

## Paso 2 - generar el componente

```bash
npx ng generate component ejemplos/ej05-control-flow --standalone --skip-tests --style=none
```

## Paso 3 - importar el modelo
En `control-flow.component.ts`, después de `Component`, escribe:

```ts
import { Producto } from '../../modelos/producto.model';
```

## Paso 4 - declarar el arreglo
Dentro de la clase escribe:

```ts
productos: Producto[] = [
  { id: 1, nombre: 'Laptop', precio: 3200, activo: true, stock: 5 },
  { id: 2, nombre: 'Monitor', precio: 980, activo: true, stock: 3 },
  { id: 3, nombre: 'Teclado', precio: 180, activo: false, stock: 0 }
];
```

El TypeScript completo queda:

```ts
import { Component } from '@angular/core';
import { Producto } from '../../modelos/producto.model';

@Component({
  selector: 'app-control-flow',
  standalone: true,
  templateUrl: './control-flow.component.html'
})
export class ControlFlowComponent {
  productos: Producto[] = [
    { id: 1, nombre: 'Laptop', precio: 3200, activo: true, stock: 5 },
    { id: 2, nombre: 'Monitor', precio: 980, activo: true, stock: 3 },
    { id: 3, nombre: 'Teclado', precio: 180, activo: false, stock: 0 }
  ];
}
```

## Paso 5 - escribir `@if`
En `control-flow.component.html` escribe:

```html
<section>
  @if (productos.length > 0) {
    <p>Hay productos</p>
  } @else {
    <p>No hay productos.</p>
  }
</section>
```

### Por qué se usa
`@if` decide qué bloque entra al DOM según la condición `productos.length > 0`.

## Paso 6 - reemplazar el texto por `@for`
Dentro del bloque verdadero sustituye `<p>Hay productos</p>` por:

```html
<ul>
  @for (producto of productos; track producto.id) {
    <li>
      {{ producto.nombre }} - S/ {{ producto.precio }} -
      {{ producto.activo ? 'Activo' : 'Inactivo' }}
    </li>
  }
</ul>
```

### Lectura pedagógica
- `producto of productos`: toma un elemento en cada iteración.
- `track producto.id`: Angular usa una identidad estable para relacionar los elementos renderizados con los datos.
- `producto.nombre`, `producto.precio` y `producto.activo`: leen el elemento actual.

El HTML completo queda:

```html
<section>
  @if (productos.length > 0) {
    <ul>
      @for (producto of productos; track producto.id) {
        <li>
          {{ producto.nombre }} - S/ {{ producto.precio }} -
          {{ producto.activo ? 'Activo' : 'Inactivo' }}
        </li>
      }
    </ul>
  } @else {
    <p>No hay productos.</p>
  }
</section>
```

## Paso 7 - integrar EJ05
`app.component.ts`:

```ts
import { Component } from '@angular/core';
import { HeaderComponent } from './ejemplos/ej02-header/header.component';
import { InterpolacionComponent } from './ejemplos/ej03-interpolacion/interpolacion.component';
import { BindingComponent } from './ejemplos/ej04-binding/binding.component';
import { ControlFlowComponent } from './ejemplos/ej05-control-flow/control-flow.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    HeaderComponent,
    InterpolacionComponent,
    BindingComponent,
    ControlFlowComponent
  ],
  templateUrl: './app.component.html'
})
export class AppComponent {}
```

`app.component.html`:

```html
<main>
  <app-header />

  <section class="lab-section">
    <h2>EJ03 - Interpolación</h2>
    <app-interpolacion />
  </section>

  <section class="lab-section">
    <h2>EJ04 - Binding</h2>
    <app-binding />
  </section>

  <section class="lab-section">
    <h2>EJ05 - Control flow</h2>
    <app-control-flow />
  </section>
</main>
```

## Traza manual antes de ejecutar
Primera iteración: `producto` apunta al objeto con `id: 1`; se renderiza `Laptop - S/ 3200 - Activo`. Segunda: `id: 2`; tercera: `id: 3`.

## Antes de ejecutar: predicción
¿Cuántos `<li>` aparecerán y qué texto tendrá el tercero?

## Variante
Cambia temporalmente `productos` por `[]`. Debe entrar el bloque `@else`. Después restaura los tres objetos.

## Error controlado
Escribe temporalmente `track producto.nombre` y cambia dos nombres para que sean iguales. Explica por qué `id` es una identidad más apropiada. Restaura `track producto.id`.

## T05 - tarea espejo
Crea `src/app/tareas/T05-control-flow/t05-control-flow.component.ts` con:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-t05-control-flow',
  standalone: true,
  template: `<!-- TODO T05: @if + @for con track curso.id -->`
})
export class T05ControlFlowComponent {
  cursos = [
    { id: 1, nombre: 'Angular', activo: true },
    { id: 2, nombre: 'TypeScript', activo: true }
  ];
}
```

Implementa `@if` y `@for` usando `track curso.id`. Incluye un mensaje para el caso vacío.

## Criterio de validación
La lista renderiza dos cursos; si vacías el arreglo, aparece `No hay cursos`.


# EJ06 - Directivas de atributo: `NgClass` y `NgStyle`

## Qué vamos a resolver
Cambiar clases y estilos a partir del estado de un producto sin modificar manualmente el DOM.

## Paso 1 - generar el componente

```bash
npx ng generate component ejemplos/ej06-directivas --standalone --skip-tests --style=none
```

## Paso 2 - importar las directivas y el modelo
En `directivas.component.ts` escribe las importaciones:

```ts
import { Component } from '@angular/core';
import { NgClass, NgStyle } from '@angular/common';
import { Producto } from '../../modelos/producto.model';
```

`NgClass` y `NgStyle` vienen de `@angular/common`.

## Paso 3 - declarar las dependencias del componente
En el decorador escribe:

```ts
@Component({
  selector: 'app-directivas',
  standalone: true,
  imports: [NgClass, NgStyle],
  templateUrl: './directivas.component.html'
})
```

### Por qué `imports` es necesario
El template usa dos directivas. Como el componente es standalone, declara directamente esas dependencias.

## Paso 4 - declarar el producto
Dentro de la clase escribe:

```ts
producto: Producto = {
  id: 3,
  nombre: 'Teclado',
  precio: 180,
  activo: false,
  stock: 0
};
```

El TypeScript completo queda:

```ts
import { Component } from '@angular/core';
import { NgClass, NgStyle } from '@angular/common';
import { Producto } from '../../modelos/producto.model';

@Component({
  selector: 'app-directivas',
  standalone: true,
  imports: [NgClass, NgStyle],
  templateUrl: './directivas.component.html'
})
export class DirectivasComponent {
  producto: Producto = {
    id: 3,
    nombre: 'Teclado',
    precio: 180,
    activo: false,
    stock: 0
  };
}
```

## Paso 5 - escribir la tarjeta base
En `directivas.component.html` comienza:

```html
<article class="card">
  <h3>{{ producto.nombre }}</h3>
  <p>Stock: {{ producto.stock }}</p>
</article>
```

## Paso 6 - agregar `NgClass`
Modifica la apertura de `<article>`:

```html
<article
  class="card"
  [ngClass]="{
    destacado: producto.activo,
    agotado: producto.stock === 0
  }"
>
```

### Cómo leer el objeto
- Si `producto.activo` es `true`, se agrega la clase `destacado`.
- Si `producto.stock === 0`, se agrega `agotado`.

## Paso 7 - agregar `NgStyle`
A la misma etiqueta agrega:

```html
[ngStyle]="{
  opacity: producto.stock === 0 ? 0.5 : 1
}"
```

El HTML completo queda:

```html
<article
  class="card"
  [ngClass]="{
    destacado: producto.activo,
    agotado: producto.stock === 0
  }"
  [ngStyle]="{
    opacity: producto.stock === 0 ? 0.5 : 1
  }"
>
  <h3>{{ producto.nombre }}</h3>
  <p>Stock: {{ producto.stock }}</p>
</article>
```

## Paso 8 - integrar EJ06
`app.component.ts`:

```ts
import { Component } from '@angular/core';
import { HeaderComponent } from './ejemplos/ej02-header/header.component';
import { InterpolacionComponent } from './ejemplos/ej03-interpolacion/interpolacion.component';
import { BindingComponent } from './ejemplos/ej04-binding/binding.component';
import { ControlFlowComponent } from './ejemplos/ej05-control-flow/control-flow.component';
import { DirectivasComponent } from './ejemplos/ej06-directivas/directivas.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    HeaderComponent,
    InterpolacionComponent,
    BindingComponent,
    ControlFlowComponent,
    DirectivasComponent
  ],
  templateUrl: './app.component.html'
})
export class AppComponent {}
```

`app.component.html`:

```html
<main>
  <app-header />

  <section class="lab-section">
    <h2>EJ03 - Interpolación</h2>
    <app-interpolacion />
  </section>

  <section class="lab-section">
    <h2>EJ04 - Binding</h2>
    <app-binding />
  </section>

  <section class="lab-section">
    <h2>EJ05 - Control flow</h2>
    <app-control-flow />
  </section>

  <section class="lab-section">
    <h2>EJ06 - Directivas</h2>
    <app-directivas />
  </section>
</main>
```

## Antes de ejecutar: predicción
Con `activo: false` y `stock: 0`, ¿qué clase dinámica se agregará y qué opacidad se aplicará?

## Ejecuta y comprueba
La tarjeta debe verse con el estado de agotado y opacidad reducida.

## Variante
Cambia temporalmente `stock: 4` y `activo: true`. Predice qué clases quedan activas y qué opacidad tendrá. Luego restaura los valores originales.

## Error controlado
Quita temporalmente `NgClass` de `imports` pero conserva `[ngClass]` en la plantilla. Observa el error y restáuralo.

## T06 - tarea espejo
Crea `src/app/tareas/T06-directivas/t06-directivas.component.ts` con:

```ts
import { Component } from '@angular/core';
import { NgClass, NgStyle } from '@angular/common';

@Component({
  selector: 'app-t06-directivas',
  standalone: true,
  imports: [NgClass, NgStyle],
  template: `<article class="card">Producto de tarea - stock {{ stock }}</article>`
})
export class T06DirectivasComponent {
  stock = 0;
  /* TODO: aplicar NgClass y NgStyle */
}
```

Aplica `NgClass` para agregar `agotado` cuando `stock === 0` y `NgStyle` para modificar el color en ese estado.

## Criterio de validación
El estilo cambia por evaluación del estado, no porque hayas escrito manualmente una segunda versión del HTML.


# EJ07 - Servicio e inyección de dependencias

## Qué vamos a resolver
Mover los datos fuera del componente y pedirlos a través de un servicio inyectable.

## Paso 1 - generar el servicio
En la terminal escribe:

```bash
npx ng generate service servicios/producto --skip-tests
```

Abre `src/app/servicios/producto.service.ts`.

## Paso 2 - escribir las importaciones

```ts
import { Injectable } from '@angular/core';
import { Producto } from '../modelos/producto.model';
```

`Injectable` permite que la clase participe en el sistema de inyección de dependencias.

## Paso 3 - registrar el servicio
Escribe:

```ts
@Injectable({ providedIn: 'root' })
export class ProductoService {
}
```

### Por qué `providedIn: 'root'`
Angular puede crear una instancia disponible desde el inyector raíz. Los componentes solicitan la dependencia; no necesitan ejecutar `new ProductoService()`.

## Paso 4 - mover los datos al servicio
Dentro de la clase agrega:

```ts
private readonly productos: Producto[] = [
  { id: 1, nombre: 'Laptop', precio: 3200, activo: true, stock: 5 },
  { id: 2, nombre: 'Monitor', precio: 980, activo: true, stock: 3 },
  { id: 3, nombre: 'Teclado', precio: 180, activo: false, stock: 0 }
];
```

### Lectura pedagógica
- `private`: el arreglo no forma parte de la API pública del servicio.
- `readonly`: la referencia de la propiedad no se reasigna.
- `Producto[]`: mantiene el contrato de tipos.

## Paso 5 - escribir el método público
Agrega:

```ts
obtenerProductos(): Producto[] {
  return this.productos.map(producto => ({ ...producto }));
}
```

El `map` crea nuevos objetos con spread. Para esta práctica evita entregar directamente las mismas referencias internas del arreglo.

## Paso 6 - dejar preparado el punto de extensión de T07
Escribe el comentario final:

```ts
// TODO T07: agrega obtenerActivos(): Producto[] sin modificar obtenerProductos().
```

El servicio completo utilizado en el ejemplo queda:

```ts
import { Injectable } from '@angular/core';
import { Producto } from '../modelos/producto.model';

@Injectable({ providedIn: 'root' })
export class ProductoService {
  private readonly productos: Producto[] = [
    { id: 1, nombre: 'Laptop', precio: 3200, activo: true, stock: 5 },
    { id: 2, nombre: 'Monitor', precio: 980, activo: true, stock: 3 },
    { id: 3, nombre: 'Teclado', precio: 180, activo: false, stock: 0 }
  ];

  obtenerProductos(): Producto[] {
    return this.productos.map(producto => ({ ...producto }));
  }

  // TODO T07: agrega obtenerActivos(): Producto[] sin modificar obtenerProductos().
}
```

## Paso 7 - generar el componente consumidor

```bash
npx ng generate component ejemplos/ej07-servicio/lista-productos --standalone --skip-tests --style=none
```

## Paso 8 - escribir las importaciones del componente
En `lista-productos.component.ts` escribe:

```ts
import { Component, OnInit } from '@angular/core';
import { Producto } from '../../modelos/producto.model';
import { ProductoService } from '../../servicios/producto.service';
```

## Paso 9 - declarar el estado local
Dentro de la clase escribe:

```ts
productos: Producto[] = [];
```

El componente comienza sin datos.

## Paso 10 - pedir el servicio en el constructor
Agrega:

```ts
constructor(private productoService: ProductoService) {}
```

### Qué ocurre conceptualmente
El componente declara la dependencia. Angular consulta su inyector y entrega una instancia de `ProductoService`.

## Paso 11 - cargar los datos en `ngOnInit`
La clase implementa `OnInit` y agrega:

```ts
ngOnInit(): void {
  this.productos = this.productoService.obtenerProductos();
}
```

El archivo completo queda:

```ts
import { Component, OnInit } from '@angular/core';
import { Producto } from '../../modelos/producto.model';
import { ProductoService } from '../../servicios/producto.service';

@Component({
  selector: 'app-lista-productos',
  standalone: true,
  templateUrl: './lista-productos.component.html'
})
export class ListaProductosComponent implements OnInit {
  productos: Producto[] = [];

  constructor(private productoService: ProductoService) {}

  ngOnInit(): void {
    this.productos = this.productoService.obtenerProductos();
  }
}
```

## Paso 12 - escribir la plantilla
En `lista-productos.component.html` escribe:

```html
<section>
  <ul>
    @for (producto of productos; track producto.id) {
      <li>{{ producto.nombre }} - stock {{ producto.stock }}</li>
    }
  </ul>
</section>
```

## Mapa mental de DI
```text
ProductoService registrado en root
        ↓
constructor solicita ProductoService
        ↓
Injector entrega la instancia
        ↓
ngOnInit llama obtenerProductos()
        ↓
productos[] recibe los datos
        ↓
@for renderiza
```

## Paso 13 - integrar EJ07
`app.component.ts`:

```ts
import { Component } from '@angular/core';
import { HeaderComponent } from './ejemplos/ej02-header/header.component';
import { InterpolacionComponent } from './ejemplos/ej03-interpolacion/interpolacion.component';
import { BindingComponent } from './ejemplos/ej04-binding/binding.component';
import { ControlFlowComponent } from './ejemplos/ej05-control-flow/control-flow.component';
import { DirectivasComponent } from './ejemplos/ej06-directivas/directivas.component';
import { ListaProductosComponent } from './ejemplos/ej07-servicio/lista-productos.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    HeaderComponent,
    InterpolacionComponent,
    BindingComponent,
    ControlFlowComponent,
    DirectivasComponent,
    ListaProductosComponent
  ],
  templateUrl: './app.component.html'
})
export class AppComponent {}
```

`app.component.html`:

```html
<main>
  <app-header />

  <section class="lab-section">
    <h2>EJ03 - Interpolación</h2>
    <app-interpolacion />
  </section>

  <section class="lab-section">
    <h2>EJ04 - Binding</h2>
    <app-binding />
  </section>

  <section class="lab-section">
    <h2>EJ05 - Control flow</h2>
    <app-control-flow />
  </section>

  <section class="lab-section">
    <h2>EJ06 - Directivas</h2>
    <app-directivas />
  </section>

  <section class="lab-section">
    <h2>EJ07 - Servicio + DI</h2>
    <app-lista-productos />
  </section>
</main>
```

## Antes de ejecutar: predicción
Si quitas la línea `this.productos = this.productoService.obtenerProductos();`, ¿cuántos elementos renderizará `@for`?

## Ejecuta y compara
Debes ver tres productos con sus stocks.

## Variante
Agrega temporalmente un cuarto producto al arreglo privado del servicio. No cambies el componente. Comprueba que la lista se actualiza.

## Error controlado
Comenta temporalmente `@Injectable({ providedIn: 'root' })` y observa el efecto al intentar resolver la dependencia. Restáuralo.

## T07 - tarea espejo
Primero amplía el servicio con `obtenerActivos(): Producto[]`; luego crea `src/app/tareas/T07-servicio/t07-servicio.component.ts` con:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-t07-servicio',
  standalone: true,
  template: `<!-- TODO T07: inyectar servicio y listar activos -->`
})
export class T07ServicioComponent {}
```

Inyecta el servicio y muestra solo productos activos.

## Criterio de validación
`obtenerProductos()` sigue devolviendo todos; `obtenerActivos()` devuelve solo activos; el componente T07 no hardcodea su propio arreglo.


# EJ08 - Integrador: catálogo con filtro

## Qué vamos a resolver
Combinar servicio, DI, estado, event binding, interpolación, `@if`, `@for` y `NgClass` en una sola funcionalidad.

## Paso 1 - generar el componente

```bash
npx ng generate component ejemplos/ej08-integrador/catalogo --standalone --skip-tests --style=none
```

## Paso 2 - escribir las importaciones
En `catalogo.component.ts` escribe:

```ts
import { Component } from '@angular/core';
import { NgClass } from '@angular/common';
import { Producto } from '../../modelos/producto.model';
import { ProductoService } from '../../servicios/producto.service';
```

## Paso 3 - declarar las dependencias del componente

```ts
@Component({
  selector: 'app-catalogo',
  standalone: true,
  imports: [NgClass],
  templateUrl: './catalogo.component.html'
})
```

## Paso 4 - declarar el estado
Dentro de la clase escribe:

```ts
productos: Producto[];
mostrarSoloActivos = false;
```

`productos` contendrá los datos; `mostrarSoloActivos` representa la decisión actual del usuario.

## Paso 5 - inyectar y cargar datos
Escribe:

```ts
constructor(private productoService: ProductoService) {
  this.productos = this.productoService.obtenerProductos();
}
```

## Paso 6 - calcular la lista visible
Agrega el getter:

```ts
get productosVisibles(): Producto[] {
  return this.mostrarSoloActivos
    ? this.productos.filter(producto => producto.activo)
    : this.productos;
}
```

### Lectura pedagógica
No creamos un segundo arreglo permanente. Cada vez que la plantilla consulta `productosVisibles`, el getter devuelve todos o solo los activos según el estado.

## Paso 7 - escribir el método de alternancia

```ts
alternarFiltro(): void {
  this.mostrarSoloActivos = !this.mostrarSoloActivos;
}
```

El operador `!` invierte `false → true` y `true → false`.

El TypeScript completo queda:

```ts
import { Component } from '@angular/core';
import { NgClass } from '@angular/common';
import { Producto } from '../../modelos/producto.model';
import { ProductoService } from '../../servicios/producto.service';

@Component({
  selector: 'app-catalogo',
  standalone: true,
  imports: [NgClass],
  templateUrl: './catalogo.component.html'
})
export class CatalogoComponent {
  productos: Producto[];
  mostrarSoloActivos = false;

  constructor(private productoService: ProductoService) {
    this.productos = this.productoService.obtenerProductos();
  }

  get productosVisibles(): Producto[] {
    return this.mostrarSoloActivos
      ? this.productos.filter(producto => producto.activo)
      : this.productos;
  }

  alternarFiltro(): void {
    this.mostrarSoloActivos = !this.mostrarSoloActivos;
  }
}
```

## Paso 8 - escribir el botón
En `catalogo.component.html` comienza:

```html
<section>
  <button (click)="alternarFiltro()">
    {{ mostrarSoloActivos ? 'Mostrar todos' : 'Solo activos' }}
  </button>
</section>
```

El texto del botón también depende del estado.

## Paso 9 - mostrar el total visible
Debajo del botón agrega:

```html
<p>Mostrando {{ productosVisibles.length }} producto(s).</p>
```

## Paso 10 - controlar el caso vacío
Agrega:

```html
@if (productosVisibles.length > 0) {
  <!-- aquí irá la cuadrícula -->
} @else {
  <p>No hay productos para mostrar.</p>
}
```

## Paso 11 - renderizar la cuadrícula
Dentro del bloque verdadero escribe:

```html
<div class="grid">
  @for (producto of productosVisibles; track producto.id) {
    <article
      class="card"
      [ngClass]="{
        destacado: producto.activo,
        agotado: producto.stock === 0
      }"
    >
      <h3>{{ producto.nombre }}</h3>
      <p>S/ {{ producto.precio }}</p>
      <p>Stock: {{ producto.stock }}</p>
      <span class="tag">
        {{ producto.activo ? 'Activo' : 'Inactivo' }}
      </span>
    </article>
  }
</div>
```

El HTML completo queda:

```html
<section>
  <button (click)="alternarFiltro()">
    {{ mostrarSoloActivos ? 'Mostrar todos' : 'Solo activos' }}
  </button>

  <p>Mostrando {{ productosVisibles.length }} producto(s).</p>

  @if (productosVisibles.length > 0) {
    <div class="grid">
      @for (producto of productosVisibles; track producto.id) {
        <article
          class="card"
          [ngClass]="{
            destacado: producto.activo,
            agotado: producto.stock === 0
          }"
        >
          <h3>{{ producto.nombre }}</h3>
          <p>S/ {{ producto.precio }}</p>
          <p>Stock: {{ producto.stock }}</p>
          <span class="tag">
            {{ producto.activo ? 'Activo' : 'Inactivo' }}
          </span>
        </article>
      }
    </div>
  } @else {
    <p>No hay productos para mostrar.</p>
  }
</section>
```

## Mapa mental del integrador
```text
ProductoService
   ↓ obtenerProductos()
productos
   ↓
mostrarSoloActivos
   ↓
productosVisibles
   ↓
@if / @for
   ↓
NgClass + interpolación
   ↓
catálogo visible
```

## Paso 12 - dejar AppComponent en su estado final
Ahora `src/app/app.component.ts` debe quedar **exactamente**:

```ts
import { Component } from '@angular/core';
import { HeaderComponent } from './ejemplos/ej02-header/header.component';
import { InterpolacionComponent } from './ejemplos/ej03-interpolacion/interpolacion.component';
import { BindingComponent } from './ejemplos/ej04-binding/binding.component';
import { ControlFlowComponent } from './ejemplos/ej05-control-flow/control-flow.component';
import { DirectivasComponent } from './ejemplos/ej06-directivas/directivas.component';
import { ListaProductosComponent } from './ejemplos/ej07-servicio/lista-productos.component';
import { CatalogoComponent } from './ejemplos/ej08-integrador/catalogo.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    HeaderComponent,
    InterpolacionComponent,
    BindingComponent,
    ControlFlowComponent,
    DirectivasComponent,
    ListaProductosComponent,
    CatalogoComponent
  ],
  templateUrl: './app.component.html'
})
export class AppComponent {}
```

Y `src/app/app.component.html` debe quedar **exactamente**:

```html
<main>
  <app-header />

  <section class="lab-section">
    <h2>EJ03 - Interpolación</h2>
    <app-interpolacion />
  </section>

  <section class="lab-section">
    <h2>EJ04 - Binding</h2>
    <app-binding />
  </section>

  <section class="lab-section">
    <h2>EJ05 - Control flow</h2>
    <app-control-flow />
  </section>

  <section class="lab-section">
    <h2>EJ06 - Directivas</h2>
    <app-directivas />
  </section>

  <section class="lab-section">
    <h2>EJ07 - Servicio + DI</h2>
    <app-lista-productos />
  </section>

  <section class="lab-section">
    <h2>EJ08 - Integrador</h2>
    <app-catalogo />
  </section>
</main>
```

Estos dos archivos ya son el mismo estado final del proyecto preparado en FASE 15.

## Antes de ejecutar: predicción
Al iniciar, `mostrarSoloActivos` es `false`. ¿Cuántos productos se verán? Después de un clic, ¿cuántos?

## Ejecuta y compara
1. Deben verse 3 productos inicialmente.
2. Pulsa **Solo activos**.
3. Deben quedar 2 productos.
4. El texto del botón cambia a **Mostrar todos**.
5. Pulsa nuevamente y deben regresar los 3.

## Variante
Cambia temporalmente los tres productos del servicio a `activo: false`. Activa el filtro. Debe aparecer `No hay productos para mostrar.` Luego restaura los datos originales.

## Error controlado
Quita temporalmente `NgClass` del arreglo `imports` de `CatalogoComponent` y conserva `[ngClass]`. Observa el error y corrige restaurando la dependencia.

## T08 - tarea espejo
Crea `src/app/tareas/T08-resumen/t08-resumen.component.ts` con:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-t08-resumen',
  standalone: true,
  template: `<!-- TODO T08: totales + @if -->`
})
export class T08ResumenCatalogoComponent {}
```

Inyecta `ProductoService`, calcula total y cantidad de activos y muestra ambos valores. Usa `@if` para informar si todos están activos.

## Criterio de validación
El resumen obtiene datos desde el servicio y no repite manualmente el arreglo de productos.


# 8. Tareas espejo - resumen

| Tarea | Relación | Qué debes producir |
|---|---|---|
| T01 | EJ01 | Workspace Angular independiente y ejecutable |
| T02 | EJ02 | Footer standalone compuesto en AppComponent |
| T03 | EJ03 | Tres propiedades TypeScript renderizadas por interpolación |
| T04 | EJ04 | Estado booleano actualizado por clic y reflejado por binding |
| T05 | EJ05 | Lista con `@if`, `@for` y `track` |
| T06 | EJ06 | Clases y estilos dinámicos según stock |
| T07 | EJ07 | Método `obtenerActivos()` + componente consumidor |
| T08 | EJ08 | Resumen del catálogo obtenido mediante servicio |

# 9. Validación final de la aplicación

Con `npm start` activo comprueba:

1. El header aparece.
2. EJ03 muestra tres valores por interpolación.
3. EJ04 cambia el estado con un clic.
4. EJ05 renderiza tres productos.
5. EJ06 aplica estado visual al producto agotado.
6. EJ07 obtiene datos mediante `ProductoService`.
7. EJ08 alterna entre todos los productos y solo los activos.

Si quieres una segunda evidencia, ejecuta:

```bash
npm run build
```

Una compilación correcta demuestra que Angular pudo procesar el proyecto para build.

# 10. FASE 15 como recurso opcional

FASE 15 **no es un prerrequisito de esta guía**. Puedes utilizarla de dos maneras:

- **Modo validación:** después de terminar, compara tus archivos con el proyecto preparado.
- **Modo arranque rápido:** si el docente desea concentrarse en la explicación y no en la digitación, abre directamente el proyecto listo de FASE 15.

La lógica y los archivos fuente de los ejemplos EJ02-EJ08 son los mismos; la diferencia pedagógica es que aquí los construiste y comprendiste antes de ejecutarlos.

# 11. Cierre de la sesión

Hoy no solo levantaste Angular: construiste la aplicación por capas de responsabilidad. Empezaste con un workspace vacío, creaste un componente y luego moviste datos desde TypeScript hacia la vista mediante interpolación. Después viste que el binding no consiste en “manipular HTML”, sino en declarar relaciones entre estado y propiedades de la interfaz. Con `@if` y `@for` controlaste qué se renderiza; con `NgClass` y `NgStyle` modificaste la presentación a partir de condiciones; y finalmente separaste los datos en un servicio que Angular entrega a los componentes mediante inyección de dependencias.

Antes de la siguiente sesión, intenta explicar sin mirar la guía tres recorridos: **propiedad → interpolación**, **clic → método → cambio de estado → vista**, y **servicio → inyección → componente → template**. Si puedes reconstruirlos y señalar los archivos involucrados, ya no estás copiando Angular: estás comprendiendo su flujo.

Para reforzar lo trabajado puedes revisar los materiales de Lideratec Academy:

- Blog: https://lideratecacademy.com/blog/
- Canal: https://www.youtube.com/@LideratecAcademy
