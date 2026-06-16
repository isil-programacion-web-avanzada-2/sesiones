---
title: "Guia del estudiante - Manejo de estado con Context API y Redux"
author: "Lideratec Academy"
date: "2026-05-14"
lang: es
---


# Guia del estudiante - Clase guiada paso a paso

# Manejo de estado con Context API y Redux

**Elaborado por el docente**  
**Proyecto academico:** Lideratec Academy  
**Blog:** https://lideratecacademy.com/  
**Canal YouTube:** https://www.youtube.com/@LideratecAcademy

---

## Proposito de la practica

Esta guia te acompaña paso a paso para comprender como se comparte informacion entre componentes en React, por que aparece el problema de pasar props manualmente por varios niveles, como Context API permite crear un estado global sencillo y como Redux organiza cambios de estado mediante acciones, reducers y un store centralizado.

La practica esta pensada para estudiantes junior de programacion web avanzada. No se asume que domines arquitectura de estado global; por eso cada bloque explica el concepto, el codigo, el resultado esperado, los errores frecuentes y una forma de validar que funciono correctamente.

## Resultado de aprendizaje observable

Al finalizar la sesion, podras implementar una practica React que use Context API para compartir estado entre componentes, explicar el rol de `createContext`, `Provider`, `useContext`, `store`, `action`, `reducer`, `dispatch`, `useSelector` y `useDispatch`, y justificar cuando conviene usar Context API o Redux segun la escala de una aplicacion.

## Duracion sugerida

150 minutos.

## Requisitos previos minimos

| Requisito | Nivel esperado | Como comprobarlo |
|---|---|---|
| JavaScript moderno | Basico | Puedes leer funciones flecha, objetos y destructuring. |
| React | Basico | Reconoces componentes funcionales y JSX. |
| Props y state | Basico | Puedes explicar que una prop viene del padre y que un state vive dentro del componente. |
| Eventos | Basico | Puedes usar `onClick` en un boton. |
| Estructura de archivos | Basico | Puedes crear archivos dentro de `src`. |

## Preparacion del entorno

Puedes trabajar de dos formas.

### Opcion A - Entorno online

**Herramienta sugerida:** StackBlitz o una plataforma equivalente compatible con React.

**Motivo pedagogico de uso:** permite practicar React sin instalar Node.js localmente. Es util para una clase presencial, virtual o hibrida porque reduce problemas de instalacion.

**Accion de validacion:** abre una plantilla React, modifica un texto visible y confirma que el navegador actualiza la vista.

### Opcion B - Entorno local con Vite

**Herramientas:** Node.js LTS, npm, navegador moderno y editor de codigo.

**Enlaces oficiales de referencia:**

- Node.js: https://nodejs.org/
- React: https://react.dev/
- Vite: https://vite.dev/
- Redux: https://redux.js.org/
- Redux Toolkit: https://redux-toolkit.js.org/

**Comandos sugeridos para crear un proyecto React:**

~~~bash
npm create vite@latest estado-global-react -- --template react
cd estado-global-react
npm install
npm run dev
~~~

**Que es cada comando:**

1. `npm create vite@latest estado-global-react -- --template react`: crea un proyecto nuevo usando Vite y la plantilla de React.
2. `cd estado-global-react`: entra a la carpeta del proyecto.
3. `npm install`: instala las dependencias necesarias.
4. `npm run dev`: inicia el servidor de desarrollo.

**Resultado esperado:** la terminal muestra una URL local, por ejemplo `http://localhost:5173/`, y el navegador carga la aplicacion.

**Error comun:** ejecutar `npm run dev` fuera de la carpeta del proyecto.  
**Correccion:** verifica que la terminal este ubicada dentro de `estado-global-react`.

## Actualizacion tecnica operativa

Los conceptos de Context API, Provider, Consumer, `useContext`, action, reducer, store y dispatch siguen siendo validos para estudiar el manejo global de estado.

Para Redux, se debe diferenciar entre el ejemplo conceptual y la practica moderna:

| Elemento | Uso en esta sesion | Recomendacion operativa actual |
|---|---|---|
| `createStore` | Sirve para explicar de forma simple que un reducer crea un nuevo estado dentro de un store. | Mantenerlo solo como recurso conceptual o historico. |
| `configureStore` | Sirve para crear stores Redux en proyectos actuales. | Preferirlo en practicas nuevas porque Redux Toolkit es el enfoque recomendado. |
| `useSelector` y `useDispatch` | Sirven para conectar componentes React con Redux. | Mantenerlos para practicar lectura de estado y envio de acciones. |

---

# Desarrollo paso a paso

## Bloque 1 - Reconocer el problema del prop drilling

**Objetivo del bloque:**  
Identificar por que una aplicacion React puede volverse dificil de mantener cuando muchas props viajan por componentes intermedios.

**Concepto trabajado:**  
Props, estado compartido y prop drilling.

**Explicacion breve:**  
En React, los datos suelen bajar desde un componente padre hacia sus hijos mediante props. Esto funciona bien cuando la estructura es pequeña. El problema aparece cuando un dato debe llegar a un componente muy profundo. En ese caso, varios componentes intermedios reciben props solo para reenviarlas, aunque no las usen. A ese problema se le conoce como prop drilling.

**Ejemplo guiado:**

~~~jsx
// Archivo sugerido: App.jsx

function App() {
  const usuario = "Ana";
  return <Layout usuario={usuario} />;
}

function Layout({ usuario }) {
  return <Panel usuario={usuario} />;
}

function Panel({ usuario }) {
  return <Saludo usuario={usuario} />;
}

function Saludo({ usuario }) {
  return <h2>Hola, {usuario}</h2>;
}

export default App;
~~~

**Paso a paso:**

1. `App` crea el dato `usuario`.
2. `Layout` recibe `usuario`, pero no lo utiliza para pintar informacion propia.
3. `Panel` tambien recibe `usuario`, pero solo lo envia a otro componente.
4. `Saludo` finalmente usa `usuario` para mostrar el texto.
5. El resultado esperado en pantalla es `Hola, Ana`.

**Actividad para ti:**  
Dibuja el recorrido de la prop `usuario`. Marca que componentes usan realmente el dato y cuales solo lo transportan.

**Espacio para responder:**

- ¿Que componente crea el dato inicial?
- ¿Que componentes actuan solo como intermediarios?
- ¿Que pasaria si ademas de `usuario` tambien viajaran `tema`, `idioma` y `rol`?

**Resultado esperado:**  
Reconoces que pasar datos manualmente por muchos niveles puede hacer que el codigo sea repetitivo y dificil de mantener.

**Error comun a evitar:**  
Pensar que prop drilling siempre es incorrecto. En estructuras pequeñas puede ser suficiente. Se vuelve problematico cuando la aplicacion crece o cuando muchos componentes intermedios no usan las props.

**Mini reto:**  
Agrega una prop llamada `tema` con el valor `"oscuro"` y pasala por los mismos componentes hasta `Saludo`.

---

## Bloque 2 - Crear un contexto con `createContext()`

**Objetivo del bloque:**  
Crear un contexto global que pueda ser usado como fuente compartida de datos.

**Concepto trabajado:**  
Context API y funcion `createContext()`.

**Explicacion breve:**  
Context API es una herramienta nativa de React que permite compartir informacion entre componentes sin pasar props manualmente por todos los niveles. El primer paso es crear un contexto. Un contexto es un objeto que luego sera conectado a un Provider y consumido por componentes hijos.

**Ejemplo guiado:**

~~~jsx
// Archivo sugerido: src/context/TemaContexto.js

import { createContext } from "react";

export const TemaContexto = createContext();
~~~

**Paso a paso:**

1. `import { createContext } from "react";` importa desde React la funcion que permite crear contextos.
2. `createContext()` crea un objeto de contexto.
3. `export const TemaContexto` permite usar ese contexto desde otros archivos.
4. El resultado esperado no se ve todavia en pantalla; se crea una pieza tecnica que sera usada en el siguiente bloque.

**Actividad para ti:**  
Crea la carpeta `src/context` y dentro de ella el archivo `TemaContexto.js`. Copia el codigo y explica con tus palabras para que sirve exportar `TemaContexto`.

**Espacio para responder:**

- ¿Que funcion se importa desde React?
- ¿Por que el contexto se declara fuera de un componente?
- ¿Que pasaria si no exportas `TemaContexto`?

**Resultado esperado:**  
Tienes un contexto disponible para ser utilizado por un Provider y por componentes consumidores.

**Error comun a evitar:**  
Escribir `createContext` sin importarlo. El error comun sera que el navegador o la consola indique que `createContext` no esta definido.

**Mini reto:**  
Crea otro archivo llamado `IdiomaContexto.js` con un contexto llamado `IdiomaContexto`.

---

## Bloque 3 - Crear un Provider de tema

**Objetivo del bloque:**  
Construir un componente Provider que almacene estado y lo comparta con los componentes hijos.

**Concepto trabajado:**  
Provider, `useState`, estado global simple y funcion modificadora.

**Explicacion breve:**  
El Provider es el componente que rodea una parte de la aplicacion y entrega informacion mediante la prop `value`. En este caso, el Provider guardara si el tema esta en modo oscuro y compartira una funcion para alternarlo.

**Ejemplo guiado:**

~~~jsx
// Archivo sugerido: src/context/TemaProvider.jsx

import { useState } from "react";
import { TemaContexto } from "./TemaContexto";

export const TemaProvider = ({ children }) => {
  const [temaOscuro, setTemaOscuro] = useState(false);

  const alternarTema = () => {
    setTemaOscuro(!temaOscuro);
  };

  return (
    <TemaContexto.Provider value={{ temaOscuro, alternarTema }}>
      {children}
    </TemaContexto.Provider>
  );
};
~~~

**Paso a paso:**

1. `useState` permite crear un estado dentro del Provider.
2. `TemaContexto` conecta el Provider con el contexto creado en el bloque anterior.
3. `temaOscuro` guarda el valor actual del tema. Inicia en `false`, por lo tanto el tema inicial es claro.
4. `setTemaOscuro` actualiza el estado.
5. `alternarTema` invierte el valor actual: si es `false`, pasa a `true`; si es `true`, pasa a `false`.
6. `<TemaContexto.Provider>` es la etiqueta que expone datos a los componentes hijos.
7. `value={{ temaOscuro, alternarTema }}` entrega tanto el dato como la funcion modificadora.
8. `{children}` representa todos los componentes que queden dentro del Provider.

**Actividad para ti:**  
Crea el Provider y subraya en tu codigo donde se almacena el estado y donde se comparte con los hijos.

**Espacio para responder:**

- ¿Que valor inicial tiene `temaOscuro`?
- ¿Que hace la funcion `alternarTema`?
- ¿Por que se envia un objeto dentro de `value`?

**Resultado esperado:**  
Tienes un Provider capaz de compartir el estado `temaOscuro` y la funcion `alternarTema`.

**Error comun a evitar:**  
Olvidar escribir `{children}` dentro del Provider. Si se omite, los componentes hijos no se renderizaran.

**Mini reto:**  
Agrega una segunda funcion llamada `activarTemaClaro` que siempre cambie el tema a `false`.

---

## Bloque 4 - Consumir el contexto con `useContext()`

**Objetivo del bloque:**  
Leer informacion global desde un componente sin recibir props intermedias.

**Concepto trabajado:**  
Consumer moderno mediante el hook `useContext()`.

**Explicacion breve:**  
Un Consumer es cualquier componente que extrae informacion del Provider. En componentes funcionales, lo mas comun es usar `useContext()`. Este hook recibe el contexto y devuelve el valor que el Provider compartio.

**Ejemplo guiado:**

~~~jsx
// Archivo sugerido: src/components/BotonTema.jsx

import { useContext } from "react";
import { TemaContexto } from "../context/TemaContexto";

export const BotonTema = () => {
  const { temaOscuro, alternarTema } = useContext(TemaContexto);

  return (
    <button onClick={alternarTema}>
      Tema actual: {temaOscuro ? "Oscuro" : "Claro"}
    </button>
  );
};
~~~

**Paso a paso:**

1. `useContext` se importa desde React.
2. `TemaContexto` indica de que contexto se leera informacion.
3. `useContext(TemaContexto)` recupera el objeto enviado por el Provider.
4. La destructuracion `{ temaOscuro, alternarTema }` separa el dato y la funcion.
5. `onClick={alternarTema}` ejecuta la funcion cuando se presiona el boton.
6. `{temaOscuro ? "Oscuro" : "Claro"}` usa una condicion para mostrar el texto correcto.

**Actividad para ti:**  
Crea el componente `BotonTema` y explica por que no necesita recibir `temaOscuro` como prop.

**Espacio para responder:**

- ¿Que hook permite consumir el contexto?
- ¿Que dato se muestra en pantalla?
- ¿Que funcion se ejecuta al hacer clic?

**Resultado esperado:**  
El boton muestra el tema actual y cambia entre `Claro` y `Oscuro` al hacer clic.

**Error comun a evitar:**  
Usar `useContext(TemaProvider)` en lugar de `useContext(TemaContexto)`. El hook debe recibir el contexto, no el componente Provider.

**Mini reto:**  
Cambia el texto del boton a `Cambiar a tema oscuro` o `Cambiar a tema claro` segun corresponda.

---

## Bloque 5 - Envolver la aplicacion con el Provider

**Objetivo del bloque:**  
Conectar el Provider con la aplicacion para que los componentes hijos puedan acceder al contexto.

**Concepto trabajado:**  
Arbol de componentes y alcance del Provider.

**Explicacion breve:**  
Un componente solo puede consumir un contexto si esta dentro del Provider correspondiente. Por eso se envuelve la aplicacion o una parte de ella con `TemaProvider`.

**Ejemplo guiado:**

~~~jsx
// Archivo sugerido: src/App.jsx

import { TemaProvider } from "./context/TemaProvider";
import { BotonTema } from "./components/BotonTema";

function App() {
  return (
    <TemaProvider>
      <main>
        <h1>Practica de Context API</h1>
        <BotonTema />
      </main>
    </TemaProvider>
  );
}

export default App;
~~~

**Paso a paso:**

1. Se importa `TemaProvider`.
2. Se importa `BotonTema`.
3. `TemaProvider` envuelve a `main` y a todos sus componentes hijos.
4. `BotonTema` queda dentro del Provider, por eso puede usar `useContext(TemaContexto)`.
5. El resultado esperado es una pantalla con titulo y boton funcional.

**Actividad para ti:**  
Mueve temporalmente `<BotonTema />` fuera de `<TemaProvider>` y observa que sucede. Luego vuelve a colocarlo dentro.

**Espacio para responder:**

- ¿Por que el orden de envoltura importa?
- ¿Que componentes tienen acceso al valor del contexto?
- ¿Que ocurre si un consumidor queda fuera del Provider?

**Resultado esperado:**  
Comprendes que el Provider define el alcance de acceso al estado global.

**Error comun a evitar:**  
Crear correctamente el contexto y el Consumer, pero olvidar envolver la aplicacion con el Provider.

**Mini reto:**  
Crea un segundo componente llamado `EtiquetaTema` que tambien consuma `temaOscuro` y muestre un mensaje diferente.

---

## Bloque 6 - Crear un Provider y Consumer de idioma

**Objetivo del bloque:**  
Repetir el patron Provider/Consumer con un caso diferente para reforzar la comprension.

**Concepto trabajado:**  
Reutilizacion del patron Context API.

**Explicacion breve:**  
El mismo patron puede aplicarse a distintos datos globales. Un caso comun es el idioma de la interfaz. El Provider guarda el idioma y el Consumer muestra textos segun el idioma seleccionado.

**Ejemplo guiado:**

~~~jsx
// Archivo sugerido: src/context/IdiomaContexto.js

import { createContext } from "react";

export const IdiomaContexto = createContext({
  idioma: "es",
  cambiarIdioma: () => {},
});
~~~

~~~jsx
// Archivo sugerido: src/context/IdiomaProvider.jsx

import { useState } from "react";
import { IdiomaContexto } from "./IdiomaContexto";

export const IdiomaProvider = ({ children }) => {
  const [idioma, setIdioma] = useState("es");

  const cambiarIdioma = (nuevoIdioma) => {
    setIdioma(nuevoIdioma);
  };

  return (
    <IdiomaContexto.Provider value={{ idioma, cambiarIdioma }}>
      {children}
    </IdiomaContexto.Provider>
  );
};
~~~

~~~jsx
// Archivo sugerido: src/components/SelectorIdioma.jsx

import { useContext } from "react";
import { IdiomaContexto } from "../context/IdiomaContexto";

export const SelectorIdioma = () => {
  const { idioma, cambiarIdioma } = useContext(IdiomaContexto);

  const texto = {
    es: "El idioma actual es Español.",
    en: "The current language is English.",
  };

  return (
    <section>
      <p>{texto[idioma]}</p>
      <button onClick={() => cambiarIdioma("es")} disabled={idioma === "es"}>
        Cambiar a Español
      </button>
      <button onClick={() => cambiarIdioma("en")} disabled={idioma === "en"}>
        Change to English
      </button>
    </section>
  );
};
~~~

**Paso a paso:**

1. `IdiomaContexto` define la estructura esperada del contexto.
2. `IdiomaProvider` guarda el estado `idioma`.
3. `cambiarIdioma` recibe un nuevo idioma y actualiza el estado.
4. `SelectorIdioma` consume el contexto mediante `useContext`.
5. El objeto `texto` permite mostrar un mensaje distinto segun el idioma.
6. Los botones cambian el idioma y se deshabilitan cuando ya esta activo.

**Actividad para ti:**  
Agrega un tercer idioma con clave `fr` y un texto en frances. Añade un boton para activarlo.

**Espacio para responder:**

- ¿Que diferencia hay entre `TemaProvider` e `IdiomaProvider`?
- ¿Por que `cambiarIdioma` recibe un parametro?
- ¿Que hace la propiedad `disabled` en los botones?

**Resultado esperado:**  
Puedes aplicar el mismo patron de Context API a otro tipo de estado global.

**Error comun a evitar:**  
Agregar un nuevo idioma en el boton, pero olvidar agregar su texto en el objeto `texto`.

**Mini reto:**  
Muestra tambien un titulo que cambie de idioma junto con el parrafo.

---

## Bloque 7 - Comprender Redux desde reducer y dispatch

**Objetivo del bloque:**  
Explicar el flujo basico de Redux: una accion describe lo ocurrido, el reducer calcula el nuevo estado y el store conserva el resultado.

**Concepto trabajado:**  
Store, action, reducer, dispatch e inmutabilidad.

**Explicacion breve:**  
Redux organiza el estado global de manera predecible. En lugar de modificar el estado directamente, se envia una accion con `dispatch`. Luego un reducer recibe el estado actual y la accion, y devuelve un nuevo estado. El store guarda el estado central de la aplicacion.

**Ejemplo guiado conceptual:**

~~~js
// Archivo sugerido: src/redux/contadorReducer.js

const estadoInicial = { contador: 0 };

export function contadorReducer(state = estadoInicial, action) {
  switch (action.type) {
    case "INCREMENTAR":
      return { contador: state.contador + 1 };
    case "DISMINUIR":
      return { contador: state.contador - 1 };
    default:
      return state;
  }
}
~~~

**Paso a paso:**

1. `estadoInicial` define la forma inicial del estado.
2. `contadorReducer` recibe `state` y `action`.
3. `state = estadoInicial` asegura que exista un valor inicial.
4. `action.type` indica que ocurrio.
5. Si la accion es `INCREMENTAR`, se devuelve un nuevo objeto con el contador aumentado.
6. Si la accion es `DISMINUIR`, se devuelve un nuevo objeto con el contador reducido.
7. `default` devuelve el estado sin cambios cuando la accion no corresponde.

**Actividad para ti:**  
Agrega un caso llamado `REINICIAR` que devuelva el contador a cero.

**Espacio para responder:**

- ¿Por que el reducer no modifica directamente `state.contador`?
- ¿Que representa `action.type`?
- ¿Para que sirve el caso `default`?

**Resultado esperado:**  
Puedes leer un reducer y explicar como calcula un nuevo estado a partir de una accion.

**Error comun a evitar:**  
Escribir `state.contador = state.contador + 1`. En Redux se debe devolver un nuevo estado, no modificar directamente el anterior.

**Mini reto:**  
Agrega una accion `SUMAR_CINCO` que aumente el contador en 5.

---

## Bloque 8 - Crear un store Redux moderno con `configureStore`

**Objetivo del bloque:**  
Crear un store Redux usando una configuracion recomendada para proyectos actuales.

**Concepto trabajado:**  
Store y Redux Toolkit.

**Explicacion breve:**  
El store es el almacen global de Redux. En ejemplos introductorios puede aparecer `createStore` porque permite explicar el concepto de forma sencilla. Para una practica actual, se recomienda usar `configureStore` de Redux Toolkit, ya que configura buenas practicas por defecto.

**Ejemplo guiado:**

~~~bash
npm install @reduxjs/toolkit react-redux
~~~

~~~js
// Archivo sugerido: src/redux/store.js

import { configureStore } from "@reduxjs/toolkit";
import { contadorReducer } from "./contadorReducer";

export const store = configureStore({
  reducer: contadorReducer,
});
~~~

**Paso a paso:**

1. `npm install @reduxjs/toolkit react-redux` instala Redux Toolkit y la libreria que conecta React con Redux.
2. `configureStore` crea el store con configuraciones utiles por defecto.
3. `contadorReducer` se importa para indicar como cambia el estado.
4. `reducer: contadorReducer` conecta el reducer al store.
5. `export const store` permite usar el store en la aplicacion.

**Actividad para ti:**  
Instala las dependencias y crea el archivo `store.js`. Luego responde que diferencia conceptual existe entre reducer y store.

**Espacio para responder:**

- ¿Que paquete instala `@reduxjs/toolkit`?
- ¿Para que sirve `react-redux`?
- ¿Que recibe `configureStore` dentro del objeto de configuracion?

**Resultado esperado:**  
Tienes un store Redux listo para conectarse con React.

**Error comun a evitar:**  
Instalar solo `redux` y olvidar `react-redux`. Sin `react-redux`, los hooks `useSelector` y `useDispatch` no estaran disponibles.

**Mini reto:**  
Crea otro reducer conceptual llamado `usuarioReducer`, aunque todavia no lo conectes.

---

## Bloque 9 - Conectar Redux con React

**Objetivo del bloque:**  
Permitir que los componentes React lean el store y envien acciones.

**Concepto trabajado:**  
`Provider` de Redux, `useSelector` y `useDispatch`.

**Explicacion breve:**  
React necesita recibir el store de Redux mediante el componente `Provider` de `react-redux`. Luego, cualquier componente dentro de ese Provider puede leer estado con `useSelector` y enviar acciones con `useDispatch`.

**Ejemplo guiado:**

~~~jsx
// Archivo sugerido: src/main.jsx

import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import { store } from "./redux/store";
import App from "./App";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);
~~~

~~~jsx
// Archivo sugerido: src/components/ContadorRedux.jsx

import { useSelector, useDispatch } from "react-redux";

export const ContadorRedux = () => {
  const contador = useSelector((state) => state.contador);
  const dispatch = useDispatch();

  return (
    <section>
      <p>Valor: {contador}</p>
      <button onClick={() => dispatch({ type: "INCREMENTAR" })}>+</button>
      <button onClick={() => dispatch({ type: "DISMINUIR" })}>-</button>
    </section>
  );
};
~~~

**Paso a paso:**

1. `Provider` de `react-redux` entrega el store a la aplicacion.
2. `store={store}` indica que store usaran los componentes.
3. `useSelector((state) => state.contador)` lee el valor `contador` del estado global.
4. `useDispatch()` entrega la funcion `dispatch`.
5. `dispatch({ type: "INCREMENTAR" })` envia una accion al reducer.
6. El reducer calcula el nuevo estado.
7. El componente se vuelve a renderizar con el valor actualizado.

**Actividad para ti:**  
Agrega el componente `ContadorRedux` dentro de `App.jsx` y prueba los botones.

**Espacio para responder:**

- ¿Que hook se usa para leer el estado?
- ¿Que hook se usa para enviar acciones?
- ¿Por que el componente no modifica el estado directamente?

**Resultado esperado:**  
El contador aumenta y disminuye al presionar los botones.

**Error comun a evitar:**  
Usar `useSelector` fuera del `Provider` de Redux. El error indicara que no se encontro el contexto de React Redux.

**Mini reto:**  
Agrega un boton `Reiniciar` que despache `{ type: "REINICIAR" }` y completa el reducer para soportarlo.

---

## Bloque 10 - Decidir entre Context API y Redux

**Objetivo del bloque:**  
Comparar Context API y Redux para elegir una herramienta segun el tamaño y la complejidad de la aplicacion.

**Concepto trabajado:**  
Criterio tecnico de seleccion de herramientas.

**Explicacion breve:**  
Context API es ideal para estados globales simples o medianos, como tema, idioma o usuario autenticado. Redux es mas conveniente cuando hay flujos complejos, muchos eventos, depuracion avanzada, multiples pantallas y necesidad de trazabilidad del estado.

**Ejemplo guiado:**

| Caso | Herramienta sugerida | Justificacion |
|---|---|---|
| Cambiar tema claro/oscuro | Context API | Estado simple y poco cambiante. |
| Cambiar idioma de interfaz | Context API | Estado global sencillo. |
| Carrito con descuentos, stock y pagos | Redux | Flujo complejo con muchas acciones. |
| Panel administrativo con muchas pantallas | Redux | Mayor escalabilidad y depuracion. |
| App mediana con algunos estados globales | Context API + reducers locales | Balance entre simplicidad y organizacion. |

**Paso a paso:**

1. Identifica que dato quieres compartir.
2. Evalua cuantos componentes lo necesitan.
3. Revisa cuantas acciones modifican ese dato.
4. Decide si necesitas depuracion avanzada.
5. Elige Context API para estados simples y Redux para flujos complejos.

**Actividad para ti:**  
Analiza una aplicacion de reservas. Decide que estados manejar con Context API y cuales con Redux.

**Espacio para responder:**

- ¿Donde ubicarias el idioma de la interfaz?
- ¿Donde ubicarias el historial de reservas?
- ¿Que herramienta usarias para un flujo de pago con varios pasos?

**Resultado esperado:**  
Puedes justificar tecnicamente una decision de arquitectura de estado.

**Error comun a evitar:**  
Usar Redux para todo desde el inicio. No todas las aplicaciones necesitan una solucion compleja.

**Mini reto:**  
Escribe tres reglas propias para decidir cuando usar Context API y cuando usar Redux.

---

# Actividades guiadas progresivas

## Actividad 1 - Reconocimiento

**Titulo:** Identificar prop drilling.  
**Objetivo:** reconocer componentes intermediarios.  
**Instrucciones:** revisa el codigo del Bloque 1 y marca que componentes solo transportan props.  
**Que observar:** cuantos niveles atraviesa el dato.  
**Que responder:** explica si el diseño aun es manejable o ya necesita Context API.  
**Resultado esperado:** identificas el problema sin modificar codigo.  
**Criterio de validacion rapida:** puedes nombrar el componente que crea el dato, los intermediarios y el consumidor final.

## Actividad 2 - Replica guiada

**Titulo:** Crear tema global.  
**Objetivo:** implementar `TemaContexto`, `TemaProvider` y `BotonTema`.  
**Instrucciones:** replica los bloques 2, 3, 4 y 5.  
**Que modificar:** cambia el texto del boton.  
**Que observar:** el texto debe cambiar al presionar el boton.  
**Que responder:** explica que se comparte en `value`.  
**Resultado esperado:** el tema alterna entre claro y oscuro.  
**Criterio de validacion rapida:** el boton responde sin recibir props.

## Actividad 3 - Modificacion controlada

**Titulo:** Agregar idioma frances.  
**Objetivo:** extender el caso de idioma.  
**Instrucciones:** modifica `SelectorIdioma` para soportar `fr`.  
**Que modificar:** agrega clave `fr`, texto y boton.  
**Que observar:** el mensaje cambia cuando eliges frances.  
**Que responder:** indica que partes del codigo tuviste que cambiar.  
**Resultado esperado:** el idioma frances funciona sin modificar componentes intermedios.  
**Criterio de validacion rapida:** no aparecen textos `undefined`.

## Actividad 4 - Aplicacion

**Titulo:** Completar acciones Redux.  
**Objetivo:** agregar acciones nuevas al reducer.  
**Instrucciones:** implementa `REINICIAR` y `SUMAR_CINCO`.  
**Que modificar:** reducer y botones del componente.  
**Que observar:** los botones cambian el valor segun la accion.  
**Que responder:** explica que hace cada caso del `switch`.  
**Resultado esperado:** el contador aumenta, disminuye, se reinicia y suma cinco.  
**Criterio de validacion rapida:** cada boton despacha una accion con `type` correcto.

## Actividad 5 - Integracion

**Titulo:** Mini panel de configuracion.  
**Objetivo:** integrar Context API y Redux en una decision tecnica.  
**Instrucciones:** diseña una app conceptual con tema, idioma y contador global.  
**Que modificar:** decide que estado ira en Context API y cual en Redux.  
**Que observar:** la decision debe estar justificada, no solo implementada.  
**Que responder:** completa una tabla con estado, herramienta y razon tecnica.  
**Resultado esperado:** explicas la arquitectura de estado de forma coherente.  
**Criterio de validacion rapida:** cada herramienta se usa con un motivo claro.

## Actividad 6 - Validacion con preguntas

Responde de forma breve:

1. ¿Que problema resuelve Context API?
2. ¿Que hace un Provider?
3. ¿Que hace `useContext()`?
4. ¿Que representa una action en Redux?
5. ¿Que debe devolver siempre un reducer?
6. ¿Que hace `dispatch`?
7. ¿Cuando conviene migrar de Context API a Redux?

---

# Checklist final de aprendizaje

Marca cada logro cuando puedas demostrarlo:

- [ ] Puedo explicar que es prop drilling.
- [ ] Puedo crear un contexto con `createContext()`.
- [ ] Puedo construir un Provider con estado y funciones.
- [ ] Puedo consumir datos globales con `useContext()`.
- [ ] Puedo explicar el patron Provider/Consumer.
- [ ] Puedo leer un reducer basico de Redux.
- [ ] Puedo explicar que hace `dispatch`.
- [ ] Puedo conectar Redux con React usando `Provider`, `useSelector` y `useDispatch`.
- [ ] Puedo decidir cuando usar Context API y cuando usar Redux.

# Cierre de la practica

En esta practica aprendiste a reconocer el problema de compartir datos manualmente mediante props, construiste un contexto global con Context API, implementaste providers y consumers, y analizaste el flujo basico de Redux mediante actions, reducers, dispatch y store. Debes repasar especialmente la diferencia entre compartir un estado sencillo con Context API y organizar flujos complejos con Redux.

Evita tres errores frecuentes: usar un Consumer fuera de su Provider, modificar directamente el estado dentro de un reducer y aplicar Redux en casos donde Context API seria suficiente. La siguiente sesion puede conectar estos conceptos con arquitecturas de componentes mas grandes, manejo de efectos, consumo de APIs o patrones de organizacion profesional en React.

Para reforzar tu aprendizaje, revisa los recursos de Lideratec Academy:  
Blog: https://lideratecacademy.com/  
Canal YouTube: https://www.youtube.com/@LideratecAcademy

