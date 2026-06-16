---
title: "Guia del estudiante - Autenticacion y seguridad web"
author: "Elaborado por el docente"
lang: es
geometry: margin=2cm
fontsize: 11pt
---

# Guia del estudiante - Implementacion de autenticacion y seguridad web

**Elaborado por el docente**  
**Proyecto academico:** Lideratec Academy  
**Blog:** https://lideratecacademy.com/  
**Canal YouTube:** https://www.youtube.com/@LideratecAcademy  

---

## 1. Proposito de la practica

En esta practica aprenderas a construir y validar el flujo basico de seguridad de una API web usando:

- JSON Web Tokens, o JWT, para autenticar usuarios.
- CORS para controlar que origenes pueden comunicarse con la API.
- bcrypt.js para proteger contrasenas mediante hashing.
- Middlewares de Express para proteger rutas.
- Roles para autorizar el acceso a funciones especificas.

La meta no es copiar codigo sin entenderlo. La meta es que puedas explicar que problema resuelve cada parte y como se conectan dentro de una API.

---

## 2. Resultado de aprendizaje observable

Al finalizar la practica, podras **aplicar** un flujo basico de autenticacion y seguridad web en una API con Node.js y Express, integrando JWT, CORS, bcrypt.js, proteccion de rutas y autorizacion basada en roles.

---

## 3. Duracion sugerida

- Lectura guiada: 25 minutos.
- Preparacion del entorno: 20 minutos.
- Ejecucion de ejemplos: 50 minutos.
- Actividades progresivas: 60 minutos.
- Checklist y validacion final: 15 minutos.

Duracion total sugerida: 2 horas y 50 minutos.

---

## 4. Requisitos previos minimos

Antes de iniciar, debes reconocer:

- Que es una API.
- Que es una ruta o endpoint en Express.
- Que es una peticion HTTP.
- Que significa enviar datos en JSON.
- Que es un middleware en Express.
- Como ejecutar comandos basicos en una terminal.

No necesitas dominar seguridad web avanzada. Esta guia parte de un nivel universitario inicial.

---

## 5. Preparacion del entorno

### 5.1 Herramientas requeridas

| Herramienta | Para que sirve | Enlace oficial |
|---|---|---|
| Node.js | Ejecutar JavaScript en el servidor | https://nodejs.org/en/download |
| npm | Instalar paquetes del proyecto | https://docs.npmjs.com/downloading-and-installing-node-js-and-npm/ |
| Visual Studio Code u otro editor | Escribir y organizar codigo | https://code.visualstudio.com/ |
| Navegador web | Probar y revisar resultados | Usa tu navegador habitual |
| Terminal | Ejecutar comandos del proyecto | Terminal del sistema o terminal integrada del editor |

### 5.2 Validar Node.js y npm

Abre una terminal y ejecuta:

```bash
node -v
npm -v
```

**Que es:**  
Estos comandos muestran las versiones instaladas de Node.js y npm.

**Para que sirve:**  
Permiten confirmar que puedes ejecutar JavaScript en servidor e instalar paquetes.

**Resultado esperado:**  
La terminal debe mostrar dos numeros de version, por ejemplo:

```bash
v24.x.x
11.x.x
```

El numero exacto puede variar. Lo importante es que ambos comandos respondan.

**Error comun:**  
La terminal indica que `node` o `npm` no se reconoce.

**Como corregir:**  
Instala Node.js desde el sitio oficial y vuelve a abrir la terminal.

---

## 6. Crear proyecto base

### Paso 1: Crear carpeta de trabajo

```bash
mkdir seguridad-web-api
cd seguridad-web-api
```

**Que hace:**  
Crea una carpeta para el proyecto y entra en ella.

### Paso 2: Inicializar npm

```bash
npm init -y
```

**Que hace:**  
Crea el archivo `package.json`, que registra la informacion basica del proyecto y sus dependencias.

### Paso 3: Activar modulos ES

Abre `package.json` y agrega la propiedad `"type": "module"`.

Ejemplo:

```json
{
  "name": "seguridad-web-api",
  "version": "1.0.0",
  "type": "module",
  "main": "index.js"
}
```

**Por que se usa en esta sesion:**  
Permite usar `import` y `export`, que aparecen en los ejemplos de Express, CORS, bcrypt.js y middlewares.

---

## 7. Instalar dependencias

Ejecuta:

```bash
npm install express jsonwebtoken cors bcryptjs dotenv
```

### Que instala cada paquete

| Paquete | Que es | Para que sirve en esta practica |
|---|---|---|
| express | Framework web para Node.js | Crear la API y sus rutas |
| jsonwebtoken | Libreria para JWT | Generar y verificar tokens |
| cors | Middleware CORS para Express | Controlar origenes permitidos |
| bcryptjs | Libreria de hashing | Proteger contrasenas |
| dotenv | Carga variables de entorno | Guardar configuraciones como el secreto JWT |

**Resultado esperado:**  
Se crea la carpeta `node_modules` y se actualiza `package.json`.

**Error comun:**  
Ejecutar el comando fuera de la carpeta del proyecto.

**Como verificar:**  
El archivo `package.json` debe mostrar las dependencias instaladas.

---

## 8. Estructura sugerida del proyecto

Crea estos archivos:

```text
seguridad-web-api/
├── .env
├── package.json
├── src/
│   ├── server.js
│   ├── auth.js
│   └── middlewares.js
```

**Que representa:**  

- `.env`: guarda configuraciones locales.
- `server.js`: crea el servidor y las rutas.
- `auth.js`: contiene funciones de contrasena y token.
- `middlewares.js`: contiene validacion de token y roles.

---

## 9. Configurar variables de entorno

Archivo sugerido: `.env`

```env
JWT_SECRET=mi_secreto_de_clase
PORT=3001
```

**Que es:**  
Un archivo para guardar valores de configuracion.

**Para que sirve:**  
Evita escribir el secreto JWT directamente dentro del codigo principal.

**Por que se usa:**  
El secreto permite firmar y verificar tokens. En una practica puede ser simple, pero no debe confundirse con una configuracion final de produccion.

**Error comun:**  
Escribir `JWT_SECRET` en el codigo y olvidarse de modificarlo luego.

**Como verificar:**  
El servidor debe poder leer `process.env.JWT_SECRET`.

---

## 10. Bloque 1 - JWT para autenticacion

### 10.1 Concepto base

Un JSON Web Token, o JWT, es un formato seguro para enviar informacion entre cliente y servidor. Se usa principalmente para confirmar la identidad de un usuario.

Un JWT tiene tres partes:

```text
HEADER.PAYLOAD.SIGNATURE
```

| Parte | Funcion |
|---|---|
| Header | Indica tipo de token y algoritmo |
| Payload | Contiene datos como id, rol o email |
| Signature | Permite verificar que el token no fue modificado |

### 10.2 Codigo de generacion de token

Archivo sugerido: `src/auth.js`

```js
import jwt from "jsonwebtoken";

export function generarToken(usuario) {
  return jwt.sign(
    { id: usuario.id, rol: usuario.rol },
    process.env.JWT_SECRET,
    { expiresIn: "1h" }
  );
}
```

### Explicacion paso a paso

- `import jwt from "jsonwebtoken";` importa la libreria que permite crear y verificar tokens.
- `generarToken(usuario)` recibe un objeto de usuario.
- `{ id: usuario.id, rol: usuario.rol }` define el payload del token.
- `process.env.JWT_SECRET` usa el secreto configurado en `.env`.
- `{ expiresIn: "1h" }` indica que el token vence en una hora.
- `return jwt.sign(...)` devuelve el token firmado.

### Resultado esperado

La funcion devuelve una cadena de texto con estructura similar a:

```text
xxxxx.yyyyy.zzzzz
```

No necesitas memorizar el token. Debes reconocer que tiene tres partes separadas por puntos.

### Error comun

Incluir la contrasena del usuario dentro del payload.

### Correccion

El payload debe contener datos minimos, como id y rol. No debe transportar la contrasena.

---

## 11. Bloque 2 - bcrypt.js para proteger contrasenas

### 11.1 Concepto base

bcrypt.js permite proteger contrasenas antes de guardarlas. No se guarda la contrasena real. Se guarda un hash irreversible.

Flujo:

```text
contrasena -> salt -> cost -> hash final
```

### 11.2 Codigo para generar hash

Archivo sugerido: `src/auth.js`

Agrega debajo de la funcion `generarToken`:

```js
import bcrypt from "bcryptjs";

export function crearHash(password) {
  const salt = bcrypt.genSaltSync(10);
  const hash = bcrypt.hashSync(password, salt);
  return hash;
}
```

### Explicacion paso a paso

- `bcrypt` permite crear hashes y comparar contrasenas.
- `crearHash(password)` recibe una contrasena ingresada.
- `genSaltSync(10)` crea un salt con 10 rondas de procesamiento (10 rondas de generacion de caracteres aleatorios).
- `hashSync(password, salt)` combina la contrasena con el salt y genera un hash.
- `return hash` devuelve el valor que se guardaria en la base de datos.

### Resultado esperado

Un hash largo, similar a:

```text
$2b$10$...
```

### Error comun

Guardar la contrasena original junto con el hash.

### Correccion

Solo debe guardarse el hash. La contrasena original no se almacena.

---

## 12. Bloque 3 - Comparar contrasena en login

Archivo sugerido: `src/auth.js`

Agrega:

```js
export function compararPassword(passwordIngresado, hashGuardado) {
  return bcrypt.compareSync(passwordIngresado, hashGuardado);
}
```

### Explicacion paso a paso

- `passwordIngresado` es la contrasena que escribe el usuario al iniciar sesion.
- `hashGuardado` representa el hash que estaria almacenado.
- `compareSync` compara ambos valores.
- Si coinciden, devuelve `true`.
- Si no coinciden, devuelve `false`.

### Resultado esperado

```text
true
```

si la contrasena coincide, o:

```text
false
```

si no coincide.

### Error comun

Intentar "desencriptar" el hash.

### Correccion

El hash no se recupera. Se compara.

---

## 13. Bloque 4 - Middleware verificarToken

Archivo sugerido: `src/middlewares.js`

```js
import jwt from "jsonwebtoken";

export function verificarToken(req, res, next) {
  const token = req.headers.authorization?.split(" ")[1];

  if (!token) {
    return res.status(401).json({
      msg: "Acceso denegado. No hay token."
    });
  }

  try {
    const usuario = jwt.verify(token, process.env.JWT_SECRET);
    req.usuario = usuario;
    next();
  } catch (error) {
    return res.status(403).json({
      msg: "Token invalido o expirado."
    });
  }
}
```

### Explicacion paso a paso

- `req.headers.authorization` lee la cabecera `Authorization`.
- `split(" ")[1]` extrae el token cuando llega como `Bearer <token>`.
- Si no hay token, responde con estado 401.
- `jwt.verify` verifica que el token sea valido.
- Si es valido, se guarda el usuario en `req.usuario`.
- `next()` permite continuar hacia la ruta.
- Si falla la verificacion, responde 403.

### Resultado esperado

- Sin token: respuesta 401.
- Token invalido o vencido: respuesta 403.
- Token valido: la ruta continua.

### Error comun

Llamar `next()` aunque el token sea invalido.

### Correccion

Solo se llama `next()` cuando `jwt.verify` fue exitoso.

---

## 14. Bloque 5 - Middleware permitirRoles

Archivo sugerido: `src/middlewares.js`

Agrega:

```js
export function permitirRoles(...rolesPermitidos) {
  return (req, res, next) => {
    const rolUsuario = req.usuario?.rol;

    if (!rolesPermitidos.includes(rolUsuario)) {
      return res.status(403).json({
        msg: "No tienes permisos para acceder a esta ruta."
      });
    }

    next();
  };
}
```

### Explicacion paso a paso

- `permitirRoles(...rolesPermitidos)` recibe uno o varios roles.
- `req.usuario?.rol` obtiene el rol del usuario autenticado.
- `includes` verifica si el rol esta dentro de la lista permitida.
- Si no esta permitido, responde 403.
- Si esta permitido, ejecuta `next()`.

### Resultado esperado

Un usuario con rol `admin` puede entrar a una ruta administrativa. Un usuario sin ese rol recibe 403.

### Error comun

Usar `permitirRoles` antes de `verificarToken`.

### Correccion

Primero se valida el token. Luego se revisa el rol.

---

## 15. Bloque 6 - CORS en Express

Archivo sugerido: `src/server.js`

```js
import express from "express";
import cors from "cors";
import dotenv from "dotenv";
import { generarToken, crearHash, compararPassword } from "./auth.js";
import { verificarToken, permitirRoles } from "./middlewares.js";

dotenv.config();

const app = express();

app.use(cors({
  origin: "http://localhost:5173",
  methods: ["GET", "POST", "PUT", "DELETE"],
  credentials: true
}));

app.use(express.json());
```

### Explicacion paso a paso

- `cors` permite configurar que origen puede comunicarse con la API desde el navegador.
- `origin: "http://localhost:5173"` representa el cliente permitido en esta practica.
- `methods` define metodos HTTP permitidos.
- `credentials: true` indica que se permiten credenciales cuando corresponda.
- `express.json()` permite leer cuerpos JSON.

### Resultado esperado

La API queda preparada para recibir peticiones desde el origen definido.

### Error comun

Usar `app.use(cors())` y asumir que siempre es la mejor configuracion.

### Correccion

Para pruebas puede ser util, pero en una configuracion mas segura se define el origen permitido.

---

## 16. Bloque 7 - Rutas de practica

Archivo sugerido: `src/server.js`

Agrega debajo de la configuracion inicial:

```js
const usuarioDemo = {
  id: 10,
  nombre: "Otto",
  email: "otto@gmail.com",
  rol: "admin",
  hash: crearHash("123456")
};

app.post("/login", (req, res) => {
  const { email, password } = req.body;

  if (email !== usuarioDemo.email) {
    return res.status(401).json({ msg: "Credenciales invalidas." });
  }

  const coincide = compararPassword(password, usuarioDemo.hash);

  if (!coincide) {
    return res.status(401).json({ msg: "Credenciales invalidas." });
  }

  const token = generarToken(usuarioDemo);

  res.json({
    msg: "Login correcto.",
    token
  });
});

app.get("/perfil", verificarToken, (req, res) => {
  res.json({
    msg: "Bienvenido a tu perfil.",
    usuario: req.usuario
  });
});

app.get("/admin/dashboard", verificarToken, permitirRoles("admin"), (req, res) => {
  res.json({
    msg: "Bienvenido al panel administrativo."
  });
});

const port = process.env.PORT || 3001;

app.listen(port, () => {
  console.log(`Servidor listo en http://localhost:${port}`);
});
```

### Explicacion paso a paso

- `usuarioDemo` simula un usuario registrado.
- El hash se genera con `crearHash("123456")`.
- `/login` valida email y password.
- Si la contrasena coincide, genera un JWT.
- `/perfil` requiere token valido.
- `/admin/dashboard` requiere token valido y rol `admin`.
- `app.listen` inicia el servidor.

### Resultado esperado

Al iniciar el servidor, la terminal debe mostrar:

```text
Servidor listo en http://localhost:3001
```

---

## 17. Ejecutar el servidor

Agrega este script en `package.json`:

```json
"scripts": {
  "dev": "node src/server.js"
}
```

Ejecuta:

```bash
npm run dev
```

**Resultado esperado:**

```text
Servidor listo en http://localhost:3001
```

---

## 18. Pruebas sugeridas

Puedes usar una herramienta de peticiones HTTP o una extension de cliente REST.

### 18.1 Probar login correcto

Metodo: `POST`  
URL: `http://localhost:3001/login`  
Body JSON:

```json
{
  "email": "otto@gmail.com",
  "password": "123456"
}
```

Resultado esperado:

```json
{
  "msg": "Login correcto.",
  "token": "..."
}
```

### 18.2 Probar perfil sin token

Metodo: `GET`  
URL: `http://localhost:3001/perfil`

Resultado esperado:

```json
{
  "msg": "Acceso denegado. No hay token."
}
```

### 18.3 Probar perfil con token

Metodo: `GET`  
URL: `http://localhost:3001/perfil`  
Header:

```text
Authorization: Bearer PEGA_AQUI_TU_TOKEN
```

Resultado esperado:

```json
{
  "msg": "Bienvenido a tu perfil.",
  "usuario": {
    "id": 10,
    "rol": "admin",
    "iat": 0000000000,
    "exp": 0000000000
  }
}
```

### 18.4 Probar dashboard admin

Metodo: `GET`  
URL: `http://localhost:3001/admin/dashboard`  
Header:

```text
Authorization: Bearer PEGA_AQUI_TU_TOKEN
```

Resultado esperado:

```json
{
  "msg": "Bienvenido al panel administrativo."
}
```

---

## 19. Actividades para resolver

### Actividad 1 - Identificacion de responsabilidades

Completa la tabla:

| Elemento | Responsabilidad | Error comun |
|---|---|---|
| JWT | | |
| CORS | | |
| bcrypt.js | | |
| verificarToken | | |
| permitirRoles | | |

### Actividad 2 - Orden del flujo

Ordena estos pasos:

- El servidor genera un JWT.
- El usuario envia email y password.
- La API verifica el token.
- bcrypt.js compara la contrasena con el hash.
- El cliente envia el token en la cabecera.
- permitirRoles valida si el rol puede acceder.

Espacio de respuesta:

```text
1.
2.
3.
4.
5.
6.
```

### Actividad 3 - Modificacion guiada

Modifica el rol del usuario demo de `admin` a `usuario`.

Luego responde:

1. Que ocurre cuando intentas acceder a `/perfil`?
2. Que ocurre cuando intentas acceder a `/admin/dashboard`?
3. Por que las respuestas son diferentes?

Espacio de respuesta:

```text
1.

2.

3.
```

### Actividad 4 - CORS seguro

Explica por que esta configuracion es mas controlada que `app.use(cors())`:

```js
app.use(cors({
  origin: "http://localhost:5173",
  methods: ["GET", "POST", "PUT", "DELETE"],
  credentials: true
}));
```

Espacio de respuesta:

```text

```

---

## 20. Preguntas de validacion

Responde con tus propias palabras:

1. Cual es la diferencia entre autenticacion y autorizacion?
2. Por que no se guarda la contrasena original?
3. Que significa `HEADER.PAYLOAD.SIGNATURE`?
4. Que indica una respuesta 401?
5. Que indica una respuesta 403?
6. Por que `permitirRoles` debe ejecutarse despues de `verificarToken`?
7. Que problema controla CORS?
8. Que problema controla bcrypt.js?

---

## 21. Errores comunes a evitar

| Error | Consecuencia | Correccion |
|---|---|---|
| Guardar contrasena original | Exposicion ante filtraciones | Guardar solo hash |
| Enviar token sin `Bearer` y extraer mal | La verificacion falla | Usar una convencion clara |
| Usar CORS abierto sin criterio | API demasiado permisiva | Definir origenes permitidos |
| Validar rol antes del token | Usuario no autenticado llega a autorizacion | Primero `verificarToken` |
| Incluir demasiados datos en JWT | Exposicion innecesaria | Payload minimo |
| Confundir CORS con login | Depuracion incorrecta | JWT autentica, CORS controla origen |

---

## 22. Checklist final de aprendizaje

Marca lo que ya puedes hacer:

- [ ] Explicar que es un JWT.
- [ ] Identificar Header, Payload y Signature.
- [ ] Generar un JWT con datos minimos.
- [ ] Verificar un JWT antes de permitir una ruta privada.
- [ ] Explicar por que CORS controla origenes.
- [ ] Generar un hash con bcrypt.js.
- [ ] Comparar una contrasena contra un hash.
- [ ] Diferenciar ruta publica, privada y administrativa.
- [ ] Usar `verificarToken` antes de una ruta privada.
- [ ] Usar `permitirRoles("admin")` antes de una ruta administrativa.

---

## 23. Cierre de la practica

En esta practica construiste el flujo esencial de seguridad para una API: validar credenciales, generar un JWT, proteger rutas, controlar origenes con CORS, proteger contrasenas con bcrypt.js y restringir funciones mediante roles.

La idea principal es que una API segura no depende de una sola herramienta. Depende de una cadena de decisiones correctas.

**Refuerzo recomendado:**  
Revisa los recursos de Lideratec Academy para continuar practicando seguridad web aplicada.

**Blog:** https://lideratecacademy.com/  
**Canal YouTube:** https://www.youtube.com/@LideratecAcademy  
