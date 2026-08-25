# Parte 1: Introducción

## Capítulo 3: Uso de React Hooks

Los **React Hooks** son funciones especiales que le permiten usar características de React, como el estado, dentro de los componentes. En este capítulo, aprenderemos sobre los Hooks comunes de React y cómo usarlos con TypeScript. Implementaremos el conocimiento de todos estos Hooks en un componente de React que permite al usuario ajustar la puntuación de una persona. Comenzaremos explorando el Hook de efecto (*effect Hook*) y empezaremos a comprender los casos de uso en los que resulta útil. Luego profundizaremos en dos Hooks de estado, `useState` y `useReducer`, comprendiendo cuándo es mejor usar cada uno. Después de eso, cubriremos el Hook de referencia (*ref Hook*) y en qué se diferencia de los Hooks de estado, y luego los Hooks `memo` y `callback`, analizando cómo pueden ayudar al rendimiento. En la última sección, abordaremos brevemente otros Hooks de React que son menos comunes o que se tratan en profundidad más adelante en este libro.

Al final de este capítulo, tendrá un conocimiento práctico de los Hooks comunes de React.

Por lo tanto, cubriremos los siguientes temas:

- Uso del Hook de efecto (`useEffect`)
- Uso de Hooks de estado (`useState`, `useReducer`)
- Uso del Hook ref (`useRef`)
- Uso del Hook memo (`useMemo`)
- Uso del Hook callback (`useCallback`)
- Otros React Hooks

---

### Requisitos técnicos

En este capítulo utilizaremos las siguientes tecnologías:

- **Navegador**: Un navegador moderno como Google Chrome
- **Node.js y npm**: Puede instalarlos desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
- **Visual Studio Code**: Puede instalarlo desde [https://code.visualstudio.com/](https://code.visualstudio.com/)

Todos los fragmentos de código de este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter03](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter03)

---

### Uso del Hook de efecto

En esta sección, aprenderemos sobre el Hook de efecto y dónde es útil. Luego crearemos un nuevo proyecto de React y un componente que haga uso del Hook de efecto.

#### Comprender los parámetros del Hook de efecto

El Hook de efecto se utiliza para manejar **efectos secundarios (side effects)** en un componente. Un efecto secundario de un componente es un conjunto de instrucciones ejecutadas fuera del alcance del componente, como una solicitud a un servicio web. El Hook de efecto se define mediante la función `useEffect` de React. `useEffect` contiene dos parámetros:

- Una función que ejecuta el efecto; como mínimo, esta función se ejecuta cada vez que se renderiza el componente.
- Un array opcional de dependencias que hacen que la función del efecto se vuelva a ejecutar cuando cambian.

Aquí hay un ejemplo del Hook de efecto en un componente:

```javascript
function SomeComponent() { function someEffect() { console.log(“Some effect”); } useEffect(someEffect); return ... }
```

Al Hook de efecto anterior se le pasa una función de efecto llamada `someEffect`. No se han pasado dependencias de efecto, por lo que la función de efecto se ejecuta cada vez que el componente se renderiza.

A menudo, se utiliza una función flecha anónima para la función de efecto. Aquí está el mismo ejemplo pero con una función de efecto anónima:

```javascript
function SomeComponent() { useEffect(() => { console.log(“Some effect”); }); return ... }
```

Como puede ver, esta versión del código es un poco más corta y posiblemente más fácil de leer.

Aquí hay otro ejemplo de un efecto:

```typescript
function SomeOtherComponent({ search }:{search: string}) { useEffect(() => { console.log(“An effect dependent on a search prop”, search); }, [search]); Return ...; }
```

Esta vez, el efecto tiene una dependencia de la prop `search`. Por lo tanto, la prop `search` se define en un array en el segundo parámetro del Hook de efecto. La función de efecto se ejecutará cada vez que cambie el valor de `search`.

#### Las reglas de los Hooks

Existen algunas reglas que la mayoría de los Hooks de React, incluido `useEffect`, deben cumplir:

- Un Hook solo se puede llamar en el nivel superior de un componente funcional. Por lo tanto, un Hook no se puede llamar dentro de un bucle o en una función anidada, como un manejador de eventos.
- Un Hook no se puede llamar condicionalmente.
- Un Hook solo se puede usar en componentes funcionales y no en componentes de clase.

El siguiente ejemplo es una violación de las reglas:

```jsx
export function AnotherComponent() { function handleClick() { useEffect(() => { console.log(“Some effect”); }); } return ( <button onClick={handleClick}>Cause effect</button> ); }
```

Esto es una violación porque `useEffect` se llama dentro de una función manejadora en lugar de en el nivel superior. Una versión corregida es la siguiente:

```jsx
export function AnotherComponent() { const [clicked, setClicked] = useState(false); useEffect(() => { if (clicked) { console.log(“Some effect”); } }, [clicked]); function handleClick() { setClicked(true); } return ( <button onClick={handleClick}>Cause effect</button> ); }
```

`useEffect` se ha elevado al nivel superior y ahora depende del estado `clicked` que se establece en la función manejadora.

#### Limpieza de efectos

Un efecto puede devolver una función que realiza la lógica de **limpieza (cleanup)** cuando el componente se desmonta. La lógica de limpieza garantiza que no quede nada que pueda provocar una fuga de memoria (*memory leak*). Consideremos el siguiente ejemplo:

```typescript
function ExampleComponent( { onClickAnywhere }: { onClickAnywhere: () => void } ) { useEffect(() => { function handleClick() { onClickAnywhere(); } document.addEventListener(“click”, handleClick); }); return ... }
```

La función de efecto anterior adjunta un manejador de eventos al elemento `document`. Sin embargo, el manejador de eventos nunca se desvincula, por lo que se adjuntarán múltiples manejadores de eventos al elemento `document` a medida que se vuelva a ejecutar el efecto. Este problema se resuelve devolviendo una función de limpieza que desvincula el manejador de eventos, de la siguiente manera:

```javascript
function ExampleComponent( ... ) { useEffect(() => { function handleClick() { onClickAnywhere(); } document.addEventListener(“click”, handleClick); return function cleanup() { document.removeEventListener(“click”, handleClick); }; }); return ...; }
```

A menudo, se utiliza una función flecha anónima para la función de limpieza:

```javascript
function ExampleComponent( ... ) { useEffect(() => { function handleClick() { onClickAnywhere(); } document.addEventListener(“click”, handleClick); return () => { document.removeEventListener(“click”, handleClick); }; }); return ...;
```

Una función flecha anónima es un poco más corta que la función con nombre del ejemplo anterior.

A continuación, exploraremos un caso de uso común para el Hook de efecto.

#### Creación del proyecto

Comencemos creando un nuevo proyecto en Visual Studio Code usando Vite. Aprendimos cómo hacer esto en el [Capítulo 2](https://subscription.packtpub.com/book/web-development/9781836643173/2), *Primeros pasos con TypeScript*: los pasos son los siguientes:

1. En una terminal, en una carpeta de su elección, ejecute el siguiente comando para indicarle a Vite que cree un proyecto de React y TypeScript:

```bash
npm create vite@latest hooks -- --template react-ts
```

2. El proyecto se crea. Ejecute los siguientes comandos en la terminal para cambiar el directorio de trabajo a la carpeta `hooks`, instalar las dependencias del proyecto y abrir el proyecto en Visual Studio Code:

```bash
cd hooks npm i code .
```

Siéntase libre de agregar formateo automático de código. Cubrimos este tema con Prettier en el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*.

3. Ejecute la aplicación en modo de desarrollo ejecutando el siguiente comando en una terminal:

```bash
npm run dev
```

4. Abra `App.tsx` y reemplace el contenido con lo siguiente:

```tsx
import ‘./App.css’; function App() { return <div></div>; } export default App;
```

La aplicación contiene una página en blanco por el momento. Mantenga la aplicación en ejecución mientras exploramos los diferentes Hooks de React en las secciones posteriores de este capítulo.

Con esto se crea el proyecto. A continuación, utilizaremos el Hook de efecto.

#### Obtención de datos usando el Hook de efecto

Un uso común del Hook de efecto es la obtención de datos (*data fetching*). Lleve a cabo los siguientes pasos para implementar un efecto que obtenga el nombre de una persona:

1. Cree una función que simule una solicitud de datos. Para hacer esto, cree un archivo llamado `getPerson.ts` en la carpeta `src` y luego agregue el siguiente contenido a este archivo:

```typescript
type Person = { name: string, }; export function getPerson(): Promise<Person> { return new Promise((resolve) => setTimeout(() => resolve({ name: “Bob” }), 1000) ); }
```

La función devuelve asincrónicamente un objeto, `{ name: “Bob” }`, después de que haya transcurrido un segundo.

Observe la anotación de tipo para el tipo de retorno, `Promise<Person>`. El tipo `Promise` representa una promesa de JavaScript, que es algo que eventualmente se completará. El tipo `Promise` tiene un argumento genérico para el tipo de elemento que se resuelve en la promesa, que es `Person` en este ejemplo. Para obtener más información sobre las promesas de JavaScript, consulte el siguiente enlace: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise).

2. A continuación, crearemos un componente de React que eventualmente mostrará una persona y una puntuación. Cree un archivo llamado `PersonScore.tsx` en la carpeta `src` y luego agregue el siguiente contenido al archivo:

```tsx
import { useEffect } from ‘react’; import { getPerson } from ‘./getPerson’; export function PersonScore() { return null; }
```

El Hook `useEffect` se ha importado desde React y la función `getPerson` que acabamos de crear también se ha importado. Por el momento, el componente simplemente devuelve `null`.

3. Agregue el siguiente efecto encima de la sentencia `return`:

```tsx
export function PersonScore() { useEffect(() => { getPerson().then((person) => console.log(person)); }, []); return null; }
```

El efecto llama a la función `getPerson` y envía la persona devuelta a la consola. El efecto solo se ejecuta después de que el componente se renderiza inicialmente porque se ha especificado un array vacío como las dependencias del efecto en su segundo argumento.

4. Abra `App.tsx` y renderice el componente `PersonScore`:

```tsx
import ‘./App.css’; import { PersonScore } from ‘./PersonScore’; function App() { return <PersonScore /> } export default App;
```

5. Vaya a la aplicación en ejecución en el navegador y vaya al panel Console en las DevTools del navegador. Observe que el objeto persona aparece en la consola, lo que verifica que el efecto que obtiene los datos de la persona se ejecutó correctamente:

*Figura 3.1 – La salida del efecto*

También puede notar que la función del efecto se ha ejecutado dos veces en lugar de una. Este comportamiento es intencional y solo ocurre en modo de desarrollo con el modo estricto de React (*React Strict Mode*).

6. A continuación, refactorizaremos cómo se llama a la función de efecto para exponer un problema interesante. Abra `PersonScore.tsx` y cambie la llamada a `useEffect` para usar la sintaxis `async/await`:

```typescript
useEffect(async () => { const person = await getPerson(); console.log(person); }, []);
```

> [!NOTE]
> La sintaxis `async/await` es una forma alternativa de escribir código asincrónico. Muchos desarrolladores la prefieren porque se lee como código sincrónico. Para más información sobre async/await, consulte: [https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Promises#async_and_await](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Promises#async_and_await).

El código anterior es posiblemente más legible, pero React genera un error. Mire en la consola del navegador y verá el siguiente error:

*Figura 3.2 – Error de async en efecto*

El error es muy informativo: el Hook `useEffect` no permite que se le pase una función marcada con `async`.

7. A continuación, actualice el código y utilice el enfoque sugerido en el mensaje de error:

```typescript
useEffect(() => { async function getThePerson() { const person = await getPerson(); console.log(person); } getThePerson(); }, []);
```

Se ha definido una función asincrónica anidada y se ha llamado inmediatamente dentro de la función de efecto; esto funciona muy bien.

Esta implementación del efecto es posiblemente menos legible que la versión inicial. Por lo tanto, vuelva a esa versión antes de continuar con la siguiente sección. El código está disponible para copiar desde el siguiente enlace: [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-effect/src/PersonScore.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-effect/src/PersonScore.tsx).

> [!NOTE]
> Aunque `useEffect` se usa comúnmente para la obtención de datos, puede ser problemático. Los problemas y los enfoques alternativos se tratan en el [Capítulo 7](https://subscription.packtpub.com/book/web-development/9781836643173/7), *Obtención de datos con Server Components y mutaciones con Server Functions*.

Eso completa nuestra exploración del Hook de efecto; he aquí un resumen:
- El Hook de efecto se utiliza para ejecutar efectos secundarios del componente cuando se renderiza un componente o cuando cambian ciertas props o estados.
- Cualquier limpieza de efecto requerida se puede realizar en una función devuelta por la función de efecto.

> [!NOTE]
> Para obtener más información sobre `useEffect`, consulte la documentación de React en [https://react.dev/reference/react/useEffect](https://react.dev/reference/react/useEffect).

A continuación, aprenderemos sobre los dos Hooks de estado en React. Mantenga la aplicación en ejecución mientras pasamos a la siguiente sección.

---

### Uso de Hooks de estado

Ya hemos aprendido sobre el Hook `useState` en capítulos anteriores, pero lo veremos nuevamente aquí y lo compararemos con otro Hook de estado que aún no hemos cubierto, `useReducer`. Ampliaremos el componente `PersonScore` que creamos en la sección anterior para explorar estos Hooks de estado.

#### Uso de useState

Como recordatorio, el Hook `useState` permite definir el estado en una variable. La sintaxis de `useState` es la siguiente:

```javascript
const [state, setState] = useState(initialState);
```

Mejoraremos el componente `PersonScore` que creamos en la última sección para almacenar el nombre de la persona en el estado. También tendremos un estado para una puntuación que se incrementa, decrementa y restablece mediante algunos botones en el componente. También agregaremos un estado de carga al componente, mostrando un indicador de carga cuando sea `true`.

Lleve a cabo los siguientes pasos:

1. Abra `PersonScore.tsx` y agregue `useState` a la declaración de importación de React:

```typescript
import { useEffect, useState } from ‘react’;
```

2. Agregue las siguientes definiciones de estado para `name`, `score` y `loading` en la parte superior de la función del componente, encima de la llamada a `useEffect`:

```typescript
export function PersonScore() { const [name, setName] = useState< string | undefined >(); const [score, setScore] = useState(0); const [loading, setLoading] = useState(true); useEffect( ... ); return null; }
```

El estado `score` se inicializa en `0` y `loading` se inicializa en `true`.

3. Cambie la función del efecto para establecer los valores de estado `loading` y `name` después de que se hayan obtenido los datos de la persona. Esto debería reemplazar la declaración `console.log` existente:

```typescript
useEffect(() => { getPerson().then((person) => { setLoading(false); setName(person.name); }); }, []);
```

Después de obtener el objeto persona, `loading` se establece en `false` y `name` se establece en el nombre de la persona.

4. A continuación, agregue la siguiente sentencia `if` entre la llamada a `useEffect` y la sentencia `return`:

```tsx
useEffect( ... ); if (loading) { return <div>Loading ...</div>; } return ...
```

Esto muestra un indicador de carga cuando el estado `loading` es `true`.

5. Cambie la sentencia `return` del componente para que genere lo siguiente:

```tsx
if (loading) { return <div>Loading ...</div>; } return ( <div> <h3> {name}, {score} </h3> <button>Add</button> <button>Subtract</button> <button>Reset</button> </div> );
```

El nombre y la puntuación de la persona se muestran en un encabezado con los botones Add, Subtract y Reset debajo (no se preocupe de que el resultado no tenga estilo; aprenderemos a diseñar componentes en el próximo capítulo):

*Figura 3.3 – El componente PersonScore después de obtener los datos*

6. Actualice el botón Add para que incremente la puntuación cuando se haga clic en él:

```jsx
<button onClick={() => setScore(score + 1)}> Add </button>
```

El evento de clic del botón llama al actualizador del estado `score` para incrementar el estado.

Existe un método alternativo para actualizar los valores de estado en función de su valor anterior. El método alternativo utiliza un parámetro en el actualizador de estado que proporciona el valor de estado anterior, por lo que nuestro ejemplo podría verse así:

```javascript
setScore(previousScore => previousScore + 1)
```

Esto es posiblemente un poco más difícil de leer, por lo que nos quedaremos con nuestro método inicial.

7. Agregue los actualizadores de estado `score` a los otros botones, de la siguiente manera:

```jsx
<button onClick={() => setScore(score - 1)}> Subtract </button> <button onClick={() => setScore(0)}>Reset</button>
```

8. En la aplicación en ejecución, haga clic en los diferentes botones. Deberían cambiar la puntuación como esperaría.

*Figura 3.4 – El componente PersonScore después de hacer clic en el botón*

Antes de terminar este ejercicio, tomémonos un tiempo para comprender cuándo se establecen realmente los valores de estado. Actualice la función del efecto para mostrar los valores de estado después de que se hayan establecido:

```typescript
useEffect(() => { getPerson().then((person) => { setLoading(false); setName(person.name); console.log(“State values”, loading, name); }); }, []); 
```

Es posible que observe que ESLint resalta dependencias faltantes, `loading` y `name`, en la llamada a `useEffect`; ignore esta advertencia porque eliminaremos esta declaración `console.log` al final de este paso.

¿Quizás esperaríamos `false` y `"Bob"` como salida en la consola? Sin embargo, `true` y `undefined` son la salida a la consola. Esto se debe a que la actualización de los valores de estado no es inmediata: en su lugar, se agrupan en lotes (*batched*) y se actualizan antes del siguiente renderizado. Por lo tanto, no es hasta el siguiente renderizado que `loading` será `false` y `name` será `"Bob"`.

Ya no necesitamos la declaración `console.log` que agregamos en este paso, así que elimínela antes de continuar.

A continuación, aprenderemos sobre un Hook de React alternativo para usar el estado.

#### Comprender useReducer

`useReducer` es un método alternativo y más complejo de gestionar el estado. Utiliza una **función reductora (reducer function)** para los cambios de estado, que recibe el valor del estado actual y devuelve el nuevo valor del estado.

Aquí hay un ejemplo de una llamada a `useReducer`:

```typescript
const [state, dispatch] = useReducer( reducer, initialState, );
```

Por lo tanto, `useReducer` recibe una función reductora y el valor del estado inicial como parámetros. Luego devuelve una tupla que contiene el valor del estado actual y una función para despachar (*dispatch*) los cambios de estado.

La función `dispatch` toma un argumento que describe el cambio. Este objeto a menudo se denomina **acción (action)**. Un ejemplo de llamada a `dispatch` es el siguiente:

```javascript
dispatch({ type: ‘add’, amount: 2 });
```

No existe una estructura definida para una acción, pero es una práctica común que contenga una propiedad, como `type`, para especificar el tipo de cambio. Otras propiedades en la acción pueden variar según el tipo de cambio. Aquí hay otro ejemplo de una llamada a `dispatch`:

```javascript
dispatch({ type: ‘loaded’ });
```

Esta vez, la acción solo necesita el tipo para cambiar el estado necesario.

Dirigiendo nuestra atención a la función reductora, tiene parámetros para el valor del estado actual y la acción. Aquí hay un fragmento de código de ejemplo de un reducer:

```typescript
function reducer(state: State, action: Action): State { switch (action.type) { case ‘add’: return { ...state, total: state.total + action.amount }; case ... ... default: return state; } }
```

La función reductora generalmente contiene una declaración `switch` basada en el tipo de acción. Cada rama del `switch` realiza los cambios necesarios en el estado y devuelve el estado actualizado. Se crea un nuevo objeto de estado durante el cambio de estado; el estado actual nunca se muta. Mutar el estado provocaría que el componente no se vuelva a renderizar.

> [!NOTE]
> En el fragmento de código anterior, dentro de la rama `'add'`, se utiliza la sintaxis de propagación (*spread syntax*) en la variable de estado (`...state`). La sintaxis de propagación copia todas las propiedades del objeto después de los tres puntos. En el fragmento de código anterior, todas las propiedades se copian de la variable `state` en el nuevo objeto de estado devuelto. El valor de la propiedad `total` luego se sobrescribirá con `state.total + action.amount` porque esto se define después de la operación de propagación en la creación del nuevo objeto. Para más información sobre la sintaxis de propagación, consulte: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax).

Los tipos para `useReducer` se pueden definir explícitamente en su parámetro genérico de la siguiente manera:

```typescript
const [state, dispatch] = useReducer< Reducer<State, Action> >(reducer, initialState);
```

`Reducer` es un tipo estándar de React que tiene parámetros genéricos para el tipo de estado y el tipo de acción.

Por lo tanto, `useReducer` es más complejo que `useState` porque los cambios de estado pasan por una función reductora que debemos implementar. Sin embargo, la complejidad adicional puede valer la pena en los siguientes casos:
- Una acción cambia múltiples valores de estado; por ejemplo, una respuesta de la API web que actualiza un valor de estado de carga así como un valor de estado de datos.
- El siguiente valor de estado depende del valor de estado anterior. La acción `'add'` en el ejemplo anterior es una muestra de esto.

A continuación, implementaremos el estado usando `useReducer`.

#### Uso de useReducer

Refactorizaremos el componente `PersonScore` en el que hemos estado trabajando para usar `useReducer` en lugar de `useState`. Para hacer esto, lleve a cabo los siguientes pasos. Los fragmentos de código utilizados están disponibles para copiar desde [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-reducer/src/PersonScore.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-reducer/src/PersonScore.tsx):

1. Abra `PersonScore.tsx` e importe `useReducer` en lugar de `useState` desde React:

```typescript
import { useEffect, useReducer } from ‘react’;
```

2. Tendremos el estado en un solo objeto, así que defina un tipo para el estado debajo de las declaraciones de importación:

```typescript
type State = { name: string | undefined; score: number; loading: boolean; };
```

3. A continuación, definamos tipos para todos los objetos de acción:

```typescript
type Action = | { type: ‘initialize’; name: string; } | { type: ‘increment’; } | { type: ‘decrement’; } | { type: ‘reset’; };
```

Estos objetos de acción representan todas las formas en que el estado puede cambiar. Los tipos de objetos de acción se combinan mediante un tipo de unión, lo que permite que una acción sea cualquiera de estos. Este tipo de unión es una **unión discriminada (discriminated union)** porque cada elemento de la unión contiene una propiedad para distinguir los elementos (la propiedad `type`).

4. Ahora, defina la siguiente función reductora debajo de las definiciones de tipo:

```typescript
function reducer(state: State, action: Action): State { switch (action.type) { case ‘initialize’: return { name: action.name, score: 0, loading: false }; case ‘increment’: return { ...state, score: state.score + 1 }; case ‘decrement’: return { ...state, score: state.score - 1 }; case ‘reset’: return { ...state, score: 0 }; default: return state; } }
```

La función reductora contiene una declaración `switch` que realiza los cambios de estado apropiados para cada tipo de acción.

Observe el excelente IntelliSense al hacer referencia a los parámetros `state` y `action`:

*Figura 3.5 – IntelliSense dentro de la función reductora*

5. Dentro del componente `PersonScore`, reemplace las llamadas a `useState` con la siguiente llamada a `useReducer`:

```typescript
const [{ name, score, loading }, dispatch] = useReducer(reducer, { name: undefined, score: 0, loading: true, });
```

El estado se ha inicializado con un `name` indefinido, un `score` de `0` y `loading` establecido en `true`.

El valor del estado actual se ha desestructurado en las variables `name`, `score` y `loading`. Si pasa el cursor sobre estas variables de estado desestructuradas, verá que sus tipos se han inferido correctamente.

6. Ahora debemos modificar los lugares del componente que actualizan el estado. Comience con la función de efecto y despache una acción `initialize` después de que se haya devuelto la persona:

```typescript
useEffect(() => { getPerson().then(({ name }) => dispatch({ type: ‘initialize’, name }) ); }, []);
```

7. Por último, despache las acciones pertinentes en los manejadores de clics de los botones:

```jsx
<button onClick={() => dispatch({ type: ‘increment’ })} > Add </button> <button onClick={() => dispatch({ type: ‘decrement’ })} > Subtract </button> <button onClick={() => dispatch({ type: ‘reset’ })}> Reset </button>
```

Si intenta hacer clic en los botones de la aplicación en ejecución, actualizarán correctamente la puntuación mostrada.

Eso completa nuestra exploración del Hook `useReducer`. Es más útil para situaciones complejas de gestión del estado que `useState`, por ejemplo, cuando el estado es un objeto complejo con propiedades relacionadas y los cambios de estado dependen de los valores de estado anteriores. El Hook `useState` es más apropiado cuando el estado se basa en valores primitivos independientes de cualquier otro estado.

> [!NOTE]
> Para más información sobre `useState` y `useReducer`, consulte la documentación de React en [https://react.dev/reference/react/useState](https://react.dev/reference/react/useState) y [https://react.dev/reference/react/useReducer](https://react.dev/reference/react/useReducer).

Continuaremos ampliando el componente `PersonScore` en las siguientes secciones. A continuación, aprenderemos cómo mover el foco al botón Add usando el Hook ref.

---

### Uso del Hook ref

En esta sección, aprenderemos sobre el Hook ref y dónde es útil. Luego veremos un caso de uso común del Hook ref mejorando el componente `PersonScore` en el que hemos estado trabajando.

#### Comprender el Hook ref

El Hook ref se llama `useRef` y devuelve una variable cuyo valor persiste durante la vida útil de un componente. Esto significa que la variable no pierde su valor cuando un componente se vuelve a renderizar.

El valor devuelto por el Hook ref a menudo se denomina **ref**. El ref se puede cambiar sin provocar un re-renderizado.

Aquí está la sintaxis para `useRef`:

```javascript
const ref = useRef(initialValue);
```

Opcionalmente, se puede pasar un valor inicial a `useRef`. El tipo de ref se puede definir explícitamente en un argumento genérico para `useRef`:

```typescript
const ref = useRef<Ref>(initialValue);
```

El argumento genérico es útil cuando no se pasa ningún valor inicial o es `null`. Esto se debe a que TypeScript no podrá inferir el tipo correctamente.

Se accede al valor del ref a través de su propiedad `current`:

```javascript
console.log(“Current ref value”, ref.current);
```

El valor del ref también se puede actualizar a través de su propiedad `current`:

```javascript
ref.current = newValue;
```

Un uso común del Hook ref es acceder a elementos HTML de forma imperativa. Los elementos HTML tienen un atributo `ref` en JSX que se puede asignar a un ref. El siguiente es un ejemplo de esto:

```tsx
function MyComponent() { const inputRef = useRef<HTMLInputElement>(null); function doSomething() { console.log( “All the properties and methods of the input”, inputRef.current ); } return <input ref={inputRef} type=”text” />; }
```

El ref utilizado aquí se llama `inputRef` y es inicialmente `null`. Por lo tanto, se le asigna explícitamente un tipo de `HTMLInputElement`, que es un tipo estándar para elementos de entrada (*input*). Luego, el ref se asigna al atributo `ref` en un elemento `input` en JSX. Todas las propiedades y métodos del input son accesibles a través de la propiedad `current` del ref.

A continuación, usaremos el Hook ref en el componente `PersonScore`.

#### Uso del Hook ref

Mejoraremos el componente `PersonScore` en el que hemos estado trabajando para usar `useRef` para mover el foco al botón Add. Para hacer esto, lleve a cabo los siguientes pasos. Todos los fragmentos de código utilizados están disponibles en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-ref/src/PersonScore.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-ref/src/PersonScore.tsx):

1. Abra `PersonScore.tsx` e importe `useRef` desde React:

```typescript
import { useEffect, useReducer, useRef } from ‘react’;
```

2. Cree un ref para el botón Add justo debajo de la sentencia `useReducer`:

```typescript
const [ ... ] = useReducer( ... ); const addButtonRef = useRef<HTMLButtonElement>(null); useEffect( ... )
```

El ref se llama `addButtonRef` e inicialmente es `null`. Se le asigna el tipo estándar `HTMLButtonElement`.

> [!NOTE]
> Todos los elementos HTML estándar tienen tipos de TypeScript correspondientes para React. Haga clic con el botón derecho en el tipo `HTMLButtonElement` y elija **Go to Definition** para descubrir todos estos tipos. Los tipos de TypeScript de React se abrirán y contendrán todos los tipos de elementos HTML.

3. Asigne el ref al atributo `ref` en el elemento JSX del botón Add:

```jsx
<button ref={addButtonRef} onClick={() => dispatch({ type: ‘increment’ })} > Add </button>
```

4. Ahora que tenemos una referencia al botón Add, podemos invocar su método `focus` para mover el foco hacia él cuando se haya obtenido la información de la persona. Agregue la siguiente línea resaltada en el efecto existente:

```typescript
useEffect(() => { getPerson().then(({ name }) => { dispatch({ type: ‘initialize’, name }); addButtonRef.current?.focus(); }); }, []);
```

Observe el símbolo `?` después de la propiedad `current` en el ref. Este es el operador de encadenamiento opcional (*optional chaining*), y permite invocar el método `focus` sin tener que verificar que `current` no sea `null`. Visite el siguiente enlace para más información: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining).

Existe un enfoque más limpio que agregar esa línea de código al efecto existente. Por el momento, ese efecto está mezclando las responsabilidades de obtener datos, establecer el estado y establecer el foco en un botón. Mezclar responsabilidades como esta puede dificultar la comprensión y modificación de los componentes. Por lo tanto, elimine esa línea del efecto existente y agreguemos otro efecto para hacer esto debajo del efecto existente:

```typescript
useEffect(() => { getPerson().then(({ name }) => dispatch({ type: ‘initialize’, name }) ); }, []); useEffect(() => { if (!loading) { addButtonRef.current?.focus(); } }, [loading]); if (loading) { return <div>Loading ...</div>; }
```

El efecto se ejecuta cuando el estado `loading` es `false`, lo que ocurrirá después de que se haya obtenido a la persona.

5. Si actualiza el navegador que contiene la aplicación en ejecución, verá un indicador de foco en el botón Add:

*Figura 3.6 – El botón Add enfocado*

Si presiona la tecla Enter, verá que se hace clic en el botón Add y se incrementa la puntuación. Esto demuestra que el botón Add está enfocado.

Eso completa la mejora y nuestra exploración del Hook ref.

En resumen, el Hook ref crea un valor mutable y no causa un re-renderizado cuando cambia. Se utiliza comúnmente para acceder a elementos HTML en React de forma imperativa.

> [!NOTE]
> Para más información sobre `useRef`, consulte la documentación de React en [https://react.dev/reference/react/useRef](https://react.dev/reference/react/useRef).

A continuación, aprenderemos sobre el Hook memo.

---

### Uso del Hook memo

En esta sección, aprenderemos sobre el Hook memo y dónde es útil. Luego veremos un ejemplo en el componente `PersonScore` en el que hemos estado trabajando.

#### Comprender el Hook memo

El Hook memo crea un **valor memorizado (memoized value)** y es beneficioso para valores que implican cálculos computacionalmente costosos. El Hook se llama `useMemo` y la sintaxis es la siguiente:

```javascript
const memoizedValue = useMemo( () => expensiveCalculation(), [] );
```

Se pasa una función que devuelve el valor a memorizar a `useMemo` como primer argumento. La función en este primer argumento debe realizar el cálculo costoso.

El segundo argumento pasado a `useMemo` es un array de dependencias. Por lo tanto, si la función `expensiveCalculation` tiene dependencias `a` y `b`, la llamada será la siguiente:

```javascript
const memoizedValue = useMemo( () => expensiveCalculation(a, b), [a, b] );
```

Cuando cambia alguna de las dependencias, la función en el primer argumento se ejecuta nuevamente para devolver un nuevo valor para memorizar. En el ejemplo anterior, se crea una nueva versión de `memoizedValue` cada vez que cambia `a` o `b`.

El tipo del valor memorizado se infiere, pero se puede definir explícitamente en un parámetro genérico en `useMemo`. El siguiente es un ejemplo de cómo definir explícitamente que el valor memorizado debe tener un tipo `number`:

```typescript
const memoizedValue = useMemo<number>( () => expensiveCalculation(), [] );
```

A continuación, experimentaremos con `useMemo`.

#### Uso del Hook memo

Usaremos el componente `PersonScore` en el que hemos estado trabajando para experimentar con el Hook `useMemo`. Para hacerlo, lleve a cabo los siguientes pasos. Los fragmentos de código utilizados están disponibles en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-memo/src/PersonScore.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-memo/src/PersonScore.tsx):

1. Abra `PersonScore.tsx` e importe `useMemo` desde React:

```typescript
import { useEffect, useReducer, useRef, useMemo } from ‘react’;
```

2. Agregue la siguiente función costosa debajo de las declaraciones de importación:

```javascript
function sillyExpensiveFunction() { console.log(“Executing silly function”); let sum = 0; for (let i = 0; i < 10000; i++) { sum += i; } return sum; }
```

La función suma todos los números entre 0 y 10000 y tardará un tiempo en ejecutarse.

3. Agregue una llamada a la función en el componente `PersonScore` debajo de los efectos:

```typescript
useEffect( ... ); const expensiveCalculation = sillyExpensiveFunction(); if (loading) { return <div>Loading ...</div>; }
```

4. Agregue el resultado de la llamada a la función al JSX debajo de `name` y `score`:

```jsx
<h3> {name}, {score} </h3> <p>{expensiveCalculation}</p> <button ... > Add </button>
```

5. Actualice el navegador que contiene la aplicación y haga clic en los botones. Si mira en la consola, verá que la función costosa se ejecuta cada vez que el componente se vuelve a renderizar después de hacer clic en un botón.

*Figura 3.7 – La función costosa ejecutada varias veces*

Recuerde que se produce un renderizado doble en el modo de desarrollo y el modo estricto de React. Por lo tanto, una vez que se hace clic en un botón, verá `Executing silly function` en la consola dos veces.

Una función costosa que se ejecuta cada vez que se vuelve a renderizar un componente puede provocar problemas de rendimiento.

6. Reestructure la llamada a `sillyExpensiveFunction` de la siguiente manera:

```javascript
const expensiveCalculation = useMemo( () => sillyExpensiveFunction(), [] );
```

El Hook `useMemo` se utiliza para memorizar el valor de la llamada a la función.

7. Actualice el navegador que contiene la aplicación en ejecución y haga clic en los botones. Si mira en la consola, verá que la función costosa solo se ejecuta en el renderizado inicial y no cuando se hace clic en los botones porque se usa el valor memorizado en su lugar.

*Figura 3.8 – La llamada a la función costosa memorizada*

Eso completa nuestra exploración del Hook `useMemo`. La conclusión de esta sección es que el Hook `useMemo` ayuda a mejorar el rendimiento de las llamadas a funciones al memorizar sus resultados y usar el valor memorizado cuando se vuelve a ejecutar la función.

> [!NOTE]
> Para obtener más información sobre `useMemo`, consulte la documentación de React en [https://react.dev/reference/react/useMemo](https://react.dev/reference/react/useMemo).

A continuación, veremos otro Hook que puede ayudar al rendimiento.

---

### Uso del Hook callback

En esta sección, aprenderemos sobre el Hook callback y dónde es útil. Luego usaremos el Hook en el componente `PersonScore` en el que hemos estado trabajando.

#### Comprender el Hook callback

El Hook callback memoriza una función para que no se vuelva a crear en cada renderizado. El Hook se llama `useCallback` y la sintaxis es la siguiente:

```javascript
const memoizedCallback = useCallback(() => someFunction(), []);
```

Se pasa una función que ejecuta la función a memorizar a `useCallback` como primer argumento. El segundo argumento pasado a `useCallback` es un array de dependencias. Por lo tanto, si la función `someFunction` tiene dependencias `a` y `b`, la llamada será la siguiente:

```javascript
const memoizedCallback = useCallback( () => someFunction(a, b), [a, b] );
```

Cuando cambia alguna de las dependencias, la función en el primer argumento se ejecuta nuevamente para devolver una nueva función para memorizar. En el ejemplo anterior, se crea una nueva versión de `memoizedCallback` cada vez que cambia `a` o `b`.

El tipo de la función memorizada se infiere, pero se puede definir explícitamente en un parámetro genérico en `useCallback`. Aquí hay un ejemplo de cómo definir explícitamente que la función memorizada no tiene parámetros y devuelve `void`:

```typescript
const memoizedValue = useCallback<() => void>( () => someFunction (), [] );
```

Un caso de uso común para `useCallback` es evitar re-renderizados innecesarios de componentes secundarios (*child components*). Antes de probar `useCallback`, nos tomaremos el tiempo para comprender cuándo se vuelve a renderizar un componente.

#### Comprender cuándo se vuelve a renderizar un componente

Ya entendemos que un componente se vuelve a renderizar cuando cambia su estado. Considere el siguiente componente:

```jsx
export function SomeComponent() { const [someState, setSomeState] = useState(‘something’); return ( <div> <ChildComponent /> <AnotherChildComponent something={someState} /> <button onClick={() => setSomeState(‘Something else’)} ></button> </div> ); }
```

Cuando `someState` cambie, `SomeComponent` se volverá a renderizar; por ejemplo, cuando se hace clic en el botón. Además, `ChildComponent` y `AnotherChildComponent` se volverán a renderizar cuando cambie `someState`. Esto se debe a que un componente se vuelve a renderizar cuando su componente padre se vuelve a renderizar.

Puede parecer que este comportamiento de re-renderizado causará problemas de rendimiento, particularmente cuando un componente se renderiza cerca de la parte superior de un árbol de componentes grande. Sin embargo, rara vez causa problemas de rendimiento. Esto se debe a que el DOM solo se actualizará después de un re-renderizado si el Virtual DOM cambia, y actualizar el DOM es la parte lenta del proceso. En el ejemplo anterior, el DOM para `ChildComponent` no se actualizará cuando `SomeComponent` se vuelva a renderizar si la definición de `ChildComponent` es la siguiente:

```jsx
export function ChildComponent() { return <span>A child component</span>; }
```

El DOM para `ChildComponent` no se actualizará durante un re-renderizado porque el Virtual DOM no cambiará.

Si bien este comportamiento de re-renderizado generalmente no causa problemas de rendimiento, puede causar problemas de rendimiento si un componente computacionalmente costoso se vuelve a renderizar con frecuencia o si un componente con un efecto secundario lento se vuelve a renderizar con frecuencia. Por ejemplo, querríamos evitar re-renderizados innecesarios en componentes con un efecto secundario que obtiene datos.

Existe una función llamada `memo` en React que se puede utilizar para evitar re-renderizados innecesarios. La función `memo` se puede aplicar de la siguiente manera a `ChildComponent` para evitar re-renderizados innecesarios:

```jsx
export const ChildComponent = memo(() => { return <span>A child component</span>; });
```

La función `memo` envuelve el componente y memoriza el resultado para un conjunto dado de props. La función memorizada se utiliza luego durante un re-renderizado si las props son las mismas. Tenga en cuenta que el fragmento de código anterior utiliza la sintaxis de funciones flecha para que el componente pueda ser una exportación con nombre.

En resumen, la función `memo` de React puede evitar el re-renderizado innecesario de componentes lentos.

A continuación, utilizaremos la función `memo` y el Hook `useCallback` para evitar re-renderizados innecesarios.

#### Uso del Hook callback

Ahora refactorizaremos el componente `PersonScore` extrayendo el botón Reset en un componente separado llamado `Reset`. Esto provocará un re-renderizado innecesario del componente `Reset`, que resolveremos utilizando la función `memo` de React y el Hook `useCallback`.

Para hacerlo, lleve a cabo los siguientes pasos. Los fragmentos de código utilizados están disponibles en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-callback/src/PersonScore.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter03/use-callback/src/PersonScore.tsx):

1. Comience creando un nuevo archivo en la carpeta `src` para el componente del botón Reset llamado `Reset.tsx` con el siguiente contenido:

```tsx
type Props = { onClick: () => void, }; export function Reset({ onClick }: Props) { console.log(“render Reset”); return <button onClick={onClick}>Reset</button>; }
```

El componente recibe un manejador de clics y muestra el botón Reset.
El componente también envía `render Reset` a la consola para que podamos ver claramente cuándo se vuelve a renderizar el componente.

2. Abra `PersonScore.tsx` e importe el componente `Reset`:

```typescript
import { Reset } from ‘./Reset’;
```

3. Reemplace el botón Reset existente con el nuevo componente `Reset` de la siguiente manera:

```jsx
<div> ... <button onClick={() => dispatch({ type: ‘decrement’ })}> Subtract </button> <Reset onClick={() => dispatch({ type: ‘reset’ }) } /> </div>;
```

4. Vaya a la aplicación que se ejecuta en el navegador y abra las herramientas de desarrollo de React. Asegúrese de que la opción **Highlight updates when components render.** esté marcada en la configuración del panel **Components**:

*Figura 3.9 – La opción de resaltar re-renderizados*

5. En el navegador, el botón Reset funcionará como antes. Haga clic en este botón, así como en los botones Add y Subtract. Si mira la consola, notará que `Reset` se vuelve a renderizar innecesariamente. También verá el resaltado de re-renderizado alrededor del botón Reset.

*Figura 3.10 – Los re-renderizados innecesarios del componente Reset*

6. Utilice la herramienta Inspeccionar del navegador para inspeccionar el DOM. Para hacer esto, haga clic derecho en el botón Reset y elija **Inspect**. Haga clic en los botones y observe los elementos del DOM. Las herramientas de desarrollo en Chrome resaltan los elementos cuando se actualizan. Verá que solo se actualizó el contenido del elemento `h3`: ninguno de los otros elementos se resalta debido a que se produjo una actualización.

*Figura 3.11 – El elemento h3 se actualizó después de un re-renderizado*

Aunque `Reset` se vuelve a renderizar innecesariamente, no da como resultado una actualización del DOM. Además, `Reset` no es computacionalmente costoso y no contiene efectos secundarios. Por lo tanto, el renderizado innecesario no es realmente un problema de rendimiento. Sin embargo, utilizaremos este ejemplo para aprender cómo la función `memo` de React y el Hook `useCallback` pueden evitar el renderizado innecesario.

7. Ahora agregaremos la función `memo` de React para intentar evitar re-renderizados innecesarios. Abra `Reset.tsx` y agregue una declaración de importación de React para la importación de `memo` en la parte superior del archivo:

```typescript
import { memo } from ‘react’;
```

8. Ahora, envuelva `memo` alrededor del componente `Reset` de la siguiente manera:

```tsx
export const Reset = memo(({ onClick }: Props) => { console.log(“render Reset”); return <button onClick={onClick}>Reset</button>; });
```

Además, agregue la siguiente línea debajo de la definición del componente `Reset` para que tenga un nombre significativo en las herramientas de desarrollo de React:

```typescript
Reset.displayName = ‘Reset’;
```

9. En el navegador, haga clic en los botones Add, Subtract y Reset. Luego, mire la consola y observe que `Reset` todavía se vuelve a renderizar innecesariamente.

10. Usaremos las herramientas de desarrollo de React para comenzar a comprender por qué `Reset` todavía se vuelve a renderizar innecesariamente cuando su resultado está memorizado. Abra el panel **Profiler** y haga clic en el icono del engranaje para abrir la configuración. Vaya a la sección de configuración de Profiler y asegúrese de que la opción **Record why each component rendered while profiling.** esté marcada:

*Figura 3.12 – Asegurarse de que la opción Record why each component rendered while profiling. esté marcada*

11. Haga clic en el icono del círculo azul para iniciar la creación de perfiles y luego haga clic en el botón Add en nuestra aplicación. Haga clic en el icono del círculo rojo para detener la creación de perfiles.

12. En el gráfico de llamas (*flamegraph*) que aparece, haga clic en la barra de `Reset`. Esto proporciona información útil sobre el re-renderizado del componente `Reset`:

*Figura 3.13 – Información sobre el re-renderizado de Reset*

Por lo tanto, el renderizado innecesario de `Reset` ocurre porque la prop `onClick` cambia. El manejador `onClick` contiene el mismo código, pero se crea una nueva instancia de la función en cada renderizado. Esto significa que `onClick` tendrá una referencia diferente en cada renderizado. La referencia cambiante de la prop `onClick` significa que no se utiliza el resultado memorizado de `Reset` y se produce un re-renderizado en su lugar.

13. Podemos usar el Hook `useCallback` para memorizar el manejador `onClick` y evitar el re-renderizado. Abra `PersonScore.tsx` y comience refactorizando el manejador en una función con nombre:

```tsx
const handleReset = () => dispatch({ type: ‘reset’ }); if (loading) { return <div>Loading ...</div>; } return ( <div> ... <Reset onClick={handleReset} /> </div> );
```

14. Ahora, agregue `useCallback` a la declaración de importación de React:

```typescript
import { useEffect, useReducer, useRef, useMemo, useCallback } from ‘react’;
```

15. Agregue un manejador de clic de reinicio memorizado, de la siguiente manera:

```typescript
const handleReset = () => dispatch({ type: “reset” }); const handleResetMemoized = useCallback( handleReset, [], );
```

16. Por último, cambie la prop `onClick` en `Reset` para hacer referencia al nuevo manejador memorizado:

```jsx
<Reset onClick={handleResetMemoized} />;
```

Ahora, si hace clic en los botones Add, Subtract y Reset, notará que `Reset` ya no se vuelve a renderizar innecesariamente. Con esto se completa nuestra exploración del Hook `useCallback`.

He aquí un resumen rápido de todo lo que aprendimos en esta sección:
- Un componente se vuelve a renderizar cuando su componente padre se vuelve a renderizar.
- La función `memo` de React se puede utilizar para evitar re-renderizados innecesarios en componentes secundarios.
- `useCallback` se puede utilizar para memorizar funciones. Esto se puede usar para crear una referencia estable para las props de función pasadas a componentes secundarios para evitar re-renderizados innecesarios.
- La función `memo` de React y `useCallback` deben usarse con prudencia: asegúrese de que ayuden al rendimiento antes de usarlos porque aumentan la complejidad del código.

> [!NOTE]
> Para más información sobre `useCallback`, consulte la documentación de React en [https://react.dev/reference/react/useCallback](https://react.dev/reference/react/useCallback).

A continuación, abordaremos algunos otros Hooks de React.

---

### Otros React Hooks

En esta sección, abordaremos algunos otros Hooks de React. También mencionaremos algunos Hooks de React que se tratan en profundidad en capítulos posteriores de este libro.

#### useId

El Hook `useId` genera identificadores únicos y normalmente se utiliza para atributos de accesibilidad en componentes reutilizables. El siguiente es un componente `Field` reutilizable donde se usa `useId` para asociar la etiqueta y el input para lectores de pantalla:

```tsx
export function Field({ label, name }: ... ) { const id = useId(); return ( <div> <label htmlFor={id}>{label}</label> <input id={id} name={name} type=”text” /> </div> ); }
```

El código completo de este ejemplo se encuentra en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter03/use-id](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter03/use-id).

Para obtener más información sobre `useId`, consulte la documentación de React en [https://react.dev/reference/react/useId](https://react.dev/reference/react/useId).

#### useTransition

El Hook `useTransition` permite que las transiciones de estado no bloqueen la interfaz de usuario, lo que lo hace ideal para transiciones de estado que pueden tardar algún tiempo en completarse.

El siguiente componente permite a un usuario filtrar una lista grande de nombres. Cuando el usuario escribe un carácter en el input de búsqueda, el estado de la lista se actualiza con los nombres filtrados. La transición de React marca la actualización del estado de la lista como de baja prioridad, lo que evita que el input de búsqueda se retrase cuando el usuario escribe en él.

El Hook `useTransition` devuelve una tupla que contiene lo siguiente:
- Un indicador (*flag*) de si la transición está en curso. La bandera se llama `isPending` en el siguiente ejemplo.
- Una función para iniciar la transición. La función se llama `startTransition` en el siguiente ejemplo.

Aquí está el ejemplo:

```tsx
function App() { const [query, setQuery] = useState(“”); const [list, setList] = useState(names); const [isPending, startTransition] = useTransition(); return ( <div> <input ... value={query} onChange={(e) => { setQuery(e.target.value); startTransition(() => { setList( names.filter((name) => name .toLowerCase() .includes(e.target.value. toLowerCase()), ), ); }); }} /> {isPending && <p>Loading...</p>} <ul> {list.map((name, index) => ( <li key={index}>{name}</li> ))} </ul> </div> ); }
```

El código completo de este ejemplo se encuentra en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter03/use-transition](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter03/use-transition).

Para obtener más información sobre `useTransition`, consulte la documentación de React en [https://react.dev/reference/react/useTransition](https://react.dev/reference/react/useTransition).

#### useDeferredValue

Al igual que `useTransition`, el Hook `useDeferredValue` ayuda a optimizar la interfaz de usuario. El Hook `useDeferredValue` se utiliza para aplazar la actualización de un valor primitivo.

El siguiente ejemplo es similar al ejemplo de `useTransition`: permite al usuario filtrar una gran lista de nombres. Sin embargo, se utiliza `useDeferredValue` para optimizar la interfaz de usuario.

En este ejemplo, la lista filtrada no se almacena en el estado; en su lugar, se calcula en cada renderizado en el componente `List`. Por lo tanto, el componente `List` es lento para renderizarse en listas grandes.

`useDeferredValue` aplaza la actualización de `deferredQuery`, lo que, a su vez, aplaza el renderizado de `List`. Esto permite que el input de búsqueda se sienta menos retrasado cuando se escribe en él:

```tsx
function App() { const [query, setQuery] = useState(“”); const deferredQuery = useDeferredValue(query); return ( <div> <input ... value={query} onChange={(e) => { setQuery(e.target.value); }} /> {query !== deferredQuery && <p>Loading...</p>} <List query={deferredQuery} /> </div> ); }
```

El componente `List` se envuelve en la función `memo` de React para que no se vuelva a renderizar cuando cambia `query` y solo se vuelva a renderizar cuando cambia `deferredQuery`:

```tsx
const List = memo(function List({ query }: ... ) { const list = names.filter((name) => name.toLowerCase().includes(query.toLowerCase()), ); return ( <ul> {list.map((name, index) => ( <li key={index}>{name}</li> ))} </ul> ); });
```

El código completo de este ejemplo se encuentra en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter03/use-deferred-value](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter03/use-deferred-value).

Para obtener más información sobre `useDeferredValue`, consulte la documentación de React en [https://react.dev/reference/react/useDeferredValue](https://react.dev/reference/react/useDeferredValue).

#### Hooks cubiertos en otros capítulos

Cubriremos los siguientes Hooks en detalle en algunos de los capítulos posteriores:

- `useContext`: Se utiliza para acceder a un contexto de React, que cubrimos en el [Capítulo 10](https://subscription.packtpub.com/book/web-development/9781836643173/10), *Gestión del Estado*.
- `useOptimistic`: Se utiliza para actualizaciones optimistas de la interfaz de usuario antes de la confirmación del servidor. Se trata en el [Capítulo 9](https://subscription.packtpub.com/book/web-development/9781836643173/9), *Trabajo con Formularios*.
- `useActionState`: Se utiliza comúnmente para el estado del formulario. Se cubre en el [Capítulo 9](https://subscription.packtpub.com/book/web-development/9781836643173/9), *Trabajo con Formularios*.
- `useFormStatus`: Realiza un seguimiento del estado de envío del formulario y también se cubre en el [Capítulo 9](https://subscription.packtpub.com/book/web-development/9781836643173/9), *Trabajo con Formularios*.

A continuación, resumiremos el capítulo.

---

### Resumen

En este capítulo, aprendimos que todos los Hooks de React deben llamarse en el nivel superior de un componente funcional y no se pueden llamar condicionalmente.

El Hook `useEffect` se puede utilizar para ejecutar efectos secundarios del componente cuando se renderiza. Aprendimos cómo usar `useEffect` para obtener datos, que es un caso de uso común.

`useReducer` es una alternativa a `useState` para usar el estado, y experimentamos con ambos enfoques en nuestro componente de ejemplo `PersonScore`. `useState` es excelente para valores de estado primitivos. `useReducer` es excelente para valores de estado de objetos complejos, particularmente cuando los cambios de estado dependen de los valores de estado anteriores.

El Hook ref crea un valor mutable y no provoca un re-renderizado cuando cambia. Usamos `useRef` para enfocar un elemento HTML después de que se renderizó, lo cual es un caso de uso común.

Los Hooks `useMemo` y `useCallback` se pueden usar para memorizar valores y funciones, respectivamente, y se pueden usar para la optimización del rendimiento. Los ejemplos que usamos para estos Hooks fueron un poco artificiales y el uso de `useCallback` no mejoró el rendimiento, así que recuerde verificar que el uso de estos Hooks realmente mejore el rendimiento.

Hasta ahora en este libro, los componentes que hemos creado no tienen estilos. En el próximo capítulo, aprenderemos varios enfoques para aplicar estilos a componentes de React.

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido sobre los React Hooks:

1. El siguiente componente renderiza un texto durante 5 segundos. Sin embargo, esto es problemático. ¿Cuál es el problema?

```tsx
export function TextVanish({ text }: Props) { if (!text) { return null; } const [textToRender, setTextToRender] = useState(text); useEffect(() => { setTimeout(() => setTextToRender(“”), 5000); }, []); return <span>{textToRender}</span>; }
```

2. El siguiente código es un fragmento de un componente de React que obtiene algunos datos y los almacena en el estado. Sin embargo, hay varios problemas con este código. ¿Puedes detectar alguno de los problemas?

```typescript
const [data, setData] = useState([]); useEffect(async () => { const data = await getData(); setData(data); });
```

3. Considere la siguiente función reductora:

```typescript
type State = { steps: number }; type Action = | { type: ‘forward’; steps: number } | { type: ‘backwards’; steps: number }; function reducer(state: State, action: Action): State { switch (action.type) { case ‘forward’: return { ...state, steps: state.steps + action.steps }; case ‘backwards’: return { ...state, steps: state.steps - action.steps }; default: return state; } }
```

¿A qué se reducirá el tipo del parámetro `action` en la rama del switch `'backwards'`?

4. Considere el siguiente componente `Counter`:

```tsx
export function Counter() { const [count, setCount] = useState(0); const memoCount = useMemo(() => count, []); return ( <div> <button onClick={() => setCount(count + 1)}> {memoCount} </button> </div> ); }
```

¿Cuál será el contenido del botón después de hacer clic en él una vez?

5. Considere el siguiente componente `Counter`:

```tsx
export function Counter() { const [count, setCount] = useState(0); const handleClick = useCallback(() => { setCount(count + 1); }, []); return ( <div> <button onClick={handleClick}>{count}</button> </div> ); }
```

¿Cuál será el contenido del botón después de hacer clic en él dos veces?

---

### Respuestas

Aquí están las respuestas a las preguntas anteriores:

1. El problema con el componente es que tanto `useState` como `useEffect` se llaman condicionalmente (cuando la prop `text` está definida), y React no permite que sus Hooks se llamen condicionalmente. Colocar los Hooks antes de la sentencia `if` resuelve el problema:

```tsx
export function TextVanish({ text }: Props) { const [textToRender, setTextToRender] = useState(text); useEffect(() => { setTimeout(() => setTextToRender(“”), 5000); }, []); if (!text) { return null; } return <span>{textToRender}</span>; }
```

2. El problema principal con el código es que la función de efecto no se puede marcar como asincrónica con la palabra clave `async`. Una solución es volver a la sintaxis anterior de promesas:

```typescript
const [data, setData] = useState([]); useEffect(() => { getData().then((theData) => setData(theData)); });
```

El otro problema importante es que no se definen dependencias en la llamada a `useEffect`. Esto significa que la función de efecto se ejecutará en cada renderizado. La función de efecto establece algún estado, lo que provoca un re-renderizado. Por lo tanto, el componente seguirá re-renderizándose y la función de efecto seguirá ejecutándose indefinidamente. Un array vacío pasado al segundo argumento de `useEffect` resolverá el problema:

```typescript
useEffect(() => { getData().then((theData) => setData(theData)); }, []);
```

Otro problema es que el estado `data` tendrá el tipo `any[]`, lo cual no es ideal. En este caso, probablemente sea mejor definir explícitamente el tipo del estado de la siguiente manera:

```typescript
const [data, setData] = useState<Data[]>([]);
```

El último problema es que el estado `data` podría establecerse después de que el componente se haya desmontado, lo que puede provocar fugas de memoria. Una solución es establecer una bandera cuando el componente se desmonte y no establecer el estado cuando la bandera esté establecida:

```typescript
useEffect(() => { let cancel = false; getData().then((theData) => { if (!cancel) { setData(theData); } }); return () => { cancel = true; }; }, []);
```

3. TypeScript limitará el tipo del parámetro `action` a `{ type: ‘backwards’; steps: number }` en la rama del switch `'backwards'`.

4. El contenido del botón siempre será `0` porque el recuento inicial de 0 está memorizado y nunca se actualiza.

5. El contenido del botón será `1` después de un clic y permanecerá en `1` después de los clics posteriores. Por lo tanto, después de dos clics, será `1`. La clave aquí es que la función `handleClick` solo se crea cuando el componente se renderiza inicialmente porque `useCallback` la memoriza. Por lo tanto, el estado `count` siempre será `0` dentro de la función memorizada. Esto significa que el estado `count` siempre se actualizará a `1`, que aparecerá en el contenido del botón.
