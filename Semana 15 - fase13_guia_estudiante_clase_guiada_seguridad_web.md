---
title: "Guia del estudiante - Seguridad web con Express y React"
author: "Elaborado por el docente"
date: "Proyecto academico: Lideratec Academy"
lang: es
geometry: margin=2cm
fontsize: 10pt
mainfont: "DejaVu Sans"
monofont: "DejaVu Sans Mono"
colorlinks: true
linkcolor: blue
urlcolor: blue
header-includes:
  - \usepackage{fancyhdr}
  - \pagestyle{fancy}
  - \fancyhead[L]{Lideratec Academy}
  - \fancyhead[R]{Seguridad web}
  - \fancyfoot[C]{\thepage}
  - \setlength{\headheight}{15pt}
  - \usepackage{fvextra}
  - \DefineVerbatimEnvironment{Highlighting}{Verbatim}{breaklines,commandchars=\\\{\}}
  - \usepackage{listings}
  - \lstset{breaklines=true,breakatwhitespace=true,basicstyle=\ttfamily\small,columns=fullflexible}
---


# Seguridad y buenas practicas en aplicaciones web con Express y React

**Elaborado por el docente**  
**Proyecto academico:** Lideratec Academy  
**Blog:** https://lideratecacademy.com/  
**Canal YouTube:** https://www.youtube.com/@LideratecAcademy

## Proposito de la practica

Esta guia te acompaña paso a paso para comprender y aplicar buenas practicas basicas de seguridad en una aplicacion web moderna. Trabajaras con HTTPS, certificados SSL/TLS, proteccion contra XSS y CSRF, validacion en Express, sanitizacion en React, Helmet.js y auditoria con Lighthouse.

La meta no es memorizar comandos aislados. La meta es entender que una aplicacion web segura se construye por capas: comunicacion cifrada, validacion de datos, renderizado seguro, control de solicitudes sensibles, encabezados HTTP de seguridad y auditoria continua.

## Resultado de aprendizaje observable

Al finalizar, podras **aplicar** una checklist inicial de seguridad en una aplicacion Express y React, identificando riesgos de comunicacion, renderizado, solicitudes sensibles, configuracion de encabezados y auditoria con Lighthouse.

## Duracion sugerida

- Lectura guiada: 45 a 60 minutos.
- Practica tecnica: 90 a 120 minutos.
- Validacion final: 20 minutos.

## Requisitos previos minimos

| Requisito | Nivel esperado |
|---|---|
| JavaScript | Variables, funciones, objetos y modulos basicos |
| React | Componentes, `useState`, `useEffect` y eventos |
| Express | Rutas, middlewares y respuestas JSON |
| Terminal | Ejecutar comandos `npm` |
| Navegador | Abrir DevTools y revisar resultados |

## Preparacion del entorno

### Herramientas necesarias

| Herramienta | Motivo pedagogico | Enlace oficial | Validacion rapida |
|---|---|---|---|
| Node.js | Ejecutar proyectos React y Express | https://nodejs.org/ | `node -v` |
| npm | Instalar dependencias | Incluido con Node.js | `npm -v` |
| Express | Crear servidor backend | https://expressjs.com/ | instalar con `npm install express` |
| React | Construir frontend | https://react.dev/ | proyecto React funcional |
| DOMPurify | Sanitizar HTML de usuario | https://dompurify.com/ | `npm install dompurify` |
| express-validator | Validar y limpiar entradas en Express | https://express-validator.github.io/docs/ | `npm install express-validator` |
| Helmet.js | Configurar encabezados HTTP de seguridad | https://helmetjs.github.io/ | `npm install helmet` |
| Lighthouse | Auditar buenas practicas web | https://developer.chrome.com/docs/lighthouse | Chrome DevTools o CLI |
| Let's Encrypt / Certbot | Obtener certificados SSL/TLS en servidores reales | https://letsencrypt.org/ / https://certbot.eff.org/ | depende del servidor |

### Pasos generales de instalacion

1. Instala Node.js desde su sitio oficial.
2. Abre una terminal.
3. Verifica Node y npm:

~~~bash
node -v
npm -v
~~~

4. Crea o abre un proyecto Express y React.
5. Instala las dependencias necesarias segun el bloque que vayas a practicar:

~~~bash
npm install express helmet express-validator
npm install dompurify
~~~

6. Si trabajas con un servidor real, revisa Certbot desde su pagina oficial y sigue las instrucciones especificas para tu sistema operativo y servidor web.

**Error comun:** instalar dependencias desde enlaces no oficiales.  
**Correccion:** usa siempre documentacion oficial o repositorios confiables.

## Nota tecnica breve

El concepto de token CSRF sigue siendo valido. Sin embargo, la dependencia `csurf` debe tratarse como ejemplo didactico y no como recomendacion automatica para proyectos nuevos, porque fue marcada como deprecada por el equipo de Express. Tambien debes considerar que `X-XSS-Protection` es un encabezado heredado: Helmet.js actualmente lo desactiva con valor `0`. En certificados SSL/TLS, prioriza siempre la renovacion automatica porque los periodos de vigencia pueden cambiar.

---

## Bloque 1 - HTTPS, HTTP y comunicacion segura

**Objetivo del bloque:**  
Diferenciar HTTP y HTTPS, identificando por que HTTPS es obligatorio en aplicaciones modernas.

**Concepto trabajado:**  
HTTPS, HTTP, cifrado en transito y SSL/TLS.

**Explicacion breve:**  
HTTP transmite datos sin cifrado. HTTPS cifra la comunicacion entre navegador y servidor usando SSL/TLS. Esto protege contraseñas, datos personales y formularios durante la transmision. El candado del navegador no significa que toda la aplicacion sea perfecta, pero si indica que la comunicacion viaja por un canal cifrado.

**Ejemplo guiado:**

~~~text
HTTP  : navegador -> datos sin cifrado -> servidor
HTTPS : navegador -> datos cifrados con TLS -> servidor
~~~

**Paso a paso:**

1. Abre un sitio que use `http://` y observa si el navegador muestra advertencia.
2. Abre un sitio que use `https://` y observa el candado o indicador de seguridad.
3. Identifica que HTTPS protege la comunicacion, no necesariamente toda la logica interna.

**Actividad para ti:**  
Escribe dos razones por las que una aplicacion con login debe usar HTTPS.

**Espacio para responder:**

1. ________________________________________________________________
2. ________________________________________________________________

**Resultado esperado:**  
Debes reconocer que HTTPS evita que los datos viajen en texto plano y mejora la confianza del usuario.

**Preguntas de validacion:**

1. ¿Que diferencia principal existe entre HTTP y HTTPS?
2. ¿Por que un formulario de login no deberia enviarse por HTTP?
3. ¿HTTPS reemplaza la validacion del backend?

**Error comun y correccion:**  
Error: pensar que HTTPS soluciona todos los problemas de seguridad.  
Correccion: HTTPS protege la comunicacion, pero tambien necesitas validacion, sanitizacion y controles adicionales.

---

## Bloque 2 - Certificados SSL/TLS y Let's Encrypt

**Objetivo del bloque:**  
Explicar que es un certificado SSL/TLS y como participa en una conexion HTTPS.

**Concepto trabajado:**  
Certificado SSL/TLS, clave publica, clave privada, dominio y autoridad certificadora.

**Explicacion breve:**  
Un certificado SSL/TLS funciona como una identificacion digital del servidor. Vincula una clave criptografica con un dominio u organizacion. La clave publica se comparte, la clave privada permanece protegida en el servidor y la autoridad certificadora valida la identidad del sitio.

**Ejemplo guiado:**

~~~text
Dominio: app-universitaria.com
Certificado: emitido para ese dominio
Clave publica: disponible para establecer cifrado
Clave privada: guardada solo en el servidor
Autoridad certificadora: valida el certificado
~~~

**Paso a paso:**

1. Revisa el certificado de un sitio HTTPS desde el navegador.
2. Identifica el dominio para el que fue emitido.
3. Observa la autoridad certificadora.
4. Relaciona el certificado con la confianza del navegador.

**Actividad para ti:**  
Completa la tabla.

| Elemento | ¿Para que sirve? |
|---|---|
| Clave publica | |
| Clave privada | |
| Autoridad certificadora | |
| Dominio | |

**Resultado esperado:**  
Debes poder explicar que el certificado autentica al servidor y permite establecer comunicacion cifrada.

**Preguntas de validacion:**

1. ¿Que protege la clave privada?
2. ¿Que valida una autoridad certificadora?
3. ¿Por que es importante renovar certificados?

**Error comun y correccion:**  
Error: compartir o subir la clave privada a un repositorio.  
Correccion: la clave privada debe mantenerse secreta en el servidor.

---

## Bloque 3 - HTTPS en Express y redireccion automatica

**Objetivo del bloque:**  
Reconocer la estructura basica para exponer una aplicacion Express mediante HTTPS y redirigir HTTP hacia HTTPS.

**Concepto trabajado:**  
Modulo `https` de Node.js, certificados, puerto 443, puerto 80 y middleware de redireccion.

**Explicacion breve:**  
Express define rutas y middlewares. Para servirlo por HTTPS, Node.js usa el modulo `https`, lee la clave privada y el certificado, y crea un servidor seguro. Ademas, se recomienda redirigir automaticamente cualquier acceso HTTP hacia HTTPS.

**Ejemplo guiado:**

Nombre del archivo sugerido: `app.js`  
Objetivo del codigo: crear un servidor HTTPS basico con Express.

~~~javascript
const express = require('express');
const https = require('https');
const fs = require('fs');

const aplicacion = express();

const opcionesSsl = {
  key: fs.readFileSync('/ruta/a/clave-privada.key'),
  cert: fs.readFileSync('/ruta/a/certificado.crt')
};

aplicacion.get('/', (peticion, respuesta) => {
  respuesta.send('Conexion segura con HTTPS establecida');
});

https.createServer(opcionesSsl, aplicacion)
  .listen(443, () => {
    console.log('Servidor HTTPS corriendo en puerto 443');
  });
~~~

**Explicacion paso a paso del codigo:**

1. `express` crea la aplicacion backend.
2. `https` permite crear un servidor seguro.
3. `fs` lee archivos del sistema.
4. `opcionesSsl` contiene la clave privada y el certificado.
5. `aplicacion.get('/')` define una ruta de prueba.
6. `https.createServer(...)` envuelve la aplicacion Express en un servidor HTTPS.
7. `listen(443)` usa el puerto estandar de HTTPS.

**Resultado esperado:**  
En consola deberia aparecer el mensaje: `Servidor HTTPS corriendo en puerto 443`.

**Actividad espejo:**  
Cambia el mensaje de la ruta `/` por: `Aplicacion segura funcionando`.

**Modificacion guiada:**  
Agrega una ruta `/estado` que responda: `Servidor activo`.

**Preguntas de validacion:**

1. ¿Por que se usa el modulo `https`?
2. ¿Que archivos se leen en `opcionesSsl`?
3. ¿Que puerto usa HTTPS por defecto?

**Error comun y correccion:**  
Error: colocar rutas incorrectas de certificado.  
Correccion: verificar que los archivos existan y que el proceso de Node tenga permisos para leerlos.

---

## Bloque 4 - XSS en React y sanitizacion con DOMPurify

**Objetivo del bloque:**  
Identificar riesgos XSS en contenido dinamico y aplicar sanitizacion cuando se necesita mostrar HTML.

**Concepto trabajado:**  
XSS reflejado, XSS almacenado, XSS basado en DOM, React, `dangerouslySetInnerHTML` y DOMPurify.

**Explicacion breve:**  
XSS ocurre cuando contenido no confiable termina ejecutandose como codigo en el navegador. React escapa contenido por defecto cuando se renderiza dentro de JSX. El riesgo aumenta cuando se usa `dangerouslySetInnerHTML`, porque inserta HTML directamente. Si necesitas mostrar HTML de usuario, primero debes sanitizarlo.

**Ejemplo guiado:**

Nombre del archivo sugerido: `EditorComentarios.jsx`  
Objetivo del codigo: mostrar HTML limitado despues de sanitizarlo.

~~~javascript
import { useState } from 'react';
import DOMPurify from 'dompurify';

function EditorComentarios() {
  const [contenidoHtml, setContenidoHtml] = useState('');

  const obtenerHtmlSeguro = (htmlSucio) => {
    return DOMPurify.sanitize(htmlSucio, {
      ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p', 'br'],
      ALLOWED_ATTR: []
    });
  };

  return (
    <div>
      <textarea
        value={contenidoHtml}
        onChange={(e) => setContenidoHtml(e.target.value)}
        placeholder="Escribe HTML permitido"
      />

      <div
        dangerouslySetInnerHTML={{
          __html: obtenerHtmlSeguro(contenidoHtml)
        }}
      />
    </div>
  );
}

export default EditorComentarios;
~~~

**Explicacion paso a paso del codigo:**

1. `useState` guarda el contenido escrito por el usuario.
2. `DOMPurify.sanitize` limpia el HTML recibido.
3. `ALLOWED_TAGS` define las etiquetas permitidas.
4. `ALLOWED_ATTR` evita permitir atributos HTML.
5. `dangerouslySetInnerHTML` se usa solo despues de sanitizar.
6. El contenido seguro se muestra en pantalla.

**Resultado esperado:**  
El componente permite formato HTML limitado y evita insertar contenido no autorizado.

**Actividad espejo:**  
Agrega la etiqueta `u` a la lista de etiquetas permitidas.

**Modificacion guiada:**  
Cambia el placeholder por un mensaje que indique claramente que solo se permite HTML basico.

**Preguntas de validacion:**

1. ¿Por que React protege por defecto al renderizar texto?
2. ¿Cuando se vuelve riesgoso `dangerouslySetInnerHTML`?
3. ¿Por que se define una lista de etiquetas permitidas?

**Error comun y correccion:**  
Error: usar `dangerouslySetInnerHTML` con contenido sin limpiar.  
Correccion: aplicar sanitizacion antes de renderizar.

---

## Bloque 5 - Validacion y sanitizacion en Express

**Objetivo del bloque:**  
Aplicar validacion y sanitizacion de entradas en el backend usando `express-validator`.

**Concepto trabajado:**  
Middleware, `body`, `validationResult`, `trim`, `escape`, `isLength` y respuesta con error 400.

**Explicacion breve:**  
Validar solo en frontend no es suficiente. Un usuario puede enviar datos directamente al backend. Por eso Express debe revisar, limpiar y limitar entradas antes de procesarlas o guardarlas.

**Ejemplo guiado:**

Nombre del archivo sugerido: `comentarios.js`  
Objetivo del codigo: validar nombre y comentario antes de responder.

~~~javascript
const express = require('express');
const { body, validationResult } = require('express-validator');

const aplicacion = express();
aplicacion.use(express.json());

aplicacion.post('/api/comentarios',
  [
    body('nombre')
      .trim()
      .escape()
      .isLength({ min: 3, max: 50 })
      .withMessage('El nombre debe tener entre 3 y 50 caracteres'),

    body('comentario')
      .trim()
      .escape()
      .isLength({ min: 10, max: 500 })
      .withMessage('El comentario debe tener entre 10 y 500 caracteres')
  ],
  (peticion, respuesta) => {
    const errores = validationResult(peticion);

    if (!errores.isEmpty()) {
      return respuesta.status(400).json({ errores: errores.array() });
    }

    const { nombre, comentario } = peticion.body;
    respuesta.json({ mensaje: 'Comentario validado de forma segura', nombre, comentario });
  }
);

aplicacion.listen(3001, () => {
  console.log('Servidor en puerto 3001');
});
~~~

**Explicacion paso a paso del codigo:**

1. `express.json()` permite leer datos JSON.
2. `body('nombre')` valida el campo `nombre`.
3. `trim()` elimina espacios al inicio y al final.
4. `escape()` convierte caracteres HTML especiales.
5. `isLength()` controla longitud minima y maxima.
6. `validationResult()` recoge errores.
7. Si hay errores, el servidor responde con estado `400`.
8. Si no hay errores, se procesa la respuesta segura.

**Resultado esperado:**  
Cuando los datos no cumplen las reglas, el backend devuelve errores. Cuando cumplen, responde con mensaje de validacion correcta.

**Actividad espejo:**  
Agrega una validacion para un campo `correo` con longitud minima de 8 caracteres.

**Modificacion guiada:**  
Cambia el maximo del comentario de 500 a 300 caracteres.

**Preguntas de validacion:**

1. ¿Por que el backend debe validar aunque React ya valide?
2. ¿Que hace `escape()`?
3. ¿Por que se responde con estado `400` ante errores?

**Error comun y correccion:**  
Error: guardar datos antes de validar.  
Correccion: ubicar la validacion antes del procesamiento principal.

---

## Bloque 6 - CSRF y tokens de proteccion

**Objetivo del bloque:**  
Interpretar el flujo de tokens CSRF entre backend y frontend.

**Concepto trabajado:**  
CSRF, token unico, solicitud sensible, validacion del servidor y error 403.

**Explicacion breve:**  
CSRF ocurre cuando una aplicacion externa intenta provocar una accion usando una sesion activa del usuario. La defensa explicada en esta sesion es usar tokens CSRF: el servidor genera un token, el cliente lo envia en solicitudes sensibles y el backend lo valida antes de procesar la accion.

**Ejemplo guiado:**

Nombre del archivo sugerido: `FormularioSeguro.jsx`  
Objetivo del codigo: enviar un token CSRF obtenido desde el backend.

~~~javascript
import { useState, useEffect } from 'react';

function FormularioSeguro() {
  const [tokenCsrf, setTokenCsrf] = useState('');
  const [nombre, setNombre] = useState('');

  useEffect(() => {
    fetch('/api/obtener-token-csrf')
      .then(respuesta => respuesta.json())
      .then(datos => setTokenCsrf(datos.tokenCsrf));
  }, []);

  const enviarFormulario = async (evento) => {
    evento.preventDefault();

    await fetch('/api/datos-importantes', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'CSRF-Token': tokenCsrf
      },
      body: JSON.stringify({ nombre })
    });

    alert('Datos enviados de forma segura');
  };

  return (
    <form onSubmit={enviarFormulario}>
      <input value={nombre} onChange={(e) => setNombre(e.target.value)} />
      <button type="submit">Enviar</button>
    </form>
  );
}

export default FormularioSeguro;
~~~

**Explicacion paso a paso del codigo:**

1. `tokenCsrf` almacena el token recibido del servidor.
2. `nombre` almacena el dato escrito por el usuario.
3. `useEffect` solicita el token al cargar el componente.
4. `fetch('/api/obtener-token-csrf')` obtiene el token.
5. `enviarFormulario` evita la recarga de pagina.
6. La solicitud `POST` incluye `CSRF-Token` en los encabezados.
7. El backend debe validar el token antes de procesar datos.

**Resultado esperado:**  
El formulario envia datos junto con el token. Si el backend valida correctamente, procesa la solicitud. Si el token falta o no coincide, debe rechazarla.

**Actividad espejo:**  
Agrega un segundo campo llamado `mensaje` y envialo junto con `nombre`.

**Modificacion guiada:**  
Cambia el texto del boton por `Guardar datos`.

**Preguntas de validacion:**

1. ¿Por que el token se obtiene desde el servidor?
2. ¿En que tipo de solicitudes se debe enviar el token?
3. ¿Que estado HTTP puede usar el servidor si el token es invalido?

**Error comun y correccion:**  
Error: escribir un token fijo dentro del frontend.  
Correccion: solicitar el token al servidor y renovarlo segun la estrategia definida.

---

## Bloque 7 - Helmet.js y encabezados de seguridad

**Objetivo del bloque:**  
Configurar Helmet.js en Express como capa adicional de encabezados HTTP de seguridad.

**Concepto trabajado:**  
Helmet.js, Content Security Policy, X-Frame-Options, X-Content-Type-Options y Strict-Transport-Security.

**Explicacion breve:**  
Helmet.js es una coleccion de middlewares para Express que configura encabezados HTTP relacionados con seguridad. Ayuda a reducir riesgos comunes mediante configuracion minima. No reemplaza HTTPS, validacion ni sanitizacion; los complementa.

**Ejemplo guiado:**

Nombre del archivo sugerido: `app.js`  
Objetivo del codigo: activar Helmet.js en una aplicacion Express.

~~~javascript
const express = require('express');
const helmet = require('helmet');

const aplicacion = express();

aplicacion.use(helmet());

aplicacion.get('/', (peticion, respuesta) => {
  respuesta.send('Aplicacion protegida con Helmet');
});

aplicacion.listen(3001, () => {
  console.log('Servidor corriendo en puerto 3001');
});
~~~

**Explicacion paso a paso del codigo:**

1. `helmet` se importa como dependencia.
2. `aplicacion.use(helmet())` registra el middleware.
3. Las rutas se declaran despues del middleware.
4. Las respuestas incluyen encabezados de seguridad configurados por Helmet.
5. El servidor se ejecuta en el puerto `3001`.

**Resultado esperado:**  
El servidor responde normalmente y añade encabezados de seguridad.

**Actividad espejo:**  
Agrega una ruta `/seguridad` que responda `Headers activos`.

**Modificacion guiada:**  
Ubica `aplicacion.use(helmet())` antes de las rutas y explica por que conviene hacerlo asi.

**Preguntas de validacion:**

1. ¿Para que sirve Helmet.js?
2. ¿Helmet reemplaza DOMPurify?
3. ¿Por que los encabezados HTTP ayudan al navegador?

**Error comun y correccion:**  
Error: creer que Helmet vuelve segura toda la aplicacion por si solo.  
Correccion: usar Helmet como parte de una defensa por capas.

---

## Bloque 8 - Auditoria con Lighthouse

**Objetivo del bloque:**  
Usar Lighthouse como auditoria inicial para revisar buenas practicas de una aplicacion web.

**Concepto trabajado:**  
Lighthouse, Chrome DevTools, linea de comandos, mejores practicas, HTTPS y encabezados.

**Explicacion breve:**  
Lighthouse es una herramienta automatizada para evaluar calidad web. Permite revisar rendimiento, accesibilidad, SEO, PWA y mejores practicas. En esta sesion se usa como apoyo para detectar señales de seguridad basica, como HTTPS, librerias vulnerables y encabezados.

**Ejemplo guiado:**

Nombre del procedimiento: auditoria desde terminal.  
Objetivo: generar un reporte de buenas practicas.

~~~bash
npm install -g lighthouse
lighthouse https://mi-aplicacion.com --only-categories=best-practices --view
~~~

**Paso a paso:**

1. Instala Lighthouse globalmente con npm.
2. Ejecuta la auditoria indicando una URL HTTPS.
3. Usa `--only-categories=best-practices` para enfocarte en buenas practicas.
4. Usa `--view` para abrir el reporte en el navegador.
5. Revisa advertencias relacionadas con HTTPS, librerias y encabezados.

**Resultado esperado:**  
Se abre un reporte Lighthouse en el navegador con una puntuacion y recomendaciones.

**Actividad para ti:**  
Ejecuta Lighthouse sobre una URL de prueba o aplicacion propia. Anota tres hallazgos.

**Espacio para responder:**

1. ________________________________________________________________
2. ________________________________________________________________
3. ________________________________________________________________

**Preguntas de validacion:**

1. ¿Que categoria de Lighthouse se relaciona mas con esta sesion?
2. ¿Por que Lighthouse no reemplaza una auditoria completa de seguridad?
3. ¿Que mejora aplicarias si Lighthouse detecta falta de encabezados?

**Error comun y correccion:**  
Error: interpretar el puntaje como garantia total de seguridad.  
Correccion: usar Lighthouse como auditoria inicial y complementar con pruebas adicionales cuando corresponda.

---

## Actividades integradoras

### Actividad 1 - Reconocimiento y correccion guiada

Lee el siguiente diagnostico y marca que practica falta.

> Una aplicacion permite login, usa React y Express, pero todavia se publica por HTTP.

**Practica faltante:** ________________________________________________

**Justificacion breve:** ______________________________________________

### Actividad 2 - Interpretacion y aplicacion

Relaciona cada riesgo con su defensa principal.

| Riesgo | Defensa |
|---|---|
| Datos viajan sin cifrado | |
| HTML de usuario se muestra directamente | |
| Formulario sensible sin token | |
| Respuestas sin encabezados de seguridad | |
| Falta de revision de buenas practicas | |

### Actividad 3 - Modelado o construccion

Diseña una checklist de 6 pasos para revisar una aplicacion Express y React antes de publicarla.

1. ________________________________________________________________
2. ________________________________________________________________
3. ________________________________________________________________
4. ________________________________________________________________
5. ________________________________________________________________
6. ________________________________________________________________

### Actividad 4 - Integracion estructural

Describe el flujo completo de seguridad para una accion `POST` importante desde React hacia Express.

**Respuesta:**

____________________________________________________________________

____________________________________________________________________

____________________________________________________________________

## Checklist final de aprendizaje

Marca cada criterio cuando lo cumplas.

- [ ] Diferencio HTTP y HTTPS.
- [ ] Explico para que sirve un certificado SSL/TLS.
- [ ] Reconozco el rol de la clave publica y clave privada.
- [ ] Comprendo por que Express debe validar datos.
- [ ] Identifico el riesgo de `dangerouslySetInnerHTML`.
- [ ] Se cuando aplicar DOMPurify.
- [ ] Interpreto el flujo de token CSRF.
- [ ] Entiendo que Helmet.js configura encabezados de seguridad.
- [ ] Puedo ejecutar una auditoria basica con Lighthouse.
- [ ] Entiendo que la seguridad web requiere varias capas.

## Resumen tecnico

Una aplicacion web segura requiere proteger la comunicacion, controlar datos no confiables, validar entradas, proteger acciones sensibles, configurar encabezados de seguridad y auditar periodicamente. HTTPS, DOMPurify, `express-validator`, tokens CSRF, Helmet.js y Lighthouse cumplen funciones distintas dentro de una misma estrategia de defensa por capas.

## Tabla de decisiones e impactos

| Decision tecnica | Impacto esperado |
|---|---|
| Activar HTTPS | Protege la comunicacion entre navegador y servidor |
| Redirigir HTTP a HTTPS | Evita accesos inseguros por enlaces antiguos o errores de usuario |
| Sanitizar HTML con DOMPurify | Reduce riesgos al mostrar contenido enriquecido |
| Validar en Express | Evita procesar datos incorrectos o no confiables |
| Usar tokens CSRF | Verifica solicitudes sensibles |
| Activar Helmet.js | Agrega encabezados HTTP de seguridad |
| Auditar con Lighthouse | Detecta oportunidades de mejora iniciales |

## Autoevaluacion profesional

Responde con tus propias palabras.

1. ¿Que parte de la seguridad web se resuelve con HTTPS?
2. ¿Que diferencia existe entre XSS y CSRF?
3. ¿Por que no basta con validar en React?
4. ¿Que aporta Helmet.js dentro de Express?
5. ¿Como usarias Lighthouse despues de hacer mejoras?

## Cierre de la practica

Has revisado una ruta completa de seguridad inicial para aplicaciones web modernas. El siguiente paso es aplicar esta checklist en un proyecto real o academico, documentar los hallazgos y repetir la auditoria despues de corregir.

## Continuacion formativa

- Canal YouTube: https://www.youtube.com/@LideratecAcademy
- Blog: https://lideratecacademy.com/

