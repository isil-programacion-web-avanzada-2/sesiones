---
course_id: ISIL-PWA
session_id: S04
module_id: TEMA-04
source_origin: PPT
status: validated
---

# Guía del Estudiante - Manejo de formularios y conexión con APIs en Angular

## 1. Propósito de la sesión
En esta clase-laboratorio construirás una aplicación Angular con formularios reactivos, navegación, protección de rutas, consumo HTTP y conexión con una API REST en Node.js. El trabajo termina con un pequeño gestor de usuarios que permite consultar, crear, actualizar y eliminar datos en memoria.

La sesión está organizada para que escribas el código de forma incremental. No necesitas FASE 15 para descubrir ningún paso: todos los comandos, rutas, archivos y bloques necesarios aparecen aquí. FASE 15 queda como recurso opcional para validar o iniciar desde un proyecto ya preparado.

## 2. Resultado observable
### Lectura sugerida del docente
Al finalizar no bastará con que la aplicación "corra". Deberás poder explicar por qué un `FormControl` cambia de válido a inválido, cómo un `FormGroup` agrupa controles, qué hace un validador personalizado, cómo el Router asocia URL y componente, qué decisión toma un guard, cómo `HttpClient` produce un Observable y cómo una petición HTTP llega finalmente a un endpoint de Express. También deberás interpretar códigos de estado básicos, distinguir GET/POST/PUT/DELETE y comprobar que el frontend refleja los cambios del backend. Ejecutar es una evidencia; comprender la relación entre entrada, operación, cambio de estado y salida es el criterio de dominio.

### Desempeños observables
- Construir controles y grupos reactivos con validadores.
- Mostrar errores con `@if` y recorrer datos con `@for`.
- Definir rutas y activar un guard funcional.
- Levantar una API REST de laboratorio con Node.js y Express.
- Consumir GET/POST/PUT/DELETE desde Angular.
- Interpretar Observables y manejo básico de errores con RxJS.
- Diagnosticar fallas de validación, navegación o conexión.
- Validar el flujo completo Angular -> HTTP -> Node.js -> respuesta.

### Criterio de dominio
Existe dominio cuando puedes modificar una validación o endpoint, predecir el efecto antes de ejecutar, comprobarlo y explicar qué parte del código produjo el resultado.

## 3. Antes de iniciar
### Debe saber
- TypeScript básico: variables, funciones, objetos y clases.
- Componentes standalone de Angular vistos previamente.
- Uso básico de terminal y Visual Studio Code.

### Debe tener disponible
- Node.js 24 LTS recomendado.
- npm.
- Visual Studio Code u otro editor equivalente.
- Navegador moderno.
- Acceso a terminal.

### No se asumirá todavía
- Autenticación JWT real.
- Persistencia en base de datos.
- Interceptores HTTP avanzados.
- Arquitectura de producción.

## 4. Actualización técnica aplicada a la práctica
La sesión trabaja con Angular 20 y la configuración base muestra `HttpClientModule` importado directamente en un componente. Para mantener la competencia pero trabajar con una configuración standalone actual, esta guía usa `provideHttpClient()` en `app.config.ts` y `provideRouter(routes)`. Angular 20.2/20.3 admite Node 20.19+, 22.12+ o 24.x; para la práctica se recomienda Node 24 LTS. También se usa `router.createUrlTree(['/'])` en el guard para representar la redirección como resultado del guard. En Express no se instala `body-parser`, porque `express.json()` cubre el parseo JSON requerido por este laboratorio.

Referencias técnicas verificadas:
- https://angular.dev/reference/versions
- https://angular.dev/guide/http/setup
- https://angular.dev/api/router/provideRouter
- https://angular.dev/guide/routing/define-routes
- https://nodejs.org/en/blog/release

## 5. Preparación del entorno desde cero
### Ruta A - El entorno ya existe
Ejecuta:
```bash
node --version
npm --version
```
Comprueba que Node corresponde a una línea compatible. Para esta sesión se recomienda Node 24 LTS.

### Ruta B - Crear el proyecto Angular
En una carpeta de trabajo:
```bash
npx @angular/cli@20 new angular-api-lab --standalone --routing --style=css --skip-tests 
cd angular-api-lab
code .
```
**Qué hace:** crea un workspace Angular 20 standalone, habilita routing, usa CSS, evita tests para reducir ruido de laboratorio y utiliza nombres como `app.component.ts`.

**Primera validación:**
```bash
npm start
```
Abre `http://localhost:4200`. Si aparece la aplicación Angular, detén momentáneamente el servidor con `Ctrl+C` para continuar creando archivos.

### Configuración base moderna
Reemplaza `src/app/app.config.ts` por:
```ts
import { ApplicationConfig } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient } from '@angular/common/http';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [provideRouter(routes), provideHttpClient()]
};

```
La aplicación registra el Router y `HttpClient` desde el inyector raíz. `provideHttpClient()` reemplaza para esta práctica la importación repetida de `HttpClientModule` en componentes standalone.

# EJ01 - FormControl: un campo, un estado y dos validaciones

## Qué vamos a construir
un campo de correo controlado desde TypeScript, con estado y mensaje de error.

## Qué aprenderás aquí
Relacionar valor, validadores y estados `valid`, `invalid` y `touched`.

## Archivos que vamos a crear o modificar
- `src/app/features/ej01-control/email-control.ts`
- `src/app/features/ej01-control/email-control.html`

```ts
ng generate component features/ej01-control/email-control --standalone --skip-tests
```

## Punto de partida
Usa el estado final del ejemplo anterior. Si es EJ01, parte del proyecto base preparado en las secciones iniciales.


## Paso 1 - Importar las piezas del formulario

### Escribe / ejecuta
```ts
import { Component } from '@angular/core';
import { FormControl, ReactiveFormsModule, Validators } from '@angular/forms';
```

### Qué significa
`FormControl` modela el campo; `Validators` aporta reglas; `ReactiveFormsModule` habilita `[formControl]`.

### Por qué lo hacemos ahora
Primero declaramos dependencias para que el resto del archivo tenga significado.

### Qué debería ocurrir
El editor debe reconocer los símbolos sin errores de nombre.


## Paso 2 - Declarar el componente y el control

### Escribe / ejecuta
```ts
@Component({
  selector: 'app-email-control',
  standalone: true,
  imports: [ReactiveFormsModule],
  templateUrl: './email-control.component.html'
})
export class EmailControlComponent {
  emailControl = new FormControl('', [Validators.required, Validators.email]);
}
```

### Qué significa
El valor inicial es vacío. `required` rechaza vacío y `email` exige formato.

### Por qué lo hacemos ahora
Antes de mostrar la interfaz necesitamos un estado que pueda observarse.

### Qué debería ocurrir
`emailControl.valid` inicia en `false`.


## Paso 3 - Agregar una acción que marque interacción

### Escribe / ejecuta
```ts
validar(): void {
  this.emailControl.markAsTouched();
}
```

### Qué significa
`touched` permite decidir cuándo mostrar mensajes para no marcar error antes de interacción.

### Por qué lo hacemos ahora
Se agrega después del control porque actúa sobre él.

### Qué debería ocurrir
Al pulsar el botón, `touched` pasa a `true`.


## Paso 4 - Vincular el input al control

### Escribe / ejecuta
```html
<label for="email">Correo</label>
<input id="email" type="email" [formControl]="emailControl" placeholder="estudiante@isil.pe">
<button type="button" (click)="validar()">Validar</button>
```

### Qué significa
`[formControl]` conecta el input con la instancia creada en TypeScript.

### Por qué lo hacemos ahora
Primero existe el estado; después conectamos la vista a ese estado.

### Qué debería ocurrir
Escribir en el input modifica `emailControl.value`.


## Paso 5 - Mostrar estado y error

### Escribe / ejecuta
```html
<p>Valor actual: {{ emailControl.value }}</p>
<p>Estado válido: {{ emailControl.valid }}</p>
@if (emailControl.invalid && emailControl.touched) {
  <p class="error">Ingresa un correo válido y obligatorio.</p>
}
```

### Qué significa
`@if` consulta el estado reactivo y solo muestra el error cuando corresponde.

### Por qué lo hacemos ahora
La validación ya existe; ahora la hacemos observable para el estudiante.

### Qué debería ocurrir
Vacío o `abc` muestran error tras tocar; un correo válido no.


## Archivo completo al terminar este ejemplo


```ts
import { Component } from '@angular/core';
import { FormControl, ReactiveFormsModule, Validators } from '@angular/forms';

@Component({
  selector: 'app-email-control',
  standalone: true,
  imports: [ReactiveFormsModule],
  templateUrl: './email-control.component.html'
})
export class EmailControlComponent {
  emailControl = new FormControl('', [Validators.required, Validators.email]);

  validar(): void {
    this.emailControl.markAsTouched();
  }
}
```


```html
<section class="card">
  <h2>EJ01 - FormControl y Validators</h2>
  <label for="email">Correo</label>
  <input id="email" type="email" [formControl]="emailControl" placeholder="estudiante@isil.pe">
  <button type="button" (click)="validar()">Validar</button>
  <p>Valor actual: {{ emailControl.value }}</p>
  <p>Estado válido: {{ emailControl.valid }}</p>
  @if (emailControl.invalid && emailControl.touched) {
    <p class="error">Ingresa un correo válido y obligatorio.</p>
  }
</section>
```


## Antes de ejecutar: predicción
1. ¿`valid` será `true` con el campo vacío?
2. ¿Qué estado tendrá con `abc`?
3. ¿Qué estado tendrá con `ana@isil.pe`?


## Ejecuta
Con EJ04 ya configurado, abre `http://localhost:4200/ej01`. Si aún estás construyendo secuencialmente, conserva los archivos y realiza la ejecución cuando termines las rutas.

## Resultado esperado
Vacío y `abc` son inválidos; `ana@isil.pe` es válido.

## Cómo interpretarlo
Compara la predicción con el resultado y localiza la línea, validador, ruta, método HTTP o endpoint que explica la diferencia. No corrijas varias cosas a la vez.

## Variación A
Sustituye temporalmente `Validators.email` por `Validators.minLength(6)` y explica por qué cambia el criterio.

## Error controlado
Retira `ReactiveFormsModule` de `imports`: `[formControl]` deja de estar disponible. Restáuralo.

## Por qué ocurre
El error modifica deliberadamente una dependencia, nombre, condición, URL o disponibilidad que el flujo necesita. Úsalo para identificar causa y efecto.

## Corrección razonada
Restaura únicamente la condición que rompiste y vuelve a ejecutar la misma evidencia.

## Qué debes poder explicar con tus palabras
- ¿Cuál fue la entrada?
- ¿Qué parte del código tomó la decisión?
- ¿Qué estado o recurso cambió?
- ¿Qué evidencia demuestra que el resultado es correcto?


## T01 - Tarea espejo
Crea un `FormControl` de nombre con `required` y `minLength(4)`.

### Restricción
No uses `FormGroup`.

### Pista
Usa un arreglo con dos validadores.

### Evidencia que debes mostrar
Demuestra `false -> true` al pasar de 3 a 4 caracteres.

### Cómo saber si está correcta
Vacío y 3 caracteres inválidos; 4 o más válidos.


# EJ02 - FormGroup: login con validaciones y mensajes `@if`

## Qué vamos a construir
un formulario reactivo de login que agrupa correo y contraseña y solo envía cuando ambos controles son válidos.

## Qué aprenderás aquí
Interpretar el estado compuesto de un `FormGroup` y su relación con los controles hijos.

## Archivos que vamos a crear o modificar
- `src/app/features/ej02-login/login.component.ts`
- `src/app/features/ej02-login/login.component.html`

```bash
ng g c features/ej02-login/login --standalone --skip-tests
```

## Punto de partida
Usa el estado final del ejemplo anterior. Si es EJ01, parte del proyecto base preparado en las secciones iniciales.


## Paso 1 - Importar FormGroup y Router

### Escribe / ejecuta
```ts
import { JsonPipe } from '@angular/common';
import { Component } from '@angular/core';
import { FormControl, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';
import { Router } from '@angular/router';
```

### Qué significa
`FormGroup` agrupa controles; `Router` permitirá navegar tras un envío válido; `JsonPipe` solo hace visible el valor durante el laboratorio.

### Por qué lo hacemos ahora
Se declaran primero las dependencias de estado, vista y navegación.

### Qué debería ocurrir
Los símbolos quedan disponibles para el componente.


## Paso 2 - Crear el grupo y sus reglas

### Escribe / ejecuta
```ts
loginForm = new FormGroup({
  email: new FormControl('', [Validators.required, Validators.email]),
  password: new FormControl('', [Validators.required, Validators.minLength(8)])
});
```

### Qué significa
El grupo es válido únicamente cuando todos los controles cumplen sus validadores.

### Por qué lo hacemos ahora
Primero modelamos la regla de negocio del formulario antes de programar el envío.

### Qué debería ocurrir
`loginForm.invalid` inicia en `true`.


## Paso 3 - Programar el envío

### Escribe / ejecuta
```ts
constructor(private router: Router) {}

onSubmit(): void {
  this.loginForm.markAllAsTouched();
  if (this.loginForm.invalid) return;
  localStorage.setItem('token', 'demo-token');
  void this.router.navigate(['/dashboard']);
}
```

### Qué significa
`markAllAsTouched()` fuerza la visualización de errores. El `return` evita continuar si hay datos inválidos. El token es una bandera didáctica para EJ05.

### Por qué lo hacemos ahora
La acción se agrega después de definir qué significa que el formulario sea válido.

### Qué debería ocurrir
Con datos válidos se guarda el token demo y se solicita navegación.


## Paso 4 - Vincular el formulario y los controles

### Escribe / ejecuta
```html
<form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
  <input formControlName="email" placeholder="correo@dominio.com">
  <input type="password" formControlName="password" placeholder="Mínimo 8 caracteres">
  <button type="submit" [disabled]="loginForm.invalid">Ingresar</button>
</form>
```

### Qué significa
`formGroup` conecta el formulario HTML con el grupo TypeScript y `formControlName` busca controles por nombre.

### Por qué lo hacemos ahora
La estructura de datos ya está definida; la plantilla ahora la consume.

### Qué debería ocurrir
El botón se habilita solo si el grupo es válido.


## Paso 5 - Mostrar errores por control

### Escribe / ejecuta
```html
@if (loginForm.get('email')?.invalid && loginForm.get('email')?.touched) {
  <p class="error">Correo inválido o requerido.</p>
}
@if (loginForm.get('password')?.invalid && loginForm.get('password')?.touched) {
  <p class="error">La contraseña es obligatoria y debe tener al menos 8 caracteres.</p>
}
```

### Qué significa
Cada mensaje consulta el control específico, no el grupo completo.

### Por qué lo hacemos ahora
Después de conectar los campos podemos explicar qué regla falló.

### Qué debería ocurrir
Solo aparecen mensajes de los controles inválidos y tocados.


## Archivo completo al terminar este ejemplo


```ts
import { JsonPipe } from '@angular/common';
import { Component } from '@angular/core';
import { FormControl, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';
import { Router } from '@angular/router';

@Component({
  selector: 'app-login',
  standalone: true,
  imports: [ReactiveFormsModule, JsonPipe],
  templateUrl: './login.component.html'
})
export class LoginComponent {
  loginForm = new FormGroup({
    email: new FormControl('', [Validators.required, Validators.email]),
    password: new FormControl('', [Validators.required, Validators.minLength(8)])
  });

  constructor(private router: Router) {}

  onSubmit(): void {
    this.loginForm.markAllAsTouched();
    if (this.loginForm.invalid) return;
    localStorage.setItem('token', 'demo-token');
    void this.router.navigate(['/dashboard']);
  }
}
```


```html
<section class="card">
  <h2>EJ02 - FormGroup y validaciones </h2>
  <form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
    <label for="login-email">Correo</label>
    <input id="login-email" formControlName="email" placeholder="correo@dominio.com">
    @if (loginForm.get('email')?.invalid && loginForm.get('email')?.touched) {
      <p class="error">Correo inválido o requerido.</p>
    }

    <label for="login-password">Contraseña</label>
    <input id="login-password" type="password" formControlName="password" placeholder="Mínimo 8 caracteres">
    @if (loginForm.get('password')?.invalid && loginForm.get('password')?.touched) {
      <p class="error">La contraseña es obligatoria y debe tener al menos 8 caracteres.</p>
    }

    <button type="submit" [disabled]="loginForm.invalid">Ingresar</button>
  </form>
  <pre>{{ loginForm.value | json }}</pre>
</section>
```


## Antes de ejecutar: predicción
1. Correo válido + password de 3 caracteres: ¿botón habilitado?
2. Ambos válidos: ¿valor de `loginForm.invalid`?
3. ¿Qué ocurre si se ejecutara `onSubmit()` con el grupo inválido?


## Ejecuta
Abre `/ej02` cuando las rutas estén listas. Prueba primero datos inválidos y luego un correo válido con contraseña de 8+ caracteres.

## Resultado esperado
Los mensajes reflejan cada control; el botón solo se habilita con ambos válidos.

## Cómo interpretarlo
Compara la predicción con el resultado y localiza la línea, validador, ruta, método HTTP o endpoint que explica la diferencia. No corrijas varias cosas a la vez.

## Variación A
Cambia `minLength(8)` a `minLength(10)` y prueba la misma contraseña.

## Error controlado
Renombra en HTML `formControlName="email"` a `correo` sin cambiar el grupo. Angular no encontrará ese control; restaura el nombre.

## Por qué ocurre
El error modifica deliberadamente una dependencia, nombre, condición, URL o disponibilidad que el flujo necesita. Úsalo para identificar causa y efecto.

## Corrección razonada
Restaura únicamente la condición que rompiste y vuelve a ejecutar la misma evidencia.

## Qué debes poder explicar con tus palabras
- ¿Cuál fue la entrada?
- ¿Qué parte del código tomó la decisión?
- ¿Qué estado o recurso cambió?
- ¿Qué evidencia demuestra que el resultado es correcto?


## T02 - Tarea espejo
Construye un formulario de registro con `name` obligatorio y `email` obligatorio + formato email.

### Restricción
Mantén dos controles y no agregues validadores ajenos.

### Pista
El grupo usa el mismo patrón que `loginForm`.

### Evidencia que debes mostrar
Botón `Registrar` deshabilitado hasta que ambos controles sean válidos.

### Cómo saber si está correcta
El estado del grupo cambia correctamente según sus hijos.


# EJ03 - Validador personalizado: contrato `null` o error

## Qué vamos a construir
un validador reutilizable de contraseña y un componente que interpreta su clave de error.

## Qué aprenderás aquí
Comprender el contrato de un validador personalizado: entrada control -> `null` o `ValidationErrors`.

## Archivos que vamos a crear o modificar
- `src/app/features/ej03-password/password-demo.component.ts`
- `src/app/features/ej03-password/password-demo.component.html`

```ts
ng g c features/ej03-password/password-demo --standalone --skip-tests
```

## Punto de partida
Usa el estado final del ejemplo anterior. Si es EJ01, parte del proyecto base preparado en las secciones iniciales.


## Paso 1 - Escribir la función pura


### Qué significa
La función transforma el valor del control en una decisión de validación. `null` significa válido; el objeto expone una clave consultable.

### Por qué lo hacemos ahora
Se aísla la regla antes de conectarla a un componente.

### Qué debería ocurrir
Con menos de 8 caracteres retorna `{ weakPassword: true }`.


## Paso 2 - Asociarla a un FormControl

### Escribe / ejecuta
```ts
import { Component } from '@angular/core';
import { FormControl, ReactiveFormsModule, Validators } from '@angular/forms';
import { AbstractControl, ValidationErrors } from '@angular/forms';

@Component({
  selector: 'app-password-demo',
  standalone: true,
  imports: [ReactiveFormsModule],
  templateUrl: './password-demo.component.html'
})
export class PasswordDemoComponent {
  passwordValidator(control: AbstractControl): ValidationErrors | null {
    const value = String(control.value ?? '');
    return value.length >= 8 ? null : { weakPassword: true };
  }

  password = new FormControl('', [Validators.required, passwordValidator]);
}
```

### Qué significa
El control combina `required` y el validador propio. Basta que uno produzca error para que el control sea inválido.

### Por qué lo hacemos ahora
Después de probar la función conceptualmente, la conectamos al estado reactivo.

### Qué debería ocurrir
El control expone `errors.weakPassword` cuando la longitud es insuficiente.


## Paso 3 - Leer la clave de error en la plantilla

### Escribe / ejecuta
```html
<section class="card">
  <h2>EJ03 - Validador personalizado</h2>
  <label for="password-demo">Contraseña</label>
  <input id="password-demo" type="password" [formControl]="password" placeholder="8 o más caracteres">
  @if (password.errors?.['required'] && password.touched) {
    <p class="error">La contraseña es obligatoria.</p>
  }
  @if (password.errors?.['weakPassword'] && password.touched) {
    <p class="error">La contraseña debe tener al menos 8 caracteres.</p>
  }
  <button type="button" (click)="password.markAsTouched()">Comprobar</button>
</section>
```

### Qué significa
La plantilla distingue `required` de `weakPassword`, por eso puede dar retroalimentación específica.

### Por qué lo hacemos ahora
El objeto de error fue creado precisamente para que la vista conozca la causa.

### Qué debería ocurrir
Mensajes distintos para vacío y contraseña débil.


## Archivo completo al terminar este ejemplo



```ts
import { Component } from '@angular/core';
import { FormControl, ReactiveFormsModule, Validators } from '@angular/forms';
import { AbstractControl, ValidationErrors } from '@angular/forms';

@Component({
  selector: 'app-password-demo',
  standalone: true,
  imports: [ReactiveFormsModule],
  templateUrl: './password-demo.component.html'
})
export class PasswordDemoComponent {
  passwordValidator(control: AbstractControl): ValidationErrors | null {
    const value = String(control.value ?? '');
    return value.length >= 8 ? null : { weakPassword: true };
  }
  password = new FormControl('', [Validators.required, passwordValidator]);
}
```


```html
<section class="card">
  <h2>EJ03 - Validador personalizado</h2>
  <label for="password-demo">Contraseña</label>
  <input id="password-demo" type="password" [formControl]="password" placeholder="8 o más caracteres">
  @if (password.errors?.['required'] && password.touched) {
    <p class="error">La contraseña es obligatoria.</p>
  }
  @if (password.errors?.['weakPassword'] && password.touched) {
    <p class="error">La contraseña debe tener al menos 8 caracteres.</p>
  }
  <button type="button" (click)="password.markAsTouched()">Comprobar</button>
</section>
```


## Antes de ejecutar: predicción
1. Para `abc`, ¿qué retorna el validador?
2. Para `12345678`, ¿qué retorna?
3. ¿Qué clave consulta la plantilla?


## Ejecuta
Abre `/ej03`, pulsa Comprobar con 3 caracteres y luego con 8.

## Resultado esperado
3 caracteres producen `weakPassword`; 8 o más eliminan esa clave.

## Cómo interpretarlo
Compara la predicción con el resultado y localiza la línea, validador, ruta, método HTTP o endpoint que explica la diferencia. No corrijas varias cosas a la vez.

## Variación A
Cambia el mínimo a 10 y actualiza también el mensaje para no crear contradicción.

## Error controlado
Invierte el ternario y observa cómo una contraseña fuerte pasa a ser inválida; corrígelo usando la traza.

## Por qué ocurre
El error modifica deliberadamente una dependencia, nombre, condición, URL o disponibilidad que el flujo necesita. Úsalo para identificar causa y efecto.

## Corrección razonada
Restaura únicamente la condición que rompiste y vuelve a ejecutar la misma evidencia.

## Qué debes poder explicar con tus palabras
- ¿Cuál fue la entrada?
- ¿Qué parte del código tomó la decisión?
- ¿Qué estado o recurso cambió?
- ¿Qué evidencia demuestra que el resultado es correcto?


## T03 - Tarea espejo
Crea `codeValidator`: válido solo con exactamente 6 caracteres; de lo contrario `{ invalidCode: true }`.

### Restricción
La función debe seguir siendo pura.

### Pista
Compara `value.length === 6`.

### Evidencia que debes mostrar
Prueba longitudes 5, 6 y 7.

### Cómo saber si está correcta
Solo longitud 6 retorna `null`.


# EJ04 - Rutas standalone, navegación y `routerLinkActive`

## Qué vamos a construir
la tabla de rutas del laboratorio, un dashboard y una navegación que renderiza dentro de `router-outlet`.

## Qué aprenderás aquí
Explicar cómo una URL se asocia a un componente y por qué el wildcard debe ser la última ruta.

## Archivos que vamos a crear o modificar
- `src/app/app.routes.ts`
- `src/app/app.component.ts`
- `src/app/app.component.html`
- `src/app/features/ej04-dashboard/dashboard.component.ts`

```ts
ng g c features/ej04-dashboard/dashboard --standalone --skip-tests
```

## Punto de partida
Usa el estado final del ejemplo anterior. Si es EJ01, parte del proyecto base preparado en las secciones iniciales.


## Paso 1 - Crear un destino simple

### Escribe / ejecuta
```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-dashboard',
  standalone: true,
  template: `<section class="card"><h2>EJ04/EJ05 - Dashboard protegido</h2><p>La ruta /dashboard fue activada correctamente.</p></section>`
})
export class DashboardComponent {}
```

### Qué significa
El dashboard es un componente destino: no contiene lógica de routing; solo puede ser seleccionado por una ruta.

### Por qué lo hacemos ahora
Conviene tener el destino antes de registrarlo en la tabla.

### Qué debería ocurrir
El archivo compila como componente standalone.


## Paso 2 - Importar destinos y guard

### Escribe / ejecuta
```ts
import { Routes } from '@angular/router';
import { EmailControlComponent } from './features/ej01-control/email-control.component';
import { LoginComponent } from './features/ej02-login/login.component';
import { PasswordDemoComponent } from './features/ej03-password/password-demo.component';
import { DashboardComponent } from './features/ej04-dashboard/dashboard.component';
```

### Qué significa
La tabla de rutas necesita referencias a los componentes que podrá activar.

### Por qué lo hacemos ahora
Primero declaramos los destinos disponibles.

### Qué debería ocurrir
Los componentes quedan disponibles para el arreglo `routes`.


## Paso 3 - Definir las rutas base

### Escribe / ejecuta
```ts
export const routes: Routes = [
  { path: '', component: LoginComponent },
  { path: 'ej01', component: EmailControlComponent },
  { path: 'ej02', component: LoginComponent },
  { path: 'ej03', component: PasswordDemoComponent },
  { path: 'dashboard', component: DashboardComponent },
  { path: '**', redirectTo: '' }
];
```

### Qué significa
Angular evalúa en orden y usa la primera coincidencia. `**` captura cualquier ruta no reconocida.

### Por qué lo hacemos ahora
Definimos las asociaciones URL-componente después de tener los destinos.

### Qué debería ocurrir
URLs conocidas activan su componente; una desconocida vuelve al login.


## Paso 4 - Habilitar directivas de navegación en el root

### Escribe / ejecuta
```ts
import { Component } from '@angular/core';
import { RouterLink, RouterLinkActive, RouterOutlet } from '@angular/router';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterLink, RouterLinkActive, RouterOutlet],
  templateUrl: './app.component.html'
})
export class AppComponent {}
```

### Qué significa
`RouterLink` crea navegación SPA, `RouterLinkActive` marca la ruta actual y `RouterOutlet` reserva el área de renderizado.

### Por qué lo hacemos ahora
La tabla por sí sola no crea enlaces ni un lugar visible para el componente.

### Qué debería ocurrir
El root queda preparado para navegar y renderizar.


## Paso 5 - Crear los enlaces y el outlet

### Escribe / ejecuta
```html
<nav>
  <a routerLink="/ej01" routerLinkActive="active">EJ01</a>
  <a routerLink="/ej02" routerLinkActive="active">EJ02</a>
  <a routerLink="/ej03" routerLinkActive="active">EJ03</a>
  <a routerLink="/dashboard" routerLinkActive="active">Dashboard</a>
  <a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{ exact: true }">Login</a>
</nav>
<main><router-outlet /></main>
```

### Qué significa
Cada enlace cambia la URL sin recarga completa y el outlet muestra el componente que el Router selecciona.

### Por qué lo hacemos ahora
Ahora hacemos visible el flujo URL -> route -> component -> outlet.

### Qué debería ocurrir
Al navegar cambia el contenido central y la clase `active`.


## Archivo completo al terminar este ejemplo


```ts
import { Routes } from '@angular/router';
import { EmailControlComponent } from './features/ej01-control/email-control.component';
import { LoginComponent } from './features/ej02-login/login.component';
import { PasswordDemoComponent } from './features/ej03-password/password-demo.component';
import { DashboardComponent } from './features/ej04-dashboard/dashboard.component';

export const routes: Routes = [
  { path: '', component: LoginComponent },
  { path: 'ej01', component: EmailControlComponent },
  { path: 'ej02', component: LoginComponent },
  { path: 'ej03', component: PasswordDemoComponent },
  { path: 'dashboard', component: DashboardComponent },
  { path: '**', redirectTo: '' }
];
```


```ts
import { Component } from '@angular/core';
import { RouterLink, RouterLinkActive, RouterOutlet } from '@angular/router';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterLink, RouterLinkActive, RouterOutlet],
  templateUrl: './app.component.html'
})
export class AppComponent {}
```


```html
<nav>
  <a routerLink="/ej01" routerLinkActive="active">EJ01</a>
  <a routerLink="/ej02" routerLinkActive="active">EJ02</a>
  <a routerLink="/ej03" routerLinkActive="active">EJ03</a>
  <a routerLink="/dashboard" routerLinkActive="active">Dashboard</a>
  <a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{ exact: true }">Login</a>
</nav>
<main><router-outlet /></main>
```


## Antes de ejecutar: predicción
1. ¿Qué componente activa `/ej01`?
2. ¿Qué hace `/ruta-que-no-existe`?
3. ¿Qué ocurriría si `**` estuviera primero?


## Ejecuta
Inicia Angular, navega entre `/ej01`, `/ej02`, `/ej03` y una URL inexistente.

## Resultado esperado
Cada URL muestra su componente; una desconocida redirige a `/`.

## Cómo interpretarlo
Compara la predicción con el resultado y localiza la línea, validador, ruta, método HTTP o endpoint que explica la diferencia. No corrijas varias cosas a la vez.

## Variación A
Agrega temporalmente `/prueba` apuntando al dashboard y verifica la coincidencia.

## Error controlado
Coloca el wildcard al inicio: todas las rutas serán capturadas. Devuélvelo al final.

## Por qué ocurre
El error modifica deliberadamente una dependencia, nombre, condición, URL o disponibilidad que el flujo necesita. Úsalo para identificar causa y efecto.

## Corrección razonada
Restaura únicamente la condición que rompiste y vuelve a ejecutar la misma evidencia.

## Qué debes poder explicar con tus palabras
- ¿Cuál fue la entrada?
- ¿Qué parte del código tomó la decisión?
- ¿Qué estado o recurso cambió?
- ¿Qué evidencia demuestra que el resultado es correcto?


## T04 - Tarea espejo
Agrega `/profile` reutilizando `DashboardComponent` y un enlace Perfil.

### Restricción
El wildcard debe continuar último.

### Pista
Copia el patrón de una ruta estática existente.

### Evidencia que debes mostrar
`/profile` renderiza el dashboard sin recarga completa.

### Cómo saber si está correcta
La ruta es alcanzable y `routerLinkActive` funciona.


# EJ05 - Guard funcional: decidir antes de activar una ruta

## Qué vamos a construir
un `CanActivateFn` que permite o redirige según un token de demostración.

## Qué aprenderás aquí
Diferenciar una decisión de navegación del mecanismo real de autorización del servidor.

## Archivos que vamos a crear o modificar
- `src/app/core/auth/auth.guard.ts`
- `src/app/app.routes.ts`

## Punto de partida
Usa el estado final del ejemplo anterior. Si es EJ01, parte del proyecto base preparado en las secciones iniciales.


## Paso 1 - Importar `inject`, `CanActivateFn` y Router

### Escribe / ejecuta
```ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
```

### Qué significa
`inject` obtiene servicios dentro de una función; `CanActivateFn` define el contrato del guard.

### Por qué lo hacemos ahora
Primero declaramos qué necesita la función para tomar una decisión.

### Qué debería ocurrir
Los tipos y Router quedan disponibles.


## Paso 2 - Leer el estado de sesión de demostración

### Escribe / ejecuta
```ts
export const authGuard: CanActivateFn = () => {
  const isLogged = Boolean(localStorage.getItem('token'));
  const router = inject(Router);
```

### Qué significa
`Boolean(...)` convierte existencia/ausencia del valor a una condición clara.

### Por qué lo hacemos ahora
La decisión depende de un estado, por eso lo calculamos antes del retorno.

### Qué debería ocurrir
`isLogged` es `false` si el token no existe.


## Paso 3 - Devolver decisión o redirección

### Escribe / ejecuta
```ts
return isLogged ? true : router.createUrlTree(['/']);
};
```

### Qué significa
Un guard debe devolver el resultado de navegación. `UrlTree` representa el destino alternativo.

### Por qué lo hacemos ahora
Se devuelve una decisión declarativa después de leer el estado.

### Qué debería ocurrir
Sin token el Router recibe una redirección hacia `/`.


## Paso 4 - Adjuntar el guard a una ruta

### Escribe / ejecuta
```ts
{ path: 'dashboard', component: DashboardComponent, canActivate: [authGuard] },
```

### Qué significa
`canActivate` hace que Angular evalúe el guard antes de activar el componente.

### Por qué lo hacemos ahora
El guard aislado no protege nada hasta asociarlo a una ruta.

### Qué debería ocurrir
Dashboard queda condicionado por el guard.


## Archivo completo al terminar este ejemplo


```ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';

export const authGuard: CanActivateFn = () => {
  const isLogged = Boolean(localStorage.getItem('token'));
  const router = inject(Router);
  return isLogged ? true : router.createUrlTree(['/']);
};
```


```ts
import { Routes } from '@angular/router';
import { EmailControlComponent } from './features/ej01-control/email-control.component';
import { LoginComponent } from './features/ej02-login/login.component';
import { PasswordDemoComponent } from './features/ej03-password/password-demo.component';
import { DashboardComponent } from './features/ej04-dashboard/dashboard.component';
import { authGuard } from './core/auth/auth.guard';

export const routes: Routes = [
  { path: '', component: LoginComponent },
  { path: 'ej01', component: EmailControlComponent },
  { path: 'ej02', component: LoginComponent },
  { path: 'ej03', component: PasswordDemoComponent },
  { path: 'dashboard', component: DashboardComponent, canActivate: [authGuard] },
  { path: '**', redirectTo: '' }
];
```


## Antes de ejecutar: predicción
1. Sin token, ¿qué retorna el guard?
2. Con token, ¿qué retorna?
3. ¿Puede este guard impedir una llamada HTTP directa al backend?


## Ejecuta
En consola ejecuta `localStorage.removeItem("token")` e intenta `/dashboard`; luego inicia sesión válidamente y repite.

## Resultado esperado
Sin token vuelve al login; con token entra al dashboard.

## Cómo interpretarlo
Compara la predicción con el resultado y localiza la línea, validador, ruta, método HTTP o endpoint que explica la diferencia. No corrijas varias cosas a la vez.

## Variación A
Protege temporalmente otra ruta y compara el comportamiento.

## Error controlado
Retorna siempre `true`; comprueba que la protección desaparece y restaura la condición.

## Por qué ocurre
El error modifica deliberadamente una dependencia, nombre, condición, URL o disponibilidad que el flujo necesita. Úsalo para identificar causa y efecto.

## Corrección razonada
Restaura únicamente la condición que rompiste y vuelve a ejecutar la misma evidencia.

## Qué debes poder explicar con tus palabras
- ¿Cuál fue la entrada?
- ¿Qué parte del código tomó la decisión?
- ¿Qué estado o recurso cambió?
- ¿Qué evidencia demuestra que el resultado es correcto?


## T05 - Tarea espejo
Protege `/ej03` con `authGuard` y documenta una prueba sin token y otra con token.

### Restricción
No inventes un segundo mecanismo de autenticación.

### Pista
Añade `canActivate: [authGuard]` a la ruta `ej03`.

### Evidencia que debes mostrar
Dos pruebas con resultados distintos según token.

### Cómo saber si está correcta
Sin token no activa `/ej03`; con token sí.


# EJ06 - Backend Node.js + Express: endpoints REST en memoria

## Qué vamos a construir
una API local con GET, POST, PUT y DELETE sobre un arreglo de usuarios.

## Qué aprenderás aquí
Relacionar método HTTP, URL, entrada, cambio de estado y código de respuesta.

## Archivos que vamos a crear o modificar
- `backend-api-lab/package.json`
- `backend-api-lab/app.js`

## Punto de partida
Usa el estado final del ejemplo anterior. Si es EJ01, parte del proyecto base preparado en las secciones iniciales.


## Paso 1 - Crear backend e instalar dependencias

### Escribe / ejecuta
```bash
cd ..
mkdir backend-api-lab
cd backend-api-lab
npm init -y
npm install express cors
```

### Qué significa
`npm init` crea metadatos; Express implementa el servidor; CORS permite el origen del frontend durante el laboratorio.

### Por qué lo hacemos ahora
El servidor necesita su propio proyecto y dependencias antes del código.

### Qué debería ocurrir
Debe existir `package.json` y las dependencias instaladas.


## Paso 2 - Inicializar Express y middleware

### Escribe / ejecuta
```js
import express from 'express';
import cors from 'cors';

const app = express();
const PORT = 3001;

app.use(cors());
app.use(express.json());
```

### Qué significa
`cors()` habilita el origen cruzado de desarrollo; `express.json()` convierte JSON entrante en `req.body`.

### Por qué lo hacemos ahora
Los middleware deben registrarse antes de endpoints que dependan de ellos.

### Qué debería ocurrir
POST/PUT podrán leer cuerpos JSON.


## Paso 3 - Crear datos de laboratorio

### Escribe / ejecuta
```js
let users = [
  { id: 1, name: 'Juan Pérez', email: 'juan@correo.com' },
  { id: 2, name: 'Ana Torres', email: 'ana@correo.com' }
];
```

### Qué significa
El arreglo sustituye una base de datos para observar cambios sin introducir persistencia.

### Por qué lo hacemos ahora
Primero necesitamos un estado sobre el cual operen los endpoints.

### Qué debería ocurrir
GET inicial tendrá dos usuarios.


## Paso 4 - Implementar lectura GET de colección

### Escribe / ejecuta
```js
app.get('/api/users', (req, res) => {
  res.json(users);
});
```

### Qué significa
Este endpoint devuelve la colección completa en JSON.

### Por qué lo hacemos ahora
Comenzamos con lectura porque no altera el estado y es fácil de verificar.

### Qué debería ocurrir
GET lista devuelve 200 con el arreglo actual.


## Paso 5 - Implementar POST

### Escribe / ejecuta
```js
app.post('/api/users', (req, res) => {
  const { name, email } = req.body;
  if (!name || !email) return res.status(400).json({ message: 'name y email son obligatorios' });
  const user = { id: Date.now(), name, email };
  users.push(user);
  return res.status(201).json(user);
});
```

### Qué significa
POST valida entrada, crea un objeto, muta el arreglo y devuelve 201.

### Por qué lo hacemos ahora
Después de leer, introducimos una operación que crea estado.

### Qué debería ocurrir
Un POST válido incrementa la colección.


## Paso 6 - Implementar PUT

### Escribe / ejecuta
```js
app.put('/api/users/:id', (req, res) => {
  const id = Number(req.params.id);
  const index = users.findIndex(item => item.id === id);
  if (index === -1) return res.status(404).json({ message: 'Usuario no encontrado' });
  users[index] = { ...users[index], ...req.body, id };
  return res.json(users[index]);
});
```

### Qué significa
PUT localiza por ID, conserva campos existentes, aplica cambios y mantiene el ID de la URL.

### Por qué lo hacemos ahora
Se construye sobre la búsqueda por ID ya comprendida.

### Qué debería ocurrir
El usuario existente cambia y se devuelve el nuevo estado.


## Paso 7 - Implementar DELETE

### Escribe / ejecuta
```js
app.delete('/api/users/:id', (req, res) => {
  const id = Number(req.params.id);
  const exists = users.some(item => item.id === id);
  if (!exists) return res.status(404).json({ message: 'Usuario no encontrado' });
  users = users.filter(item => item.id !== id);
  return res.status(204).send();
});
```

### Qué significa
DELETE valida existencia, filtra el arreglo y devuelve 204 sin cuerpo.

### Por qué lo hacemos ahora
Cerramos CRUD con una operación destructiva controlada sobre datos ficticios.

### Qué debería ocurrir
El ID desaparece de la siguiente consulta GET.


## Paso 8 - Levantar el servidor

### Escribe / ejecuta
```js
app.listen(PORT, () => console.log(`Servidor backend en http://localhost:${PORT}`));
```

### Qué significa
`listen` abre el puerto y mantiene el proceso esperando peticiones.

### Por qué lo hacemos ahora
Se coloca al final, después de configurar middleware y rutas.

### Qué debería ocurrir
La terminal informa `http://localhost:3001`.


## Archivo completo al terminar este ejemplo


```json
{
  "name": "backend-api-lab",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^5.1.0"
  }
}
```


```js
import express from 'express';
import cors from 'cors';

const app = express();
const PORT = 3001;

app.use(cors());
app.use(express.json());

let users = [
  { id: 1, name: 'Juan Pérez', email: 'juan@correo.com' },
  { id: 2, name: 'Ana Torres', email: 'ana@correo.com' }
];

app.get('/api/users', (req, res) => {
  res.json(users);
});


app.post('/api/users', (req, res) => {
  const { name, email } = req.body;
  if (!name || !email) return res.status(400).json({ message: 'name y email son obligatorios' });
  const user = { id: Date.now(), name, email };
  users.push(user);
  return res.status(201).json(user);
});

app.put('/api/users/:id', (req, res) => {
  const id = Number(req.params.id);
  const index = users.findIndex(item => item.id === id);
  if (index === -1) return res.status(404).json({ message: 'Usuario no encontrado' });
  users[index] = { ...users[index], ...req.body, id };
  return res.json(users[index]);
});

app.delete('/api/users/:id', (req, res) => {
  const id = Number(req.params.id);
  const exists = users.some(item => item.id === id);
  if (!exists) return res.status(404).json({ message: 'Usuario no encontrado' });
  users = users.filter(item => item.id !== id);
  return res.status(204).send();
});

app.listen(PORT, () => console.log(`Servidor backend en http://localhost:${PORT}`));
```


## Antes de ejecutar: predicción
1. ¿Cuántos usuarios devuelve el primer GET?
2. ¿Qué código esperas de POST válido?
3. ¿Qué efecto observable deja DELETE?


## Ejecuta
En `backend-api-lab`: `npm start`. Abre `http://localhost:3001/api/users`. Para POST/PUT/DELETE utiliza el frontend en EJ09 o una herramienta HTTP ya disponible en tu entorno.

## Resultado esperado
GET devuelve dos usuarios; operaciones válidas modifican el arreglo en memoria.

## Cómo interpretarlo
Compara la predicción con el resultado y localiza la línea, validador, ruta, método HTTP o endpoint que explica la diferencia. No corrijas varias cosas a la vez.

## Variación A
Cambia el puerto a 3002 y explica qué URL del frontend deberá cambiar.

## Error controlado
Envía POST sin `email`: debe responder 400, no crear un registro incompleto.

## Por qué ocurre
El error modifica deliberadamente una dependencia, nombre, condición, URL o disponibilidad que el flujo necesita. Úsalo para identificar causa y efecto.

## Corrección razonada
Restaura únicamente la condición que rompiste y vuelve a ejecutar la misma evidencia.

## Qué debes poder explicar con tus palabras
- ¿Cuál fue la entrada?
- ¿Qué parte del código tomó la decisión?
- ¿Qué estado o recurso cambió?
- ¿Qué evidencia demuestra que el resultado es correcto?


## T06 - Tarea espejo
Reconstruye `GET /api/users/:id` con conversión numérica y 404 si no existe.

### Restricción
No devuelvas toda la colección.

### Pista
Usa `find` y compara el ID numérico.

### Evidencia que debes mostrar
Prueba ID 1 e ID inexistente.

### Cómo saber si está correcta
200 con usuario existente; 404 con inexistente.


# EJ07 - HttpClient GET: Observable, `async`, `@for` y error controlado

## Qué vamos a construir
un componente que obtiene usuarios del backend y los representa de forma asíncrona.

## Qué aprenderás aquí
Trazar la petición desde `HttpClient` hasta `@for` y manejar un backend no disponible.

## Archivos que vamos a crear o modificar
- `src/app/features/ej07-users/users.component.ts`
- `src/app/features/ej07-users/users.component.html`

## Punto de partida
Usa el estado final del ejemplo anterior. Si es EJ01, parte del proyecto base preparado en las secciones iniciales.


## Paso 1 - Importar HTTP, AsyncPipe y operadores RxJS

### Escribe / ejecuta
```ts
import { AsyncPipe } from '@angular/common';
import { Component, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { catchError, of } from 'rxjs';
```

### Qué significa
`HttpClient` crea la petición; `catchError` intercepta fallas; `of([])` produce un Observable alternativo; `AsyncPipe` consume el Observable en HTML.

### Por qué lo hacemos ahora
Se declaran las piezas del flujo asíncrono antes de construirlo.

### Qué debería ocurrir
Los símbolos quedan disponibles.


## Paso 2 - Definir la forma mínima del usuario

### Escribe / ejecuta
```ts
interface UserSummary { id: number; name: string; email?: string; }
```

### Qué significa
Tipar la respuesta permite que TypeScript conozca `id`, `name` y `email`.

### Por qué lo hacemos ahora
Antes de solicitar datos definimos qué esperamos recibir.

### Qué debería ocurrir
El editor valida accesos a propiedades.


## Paso 3 - Inyectar HttpClient y crear el Observable

### Escribe / ejecuta
```ts
private readonly http = inject(HttpClient);

users$ = this.http.get<UserSummary[]>('http://localhost:3001/api/users').pipe(
  catchError(err => {
    console.error('Error al obtener usuarios:', err);
    return of([] as UserSummary[]);
  })
);
```

### Qué significa
El sufijo `$` comunica que es un Observable. Si GET falla, el flujo cambia a un arreglo vacío.

### Por qué lo hacemos ahora
La URL del backend ya existe desde EJ06; ahora conectamos el cliente.

### Qué debería ocurrir
Backend activo emite usuarios; inactivo emite `[]`.


## Paso 4 - Consumir con `async` y recorrer con `@for`

### Escribe / ejecuta
```html
<section class="card">
  <h2>EJ07 - GET con HttpClient y Observable</h2>
  @for (user of users$ | async; track user.id) {
    <p><strong>{{ user.name }}</strong></p>
  } @empty {
    <p>No hay usuarios o el backend no responde.</p>
  }
</section>
```

### Qué significa
`async` se suscribe y entrega el último valor; `@for` crea una fila por elemento y `track user.id` identifica cada registro.

### Por qué lo hacemos ahora
La plantilla se escribe después de definir el flujo que consumirá.

### Qué debería ocurrir
Con dos usuarios se renderizan dos filas.


## Archivo completo al terminar este ejemplo


```ts
import { AsyncPipe } from '@angular/common';
import { Component, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { catchError, of } from 'rxjs';

interface UserSummary { id: number; name: string; email?: string; }

@Component({
  selector: 'app-users',
  standalone: true,
  imports: [AsyncPipe],
  templateUrl: './users.component.html'
})
export class UsersComponent {
  private readonly http = inject(HttpClient);

  users$ = this.http.get<UserSummary[]>('http://localhost:3001/api/users').pipe(
    catchError(err => {
      console.error('Error al obtener usuarios:', err);
      return of([] as UserSummary[]);
    })
  );
}
```


```html
<section class="card">
  <h2>EJ07 - GET con HttpClient y Observable</h2>
  @for (user of users$ | async; track user.id) {
    <p><strong>{{ user.name }}</strong></p>
  } @empty {
    <p>No hay usuarios o el backend no responde.</p>
  }
</section>
```


## Antes de ejecutar: predicción
1. Backend activo: ¿cuántas filas aparecen inicialmente?
2. Backend detenido: ¿el Observable termina sin alternativa o entrega `[]`?
3. ¿Qué usa `track` como identidad?


## Ejecuta
Con backend activo abre `/users`. Luego detén backend, recarga y observa consola + mensaje `@empty`.

## Resultado esperado
Activo: usuarios visibles. Inactivo: error en consola y UI estable con estado vacío.

## Cómo interpretarlo
Compara la predicción con el resultado y localiza la línea, validador, ruta, método HTTP o endpoint que explica la diferencia. No corrijas varias cosas a la vez.

## Variación A
Modifica temporalmente la URL a puerto 3002 y relaciona el error con la URL.

## Error controlado
Detener el backend es el error controlado; no cambies antivirus, CORS global ni certificados.

## Por qué ocurre
El error modifica deliberadamente una dependencia, nombre, condición, URL o disponibilidad que el flujo necesita. Úsalo para identificar causa y efecto.

## Corrección razonada
Restaura únicamente la condición que rompiste y vuelve a ejecutar la misma evidencia.

## Qué debes poder explicar con tus palabras
- ¿Cuál fue la entrada?
- ¿Qué parte del código tomó la decisión?
- ¿Qué estado o recurso cambió?
- ¿Qué evidencia demuestra que el resultado es correcto?


## T07 - Tarea espejo
Muestra también `email`; si falta, presenta `sin correo`.

### Restricción
No hagas una segunda petición.

### Pista
Usa la misma variable `user` del `@for`.

### Evidencia que debes mostrar
Cada fila muestra nombre y correo/fallback.

### Cómo saber si está correcta
No rompe si `email` es `undefined`.


# EJ08 - Servicio CRUD: centralizar URLs y errores

## Qué vamos a construir
un servicio injectable que encapsula GET, POST, PUT y DELETE.

## Qué aprenderás aquí
Separar la comunicación HTTP de la lógica visual del componente.

## Archivos que vamos a crear o modificar
- `src/app/core/users/user.service.ts`

## Punto de partida
Usa el estado final del ejemplo anterior. Si es EJ01, parte del proyecto base preparado en las secciones iniciales.


## Paso 1 - Definir el contrato `User` y el servicio

### Escribe / ejecuta
```ts
export interface User {
  id?: number;
  name: string;
  email: string;
}

@Injectable({ providedIn: 'root' })
export class UserService {
```

### Qué significa
`User` documenta la forma de los datos. `providedIn: root` hace disponible una instancia compartida mediante DI.

### Por qué lo hacemos ahora
Se modelan datos y ciclo de vida antes de los métodos HTTP.

### Qué debería ocurrir
El servicio puede ser inyectado sin registrarlo manualmente.


## Paso 2 - Centralizar URL e inyectar HttpClient

### Escribe / ejecuta
```ts
private readonly apiUrl = 'http://localhost:3001/api/users';

constructor(private http: HttpClient) {}
```

### Qué significa
Una URL base evita repetir cadenas y reduce inconsistencias.

### Por qué lo hacemos ahora
Los métodos siguientes usarán la misma dependencia y base.

### Qué debería ocurrir
Cambiar backend requiere modificar un solo lugar.


## Paso 3 - Implementar GET de colección

### Escribe / ejecuta
```ts
getUsers() {
  return this.http.get<User[]>(this.apiUrl).pipe(this.handleError('GET'));
}
```

### Qué significa
La operación devuelve una colección tipada y reutiliza el manejo de errores.

### Por qué lo hacemos ahora
Se empieza por la lectura ya conocida de EJ07.

### Qué debería ocurrir
El método retorna un Observable al llamador.


## Paso 4 - Implementar POST, PUT y DELETE

### Escribe / ejecuta
```ts
addUser(user: Omit<User, 'id'>) {
  return this.http.post<User>(this.apiUrl, user).pipe(this.handleError('POST'));
}

updateUser(id: number, data: Partial<User>) {
  return this.http.put<User>(`${this.apiUrl}/${id}`, data).pipe(this.handleError('PUT'));
}

deleteUser(id: number) {
  return this.http.delete<void>(`${this.apiUrl}/${id}`).pipe(this.handleError('DELETE'));
}
```

### Qué significa
POST envía cuerpo a la colección; PUT envía cambios a un ID; DELETE no necesita cuerpo en este caso.

### Por qué lo hacemos ahora
Extendemos el mismo patrón a las mutaciones del backend.

### Qué debería ocurrir
Los tres métodos reutilizan URL y manejo de errores.


## Paso 5 - Reutilizar `catchError`

### Escribe / ejecuta
```ts
private handleError(operation: string) {
  return catchError((err: unknown) => {
    console.error(`Error en ${operation}:`, err);
    return throwError(() => err);
  });
}
```

### Qué significa
El operador captura, registra contexto y vuelve a emitir el error para que el componente decida qué mostrar.

### Por qué lo hacemos ahora
Se extrae después de ver que todos los métodos necesitan el mismo patrón.

### Qué debería ocurrir
Los componentes pueden manejar `error:` en `subscribe`.


## Archivo completo al terminar este ejemplo


```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { catchError, throwError } from 'rxjs';

export interface User {
  id?: number;
  name: string;
  email: string;
}

@Injectable({ providedIn: 'root' })
export class UserService {
  private readonly apiUrl = 'http://localhost:3001/api/users';

  constructor(private http: HttpClient) {}

  getUsers() {
    return this.http.get<User[]>(this.apiUrl).pipe(this.handleError('GET'));
  }


  addUser(user: Omit<User, 'id'>) {
    return this.http.post<User>(this.apiUrl, user).pipe(this.handleError('POST'));
  }

  updateUser(id: number, data: Partial<User>) {
    return this.http.put<User>(`${this.apiUrl}/${id}`, data).pipe(this.handleError('PUT'));
  }

  deleteUser(id: number) {
    return this.http.delete<void>(`${this.apiUrl}/${id}`).pipe(this.handleError('DELETE'));
  }

  private handleError(operation: string) {
    return catchError((err: unknown) => {
      console.error(`Error en ${operation}:`, err);
      return throwError(() => err);
    });
  }
}
```


## Antes de ejecutar: predicción
1. ¿Qué URL usa `getUsers()`?
2. ¿Cuál método necesita un cuerpo para crear?
3. ¿Qué método espera `void` como respuesta?


## Ejecuta
No hace falta una pantalla nueva: EJ09 inyectará este servicio. Antes, lee cada método y empareja verbo + URL + cuerpo + tipo esperado.

## Resultado esperado
Cinco operaciones CRUD quedan centralizadas en un único servicio.

## Cómo interpretarlo
Compara la predicción con el resultado y localiza la línea, validador, ruta, método HTTP o endpoint que explica la diferencia. No corrijas varias cosas a la vez.

## Variación A
Cambia temporalmente `apiUrl` a puerto 3999 para forzar `handleError`, luego restaura.

## Error controlado
Puerto incorrecto: observa que el servicio registra la operación que falló y reemite el error.

## Por qué ocurre
El error modifica deliberadamente una dependencia, nombre, condición, URL o disponibilidad que el flujo necesita. Úsalo para identificar causa y efecto.

## Corrección razonada
Restaura únicamente la condición que rompiste y vuelve a ejecutar la misma evidencia.

## Qué debes poder explicar con tus palabras
- ¿Cuál fue la entrada?
- ¿Qué parte del código tomó la decisión?
- ¿Qué estado o recurso cambió?
- ¿Qué evidencia demuestra que el resultado es correcto?


## T08 - Tarea espejo
Escribe desde cero `getUser(id)` y explica la interpolación `${this.apiUrl}/${id}`.

### Restricción
Reutiliza `handleError`.

### Pista
Es un GET de un solo `User`.

### Evidencia que debes mostrar
La URL final para id 1 termina en `/1`.

### Cómo saber si está correcta
El método retorna el Observable y conserva tipado `User`.


# EJ09 - Integrador full stack: formulario -> API -> respuesta -> vista

## Qué vamos a construir
un gestor de usuarios que carga, crea, actualiza y elimina usando el formulario reactivo y `UserService`.

## Qué aprenderás aquí
Explicar el flujo completo de extremo a extremo y localizar dónde cambia el estado en cada capa.

## Archivos que vamos a crear o modificar
- `src/app/features/ej09-fullstack/user-manager.component.ts`
- `src/app/features/ej09-fullstack/user-manager.component.html`

## Punto de partida
Usa el estado final del ejemplo anterior. Si es EJ01, parte del proyecto base preparado en las secciones iniciales.


## Paso 1 - Crear estado del componente y formulario

### Escribe / ejecuta
```ts
users: User[] = [];
message = '';

userForm = new FormGroup({
  name: new FormControl('', [Validators.required, Validators.minLength(2)]),
  email: new FormControl('', [Validators.required, Validators.email])
});
```

### Qué significa
Hay dos estados: datos remotos en `users` y estado local del formulario. Ambos se coordinan pero no son lo mismo.

### Por qué lo hacemos ahora
Primero definimos lo que la pantalla necesita recordar.

### Qué debería ocurrir
Formulario inicia inválido y lista vacía hasta cargar.


## Paso 2 - Cargar usuarios al iniciar

### Escribe / ejecuta
```ts
ngOnInit(): void { this.loadUsers(); }

loadUsers(): void {
  this.usersApi.getUsers().subscribe({
    next: users => this.users = users,
    error: () => this.message = 'No se pudo cargar la lista.'
  });
}
```

### Qué significa
`ngOnInit` dispara GET; `next` reemplaza la lista local con la respuesta.

### Por qué lo hacemos ahora
Antes de crear datos necesitamos mostrar el estado actual del backend.

### Qué debería ocurrir
Al abrir la pantalla aparecen los usuarios existentes.


## Paso 3 - Crear usuario desde el formulario

### Escribe / ejecuta
```ts
createUser(): void {
  this.userForm.markAllAsTouched();
  if (this.userForm.invalid) return;
  const raw = this.userForm.getRawValue();
  this.usersApi.addUser({ name: raw.name ?? '', email: raw.email ?? '' }).subscribe({
    next: () => {
      this.message = 'Usuario creado correctamente.';
      this.userForm.reset();
      this.loadUsers();
    },
    error: () => this.message = 'No se pudo crear el usuario.'
  });
}
```

### Qué significa
La validación ocurre antes del POST. El POST exitoso no modifica directamente `users`; se vuelve a consultar para mostrar la fuente actual.

### Por qué lo hacemos ahora
Se construye sobre FormGroup + UserService ya dominados.

### Qué debería ocurrir
POST válido produce mensaje, reset y nueva lista.


## Paso 4 - Actualizar y eliminar

### Escribe / ejecuta
```ts
renameFirst(): void {
  const first = this.users[0];
  if (!first?.id) return;
  this.usersApi.updateUser(first.id, { name: 'Usuario actualizado' }).subscribe({
    next: () => { this.message = 'Usuario actualizado.'; this.loadUsers(); },
    error: () => this.message = 'No se pudo actualizar.'
  });
}

remove(id: number | undefined): void {
  if (!id) return;
  this.usersApi.deleteUser(id).subscribe({
    next: () => { this.message = 'Usuario eliminado.'; this.loadUsers(); },
    error: () => this.message = 'No se pudo eliminar.'
  });
}
```

### Qué significa
Ambas operaciones esperan confirmación del backend y luego recargan la colección.

### Por qué lo hacemos ahora
Después de crear, completamos las mutaciones PUT y DELETE.

### Qué debería ocurrir
El resultado se confirma con una nueva lectura GET.


## Paso 5 - Construir el formulario visual

### Escribe / ejecuta
```html
<form [formGroup]="userForm" (ngSubmit)="createUser()">
  <input formControlName="name">
  <input formControlName="email">
  <button type="submit" [disabled]="userForm.invalid">Crear usuario</button>
</form>
```

### Qué significa
La plantilla dispara `createUser()` solo al submit y mantiene el botón ligado al estado del grupo.

### Por qué lo hacemos ahora
Con la lógica preparada, conectamos entradas del usuario.

### Qué debería ocurrir
Solo datos válidos habilitan creación.


## Paso 6 - Mostrar lista y acciones

### Escribe / ejecuta
```html
@for (user of users; track user.id) {
  <p>{{ user.id }} - {{ user.name }} - {{ user.email }}
    <button type="button" (click)="remove(user.id)">Eliminar</button>
  </p>
}
<button type="button" (click)="renameFirst()">Actualizar primer usuario</button>
```

### Qué significa
La colección local es la fuente de la vista; botones envían IDs de registros reales a los métodos.

### Por qué lo hacemos ahora
La salida visual se agrega al final del flujo.

### Qué debería ocurrir
Cambios del backend se ven tras `loadUsers()`.


## Archivo completo al terminar este ejemplo


```ts
import { Component, OnInit } from '@angular/core';
import { FormControl, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';
import { User, UserService } from '../../core/users/user.service';

@Component({
  selector: 'app-user-manager',
  standalone: true,
  imports: [ReactiveFormsModule],
  templateUrl: './user-manager.component.html'
})
export class UserManagerComponent implements OnInit {
  users: User[] = [];
  message = '';

  userForm = new FormGroup({
    name: new FormControl('', [Validators.required, Validators.minLength(2)]),
    email: new FormControl('', [Validators.required, Validators.email])
  });

  constructor(private usersApi: UserService) {}

  ngOnInit(): void { this.loadUsers(); }

  loadUsers(): void {
    this.usersApi.getUsers().subscribe({
      next: users => this.users = users,
      error: () => this.message = 'No se pudo cargar la lista.'
    });
  }

  createUser(): void {
    this.userForm.markAllAsTouched();
    if (this.userForm.invalid) return;
    const raw = this.userForm.getRawValue();
    this.usersApi.addUser({ name: raw.name ?? '', email: raw.email ?? '' }).subscribe({
      next: () => {
        this.message = 'Usuario creado correctamente.';
        this.userForm.reset();
        this.loadUsers();
      },
      error: () => this.message = 'No se pudo crear el usuario.'
    });
  }

  renameFirst(): void {
    const first = this.users[0];
    if (!first?.id) return;
    this.usersApi.updateUser(first.id, { name: 'Usuario actualizado' }).subscribe({
      next: () => { this.message = 'Usuario actualizado.'; this.loadUsers(); },
      error: () => this.message = 'No se pudo actualizar.'
    });
  }

  remove(id: number | undefined): void {
    if (!id) return;
    this.usersApi.deleteUser(id).subscribe({
      next: () => { this.message = 'Usuario eliminado.'; this.loadUsers(); },
      error: () => this.message = 'No se pudo eliminar.'
    });
  }
}
```


```html
<section class="card">
  <h2>EJ09 - Integración Angular + API Node.js</h2>
  <form [formGroup]="userForm" (ngSubmit)="createUser()">
    <label for="name">Nombre</label>
    <input id="name" formControlName="name">
    @if (userForm.get('name')?.invalid && userForm.get('name')?.touched) { <p class="error">Nombre requerido.</p> }

    <label for="email-manager">Correo</label>
    <input id="email-manager" formControlName="email">
    @if (userForm.get('email')?.invalid && userForm.get('email')?.touched) { <p class="error">Correo inválido.</p> }

    <button type="submit" [disabled]="userForm.invalid">Crear usuario</button>
  </form>

  @if (message) { <p class="success">{{ message }}</p> }

  <h3>Usuarios</h3>
  @for (user of users; track user.id) {
    <p>{{ user.id }} - {{ user.name }} - {{ user.email }} <button type="button" (click)="remove(user.id)">Eliminar</button></p>
  }
  <button type="button" (click)="renameFirst()">Actualizar primer usuario</button>
</section>
```


## Antes de ejecutar: predicción
1. Email inválido: ¿se ejecuta POST?
2. POST válido: ¿qué código HTTP devuelve backend?
3. ¿Por qué se llama `loadUsers()` en `next`?
4. ¿Qué capa produce el mensaje de error si backend está apagado?


## Ejecuta
Terminal backend: `npm start`. Terminal frontend: `npm start`. Inicia sesión y abre `/manager`. Crea un usuario, actualiza el primero y elimina uno.

## Resultado esperado
Las operaciones modifican el arreglo del servidor y la vista se sincroniza después de cada respuesta exitosa.

## Cómo interpretarlo
Compara la predicción con el resultado y localiza la línea, validador, ruta, método HTTP o endpoint que explica la diferencia. No corrijas varias cosas a la vez.

## Variación A
Cambia el nombre usado en `renameFirst()` y predice la fila que cambiará.

## Error controlado
Detén backend y pulsa Crear: debe mostrarse el mensaje de fallo. Reinicia backend sin tocar controles de seguridad del equipo.

## Por qué ocurre
El error modifica deliberadamente una dependencia, nombre, condición, URL o disponibilidad que el flujo necesita. Úsalo para identificar causa y efecto.

## Corrección razonada
Restaura únicamente la condición que rompiste y vuelve a ejecutar la misma evidencia.

## Qué debes poder explicar con tus palabras
- ¿Cuál fue la entrada?
- ¿Qué parte del código tomó la decisión?
- ¿Qué estado o recurso cambió?
- ¿Qué evidencia demuestra que el resultado es correcto?


## T09 - Tarea espejo
Agrega `updateFirstFromForm()` para actualizar mediante PUT al primer usuario usando los valores actuales y válidos de `userForm`, y recarga la lista en `next`.

### Restricción
Debes validar el formulario, conservar el ID del primer usuario y usar `updateUser`.

### Pista
Obtén `raw = userForm.getRawValue()` y usa nombre/correo en el objeto del PUT.

### Evidencia que debes mostrar
Mismo ID antes/después, con nombre/correo tomados del formulario.

### Cómo saber si está correcta
Si el formulario es inválido no hace PUT; si es válido actualiza y la vista se sincroniza tras `loadUsers()`.


## 6. Cierre de sesión
Hoy construiste una cadena completa. Empezaste controlando un solo campo con `FormControl`, luego agrupaste campos con `FormGroup`, añadiste mensajes con `@if` y extendiste las reglas con un validador personalizado. Después conectaste URL y componente mediante el Router, comprobaste cómo un guard decide si una ruta puede activarse y aclaraste que esa protección del cliente no reemplaza la autorización del servidor. En la segunda mitad levantaste una API REST con Node.js y Express, relacionaste GET, POST, PUT y DELETE con operaciones concretas y consumiste esos endpoints desde Angular usando `HttpClient` y Observables. Finalmente integraste formulario, servicio HTTP y backend en un flujo completo.

Para repasar, no memorices archivos completos: reconstruye los mapas mentales. Pregúntate qué entrada inicia el flujo, qué objeto cambia de estado, qué operación se ejecuta y qué evidencia confirma el resultado. Revisa especialmente la diferencia entre validación de formulario, control de navegación y validación del backend.

Como recurso de continuidad puedes consultar Lideratec Academy: https://lideratecacademy.com/blog/ y https://www.youtube.com/@LideratecAcademy. Si deseas validar tu implementación o trabajar con una versión completa del proyecto, puedes utilizar el laboratorio ejecutable de la FASE 15; no es requisito para completar esta guía.
