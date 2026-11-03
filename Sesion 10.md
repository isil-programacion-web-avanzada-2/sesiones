---
course_id: ISIL-PROGRAMACION-WEB-AVANZADA
session_id: S10
source_origin: PPT
status: validated
---

# Sesión 10 · Guía del estudiante
## Manejo de estado global con Context API y Redux

**Lideratec Academy · Programación Web Avanzada · ISIL · Duración presencial: 120 minutos**

## 1. Propósito de la sesión

Cuando una aplicación React crece, varios componentes necesitan compartir información. Al comenzar, es natural pasar datos de un componente padre a otro mediante props, pero una cadena larga puede obligar a transportar datos por lugares donde nunca se utilizan. En esta sesión vamos a reconocer ese problema construyendo primero una cadena pequeña de componentes y luego la reemplazaremos por un Provider y consumidores de Context API. El cambio se apreciará en ejemplos observables: el estado de un tema claro u oscuro y la selección de idioma de una interfaz.

Después estudiaremos la idea básica de Redux sin configuraciones avanzadas. Antes de conectar la librería, escribiremos un reducer y simularemos acciones desde la terminal para comprender por qué el estado se modifica de manera predecible. Finalmente conectaremos el store con un componente React mediante `useSelector` y `useDispatch`. El objetivo no es memorizar la estructura de archivos: es ser capaz de explicar dónde vive el estado, quién puede leerlo, qué operación lo modifica y cómo comprobar que la interfaz muestra el resultado esperado.

## 2. Resultado de aprendizaje observable

Al finalizar, podrás demostrar cómo una aplicación pasa de transferir datos manualmente entre componentes a compartirlos a través de un contexto. Construirás Providers que administran estados de tema e idioma y consumidores que leen esos valores sin props intermedias. También interpretarás la secuencia *acción → reducer → nuevo estado* y conectarás un contador elemental a un store de Redux. Por ejemplo, al seleccionar English, el saludo deberá cambiar y el botón elegido quedará inactivo; al pulsar `+`, el contador Redux deberá aumentar exactamente en una unidad. Para demostrar dominio, explicarás por qué ocurre cada cambio, no solo mostrarás una pantalla que funciona.

**Desempeños evaluables:** reconocer prop drilling, crear contextos, diferenciar Provider y consumer, mantener un estado compartido, producir cambios desde eventos, razonar un reducer puro, interpretar dispatch y validar el comportamiento de una UI conectada a Redux.

## 3. Organización de la clase (120 minutos)

| Minutos | Trabajo |
|---:|---|
| 00–20 | Objetivo, conceptos y creación/validación del proyecto React |
| 20–32 | EJ01: prop drilling y evidencia de su limitación |
| 32–48 | EJ02: contexto y dos consumidores |
| 48–68 | EJ03: Provider de tema y actualizaciones con useState |
| 68–90 | EJ04: Provider de idioma, botones y estado compartido |
| 90–103 | EJ05: reducer y acciones desde la terminal |
| 103–113 | EJ06: store y conexión de React-Redux |
| 113–120 | Cierre y comprobación del aprendizaje |

Las tareas T01–T06 son **práctica espejo autónoma**: pueden comenzarse en los intervalos de clase y finalizarse fuera del aula. No se presupone que seis tareas completas quepan en los 120 minutos.

## 4. Conceptos que necesitas comprender antes de comenzar

**Props** son datos que un componente React recibe de su padre. **State** es información que puede cambiar durante la vida de un componente; `useState` permite guardarla y actualizarla. **Prop drilling** ocurre cuando debemos pasar una prop a través de componentes que no la necesitan, únicamente para llegar a otro descendiente. **Context API** es una funcionalidad de React que permite que un componente descendiente lea valores de un Provider cercano mediante `useContext`. No convierte automáticamente cualquier variable en estado global: la accesibilidad depende del lugar del Provider dentro del árbol de componentes.

**Provider** es quien pone a disposición un `value` para su árbol descendiente; **consumer** es el componente que lee ese valor. En este laboratorio utilizaremos el hook `useContext()` como forma de consumo. **Redux** es una librería externa que administra un estado centralizado mediante un store; una **action** describe qué ocurrió, un **reducer** calcula el siguiente estado y **dispatch** entrega la acción al store. **useSelector** y **useDispatch** son hooks de *React-Redux*, una librería que conecta el store con la interfaz React.

**Vite** es la herramienta que crea el proyecto y ofrece un servidor de desarrollo rápido; no es una alternativa a React. **JSX** es la sintaxis que permite describir elementos de interfaz dentro de JavaScript, como `<button>Guardar</button>`; Vite transforma ese código para el navegador. Usaremos archivos `.jsx` porque contienen JSX y `.mjs` para el reducer ejecutable desde Node.js, que no necesita JSX.

## 5. Preparar el entorno con comprobaciones visibles

**Necesitas:** Node.js compatible con Vite, npm, Visual Studio Code o editor similar y navegador. Si ya trabajaste React en la sesión anterior, verifica el entorno en lugar de reinstalarlo. No necesitas MongoDB, Express, base de datos ni servidor backend para este ejercicio.

Abre una terminal integrada de VS Code (Terminal → New Terminal). Ejecuta:

```bash
node --version
npm --version
```

Si `node` o `npm` no se reconoce, instala una versión LTS de Node.js desde su sitio oficial y vuelve a abrir la terminal. Si Vite presenta un aviso de compatibilidad, revisa la versión mínima que exige la versión instalada. Como referencia, Vite 7 requiere Node.js 20.19+ o 22.12+. No es necesario instalar React de forma global.

### Paso 1 — Crear el proyecto con Vite

Coloca la terminal en la carpeta donde guardas tus prácticas. Ejecuta exactamente:

```bash
npm create vite@latest estado-react-s10 -- --template react
cd estado-react-s10
npm install
```
Al ejecutar la primera linea create vite@latest estado-react-s10 -- --template react, debes dar click en "y" luego marcar ESLint
El primer comando crea una carpeta `estado-react-s10` con la plantilla React. `--template react` solicita JSX y configuración React; el separador `--` hace llegar esa opción al creador de Vite. `cd` entra en la carpeta; `npm install` descarga las dependencias definidas en `package.json`. No continúes hasta que el comando termine sin errores. Si la carpeta ya existe, conserva tu trabajo y crea otra con nombre diferente.

### Paso 2 — Comprobar que React funciona antes de modificarlo

```bash
npm run dev
```

La terminal debe mostrar una URL local (frecuentemente `http://localhost:5173/`, pero usa la dirección exacta que imprima Vite). Abre esa URL en tu navegador: debe aparecer la pantalla de plantilla. Mantén esta terminal en ejecución para los ejercicios de interfaz; para otros comandos abre **otra terminal** en la misma raíz. Para detener el servidor utiliza `Ctrl+C`.

### Paso 3 — Crear las carpetas de trabajo

Desde el Explorador de VS Code, dentro de `src`, crea las carpetas `ejemplos`, `contextos`, `redux` y `tareas`. En la raíz crea también `pruebas` si deseas incorporar pruebas automatizadas.

### Paso 4 — Preparar el punto de entrada una sola vez

Abre `src/main.jsx` y reemplaza **todo** su contenido por el siguiente bloque. Se conservan los elementos esenciales de la plantilla, pero se evita cargar estilos de demostración que podrían distraer del laboratorio.

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.jsx';

createRoot(document.getElementById('root')).render(<React.StrictMode><App /></React.StrictMode>);
```

`createRoot` conecta React con el elemento HTML de id `root`. `App` es el componente que decidiremos mostrar; `StrictMode` agrega comprobaciones de desarrollo y no es un Provider de datos. En `index.html` debe existir `<div id="root"></div>`; Vite ya lo crea en su plantilla. Comprueba también que, si tu plantilla tiene imports a `App.css` o `index.css`, no los mantengas en los archivos reemplazados.

En `src/App.jsx` reemplaza **todo** el contenido por este punto de partida temporal:

```jsx
export default function App() {
  return <main><h2>Sesión 10: Context API y Redux</h2></main>;
}
```

Guarda y actualiza el navegador: debe aparecer el título. No escribiremos todo el proyecto de una vez. Cada ejercicio tendrá su propio archivo y activaremos **solo uno** en `App.jsx`, reemplazando sus dos referencias, con la secuencia indicada más adelante.

### Paso 5 — Comprender los comandos que usaremos

- `npm run dev`: ejecuta el servidor Vite y mantiene la página React disponible mientras programas.
- `npm install redux react-redux`: se ejecuta **cuando lleguemos a EJ06**; agrega Redux y los hooks oficiales de conexión a React. No se necesita para Context API.
- `node src/ejemplos/EJ05.mjs`: ejecuta el reducer puro en Node.js, sin abrir navegador.
- `npm run build`: verifica que Vite pueda preparar los archivos frontend para distribución; requiere que las dependencias estén instaladas.

En los ejercicios de pantalla, al terminar el código, reemplaza `src/App.jsx` usando exactamente este patrón, sustituyendo `EJXX` por el identificador indicado (`EJ01`, `EJ02`, `EJ03`, `EJ04` o `EJ06`):

```jsx
import EJXX from './ejemplos/EJXX.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><EJXX /></main>;
}
```

**Importante:** el patrón `EJXX` es ilustrativo; no lo ejecutes literalmente. Debes sustituir las dos apariciones. A continuación encontrarás el `App.jsx` exacto para cada ejecución. Cuando crees archivos por partes, **no guardes un bloque JSX incompleto esperando que el navegador lo ejecute**: completa las llaves y cierres y solo entonces valida.

## 6. Ejemplos completos resueltos paso a paso

Todos los bloques siguientes se escriben en el orden indicado. La guía contiene el código íntegro necesario; no necesitas otro documento para construir el proyecto.

---

# EJ01 — Reconocer prop drilling con tres componentes

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

Una aplicación puede necesitar mostrar el nombre del usuario en un componente que está varios niveles debajo de la pantalla principal. En este primer ejemplo, el componente Padre tendrá el dato; Intermedio lo recibirá aunque no lo use y Hijo finalmente lo mostrará. Construiremos la cadena de props para observar con precisión dónde está la repetición. Antes de aprender Context API es importante comprobar que este código funciona: prop drilling no es un error de React, sino una dificultad de mantenimiento cuando muchos intermediarios transfieren datos que no necesitan.

## ¿Qué problema concreto resolveremos?

¿Por qué el componente Intermedio tiene que transportar el nombre si únicamente Hijo lo muestra?

**Archivos:** `src/ejemplos/EJ01.jsx`. **Punto de partida:** proyecto Vite de la sección 5, con `npm run dev` activo para las pantallas.

### Paso 1 — Dar al hijo la responsabilidad de mostrar el dato

**Antes de escribir, comprendamos la necesidad.** Necesitamos primero la parte que realmente utilizará el dato, para distinguirla del intermediario.

**Dónde y qué escribir.** Crea `src/ejemplos/EJ01.jsx` y escribe desde la primera línea. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
function Hijo({ nombre }) {
  return <p>Hola, {nombre}</p>;
}
```

**Qué hace este bloque y por qué.** `Hijo` recibe un objeto de props; `{ nombre }` extrae la propiedad `nombre`. JSX intercala la variable con llaves dentro de la etiqueta `p`.

**Estado actual y siguiente comprobación.** La función todavía no se muestra: falta que otro componente la invoque.

### Paso 2 — Crear el intermediario que no necesita el nombre

**Antes de escribir, comprendamos la necesidad.** El árbol de ejemplo debe tener un nivel adicional para evidenciar la fricción de prop drilling.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ01.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
function Intermedio({ nombre }) {
  return <Hijo nombre={nombre} />;
}
```

**Qué hace este bloque y por qué.** `Intermedio` recibe `nombre` y lo vuelve a enviar con `nombre={nombre}`. No procesa ese dato: solo sirve de paso hacia `Hijo`.

**Estado actual y siguiente comprobación.** Ahora existe la cadena parcial Intermedio → Hijo, pero nadie fija todavía el valor inicial.

### Paso 3 — Fijar el nombre en el componente de entrada

**Antes de escribir, comprendamos la necesidad.** Falta un origen reconocible de datos para poder hacer una predicción y cambiar solo un punto.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ01.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Guarda el archivo cuando este bloque esté completo.

```jsx
export default function EJ01() {
  return <Intermedio nombre="Ana" />;
}
```

**Qué hace este bloque y por qué.** `EJ01` es el componente exportado que mostrará `App`; la prop literal `nombre="Ana"` inicia el recorrido descendente.

**Estado actual y siguiente comprobación.** Archivo completo y ejecutable al seleccionar EJ01 en `App.jsx`.

### Auditoría del artefacto construido

| Elemento | Función | Evidencia de comprensión |
|---|---|---|
| `EJ01.jsx` · Dar al hijo la responsabilidad de mostrar el dato | Necesitamos primero la parte que realmente utilizará el dato, para distinguirla del intermediario. | La función todavía no se muestra: falta que otro componente la invoque. |
| `EJ01.jsx` · Crear el intermediario que no necesita el nombre | El árbol de ejemplo debe tener un nivel adicional para evidenciar la fricción de prop drilling. | Ahora existe la cadena parcial Intermedio → Hijo, pero nadie fija todavía el valor inicial. |
| `EJ01.jsx` · Fijar el nombre en el componente de entrada | Falta un origen reconocible de datos para poder hacer una predicción y cambiar solo un punto. | Archivo completo y ejecutable al seleccionar EJ01 en `App.jsx`. |

### Archivos completos al terminar este ejemplo

En esta sección puedes comprobar que la suma de los microbloques produce **exactamente** los archivos finales. Si una línea no coincide, corrige tu archivo antes de ejecutar.

**`src/ejemplos/EJ01.jsx`**

```jsx
function Hijo({ nombre }) {
  return <p>Hola, {nombre}</p>;
}
function Intermedio({ nombre }) {
  return <Hijo nombre={nombre} />;
}
export default function EJ01() {
  return <Intermedio nombre="Ana" />;
}
```
### Activar este ejemplo en React

Abre `src/App.jsx` y **reemplaza todo el contenido** por:

```jsx
import EJ01 from './ejemplos/EJ01.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><EJ01 /></main>;
}
```

En la terminal que quedó ejecutando Vite usa la URL local que se mostró con `npm run dev`. Si cerraste el servidor, inicia de nuevo con `npm run dev` desde la raíz.

### Predecir → ejecutar → interpretar

**Antes de ejecutar:** Si cambiamos el prop nombre de Ana a Luis únicamente en Padre, ¿qué texto leerá Hijo?

**Resultado esperado:** En pantalla aparece: «Hola, Ana».

**Traza para explicar qué sucedió:** Padre (nombre = Ana) → Intermedio recibe nombre = Ana → Hijo recibe nombre = Ana → se muestra «Hola, Ana».

**Variación controlada:** Cambiar «Ana» por «Luis» en Padre y comprobar que no se modifica ningún componente intermedio.

**Error frecuente y diagnóstico:** Quitar `nombre={nombre}` de la llamada `<Hijo />`: Hijo recibirá `undefined`, aunque la aplicación puede seguir cargando.

**Corrección:** Restituir `<Hijo nombre={nombre} />`. En EJ02 evitaremos esta transferencia intermedia.

**Comprueba tu dominio:** explica qué dato vive en el origen, qué componente o función lo lee y qué operación causa el cambio observado.

## T01 — Tarea espejo de EJ01

**Enunciado.** En `src/tareas/T01.jsx` cambia el saludo para que Hijo muestre también una ciudad enviada desde Padre; Intermedio debe trasladar `ciudad` por props sin usarla.

**Archivos a preparar:** `src/tareas/T01.jsx`. **Restricción:** conserva los archivos originales de EJ01 intactos para poder regresar al ejemplo resuelto. **Pista:** Agrega `ciudad="Lima"` al prop en el punto de entrada; cada función intermedia debe recibirlo y reenviarlo.

**Evidencia y validación:** Hola, Ana desde Lima. Realiza una captura o registra la salida de terminal; añade dos o tres frases que expliquen por qué se produjo ese resultado.

**Cómo preparar la copia sin perder el ejemplo:** crea `src/tareas/T01.jsx` copiando íntegramente el contenido de `src/ejemplos/EJ01.jsx` mostrado arriba; modifica solo esa copia para resolver la tarea. Para visualizarla, en `src/App.jsx` sustituye el import y la etiqueta por `import T01 from './tareas/T01.jsx'` y `<T01 />` **solo después de renombrar la función exportada en la copia a `T01`**. No alteres el original.

Al comprobar la tarea, actualiza `src/App.jsx` mediante el siguiente contenido exacto **una vez hayas renombrado la exportación como `T01`**:

```jsx
import T01 from './tareas/T01.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><T01 /></main>;
}
```


---

# EJ02 — Compartir un dato sin prop drilling mediante Context API

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

El ejemplo anterior obligaba a un componente intermedio a reenviar un dato irrelevante para él. Ahora crearemos un contexto de usuario y un Provider que declara qué valor está disponible para sus descendientes. Dos componentes consumirán ese valor mediante useContext: uno mostrará un saludo y otro identificará el usuario activo. El valor será fijo en este ejercicio para aislar la nueva habilidad: crear, proveer y consumir. En el ejemplo siguiente agregaremos useState y comprobaremos que el valor compartido también puede cambiar.

## ¿Qué problema concreto resolveremos?

¿Cómo puede una tarjeta leer el usuario de su antecesor sin recibirlo como prop desde cada nivel?

**Archivos:** `src/ejemplos/EJ02.jsx`. **Punto de partida:** proyecto Vite de la sección 5, con `npm run dev` activo para las pantallas.

### Paso 1 — Crear el contexto que representará un usuario compartido

**Antes de escribir, comprendamos la necesidad.** Necesitamos una referencia común para que Provider y consumidores hablen del mismo dato.

**Dónde y qué escribir.** Crea `src/ejemplos/EJ02.jsx` y escribe desde la primera línea. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
import { createContext, useContext } from 'react';

const UsuarioContexto = createContext(null);
```

**Qué hace este bloque y por qué.** `createContext(null)` crea un objeto de contexto; `null` significa que no existe valor significativo fuera de un Provider. `useContext` será la función de lectura desde un consumidor.

**Estado actual y siguiente comprobación.** Aún no existen ni valor provisto ni componentes consumidores.

### Paso 2 — Crear un consumidor que muestre el saludo

**Antes de escribir, comprendamos la necesidad.** Primero necesitamos un componente que solicite el valor compartido, en vez de recibirlo como prop.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ02.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
function Saludo() {
  const { nombre } = useContext(UsuarioContexto);
  return <h3>Bienvenida, {nombre}</h3>;
}
```

**Qué hace este bloque y por qué.** `useContext(UsuarioContexto)` busca el Provider más próximo por encima; el objeto resultante contiene `nombre`. Las llaves de la asignación extraen esa propiedad.

**Estado actual y siguiente comprobación.** Por ahora no ejecutar: aún no hay Provider y la lectura de `null` fallaría.

### Paso 3 — Crear un segundo consumidor independiente

**Antes de escribir, comprendamos la necesidad.** Un solo consumidor no demuestra que el dato puede aprovecharse en lugares distintos.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ02.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
function Tarjeta() {
  const { nombre } = useContext(UsuarioContexto);
  return <p>Sesión de {nombre}</p>;
}
```

**Qué hace este bloque y por qué.** `Tarjeta` repite el patrón de lectura con el mismo objeto de contexto; no recibe `nombre` por props y renderiza otro mensaje.

**Estado actual y siguiente comprobación.** Ahora hay dos lectores que dependen del mismo valor compartido.

### Paso 4 — Colocar ambos lectores dentro del Provider

**Antes de escribir, comprendamos la necesidad.** Falta proveer el objeto que los consumidores van a leer; colocarlo arriba en el árbol resuelve la dependencia.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ02.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Guarda el archivo cuando este bloque esté completo.

```jsx
export default function EJ02() {
  return (
    <UsuarioContexto.Provider value={{ nombre: 'Ana' }}>
      <Saludo />
      <Tarjeta />
    </UsuarioContexto.Provider>
  );
}
```

**Qué hace este bloque y por qué.** `Provider` delimita los componentes que acceden al valor. `value={{ nombre: "Ana" }}` pasa un objeto de JavaScript como prop; las llaves externas indican expresión JSX y las internas representan el objeto literal. Ambos descendientes reciben la misma información.

**Estado actual y siguiente comprobación.** Al seleccionar EJ02, ambos mensajes deben mostrar «Ana».

### Auditoría del artefacto construido

| Elemento | Función | Evidencia de comprensión |
|---|---|---|
| `EJ02.jsx` · Crear el contexto que representará un usuario compartido | Necesitamos una referencia común para que Provider y consumidores hablen del mismo dato. | Aún no existen ni valor provisto ni componentes consumidores. |
| `EJ02.jsx` · Crear un consumidor que muestre el saludo | Primero necesitamos un componente que solicite el valor compartido, en vez de recibirlo como prop. | Por ahora no ejecutar: aún no hay Provider y la lectura de `null` fallaría. |
| `EJ02.jsx` · Crear un segundo consumidor independiente | Un solo consumidor no demuestra que el dato puede aprovecharse en lugares distintos. | Ahora hay dos lectores que dependen del mismo valor compartido. |
| `EJ02.jsx` · Colocar ambos lectores dentro del Provider | Falta proveer el objeto que los consumidores van a leer; colocarlo arriba en el árbol resuelve la dependencia. | Al seleccionar EJ02, ambos mensajes deben mostrar «Ana». |

### Archivos completos al terminar este ejemplo

En esta sección puedes comprobar que la suma de los microbloques produce **exactamente** los archivos finales. Si una línea no coincide, corrige tu archivo antes de ejecutar.

**`src/ejemplos/EJ02.jsx`**

```jsx
import { createContext, useContext } from 'react';

const UsuarioContexto = createContext(null);
function Saludo() {
  const { nombre } = useContext(UsuarioContexto);
  return <h3>Bienvenida, {nombre}</h3>;
}
function Tarjeta() {
  const { nombre } = useContext(UsuarioContexto);
  return <p>Sesión de {nombre}</p>;
}
export default function EJ02() {
  return (
    <UsuarioContexto.Provider value={{ nombre: 'Ana' }}>
      <Saludo />
      <Tarjeta />
    </UsuarioContexto.Provider>
  );
}
```
### Activar este ejemplo en React

Abre `src/App.jsx` y **reemplaza todo el contenido** por:

```jsx
import EJ02 from './ejemplos/EJ02.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><EJ02 /></main>;
}
```

En la terminal que quedó ejecutando Vite usa la URL local que se mostró con `npm run dev`. Si cerraste el servidor, inicia de nuevo con `npm run dev` desde la raíz.

### Predecir → ejecutar → interpretar

**Antes de ejecutar:** Si cambias el `value` del Provider a `{ nombre: "Luis" }`, ¿qué dos textos cambiarán?

**Resultado esperado:** Saludo «Bienvenida, Ana» y tarjeta «Sesión de Ana», sin props entre componentes.

**Traza para explicar qué sucedió:** Provider { nombre: Ana } → useContext en Saludo = Ana → useContext en Tarjeta = Ana. Ninguno recibe nombre como prop.

**Variación controlada:** En el Provider cambia nombre a «Luis» y comprueba que los dos consumidores se actualizan.

**Error frecuente y diagnóstico:** Colocar `<Saludo />` fuera de `<UsuarioContexto.Provider>`: `useContext` devolverá el valor predeterminado `null`; la desestructuración fallará.

**Corrección:** Mantener ambos consumidores bajo el Provider y conservar el `value` con la propiedad `nombre`.

**Comprueba tu dominio:** explica qué dato vive en el origen, qué componente o función lo lee y qué operación causa el cambio observado.

## T02 — Tarea espejo de EJ02

**Enunciado.** En `src/tareas/T02.jsx`, agrega la propiedad `rol: "Estudiante"` al objeto `value` del Provider y muestra «Rol: Estudiante» en la tarjeta, sin props.

**Archivos a preparar:** `src/tareas/T02.jsx`. **Restricción:** conserva los archivos originales de EJ02 intactos para poder regresar al ejemplo resuelto. **Pista:** Desestructura `rol` desde `useContext(UsuarioContexto)` en Tarjeta; amplía el objeto `value`.

**Evidencia y validación:** Bienvenida, Ana · Sesión de Ana · Rol: Estudiante. Realiza una captura o registra la salida de terminal; añade dos o tres frases que expliquen por qué se produjo ese resultado.

**Cómo preparar la copia sin perder el ejemplo:** crea `src/tareas/T02.jsx` copiando íntegramente el contenido de `src/ejemplos/EJ02.jsx` mostrado arriba; modifica solo esa copia para resolver la tarea. Para visualizarla, en `src/App.jsx` sustituye el import y la etiqueta por `import T02 from './tareas/T02.jsx'` y `<T02 />` **solo después de renombrar la función exportada en la copia a `T02`**. No alteres el original.

Al comprobar la tarea, actualiza `src/App.jsx` mediante el siguiente contenido exacto **una vez hayas renombrado la exportación como `T02`**:

```jsx
import T02 from './tareas/T02.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><T02 /></main>;
}
```


---

# EJ03 — Crear un tema claro/oscuro con Provider y useState

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

Hasta ahora el contexto compartía un objeto fijo. En una interfaz real, el usuario puede pulsar un botón y cambiar el tema de toda una sección. Separaremos el contrato del contexto, la lógica de estado y el componente consumidor en archivos distintos: TemaContexto declara qué se comparte; TemaProvider conserva el estado y expone una función para alternarlo; EJ03 muestra el resultado y permite interactuar. El objetivo es observar una actualización en dos componentes sin pasar props y explicar la relación entre useState, Provider y useContext.

## ¿Qué problema concreto resolveremos?

¿Cómo mantener coordinados el texto del botón y el aspecto del panel con un único estado?

**Archivos:** `src/contextos/TemaContexto.jsx`, `src/contextos/TemaProvider.jsx`, `src/ejemplos/EJ03.jsx`. **Punto de partida:** proyecto Vite de la sección 5, con `npm run dev` activo para las pantallas.

### Paso 1 — Declarar el contrato de información compartida

**Antes de escribir, comprendamos la necesidad.** Conviene separar la referencia de contexto para que otros archivos importen exactamente el mismo objeto.

**Dónde y qué escribir.** Crea `src/contextos/TemaContexto.jsx` y escribe desde la primera línea. Guarda el archivo cuando este bloque esté completo.

```jsx
import { createContext } from 'react';

export const TemaContexto = createContext(null);
```

**Qué hace este bloque y por qué.** `export` hace disponible `TemaContexto`. Este archivo no guarda el estado: únicamente declara el canal que utilizarán Provider y consumidores.

**Estado actual y siguiente comprobación.** El archivo se puede guardar; todavía no hay interfaz.

### Paso 2 — Crear el estado que cambiará de claro a oscuro

**Antes de escribir, comprendamos la necesidad.** El valor de tema debe poder cambiar, así que necesitamos una fuente de estado ubicada por encima de los consumidores.

**Dónde y qué escribir.** Crea `src/contextos/TemaProvider.jsx` y escribe desde la primera línea. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
import { useState } from 'react';
import { TemaContexto } from './TemaContexto.jsx';

export function TemaProvider({ children }) {
  const [temaOscuro, setTemaOscuro] = useState(false);
```

**Qué hace este bloque y por qué.** `useState(false)` inicia el estado en modo claro. `temaOscuro` es el valor actual y `setTemaOscuro` solicita cambios. `children` representará el contenido envuelto por el Provider.

**Estado actual y siguiente comprobación.** El bloque de función permanece abierto: todavía no se puede compilar hasta agregar la acción y el retorno.

### Paso 3 — Definir el cambio y compartir la función

**Antes de escribir, comprendamos la necesidad.** Los consumidores deben poder solicitar cambios sin conocer ni manejar directamente el setter.

**Dónde y qué escribir.** Abre `src/contextos/TemaProvider.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Guarda el archivo cuando este bloque esté completo.

```jsx
  const alternarTema = () => setTemaOscuro(actual => !actual);

  return (
    <TemaContexto.Provider value={{ temaOscuro, alternarTema }}>
      {children}
    </TemaContexto.Provider>
  );
}
```

**Qué hace este bloque y por qué.** `alternarTema` usa la actualización funcional para invertir el valor más reciente; evita depender de una captura anterior. `value` expone tanto dato como acción, `{children}` coloca componentes descendientes dentro de su alcance.

**Estado actual y siguiente comprobación.** El Provider ya está completo, pero hace falta un componente consumidor para observarlo.

### Paso 4 — Construir el botón que lee el estado y solicita cambiarlo

**Antes de escribir, comprendamos la necesidad.** La interfaz necesita observar el valor actual y permitir que el usuario invoque la función del Provider.

**Dónde y qué escribir.** Crea `src/ejemplos/EJ03.jsx` y escribe desde la primera línea. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
import { useContext } from 'react';
import { TemaContexto } from '../contextos/TemaContexto.jsx';
import { TemaProvider } from '../contextos/TemaProvider.jsx';

function BotonTema() {
  const { temaOscuro, alternarTema } = useContext(TemaContexto);
  return <button onClick={alternarTema}>Cambiar tema ({temaOscuro ? 'Oscuro' : 'Claro'})</button>;
}
```

**Qué hace este bloque y por qué.** `useContext` lee un objeto con `temaOscuro` y `alternarTema`; el operador ternario elige el texto según el booleano. `onClick` recibe la función para ejecutarse solo tras la acción.

**Estado actual y siguiente comprobación.** No ejecutar EJ03 todavía: falta el panel y el componente que envuelve con Provider.

### Paso 5 — Construir una segunda vista que reaccione al mismo estado

**Antes de escribir, comprendamos la necesidad.** Queremos demostrar que el cambio no se limita al botón que originó la acción.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ03.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
function PanelTema() {
  const { temaOscuro } = useContext(TemaContexto);
  return <div style={{ padding: 20, marginTop: 12, background: temaOscuro ? '#1e293b' : '#e2e8f0', color: temaOscuro ? '#ffffff' : '#0f172a' }}>Panel en tema {temaOscuro ? 'oscuro' : 'claro'}</div>;
}
```

**Qué hace este bloque y por qué.** `PanelTema` lee el mismo booleano sin props; `style` recibe un objeto JSX que establece color de fondo y texto de acuerdo con el estado. La actualización del Provider causa un nuevo render en sus consumidores.

**Estado actual y siguiente comprobación.** Tenemos botón y panel; falta incluirlos dentro del límite del Provider.

### Paso 6 — Envolver los consumidores para completar la aplicación

**Antes de escribir, comprendamos la necesidad.** Ningún consumidor debería quedar fuera de la cobertura del Provider que suministra sus datos.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ03.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Guarda el archivo cuando este bloque esté completo.

```jsx
export default function EJ03() {
  return <TemaProvider><h2>Tema compartido</h2><BotonTema /><PanelTema /></TemaProvider>;
}
```

**Qué hace este bloque y por qué.** `TemaProvider` envuelve el título, el botón y el panel. Los últimos dos son consumidores; el título no usa el contexto. El estado queda unificado en el proveedor.

**Estado actual y siguiente comprobación.** Ahora el ejercicio se puede ejecutar seleccionando EJ03 en App.jsx.

### Auditoría del artefacto construido

| Elemento | Función | Evidencia de comprensión |
|---|---|---|
| `TemaContexto.jsx` · Declarar el contrato de información compartida | Conviene separar la referencia de contexto para que otros archivos importen exactamente el mismo objeto. | El archivo se puede guardar; todavía no hay interfaz. |
| `TemaProvider.jsx` · Crear el estado que cambiará de claro a oscuro | El valor de tema debe poder cambiar, así que necesitamos una fuente de estado ubicada por encima de los consumidores. | El bloque de función permanece abierto: todavía no se puede compilar hasta agregar la acción y el retorno. |
| `TemaProvider.jsx` · Definir el cambio y compartir la función | Los consumidores deben poder solicitar cambios sin conocer ni manejar directamente el setter. | El Provider ya está completo, pero hace falta un componente consumidor para observarlo. |
| `EJ03.jsx` · Construir el botón que lee el estado y solicita cambiarlo | La interfaz necesita observar el valor actual y permitir que el usuario invoque la función del Provider. | No ejecutar EJ03 todavía: falta el panel y el componente que envuelve con Provider. |
| `EJ03.jsx` · Construir una segunda vista que reaccione al mismo estado | Queremos demostrar que el cambio no se limita al botón que originó la acción. | Tenemos botón y panel; falta incluirlos dentro del límite del Provider. |
| `EJ03.jsx` · Envolver los consumidores para completar la aplicación | Ningún consumidor debería quedar fuera de la cobertura del Provider que suministra sus datos. | Ahora el ejercicio se puede ejecutar seleccionando EJ03 en App.jsx. |

### Archivos completos al terminar este ejemplo

En esta sección puedes comprobar que la suma de los microbloques produce **exactamente** los archivos finales. Si una línea no coincide, corrige tu archivo antes de ejecutar.

**`src/contextos/TemaContexto.jsx`**

```jsx
import { createContext } from 'react';

export const TemaContexto = createContext(null);
```
**`src/contextos/TemaProvider.jsx`**

```jsx
import { useState } from 'react';
import { TemaContexto } from './TemaContexto.jsx';

export function TemaProvider({ children }) {
  const [temaOscuro, setTemaOscuro] = useState(false);
  const alternarTema = () => setTemaOscuro(actual => !actual);

  return (
    <TemaContexto.Provider value={{ temaOscuro, alternarTema }}>
      {children}
    </TemaContexto.Provider>
  );
}
```
**`src/ejemplos/EJ03.jsx`**

```jsx
import { useContext } from 'react';
import { TemaContexto } from '../contextos/TemaContexto.jsx';
import { TemaProvider } from '../contextos/TemaProvider.jsx';

function BotonTema() {
  const { temaOscuro, alternarTema } = useContext(TemaContexto);
  return <button onClick={alternarTema}>Cambiar tema ({temaOscuro ? 'Oscuro' : 'Claro'})</button>;
}
function PanelTema() {
  const { temaOscuro } = useContext(TemaContexto);
  return <div style={{ padding: 20, marginTop: 12, background: temaOscuro ? '#1e293b' : '#e2e8f0', color: temaOscuro ? '#ffffff' : '#0f172a' }}>Panel en tema {temaOscuro ? 'oscuro' : 'claro'}</div>;
}
export default function EJ03() {
  return <TemaProvider><h2>Tema compartido</h2><BotonTema /><PanelTema /></TemaProvider>;
}
```
### Activar este ejemplo en React

Abre `src/App.jsx` y **reemplaza todo el contenido** por:

```jsx
import EJ03 from './ejemplos/EJ03.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><EJ03 /></main>;
}
```

En la terminal que quedó ejecutando Vite usa la URL local que se mostró con `npm run dev`. Si cerraste el servidor, inicia de nuevo con `npm run dev` desde la raíz.

### Predecir → ejecutar → interpretar

**Antes de ejecutar:** ¿Qué valores mostrarán BotonTema y PanelTema después del primer clic y después del segundo?

**Resultado esperado:** Tema claro al iniciar; al pulsar «Cambiar tema» se lee «Tema oscuro» y el panel cambia su fondo.

**Traza para explicar qué sucedió:** temaOscuro=false → clic → setTemaOscuro(actual => !actual) → true → los dos consumidores leen true → segundo clic → false.

**Variación controlada:** Cambiar el estado inicial en TemaProvider a `true` y observar el tema oscuro desde el primer render.

**Error frecuente y diagnóstico:** Usar `onClick={alternarTema()}` en el botón: se invoca durante el render, en lugar de esperar un clic, y puede provocar un bucle de renders.

**Corrección:** Utilizar `onClick={alternarTema}` para entregar la referencia de la función.

**Comprueba tu dominio:** explica qué dato vive en el origen, qué componente o función lo lee y qué operación causa el cambio observado.

## T03 — Tarea espejo de EJ03

**Enunciado.** En `src/tareas/T03.jsx` incorpora un segundo botón «Volver a claro», que restablezca el tema a claro mediante una nueva función pública `restablecerTema` de la copia `T03Provider` para no modificar EJ03.

**Archivos a preparar:** `src/tareas/T03Provider.jsx`, `src/tareas/T03.jsx`. **Restricción:** conserva los archivos originales de EJ03 intactos para poder regresar al ejemplo resuelto. **Pista:** Amplía `value` del Provider de T03 con `restablecerTema: () => setTemaOscuro(false)`. El consumidor debe extraerla desde `useContext`.

**Evidencia y validación:** Tras activar oscuro, «Volver a claro» deja el panel claro.. Realiza una captura o registra la salida de terminal; añade dos o tres frases que expliquen por qué se produjo ese resultado.

**Cómo preparar la copia sin perder el ejemplo:** crea `src/tareas/T03.jsx` copiando íntegramente el contenido de `src/ejemplos/EJ03.jsx` mostrado arriba; modifica solo esa copia para resolver la tarea. Para visualizarla, en `src/App.jsx` sustituye el import y la etiqueta por `import T03 from './tareas/T03.jsx'` y `<T03 />` **solo después de renombrar la función exportada en la copia a `T03`**. No alteres el original.

**Atención especial para T03:** para no alterar el EJ03, crea también `src/tareas/T03Provider.jsx` copiando `src/contextos/TemaProvider.jsx`, cambia la función exportada `TemaProvider` a `TemaProviderT03`, y en `T03.jsx` importa esa función de `./T03Provider.jsx`. Después agrega `restablecerTema` al nuevo Provider; la función de EJ03 queda intacta. El archivo de tarea incluido en FASE 15 ya prepara esta separación.

Al comprobar la tarea, actualiza `src/App.jsx` mediante el siguiente contenido exacto **una vez hayas renombrado la exportación como `T03`**:

```jsx
import T03 from './tareas/T03.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><T03 /></main>;
}
```


---

# EJ04 — Cambiar el idioma global desde dos consumidores

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

Un sitio puede mostrar texto en español o inglés y necesita que distintos componentes respeten la selección. Crearemos IdiomaContexto, IdiomaProvider y un componente EJ04 que contiene dos consumidores: SaludoIdioma muestra el mensaje y SelectorIdioma ofrece los botones. El Provider guarda un único valor con useState y distribuye tanto el idioma actual como la función para cambiarlo. Verificaremos que los dos consumidores permanecen sincronizados y que deshabilitar el idioma ya seleccionado evita acciones redundantes.

## ¿Qué problema concreto resolveremos?

¿Cómo conseguir que el saludo y los botones compartan una sola selección de idioma sin pasar props?

**Archivos:** `src/contextos/IdiomaContexto.jsx`, `src/contextos/IdiomaProvider.jsx`, `src/ejemplos/EJ04.jsx`. **Punto de partida:** proyecto Vite de la sección 5, con `npm run dev` activo para las pantallas.

### Paso 1 — Definir un contexto específico para idioma

**Antes de escribir, comprendamos la necesidad.** Tema e idioma son responsabilidades distintas; no conviene mezclarlas en un Provider gigante.

**Dónde y qué escribir.** Crea `src/contextos/IdiomaContexto.jsx` y escribe desde la primera línea. Guarda el archivo cuando este bloque esté completo.

```jsx
import { createContext } from 'react';

export const IdiomaContexto = createContext(null);
```

**Qué hace este bloque y por qué.** El contexto de idioma es un objeto diferente de TemaContexto. Importarlo desde un archivo único evita leer accidentalmente otro contexto.

**Estado actual y siguiente comprobación.** Todavía no define cómo cambiar el idioma.

### Paso 2 — Guardar idioma inicial en el Provider

**Antes de escribir, comprendamos la necesidad.** Necesitamos una única fuente de verdad para que los consumidores nunca discrepen sobre la selección.

**Dónde y qué escribir.** Crea `src/contextos/IdiomaProvider.jsx` y escribe desde la primera línea. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
import { useState } from 'react';
import { IdiomaContexto } from './IdiomaContexto.jsx';

export function IdiomaProvider({ children }) {
  const [idioma, setIdioma] = useState('es');
  const cambiarIdioma = nuevoIdioma => setIdioma(nuevoIdioma);
```

**Qué hace este bloque y por qué.** `idioma` comienza en `es`; `setIdioma` actualiza el valor; `cambiarIdioma(nuevoIdioma)` es la operación pública que usarán los botones. `children` sitúa todo el contenido bajo el proveedor.

**Estado actual y siguiente comprobación.** La función aún está abierta; falta devolver el Provider para que el contexto pueda leerse.

### Paso 3 — Exponer dato y acción a los descendientes

**Antes de escribir, comprendamos la necesidad.** El valor debe estar en el contexto, no en props que atraviesan intermediarios.

**Dónde y qué escribir.** Abre `src/contextos/IdiomaProvider.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Guarda el archivo cuando este bloque esté completo.

```jsx
  return (
    <IdiomaContexto.Provider value={{ idioma, cambiarIdioma }}>
      {children}
    </IdiomaContexto.Provider>
  );
}
```

**Qué hace este bloque y por qué.** `value` comparte el estado y la acción; `{children}` puede incluir varios consumidores, incluso en diferentes niveles.

**Estado actual y siguiente comprobación.** Provider completo; el siguiente paso es crear las vistas que consumirán el dato.

### Paso 4 — Mostrar un saludo asociado al idioma compartido

**Antes de escribir, comprendamos la necesidad.** Un consumidor debe transformar el código de idioma en un texto útil para la persona.

**Dónde y qué escribir.** Crea `src/ejemplos/EJ04.jsx` y escribe desde la primera línea. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
import { useContext } from 'react';
import { IdiomaContexto } from '../contextos/IdiomaContexto.jsx';
import { IdiomaProvider } from '../contextos/IdiomaProvider.jsx';

function SaludoIdioma() {
  const { idioma } = useContext(IdiomaContexto);
  const mensaje = idioma === 'es' ? '¡Hola! El idioma actual es Español.' : 'Hello! The current language is English.';
  return <p>{mensaje}</p>;
}
```

**Qué hace este bloque y por qué.** `useContext` extrae la propiedad `idioma`; el condicional devuelve una cadena en español si el valor es `es` y otra en inglés si es `en`. La variable `mensaje` es derivada del estado: no necesita otro useState.

**Estado actual y siguiente comprobación.** Por el momento faltan botones y envoltorio para interactuar.

### Paso 5 — Crear los botones sin invocar el cambio en render

**Antes de escribir, comprendamos la necesidad.** El estudiante necesita una manera de producir cambios y ver cómo otros componentes reaccionan.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ04.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
function SelectorIdioma() {
  const { idioma, cambiarIdioma } = useContext(IdiomaContexto);
  return <div><button disabled={idioma === 'es'} onClick={() => cambiarIdioma('es')}>Español</button> <button disabled={idioma === 'en'} onClick={() => cambiarIdioma('en')}>English</button></div>;
}
```

**Qué hace este bloque y por qué.** `disabled` es true para el idioma actual. Las funciones flecha de `onClick` aplazan la ejecución y entregan el valor solicitado. Los dos botones comparten un solo estado.

**Estado actual y siguiente comprobación.** Ambos consumidores están listos; todavía necesitan un Provider sobre ellos.

### Paso 6 — Conectar ambos consumidores con IdiomaProvider

**Antes de escribir, comprendamos la necesidad.** La actualización debe llegar a los dos componentes y no solo al que contiene el clic.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ04.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Guarda el archivo cuando este bloque esté completo.

```jsx
export default function EJ04() {
  return <IdiomaProvider><h2>Idioma compartido</h2><SelectorIdioma /><SaludoIdioma /></IdiomaProvider>;
}
```

**Qué hace este bloque y por qué.** El envoltorio es el antecesor común; no se entrega `idioma` por props a SelectorIdioma ni a SaludoIdioma.

**Estado actual y siguiente comprobación.** Ya se puede seleccionar EJ04 en App.jsx y probar ambos botones.

### Auditoría del artefacto construido

| Elemento | Función | Evidencia de comprensión |
|---|---|---|
| `IdiomaContexto.jsx` · Definir un contexto específico para idioma | Tema e idioma son responsabilidades distintas; no conviene mezclarlas en un Provider gigante. | Todavía no define cómo cambiar el idioma. |
| `IdiomaProvider.jsx` · Guardar idioma inicial en el Provider | Necesitamos una única fuente de verdad para que los consumidores nunca discrepen sobre la selección. | La función aún está abierta; falta devolver el Provider para que el contexto pueda leerse. |
| `IdiomaProvider.jsx` · Exponer dato y acción a los descendientes | El valor debe estar en el contexto, no en props que atraviesan intermediarios. | Provider completo; el siguiente paso es crear las vistas que consumirán el dato. |
| `EJ04.jsx` · Mostrar un saludo asociado al idioma compartido | Un consumidor debe transformar el código de idioma en un texto útil para la persona. | Por el momento faltan botones y envoltorio para interactuar. |
| `EJ04.jsx` · Crear los botones sin invocar el cambio en render | El estudiante necesita una manera de producir cambios y ver cómo otros componentes reaccionan. | Ambos consumidores están listos; todavía necesitan un Provider sobre ellos. |
| `EJ04.jsx` · Conectar ambos consumidores con IdiomaProvider | La actualización debe llegar a los dos componentes y no solo al que contiene el clic. | Ya se puede seleccionar EJ04 en App.jsx y probar ambos botones. |

### Archivos completos al terminar este ejemplo

En esta sección puedes comprobar que la suma de los microbloques produce **exactamente** los archivos finales. Si una línea no coincide, corrige tu archivo antes de ejecutar.

**`src/contextos/IdiomaContexto.jsx`**

```jsx
import { createContext } from 'react';

export const IdiomaContexto = createContext(null);
```
**`src/contextos/IdiomaProvider.jsx`**

```jsx
import { useState } from 'react';
import { IdiomaContexto } from './IdiomaContexto.jsx';

export function IdiomaProvider({ children }) {
  const [idioma, setIdioma] = useState('es');
  const cambiarIdioma = nuevoIdioma => setIdioma(nuevoIdioma);
  return (
    <IdiomaContexto.Provider value={{ idioma, cambiarIdioma }}>
      {children}
    </IdiomaContexto.Provider>
  );
}
```
**`src/ejemplos/EJ04.jsx`**

```jsx
import { useContext } from 'react';
import { IdiomaContexto } from '../contextos/IdiomaContexto.jsx';
import { IdiomaProvider } from '../contextos/IdiomaProvider.jsx';

function SaludoIdioma() {
  const { idioma } = useContext(IdiomaContexto);
  const mensaje = idioma === 'es' ? '¡Hola! El idioma actual es Español.' : 'Hello! The current language is English.';
  return <p>{mensaje}</p>;
}
function SelectorIdioma() {
  const { idioma, cambiarIdioma } = useContext(IdiomaContexto);
  return <div><button disabled={idioma === 'es'} onClick={() => cambiarIdioma('es')}>Español</button> <button disabled={idioma === 'en'} onClick={() => cambiarIdioma('en')}>English</button></div>;
}
export default function EJ04() {
  return <IdiomaProvider><h2>Idioma compartido</h2><SelectorIdioma /><SaludoIdioma /></IdiomaProvider>;
}
```
### Activar este ejemplo en React

Abre `src/App.jsx` y **reemplaza todo el contenido** por:

```jsx
import EJ04 from './ejemplos/EJ04.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><EJ04 /></main>;
}
```

En la terminal que quedó ejecutando Vite usa la URL local que se mostró con `npm run dev`. Si cerraste el servidor, inicia de nuevo con `npm run dev` desde la raíz.

### Predecir → ejecutar → interpretar

**Antes de ejecutar:** ¿Por qué se deshabilita el botón Español al comenzar y cuál quedará deshabilitado después de pulsar English?

**Resultado esperado:** Al inicio «¡Hola! El idioma actual es Español»; al pulsar English aparece «Hello! The current language is English».

**Traza para explicar qué sucedió:** idioma="es" → click English → cambiarIdioma("en") → setIdioma("en") → SaludoIdioma cambia mensaje → botón English se deshabilita.

**Variación controlada:** Cambiar `useState("es")` por `useState("en")` y observar en qué idioma aparece al iniciar.

**Error frecuente y diagnóstico:** Llamar `cambiarIdioma("en")` directamente en `onClick` ejecuta la función durante el render.

**Corrección:** Pasar una función: `onClick={() => cambiarIdioma("en")}`.

**Comprueba tu dominio:** explica qué dato vive en el origen, qué componente o función lo lee y qué operación causa el cambio observado.

## T04 — Tarea espejo de EJ04

**Enunciado.** En `src/tareas/T04.jsx` agrega un tercer consumidor `EstadoIdioma` que muestre `Idioma seleccionado: Español` o `Idioma seleccionado: English` leyendo directamente IdiomaContexto.

**Archivos a preparar:** `src/tareas/T04.jsx`. **Restricción:** conserva los archivos originales de EJ04 intactos para poder regresar al ejemplo resuelto. **Pista:** Usa `const { idioma } = useContext(IdiomaContexto)` en el nuevo componente y colócalo dentro de `IdiomaProvider`.

**Evidencia y validación:** El saludo, los controles y el indicador muestran siempre el mismo idioma.. Realiza una captura o registra la salida de terminal; añade dos o tres frases que expliquen por qué se produjo ese resultado.

**Cómo preparar la copia sin perder el ejemplo:** crea `src/tareas/T04.jsx` copiando íntegramente el contenido de `src/ejemplos/EJ04.jsx` mostrado arriba; modifica solo esa copia para resolver la tarea. Para visualizarla, en `src/App.jsx` sustituye el import y la etiqueta por `import T04 from './tareas/T04.jsx'` y `<T04 />` **solo después de renombrar la función exportada en la copia a `T04`**. No alteres el original.

Al comprobar la tarea, actualiza `src/App.jsx` mediante el siguiente contenido exacto **una vez hayas renombrado la exportación como `T04`**:

```jsx
import T04 from './tareas/T04.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><T04 /></main>;
}
```


---

# EJ05 — Comprender las acciones y el reducer de Redux sin interfaz

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

Context API enseña a compartir valores; Redux añade una disciplina explícita para describir y procesar cambios. Comenzaremos por la parte que no necesita interfaz ni configuración compleja: una función reducer que recibe un estado y una acción y devuelve un nuevo estado. Después ejecutaremos un pequeño archivo desde Node.js que aplica INCREMENTAR y DISMINUIR en secuencia. La meta es poder anticipar 0 → 1 → 2 → 1 y explicar por qué no se modifica directamente el objeto anterior.

## ¿Qué problema concreto resolveremos?

¿Cómo expresar los cambios de un contador mediante acciones explícitas y una función predecible?

**Archivos:** `src/redux/contadorReducer.mjs`, `src/ejemplos/EJ05.mjs`. **Punto de partida:** proyecto Vite de la sección 5, con `npm run dev` activo para las pantallas.

### Paso 1 — Definir un estado inicial y una función pura

**Antes de escribir, comprendamos la necesidad.** Primero necesitamos especificar el dato controlado y una puerta de entrada para evaluar las acciones.

**Dónde y qué escribir.** Crea `src/redux/contadorReducer.mjs` y escribe desde la primera línea. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```js
export const estadoInicial = { contador: 0 };

export function contadorReducer(state = estadoInicial, action) {
  switch (action.type) {
```

**Qué hace este bloque y por qué.** `estadoInicial` tiene un único campo numérico. `state` es el estado previo; su valor predeterminado se usa cuando recibe `undefined`. `action` es un objeto cuyo campo `type` identifica la operación; `switch` elige el caso.

**Estado actual y siguiente comprobación.** No ejecutar todavía: la función y el switch siguen abiertos.

### Paso 2 — Implementar las dos acciones sin mutación

**Antes de escribir, comprendamos la necesidad.** Las acciones deben producir estados nuevos y observables sin modificar el parámetro anterior.

**Dónde y qué escribir.** Abre `src/redux/contadorReducer.mjs` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```js
case 'INCREMENTAR':
  return { contador: state.contador + 1 };
case 'DISMINUIR':
  return { contador: state.contador - 1 };
```

**Qué hace este bloque y por qué.** Cada `return` crea un objeto diferente. `INCREMENTAR` suma uno y `DISMINUIR` resta uno. El tipo exacto de cadena debe coincidir con la acción enviada.

**Estado actual y siguiente comprobación.** Falta manejar tipos desconocidos y cerrar el bloque.

### Paso 3 — Conservar el estado si la acción es desconocida

**Antes de escribir, comprendamos la necesidad.** Un reducer debe responder de forma segura cuando recibe una acción no reconocida.

**Dónde y qué escribir.** Abre `src/redux/contadorReducer.mjs` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Guarda el archivo cuando este bloque esté completo.

```js
    default:
      return state;
  }
}
```

**Qué hace este bloque y por qué.** `default` retorna el objeto que llegó, sin inventar un cambio. Las llaves cierran la selección y la función; ahora la sintaxis está completa.

**Estado actual y siguiente comprobación.** Ya se puede importar el reducer desde un archivo de prueba.

### Paso 4 — Ejecutar un flujo controlado de tres acciones

**Antes de escribir, comprendamos la necesidad.** El reducer ya tiene reglas, pero necesitamos una prueba observable que muestre los cambios de principio a fin.

**Dónde y qué escribir.** Crea `src/ejemplos/EJ05.mjs` y escribe desde la primera línea. Guarda el archivo cuando este bloque esté completo.

```js
import { contadorReducer } from '../redux/contadorReducer.mjs';

let estado = contadorReducer(undefined, { type: '@@INICIO' });
console.log('Estado inicial:', estado.contador);
for (const tipo of ['INCREMENTAR', 'INCREMENTAR', 'DISMINUIR']) {
  estado = contadorReducer(estado, { type: tipo });
  console.log(tipo + ':', estado.contador);
}
```

**Qué hace este bloque y por qué.** La importación apunta al archivo `.mjs` real. La acción desconocida inicial provoca que se use el estado por defecto. `for...of` recorre los tres tipos; `estado = contadorReducer(...)` conserva el resultado de cada operación para la siguiente. `console.log` evidencia la secuencia.

**Estado actual y siguiente comprobación.** Ejecuta `node src/ejemplos/EJ05.mjs` en la raíz y compara la salida.

### Auditoría del artefacto construido

| Elemento | Función | Evidencia de comprensión |
|---|---|---|
| `contadorReducer.mjs` · Definir un estado inicial y una función pura | Primero necesitamos especificar el dato controlado y una puerta de entrada para evaluar las acciones. | No ejecutar todavía: la función y el switch siguen abiertos. |
| `contadorReducer.mjs` · Implementar las dos acciones sin mutación | Las acciones deben producir estados nuevos y observables sin modificar el parámetro anterior. | Falta manejar tipos desconocidos y cerrar el bloque. |
| `contadorReducer.mjs` · Conservar el estado si la acción es desconocida | Un reducer debe responder de forma segura cuando recibe una acción no reconocida. | Ya se puede importar el reducer desde un archivo de prueba. |
| `EJ05.mjs` · Ejecutar un flujo controlado de tres acciones | El reducer ya tiene reglas, pero necesitamos una prueba observable que muestre los cambios de principio a fin. | Ejecuta `node src/ejemplos/EJ05.mjs` en la raíz y compara la salida. |

### Archivos completos al terminar este ejemplo

En esta sección puedes comprobar que la suma de los microbloques produce **exactamente** los archivos finales. Si una línea no coincide, corrige tu archivo antes de ejecutar.

**`src/redux/contadorReducer.mjs`**

```js
export const estadoInicial = { contador: 0 };

export function contadorReducer(state = estadoInicial, action) {
  switch (action.type) {
case 'INCREMENTAR':
  return { contador: state.contador + 1 };
case 'DISMINUIR':
  return { contador: state.contador - 1 };
    default:
      return state;
  }
}
```
**`src/ejemplos/EJ05.mjs`**

```js
import { contadorReducer } from '../redux/contadorReducer.mjs';

let estado = contadorReducer(undefined, { type: '@@INICIO' });
console.log('Estado inicial:', estado.contador);
for (const tipo of ['INCREMENTAR', 'INCREMENTAR', 'DISMINUIR']) {
  estado = contadorReducer(estado, { type: tipo });
  console.log(tipo + ':', estado.contador);
}
```
### Ejecutar desde la terminal

Abre una **segunda terminal** en la raíz de `estado-react-s10`, distinta de la que ejecuta Vite, y escribe:

```bash
node src/ejemplos/EJ05.mjs
```

Esta ejecución no usa la interfaz React ni requiere la librería `redux`; comprueba la lógica del reducer con JavaScript.

### Predecir → ejecutar → interpretar

**Antes de ejecutar:** ¿Cuál será el contador tras INCREMENTAR, INCREMENTAR, DISMINUIR? ¿Y el objeto de estado inicial cambia?

**Resultado esperado:** En terminal: Estado inicial: 0; INCREMENTAR: 1; INCREMENTAR: 2; DISMINUIR: 1.

**Traza para explicar qué sucedió:** Inicial {contador:0} → acción INCREMENTAR → {contador:1} → acción INCREMENTAR → {contador:2} → acción DISMINUIR → {contador:1}. Se devuelve un objeto nuevo en cada acción conocida.

**Variación controlada:** Agregar una tercera llamada a INCREMENTAR y comprobar que el resultado cambia de 1 a 2.

**Error frecuente y diagnóstico:** Modificar `state.contador++` dentro del reducer altera el objeto recibido y rompe el principio de inmutabilidad.

**Corrección:** Devolver `{ contador: state.contador + 1 }` como nuevo objeto; no cambiar directamente `state`.

**Comprueba tu dominio:** explica qué dato vive en el origen, qué componente o función lo lee y qué operación causa el cambio observado.

## T05 — Tarea espejo de EJ05

**Enunciado.** En `src/tareas/T05.mjs`, completa `reducerExtendido` para que reconozca la acción `REINICIAR` y devuelva `{ contador: 0 }`; las demás acciones deben delegarse en `contadorReducer`.

**Archivos a preparar:** `src/tareas/T05.mjs`. **Restricción:** conserva los archivos originales de EJ05 intactos para poder regresar al ejemplo resuelto. **Pista:** Comprueba primero `accion.type === "REINICIAR"`; si no se cumple, devuelve la llamada `contadorReducer(estado, accion)`.

**Evidencia y validación:** Tras dos incrementos y REINICIAR, la salida termina en contador 0.. Realiza una captura o registra la salida de terminal; añade dos o tres frases que expliquen por qué se produjo ese resultado.

**Punto de partida de T05, dentro de `src/tareas/T05.mjs` (escríbelo completo):**

```js
import { contadorReducer } from '../redux/contadorReducer.mjs';

const estadoInicial = { contador: 0 };
function reducerExtendido(estado = estadoInicial, accion) {
  // TODO T05: procesa primero la acción REINICIAR.
  return contadorReducer(estado, accion);
}

let estado = reducerExtendido(undefined, { type: '@@INICIO' });
for (const tipo of ['INCREMENTAR', 'INCREMENTAR', 'REINICIAR']) {
  estado = reducerExtendido(estado, { type: tipo });
}
console.log('Resultado T05:', estado.contador);
```

Para comprobar tu tarea ejecuta `node src/tareas/T05.mjs`. Al principio obtendrás un resultado incorrecto para el requisito de reinicio: es intencional. Implementa el `TODO` y vuelve a ejecutar hasta que aparezca `Resultado T05: 0`. No modifiques el reducer original de EJ05.

---

# EJ06 — Conectar Redux con React mediante store y hooks

## Objetivo técnico — Qué vamos a hacer y qué aprenderemos

En EJ05 pudimos calcular cambios con un reducer, pero todavía no existía un store central ni una pantalla React. Ahora incorporaremos la librería Redux para que el store mantenga el estado y procese `dispatch`. React-Redux permitirá que un componente lea el contador con `useSelector` y envíe las acciones con `useDispatch`. Envolveremos la vista con un Provider de Redux y comprobaremos que los clics actualizan el número. Esta implementación es deliberadamente básica, correspondiente al flujo clásico presentado en la sesión; en aplicaciones nuevas la recomendación oficial es Redux Toolkit.

## ¿Qué problema concreto resolveremos?

¿Cómo se relacionan los eventos de un botón React con el reducer y el estado almacenado en Redux?

**Archivos:** `src/redux/store.js`, `src/ejemplos/EJ06.jsx`. **Punto de partida:** proyecto Vite de la sección 5, con `npm run dev` activo para las pantallas.

### Paso 1 — Crear un store que utilice el reducer ya probado

**Antes de escribir, comprendamos la necesidad.** Ahora necesitamos que Redux guarde el estado y distribuya cada acción al reducer, algo que la prueba manual de EJ05 no hacía.

**Dónde y qué escribir.** Crea `src/redux/store.js` y escribe desde la primera línea. Guarda el archivo cuando este bloque esté completo.

```js
import { legacy_createStore } from 'redux';
import { contadorReducer } from './contadorReducer.mjs';

export const store = legacy_createStore(contadorReducer);
```

**Qué hace este bloque y por qué.** `legacy_createStore` crea el store utilizando `contadorReducer`. Se emplea únicamente para estudiar el flujo clásico con la menor configuración. `store.getState()` permitiría consultar el estado y `store.dispatch(...)` enviaría una acción; ambos pertenecen al objeto `store`.

**Estado actual y siguiente comprobación.** Antes de ejecutar la pantalla, confirma `npm install redux react-redux`.

### Paso 2 — Importar los hooks y la infraestructura de Redux

**Antes de escribir, comprendamos la necesidad.** Para conectar React con el store necesitamos los enlaces oficiales de React-Redux, distintos del Provider de Context API de EJ03.

**Dónde y qué escribir.** Crea `src/ejemplos/EJ06.jsx` y escribe desde la primera línea. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
import { Provider, useSelector, useDispatch } from 'react-redux';
import { store } from '../redux/store.js';
```

**Qué hace este bloque y por qué.** `Provider` hace disponible el store a descendientes, `useSelector` lee una parte del estado global y `useDispatch` entrega la función para despachar acciones. El segundo import refiere al store real.

**Estado actual y siguiente comprobación.** Falta crear el componente consumidor y envolverlo en el Provider.

### Paso 3 — Leer el contador y obtener dispatch

**Antes de escribir, comprendamos la necesidad.** El botón debe reflejar siempre el estado vigente; guardar otro contador local duplicaría la fuente de verdad.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ06.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
function Contador() {
  const contador = useSelector(state => state.contador);
  const dispatch = useDispatch();
```

**Qué hace este bloque y por qué.** `state => state.contador` elige el campo del estado global y hace que React vuelva a renderizar cuando cambie. `dispatch` sirve para enviar acciones al store; no modifica el valor por sí solo.

**Estado actual y siguiente comprobación.** La función está abierta: no ejecutar hasta agregar el retorno y cerrar llaves.

### Paso 4 — Representar el valor y despachar acciones desde los botones

**Antes de escribir, comprendamos la necesidad.** Necesitamos asociar cada interacción de la vista con una acción explícita del reducer.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ06.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Este microbloque forma parte de una función/estructura que permanece abierta; no intentes ejecutarlo hasta terminar el archivo.

```jsx
  return <section><h2>Contador Redux: {contador}</h2><button onClick={() => dispatch({ type: 'INCREMENTAR' })}>+</button> <button onClick={() => dispatch({ type: 'DISMINUIR' })}>−</button></section>;
}
```

**Qué hace este bloque y por qué.** Las funciones flecha de `onClick` ejecutan `dispatch` después del clic y aportan objetos con `type` coherentes con EJ05. El encabezado muestra el dato seleccionado; no se actualiza mediante un setter de React local.

**Estado actual y siguiente comprobación.** Los botones existen, pero sin el Provider padre los hooks todavía no encontrarían el store.

### Paso 5 — Conectar el componente al Provider del store

**Antes de escribir, comprendamos la necesidad.** El `Contador` debe quedar dentro del contexto interno utilizado por React-Redux para acceder al store.

**Dónde y qué escribir.** Abre `src/ejemplos/EJ06.jsx` y escribe el siguiente fragmento **inmediatamente después de la última línea que ya agregaste en ese archivo**. Guarda el archivo cuando este bloque esté completo.

```jsx
export default function EJ06() {
  return <Provider store={store}><Contador /></Provider>;
}
```

**Qué hace este bloque y por qué.** El Provider recibe la prop `store`, que es la instancia de Redux. Su hijo consume el valor y despacha acciones. Este Provider no es `TemaContexto.Provider`: tienen responsabilidades distintas.

**Estado actual y siguiente comprobación.** EJ06 está listo para mostrar en App.jsx con `npm run dev`.

### Auditoría del artefacto construido

| Elemento | Función | Evidencia de comprensión |
|---|---|---|
| `store.js` · Crear un store que utilice el reducer ya probado | Ahora necesitamos que Redux guarde el estado y distribuya cada acción al reducer, algo que la prueba manual de EJ05 no hacía. | Antes de ejecutar la pantalla, confirma `npm install redux react-redux`. |
| `EJ06.jsx` · Importar los hooks y la infraestructura de Redux | Para conectar React con el store necesitamos los enlaces oficiales de React-Redux, distintos del Provider de Context API de EJ03. | Falta crear el componente consumidor y envolverlo en el Provider. |
| `EJ06.jsx` · Leer el contador y obtener dispatch | El botón debe reflejar siempre el estado vigente; guardar otro contador local duplicaría la fuente de verdad. | La función está abierta: no ejecutar hasta agregar el retorno y cerrar llaves. |
| `EJ06.jsx` · Representar el valor y despachar acciones desde los botones | Necesitamos asociar cada interacción de la vista con una acción explícita del reducer. | Los botones existen, pero sin el Provider padre los hooks todavía no encontrarían el store. |
| `EJ06.jsx` · Conectar el componente al Provider del store | El `Contador` debe quedar dentro del contexto interno utilizado por React-Redux para acceder al store. | EJ06 está listo para mostrar en App.jsx con `npm run dev`. |

### Archivos completos al terminar este ejemplo

En esta sección puedes comprobar que la suma de los microbloques produce **exactamente** los archivos finales. Si una línea no coincide, corrige tu archivo antes de ejecutar.

**`src/redux/store.js`**

```js
import { legacy_createStore } from 'redux';
import { contadorReducer } from './contadorReducer.mjs';

export const store = legacy_createStore(contadorReducer);
```
**`src/ejemplos/EJ06.jsx`**

```jsx
import { Provider, useSelector, useDispatch } from 'react-redux';
import { store } from '../redux/store.js';
function Contador() {
  const contador = useSelector(state => state.contador);
  const dispatch = useDispatch();
  return <section><h2>Contador Redux: {contador}</h2><button onClick={() => dispatch({ type: 'INCREMENTAR' })}>+</button> <button onClick={() => dispatch({ type: 'DISMINUIR' })}>−</button></section>;
}
export default function EJ06() {
  return <Provider store={store}><Contador /></Provider>;
}
```
### Activar este ejemplo en React

Antes de activar EJ06, detén el servidor con `Ctrl+C` si fuera necesario y en la raíz ejecuta `npm install redux react-redux`; después usa `npm run dev` de nuevo. Esta dependencia añade el store de Redux y los hooks React-Redux.

Abre `src/App.jsx` y **reemplaza todo el contenido** por:

```jsx
import EJ06 from './ejemplos/EJ06.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><EJ06 /></main>;
}
```

En la terminal que quedó ejecutando Vite usa la URL local que se mostró con `npm run dev`. Si cerraste el servidor, inicia de nuevo con `npm run dev` desde la raíz.

### Predecir → ejecutar → interpretar

**Antes de ejecutar:** Si haces clic +, + y −, ¿qué dato tendrá el store y qué valor mostrará React?

**Resultado esperado:** Se ve Contador Redux: 0; cada clic en + aumenta uno; cada clic en − disminuye uno.

**Traza para explicar qué sucedió:** Clic + → dispatch({type:"INCREMENTAR"}) → store llama a contadorReducer → state contador 1 → useSelector lee 1 → React vuelve a mostrar 1.

**Variación controlada:** Realizar +, +, − y comprobar que aparece 1. Abrir consola de navegador si se necesita revisar errores.

**Error frecuente y diagnóstico:** Omitir `<Provider store={store}>` produce error en useSelector/useDispatch por no encontrar un contexto React-Redux.

**Corrección:** Envolver `<Contador />` con `<Provider store={store}>` y verificar el import correcto desde `react-redux`.

**Comprueba tu dominio:** explica qué dato vive en el origen, qué componente o función lo lee y qué operación causa el cambio observado.

## T06 — Tarea espejo de EJ06

**Enunciado.** En `src/tareas/T06.jsx` deshabilita el botón `−` cuando el contador valga cero, utilizando el valor obtenido por `useSelector`.

**Archivos a preparar:** `src/tareas/T06.jsx`. **Restricción:** conserva los archivos originales de EJ06 intactos para poder regresar al ejemplo resuelto. **Pista:** El botón `−` debe tener `disabled={contador === 0}`; no modifiques el reducer ni el store.

**Evidencia y validación:** Con contador 0 el botón − no puede pulsarse; al incrementar queda habilitado.. Realiza una captura o registra la salida de terminal; añade dos o tres frases que expliquen por qué se produjo ese resultado.

**Cómo preparar la copia sin perder el ejemplo:** crea `src/tareas/T06.jsx` copiando íntegramente el contenido de `src/ejemplos/EJ06.jsx` mostrado arriba; modifica solo esa copia para resolver la tarea. Para visualizarla, en `src/App.jsx` sustituye el import y la etiqueta por `import T06 from './tareas/T06.jsx'` y `<T06 />` **solo después de renombrar la función exportada en la copia a `T06`**. No alteres el original.

Al comprobar la tarea, actualiza `src/App.jsx` mediante el siguiente contenido exacto **una vez hayas renombrado la exportación como `T06`**:

```jsx
import T06 from './tareas/T06.jsx';

export default function App() {
  return <main style={{ maxWidth: 760, margin: '48px auto', fontFamily: 'Arial, sans-serif' }}><T06 /></main>;
}
```


## 7. Comparación final: cuándo escoger Context API o Redux

Los ejemplos EJ02–EJ04 utilizan Context API cuando el problema es hacer accesible un estado a varios componentes, sin forzar a un intermediario a pasar props. EJ05–EJ06 añaden una disciplina basada en acciones y un reducer: el cambio se puede describir como un flujo explícito y centralizado. Ninguna herramienta es una obligación universal. Para compartir un tema o un idioma en una aplicación pequeña, Context API suele ser suficiente; en escenarios con cambios coordinados más complejos puede ser útil Redux. En una aplicación real, la elección depende del flujo de actualizaciones, organización del equipo y necesidad de herramientas de depuración, no solo de contar componentes.

**Nota de vigencia:** se usa la sintaxis `<Contexto.Provider>` por continuidad con la presentación institucional y compatibilidad con React. React 19 también permite usar `<Contexto value={...}>`. Para explicar el Redux clásico se usa `legacy_createStore`, equivalente funcional al `createStore` de la presentación, que está marcado como deprecado. Para proyectos nuevos Redux recomienda Redux Toolkit; no lo configuramos hoy porque excedería la introducción básica.

## 8. Verificación final sin depender de proyectos resueltos

En la raíz del proyecto, cuando hayas instalado las dependencias de EJ06, ejecuta `npm run build`. Si todo está correcto, Vite construirá una carpeta `dist`. Esta prueba valida el proyecto de frontend que `App.jsx` tenga seleccionado en ese momento, no ejecuta las seis tareas automáticamente. Ejecuta `node src/ejemplos/EJ05.mjs` para validar de forma independiente el reducer. Visita en el navegador el proyecto y cambia los imports de `App.jsx` para validar uno a uno EJ01, EJ02, EJ03, EJ04 y EJ06.

Si aparece `Failed to resolve import`, verifica la ruta exacta, extensión `.jsx` y mayúsculas de archivo. Si aparece un error de `useContext` con valor `null`, revisa que el consumidor esté dentro de su Provider. Si el servidor no inicia, comprueba que estás dentro de `estado-react-s10`, que se ejecutó `npm install` y que otro proceso no ocupa el mismo puerto. Nunca pegues imports dentro de componentes ni elimines llaves de funciones sin cerrar el bloque.

## 9. Cierre docente y continuidad

Hoy construiste un recorrido completo de manejo de estado en React. Empezaste observando que pasar datos mediante props funciona, pero se vuelve incómodo cuando un componente intermedio actúa únicamente como transportador. Después creaste un contexto, definiste un Provider y comprobaste que dos consumidores pueden recibir el mismo valor sin props intermedias. A partir de ahí incorporaste `useState` para que una interacción actualice simultáneamente el botón y el panel del tema, y repetiste el patrón con un selector de idioma cuyos controles y saludo permanecen coherentes.

En la segunda parte te acercaste a Redux desde su fundamento: una acción describe lo que ocurre y un reducer calcula el estado siguiente sin alterar el anterior. Validaste esa idea desde Node.js y luego conectaste Redux con React mediante un store, un Provider de React-Redux y los hooks `useSelector` y `useDispatch`. Si aparece un fallo, revisa primero quién posee el estado, quién proporciona el valor y qué acción produce el cambio; esas preguntas son más útiles que copiar una solución sin comprenderla. Como refuerzo, revisa los materiales de **Lideratec Academy**, el blog https://lideratecacademy.com/blog/ y el canal https://www.youtube.com/@LideratecAcademy. La siguiente práctica podrá apoyarse en esta base sin volver a transmitir información por cada componente intermedio.
