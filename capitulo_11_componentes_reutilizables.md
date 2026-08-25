# Parte 4: React Avanzado

## Capítulo 11: Componentes reutilizables

En este capítulo, crearemos un componente de lista de verificación (*checklist*) y utilizaremos varios patrones para hacerlo altamente reutilizable pero con un tipado estricto (*strongly typed*).

Comenzaremos utilizando los **genéricos de TypeScript** para tipar fuertemente los datos pasados al componente. Luego, utilizaremos el patrón de **propagación de props (prop spreading)** para hacer que la API del componente sea flexible y permitir que los consumidores del componente rendericen de forma personalizada partes del componente utilizando el patrón de **render props**. Después de eso, aprenderemos cómo crear **Hooks personalizados (custom hooks)** y los usaremos para extraer la lógica de los elementos marcados y cómo hacer que el estado dentro de un componente sea **controlable** para cambiar el comportamiento del componente.

Al final de este capítulo, habrá dominado patrones clave de TypeScript y React que fortalecerán su capacidad para crear componentes flexibles, mantenibles y reutilizables.

Cubriremos los siguientes temas:

- Creación del proyecto
- Uso de generic props
- Uso de prop spreading
- Uso de render props
- Adición de la funcionalidad de marcado (*checked*)
- Creación de custom hooks
- Permitir que el estado interno sea controlado

---

### Requisitos técnicos

Utilizaremos las siguientes tecnologías en este capítulo:

- **Node.js y npm**: Puede instalarlos aquí: [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
- **Visual Studio Code**: Puede instalarlo aquí: [https://code.visualstudio.com/](https://code.visualstudio.com/)

Todos los fragmentos de código de este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter11](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter11)

---

### Creación del proyecto

En esta breve sección, crearemos el proyecto para la aplicación que crearemos y su estructura de carpetas. La estructura de carpetas será sencilla porque contiene una sola página con el componente de lista de verificación que crearemos.

Desarrollaremos la aplicación utilizando Visual Studio Code como en capítulos anteriores, así que abra Visual Studio Code y realice los siguientes pasos:

1. Cree el proyecto utilizando Vite. Consulte el [Capítulo 2](https://subscription.packtpub.com/book/web-development/9781836643173/2), *Primeros pasos con TypeScript*, si no recuerda los pasos para esto.
2. Copie los estilos del repositorio de GitHub en el archivo [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter11/start/src/index.css](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter11/start/src/index.css), sobrescribiendo el contenido existente.

Eso completa la configuración del proyecto. A continuación, aprenderemos sobre un patrón que permite que un componente reutilizable acepte diferentes tipos de datos de una manera fuertemente tipada.

---

### Uso de generic props

En esta sección, nos tomaremos un tiempo para comprender cómo crear nuestros propios tipos genéricos y también aprenderemos sobre la característica `keyof` de TypeScript, que es útil para los tipos genéricos. Usaremos este conocimiento para construir la primera iteración del componente de lista de verificación con un tipo genérico para sus props.

#### Comprender los genéricos

Hemos utilizado genéricos a lo largo de este libro. Por ejemplo, el Hook `useState` tiene un parámetro genérico opcional para el tipo de variable de estado:

```typescript
const [visible, setVisible] = useState<boolean>()
```

Los parámetros genéricos en una función permiten que esa función sea reutilizable con diferentes tipos y tenga un tipado fuerte. La siguiente función devuelve el primer elemento de un array, o `null` si el array está vacío. Sin embargo, la función solo funciona con un array de cadenas:

```typescript
function first(array: Array<string>): string | null { return array.length === 0 ? null : array[0]; }
```

Los genéricos nos permiten hacer que esta función sea utilizable con cualquier tipo de array.

#### Funciones genéricas

Aunque hemos utilizado funciones genéricas a lo largo de este libro, todavía no hemos creado nuestra propia función genérica. Los parámetros de tipo genérico se definen entre corchetes angulares antes de los paréntesis de la función:

```typescript
function someFunc<T1, T2, ...>(...) { ... }
```

El nombre de un tipo genérico puede ser el que desee, pero debe ser significativo para que sea fácil de entender.

Aquí hay una versión genérica de la función que vimos anteriormente. Ahora, puede funcionar con arrays que contienen cualquier tipo de elemento:

```typescript
function first<Item>(array: Array<Item>): Item | null { return array.length === 0 ? null : array[0]; }
```

La función tiene un único parámetro genérico llamado `Item`, que se utiliza en el tipo del parámetro de función `array`, así como en el tipo de retorno de la función.

#### Tipos genéricos

Los tipos personalizados también pueden ser genéricos. Para un alias de tipo, sus parámetros genéricos se definen entre corchetes angulares después del nombre del tipo:

```typescript
type TypeName<T1, T2, …> = { ... }
```

Por ejemplo, las props de un componente de React pueden ser genéricas. Un ejemplo de un tipo `Props` genérico es el siguiente:

```typescript
type Props<Item> = { items: Item[]; ... };
```

El tipo `Props` tiene un único parámetro genérico llamado `Item`, que se utiliza en el tipo de la prop `items`.

#### El operador keyof

El operador `keyof` es un operador de TypeScript que devuelve una unión de tipos de literales de cadena que representan todas las claves de un tipo determinado:

```typescript
type User = { name: string; email: string; }; type UserKeys = keyof User; // “name” | “email”
```

Puede combinar `keyof` con un tipo genérico para hacer referencia dinámicamente a las claves en el parámetro genérico:

```typescript
type List<Item> = { items: Item[]; id: keyof Item; }; const users: List<User> = { items: [{ name: ‘user1’, email: ‘user1@somewhere.com’ }], id: ‘name’, // must be ‘name’ or ‘email’ };
```

En el ejemplo anterior, la propiedad `id` del objeto `users` debe ser una clave del tipo `User`, es decir, `'name'` o `'email'`.

#### Componentes de React genéricos

Las props genéricas se pueden integrar en una función genérica para producir un componente de React genérico. Aquí hay un ejemplo de un componente genérico `List`:

```tsx
type Props<Item> = { items: Item[]; }; export function List<Item>({ items }: Props<Item>) { ... }
```

La prop `items` en el componente `List` ahora puede tener cualquier tipo, lo que hace que el componente sea flexible y reutilizable.

Ahora que entendemos cómo crear un componente con props genéricas, crearemos la primera iteración del componente de lista de verificación.

#### Creación de un componente de lista básico

Ahora comenzaremos a crear nuestro componente reutilizable. En esta iteración, será una lista básica que contiene texto principal y secundario obtenido de un array de datos.

Lleve a cabo los siguientes pasos:

1. Comience creando una carpeta para el componente llamada `Checklist` en la carpeta `src`. Luego, cree un archivo llamado `Checklist.tsx` en esta carpeta.
2. Abra `Checklist.tsx` y agregue el siguiente tipo `Props`:

```typescript
type Props<Data> = { data: Data[]; id: keyof Data; primary: keyof Data; secondary: keyof Data; };
```

Aquí hay una explicación de cada prop:
- La prop `data` son los datos que dirigen los elementos de la lista.
- La prop `id` es el nombre de la propiedad en cada elemento de datos que identifica de forma única al elemento.
- La prop `primary` es el nombre de la propiedad en cada elemento de datos que contiene el texto principal que se representará en cada elemento.
- La prop `secondary` es el nombre de la propiedad en cada elemento de datos que incluye el texto suplementario que se representará en cada elemento.

3. A continuación, comience a implementar la función del componente de la siguiente manera:

```tsx
export function Checklist<Data>({ data, id, primary, secondary, }: Props<Data>) { return ( <ul> {data.map((item) => { })} </ul> ); }
```

El componente representa un elemento de lista desordenada (`ul`). Mapea sobre los elementos de datos donde eventualmente renderizaremos cada elemento de la lista.

4. Comience a implementar la función dentro de `data.map` como se resalta aquí:

```tsx
{data.map((item) => { const idValue = item[id] as unknown; if ( typeof idValue !== ‘string’ && typeof idValue !== ‘number’ ) { return null; } const primaryText = item[primary] as unknown; if (typeof primaryText !== ‘string’) { return null; } const secondaryText = item[secondary] as unknown; }
```

La función comprueba si el identificador único (`idValue`) es una cadena o un número y, si no, no se representa nada. La función también comprueba que la propiedad de texto principal (`primaryText`) sea una cadena y, nuevamente, si no, no se representa nada.

Es importante realizar estas comprobaciones de tipo en tiempo de ejecución porque queremos que el componente `Checklist` sea reutilizable con muchas fuentes de datos diferentes, incluidos los datos de un servidor.

5. Finalice la implementación de la función `map` renderizando los elementos de la lista de la siguiente manera:

```tsx
{data.map((item) => { ... return ( <li key={idValue} > <div className=”primary”> {primaryText} </div> {typeof secondaryText === ‘string’ && ( <div className=”secondary”> {secondaryText} </div> )} </li> ); })}
```

Los elementos de la lista se representan con un elemento `div` principal y un elemento `div` secundario opcional.

6. Cree un nuevo archivo en la carpeta `Checklist` llamado `index.ts` y exporte el componente `Checklist` en él:

```typescript
export * from ‘./Checklist’;
```

Este archivo simplificará las declaraciones de importación para el componente `Checklist`.

7. El paso final antes de ver el componente en acción es agregarlo al árbol de componentes en la aplicación. Abra `App.tsx` y reemplace el contenido con lo siguiente:

```tsx
import { Checklist } from ‘./Checklist’; function App() { return ( <div> <Checklist data={[ { id: 1, name: ‘Lucy’, role: ‘Manager’ }, { id: 2, name: ‘Bob’, role: ‘Developer’ }, ]} id=”id” primary=”name” secondary=”role” /> </div> ); } export default App;
```

Hacemos referencia al componente `Checklist` y le pasamos algunos datos. Observe qué tan seguros en cuanto a tipos son los atributos `id`, `primary` y `secondary`: nos vemos obligados a ingresar un nombre de propiedad válido con los elementos de datos.

8. Ejecute la aplicación ingresando `npm run dev` en la terminal. El componente de lista de verificación aparece como se muestra aquí:

*Figura 11.1 – Nuestro componente básico de lista de verificación*

Actualmente, el componente muestra una lista básica; agregaremos la funcionalidad de marcado más adelante en este capítulo.

Eso completa esta sección sobre generic props.

Para recapitular, aquí hay algunos puntos clave:
- Los genéricos de TypeScript permiten que el código reutilizable tenga un tipado fuerte.
- Las funciones pueden tener parámetros genéricos a los que se hace referencia dentro de la implementación.
- Los tipos también pueden tener parámetros genéricos a los que se hace referencia dentro de la implementación.
- Se puede hacer que un componente de React sea genérico alimentando un tipo de props genérico en un componente de función genérico. La implementación del componente se basará entonces en props genéricas.

A continuación, aprenderemos sobre un patrón que permite que el tipo de props herede props de un elemento HTML.

---

### Uso de prop spreading

En esta sección, aprenderemos sobre un patrón llamado **prop spreading (propagación de props)**. Este patrón es útil cuando desea utilizar todas las props de un elemento HTML en la implementación de un componente. En nuestro componente de lista de verificación, usaremos este patrón para agregar todas las props para el elemento `ul`. Esto permitirá a los consumidores del componente especificar props, como la altura y el ancho de la lista de verificación.

Por lo tanto, lleve a cabo los siguientes pasos:

1. Abra `Checklist.tsx` e importe el siguiente tipo de React:

```typescript
import { ComponentPropsWithoutRef } from ‘react’;
```

Este tipo nos permite hacer referencia a las props de un elemento HTML como `ul`. Es un tipo genérico que toma el nombre del elemento HTML como parámetro genérico.

2. Agregue las props del elemento `ul` al tipo de props del componente de la siguiente manera:

```typescript
type Props<Data> = { data: Data[]; id: keyof Data; primary: keyof Data; secondary: keyof Data; } & ComponentPropsWithoutRef<’ul’>;
```

3. Agregue un parámetro rest llamado `ulProps` para recopilar todas las props para el elemento `ul` en una sola variable `ulProps`:

```tsx
export function Checklist<Data>({ data, id, primary, secondary, ...ulProps }: Props<Data>) { ... }
```

Esta es la primera vez que usamos parámetros rest en este libro. Recopilan múltiples argumentos que se pasan a la función en un array, por lo que cualquier prop que no se llame `data`, `id`, `primary` o `secondary` se recopilará en el array `ulProps`. Para obtener más información sobre los parámetros rest, consulte [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters).

4. Ahora, podemos propagar `ulProps` en el elemento `ul` usando la sintaxis de propagación (*spread*):

```tsx
export function Checklist<Data>({ data, id, primary, secondary, ...ulProps }: Props<Data>) { return ( <ul {...ulProps} >...</ul> ); }
```

Podemos utilizar esta nueva función de `Checklist` para especificar la altura y el ancho de la lista. Abra `App.tsx` y agregue el siguiente atributo `style`, así como más elementos de datos:

```tsx
<Checklist data={[ { id: 1, name: ‘Lucy’, role: ‘Manager’ }, { id: 2, name: ‘Bob’, role: ‘Developer’ }, { id: 3, name: ‘Bill’, role: ‘Developer’ }, { id: 4, name: ‘Tara’, role: ‘Developer’ }, { id: 5, name: ‘Sara’, role: ‘UX’ }, { id: 6, name: ‘Derik’, role: ‘QA’ } ]} id=”id” primary=”name” secondary=”role” style={{ width: ‘300px’, maxHeight: ‘380px’, overflowY: ‘auto’ }} />
```

5. Si la aplicación no se está ejecutando, ejecútela ingresando `npm run dev` en la terminal. El componente de lista de verificación aparece con el tamaño que esperamos:

*Figura 11.2 – El componente de lista de verificación con tamaño definido*

El componente ahora tiene una altura fija con una barra de desplazamiento vertical como resultado del estilo que pasamos al componente.

Eso completa nuestro uso del patrón de propagación de props. He aquí un resumen de los puntos clave:
- Realizamos una intersección (`&`) del tipo de props con `ComponentPropsWithoutRef` para agregar props para el elemento HTML sobre el que queremos propagar.
- Usamos un parámetro rest en las props del componente para recopilar todas las props del elemento HTML en un array.
- Luego podemos propagar el parámetro rest en un elemento HTML en el JSX.

A continuación, aprenderemos sobre un patrón que permite a los consumidores renderizar partes de un componente.

---

### Uso de render props

En esta sección, aprenderemos sobre el patrón **render props** y lo usaremos para permitir que el consumidor del componente renderice elementos dentro del componente de lista de verificación.

#### Comprender el patrón render props

Una forma de hacer que un componente sea altamente reutilizable es permitir que el consumidor represente elementos internos dentro de él. La prop `children` en un elemento de botón es un ejemplo de esto porque nos permite especificar cualquier contenido de botón que queramos:

```tsx
<button>We can specify any content here</button>
```

El patrón render props nos permite utilizar una prop distinta de `children` para proporcionar esta capacidad. Esto resulta útil cuando la prop `children` ya se utiliza para otra cosa, como en el siguiente ejemplo:

```tsx
<Modal heading={<h3>Enter Details</h3>}> Some content </Modal>
```

Aquí, `heading` es una render prop en un componente `Modal`.

Las render props son útiles cuando permiten al consumidor renderizar elementos asociados con los datos pasados al componente porque la render prop puede ser una función:

```tsx
<List data={[...]} renderItem={(item) => <div>{item.text}</div>} />
```

El ejemplo anterior tiene una render prop llamada `renderItem` que renderiza cada elemento de lista en un componente `List`. El elemento de datos se le pasa para que pueda incluir sus propiedades en el elemento de la lista. Esto es similar a lo que implementaremos a continuación para nuestro componente de lista de verificación.

#### Adición de una prop renderItem

Agregaremos una prop llamada `renderItem` a la lista de verificación que permite a los consumidores tomar el control del renderizado de los elementos de la lista. Lleve a cabo los siguientes pasos:

1. Abra `Checklist.tsx` y agregue el tipo `ReactNode` a la declaración de importación de React:

```typescript
import { ComponentPropsWithoutRef, type ReactNode } from ‘react’;
```

`ReactNode` representa un elemento que React puede representar. Por lo tanto, usaremos `ReactNode` como el tipo de retorno para nuestra render prop.

2. Agregue una render prop llamada `renderItem` al tipo `Props`:

```typescript
type Props<Data> = { data: Data[]; id: keyof Data; primary: keyof Data; secondary: keyof Data; renderItem?: (item: Data) => ReactNode; } & ComponentPropsWithoutRef<’ul’>;
```

La prop es una función que recibe el elemento de datos y devuelve lo que necesita ser renderizado. Hemos hecho que la prop sea opcional porque proporcionaremos una implementación predeterminada para los elementos de la lista, pero también permitiremos que los consumidores la anulen.

3. Agregue `renderItem` a los parámetros de la función del componente:

```tsx
export function Checklist<Data>({ data, id, primary, secondary, renderItem, ...ulProps }: Props<Data>) { ... }
```

4. En el JSX, en la parte superior de la función de mapeo, agregue una declaración `if` para verificar si se ha especificado la prop `renderItem`. Si se ha especificado `renderItem`, llámela con el elemento de datos y devuelva su resultado desde la función de mapeo:

```tsx
<ul ...> {data.map((item) => { if (renderItem) { return renderItem(item); } const idValue = item[id] as unknown; ... })} </ul>
```

Por lo tanto, si se ha especificado `renderItem`, se llamará para obtener el elemento que se representará como el elemento de la lista. Si no se ha especificado `renderItem`, renderizará el elemento de la lista como lo hacía anteriormente.

5. Para probar la nueva prop, abra `App.tsx` y agregue el siguiente atributo `renderItem`:

```tsx
<Checklist ... renderItem={(item) => ( <li key={item.id}> <div className=”primary”> {item.name},{“ “} <small style={{ textTransform: “uppercase” }}> {item.role} </small> </div> </li> )} />
```

Los elementos de la lista ahora se representan con el rol en mayúsculas. El atributo `key` en los elementos de la lista se establece en el ID único del elemento, lo que permite a React actualizar eficientemente el DOM cuando los elementos de la lista hayan cambiado, se hayan agregado o se hayan eliminado.

6. Si la aplicación no se está ejecutando, ejecútela ingresando `npm run dev` en la terminal. El componente de lista de verificación aparece con los elementos de la lista anulados:

*Figura 11.3 – Elementos de lista anulados*

7. Antes de continuar con la siguiente sección, elimine el uso de `renderItem` en el elemento `Checklist` en `App.tsx`. Entonces debería aparecer la representación predeterminada de los elementos de la lista.

Eso completa esta sección sobre el patrón render props. Para recapitular, aquí hay algunos puntos clave:
- El patrón render props permite al consumidor de un componente anular el renderizado de partes del componente.
- Una render prop puede ser un elemento o una función que devuelve un elemento.
- Un caso de uso común para una render prop es una lista basada en datos en la que se puede anular la representación de los elementos de la lista.

A continuación, agregaremos la funcionalidad de marcado a nuestro componente de lista de verificación.

---

### Adición de la funcionalidad de marcado (*checked*)

Actualmente, nuestro componente de lista de verificación no contiene la capacidad de marcar elementos, por lo que ahora agregaremos casillas de verificación (*checkboxes*) a la lista de elementos, brindando a los usuarios la capacidad de marcarlos. Haremos un seguimiento de los elementos marcados utilizando un estado de React.

Por lo tanto, lleve a cabo los siguientes pasos para agregar esta funcionalidad a nuestro componente:

1. Abra `Checklist.tsx` y agregue `useState` a la declaración de importación de React:

```typescript
import { ..., useState } from ‘react’;
```

Usaremos el estado para almacenar los IDs de los elementos marcados.

2. En la parte superior de la implementación del componente, agregue el estado para los IDs de los elementos marcados:

```typescript
const [checkedIds, setCheckedIds] = useState<IdValue[]>([]);
```

Hemos hecho referencia a un tipo `IdValue` que aún no hemos definido; definiremos esto después de que hayamos terminado la implementación del componente en el paso 6.

3. Agregue casillas de verificación a la lista de elementos de la siguiente manera:

```tsx
<li key={idValue}> <label> <input type=”checkbox” checked={checkedIds.includes(idValue)} onChange={handleCheckChange(idValue)} /> <div> <div ... > {primaryText} </div> {typeof secondaryText === ‘string’ && ( <div ... > {secondaryText} </div> )} </div> </label> </li>
```

El estado `checkedIds` alimenta el atributo `checked` de la casilla de verificación comprobando si el ID del elemento de la lista está contenido en él.

El elemento `label` envuelve intencionalmente el elemento `input` para que al hacer clic en cualquier lugar dentro de la etiqueta, incluido el texto o los elementos `div` anidados, se alterne la casilla de verificación. Esto mejora la usabilidad para los usuarios con discapacidades motoras que pueden tener dificultades para hacer clic en casillas de verificación pequeñas. Los lectores de pantalla también asociarán el contenido de la etiqueta con la casilla de verificación, por lo que los usuarios que utilicen esta herramienta sabrán qué representa la casilla de verificación.

Implementaremos la función `handleCheckChange` referenciada en el siguiente paso. Observe que la referencia llama a la función pasando el ID del elemento de la lista que se ha marcado.

4. Comience a implementar la función `handleCheckChange` en el componente de la siguiente manera:

```tsx
const [checkedIds, setCheckedIds] = ... const handleCheckChange = (checkedId: IdValue) => () => {}; return ...
```

Esta es una función que devuelve la función de manejador. Esta complejidad se debe a que un manejador de marcado básico no pasa el ID del elemento de la lista. Este enfoque se llama **currificación (currying)**, y se puede encontrar más información sobre él en el siguiente enlace: [https://javascript.info/currying-partials](https://javascript.info/currying-partials).

5. Complete la implementación del manejador de la siguiente manera:

```typescript
const handleCheckChange = (checkedId: IdValue) => () => { const isChecked = checkedIds.includes(checkedId); const newCheckedIds = isChecked ? checkedIds.filter( (itemCheckedid) => itemCheckedid !== checkedId ) : checkedIds.concat(checkedId); setCheckedIds(newCheckedIds); };
```

La implementación actualiza el ID del elemento de la lista en el estado `checkedIds` si el elemento de la lista se ha marcado y lo elimina si no está marcado.

6. A continuación, definamos el tipo `IdValue`. Cree un nuevo archivo en la carpeta `Checklist` llamado `types.ts` con la definición de `IdValue`:

```typescript
export type IdValue = string | number;
```

Aquí, el ID del elemento de la lista puede ser un valor de cadena o de número.

7. Vuelva a `Checklist.tsx` e importe `IdValue`:

```typescript
import { IdValue } from ‘./types’;
```

Los errores de compilación ahora deberían estar resueltos.

8. Si la aplicación no se está ejecutando, ejecútela ingresando `npm run dev` en la terminal. El componente de lista de verificación aparece con casillas de verificación para cada elemento de la lista:

*Figura 11.4 – Casillas de verificación para elementos de lista*

El componente de lista de verificación ahora incluye casillas de verificación. Sin embargo, existe la oportunidad de hacer que la lógica de marcado sea reutilizable; cubriremos esto en la siguiente sección.

---

### Creación de custom hooks

En esta sección, aprenderemos sobre los **custom hooks** de React. Luego, usaremos este conocimiento para extraer la lógica de marcado del componente de lista de verificación en un Hook personalizado reutilizable.

#### Comprender los custom hooks

Además de los Hooks estándar como `useState`, React nos permite crear nuestros propios Hooks personalizados. Los Hooks personalizados permiten aislar la lógica y reutilizarla en múltiples componentes.

Un Hook personalizado se define mediante una función con un nombre que comienza con la palabra `use`. Esta convención de nomenclatura ayuda a ESLint a comprobar si hay problemas con el uso del Hook personalizado. Aquí hay un Hook personalizado que proporciona lógica de alternancia (*toggle*):

```typescript
export function useToggle() { const [toggleValue, setToggleValue] = useState(false); function toggle() { setToggleValue(!toggleValue); } return {toggleValue, toggle}; };
```

El Hook personalizado contiene el estado del valor de alternancia actual, que es `true` o `false`. También incluye una función llamada `toggle`, que alterna el valor actual. El valor de alternancia actual y la función de alternancia se devuelven desde el Hook personalizado en una estructura de objeto.

Tenga en cuenta que no es obligatorio devolver una estructura de objeto. Si el Hook personalizado solo devuelve un único elemento, ese elemento se puede devolver directamente. Si el Hook personalizado devuelve dos cosas (como en el ejemplo anterior), puede devolver una tupla (como lo hace `useState`). Una estructura de objeto es agradable para más de dos elementos porque las claves del objeto dejan claro qué es cada elemento.

Otro rasgo de un Hook personalizado es que **utiliza otros Hooks estándar de React**. Por ejemplo, el Hook personalizado `useToggle` utiliza `useState`. Si el Hook personalizado no llama a un Hook de React ni a otro Hook personalizado, es solo una función normal en lugar de un Hook personalizado.

Este Hook personalizado se puede utilizar luego en la implementación de un componente de la siguiente manera:

```tsx
const { toggleValue, toggle } = useToggle(); return ( <div> <button onClick={toggle}>{toggleValue ? ‘ON’ : ‘OFF’}</button> </div> );
```

El valor de alternancia (`toggleValue`) y la función de alternancia se desestructuran a partir del valor devuelto del Hook personalizado. El valor de alternancia se utiliza para representar el texto ON u OFF dentro del contenido del botón según sea verdadero o falso. La función de alternancia también se asigna al manejador de clics del botón.

Los Hooks personalizados también pueden recibir parámetros. En el ejemplo aquí, hemos agregado un valor predeterminado en el Hook `useToggle`:

```typescript
type Params = { defaultToggleValue?: boolean; }; export function useToggle({ defaultToggleValue }: Params) { const [toggleValue, setToggleValue] = useState( defaultToggleValue ); ... }
```

En el ejemplo anterior, los parámetros están en una estructura de objeto. Una estructura de objeto es adecuada cuando hay varios parámetros y no se rompe nada cuando se agregan nuevos parámetros.

Los argumentos se pasan al Hook personalizado en un objeto. Aquí hay un ejemplo del uso de `useToggle` con su valor inicialmente en `true`:

```typescript
const { toggleValue, toggle } = useToggle({ defaultToggleValue: true });
```

Ahora que entendemos cómo crear y utilizar Hooks personalizados, pondremos esto en práctica en nuestro componente de lista de verificación.

#### Extracción de la lógica de marcado en un custom hook

Extraeremos la lógica para los elementos marcados en un Hook personalizado. Esto permitirá que futuros componentes potenciales utilicen la lógica y limpien un poco el código.

El Hook personalizado se llamará `useChecked` y contendrá el estado para los IDs de elementos de lista marcados. El Hook también incluirá un manejador que se puede adjuntar a las casillas de verificación, actualizando el estado de los IDs de elementos de lista marcados.

Para hacer esto, lleve a cabo los siguientes pasos:

1. En la carpeta `Checklist`, cree un archivo para el Hook personalizado llamado `useChecked.ts`.
2. Abra `useChecked.ts` y agregue las siguientes declaraciones de importación:

```typescript
import { useState } from ‘react’; import { IdValue } from ‘./types’;
```

El Hook utilizará el estado de React que está tipado utilizando `IdValue`.

3. Comience a implementar la función para el Hook personalizado inicializando el estado:

```typescript
export function useChecked() { const [checkedIds, setCheckedIds] = useState<IdValue[]>([]); }
```

El Hook no tiene ningún parámetro. La llamada a `useState` es exactamente la misma que la que se encuentra actualmente en el componente `Checklist`; esto se puede copiar y pegar en el Hook personalizado.

4. Agregue un manejador de marcado al Hook personalizado. Esto se puede copiar de la implementación del componente `Checklist`:

```typescript
export function useChecked() { const [checkedIds, setCheckedIds] = useState<IdValue[]>([]); const handleCheckChange = (checkedId: IdValue) => () => { const isChecked = checkedIds.includes(checkedId); const newCheckedIds = isChecked ? checkedIds.filter( (itemCheckedid) => itemCheckedid !== checkedId ) : checkedIds.concat(checkedId); setCheckedIds(newCheckedIds); }; }
```

5. La última tarea en la implementación del Hook personalizado es devolver los IDs marcados y la función de manejador:

```typescript
export function useChecked() { ... return { handleCheckChange, checkedIds }; }
```

6. A continuación, abra `Checklist.tsx` y elimine la definición de estado y la función de manejador `handleCheckChange`. Además, elimine `useState` e `IdValue` de las declaraciones de importación, ya que son redundantes.
7. Aún en `Checklist.tsx`, importe el Hook `useChecked` que acabamos de crear:

```typescript
import { useChecked } from ‘./useChecked’;
```

8. Agregue una llamada a `useChecked` y desestructure los IDs marcados y la función del manejador:

```tsx
export function Checklist<Data>({ ... }: Props<Data>) { const { checkedIds, handleCheckChange } = useChecked(); return ... }
```

9. Si la aplicación no se está ejecutando, ejecútela ingresando `npm run dev` en la terminal. El componente de lista de verificación aparecerá y se comportará como lo hacía antes de realizar estos cambios.

Eso completa la implementación y el uso del Hook personalizado. Para recapitular, aquí hay algunos puntos clave:
- Los Hooks personalizados hacen que el código sea un poco más limpio y reutilizable porque aíslan la lógica, que puede ser compleja.
- Los Hooks personalizados deben comenzar con `use`.
- Los Hooks personalizados deben usar un Hook de React estándar u otro Hook personalizado.
- Un Hook personalizado es simplemente una función que devuelve cosas útiles para que las utilicen los componentes. Usar una estructura de objeto para los elementos devueltos es ideal al devolver muchos elementos porque las claves del objeto dejan claro qué es cada elemento.
- Un Hook personalizado puede tener parámetros. El uso de una estructura de objeto para los parámetros es ideal para muchos elementos y no rompe nada cuando se agregan nuevos parámetros.

A continuación, cubriremos un patrón que permitirá al consumidor de un componente controlar parte de su comportamiento con el estado.

---

### Permitir que el estado interno sea controlado

En esta sección, aprenderemos cómo permitir que los consumidores de un componente controlen su estado interno. Usaremos este patrón en el componente de lista de verificación para que los usuarios puedan marcar solo un único elemento.

#### Comprender cómo se puede controlar el estado interno

Permitir que los consumidores de un componente controlen el estado permite ajustar el comportamiento de un componente si ese comportamiento está impulsado por el estado. Repasemos un ejemplo utilizando el Hook personalizado `useToggle` que cubrimos en la última sección:

Se requieren dos props adicionales para permitir que se controle el estado interno: una para el valor del estado actual y otra para un manejador de cambios. Estas props adicionales son `toggleValue` y `onToggleValueChange` en `useToggle`:

```typescript
type Params = { defaultToggleValue?: boolean; toggleValue?: boolean; onToggleValueChange?: ( toggleValue: Boolean ) => void; }; export function useToggle({ defaultToggleValue, toggleValue, onToggleValueChange, }: Params) { ... }
```

Estas props están marcadas como opcionales porque este patrón no obliga al consumidor del componente a controlar el estado; es una característica a la que pueden optar voluntariamente (*opt in*).

> [!NOTE]
> El consumidor del componente nunca especificará tanto `defaultToggleValue` como `toggleValue`. El parámetro `defaultToggleValue` solo debe usarse cuando el consumidor no desea controlar `toggleValue` con el estado. Cuando el consumidor desea controlar `toggleValue` con el estado, puede establecer el valor inicial de su propio estado.

La prop `toggleValue` ahora choca con el estado `toggleValue` porque tienen el mismo nombre, por lo que es necesario cambiar el nombre del estado:

```typescript
const [resolvedToggleValue, setResolvedToggleValue] = useState(defaultToggleValue); function toggle() { setResolvedToggleValue(!resolvedToggleValue); } return { resolvedToggleValue, toggle };
```

El valor predeterminado del estado interno ahora debe considerar que podría haber una prop que controle el estado:

```typescript
const [resolvedToggleValue, setResolvedToggleValue] = useState(defaultToggleValue || toggleValue); 
```

Cuando se cambia el estado, se llama al manejador de cambios, si se ha definido:

```typescript
function toggle() { if (onToggleValueChange) { onToggleValueChange(!resolvedToggleValue); } else { setResolvedToggleValue(!resolvedToggleValue); } }
```

Nuevamente, es importante que sigamos actualizando el estado interno en caso de que el consumidor no controle el estado.

El último paso en la implementación de este patrón es actualizar el estado interno cuando se actualiza el estado controlado. Podemos hacer esto con `useEffect` de la siguiente manera:

```typescript
useEffect(() => { const isControlled = toggleValue !== undefined; if (isControlled) { setResolvedToggleValue(toggleValue); } }, [toggleValue]);
```

El efecto se activa cuando cambia la prop de estado. Comprobamos si la prop de estado está siendo controlada; si es así, el estado interno se actualiza con su valor.

Aquí hay un ejemplo de control de `toggleValue` en `useToggle`:

```typescript
const [toggleValue, setToggleValue] = useState(false); const onCount = useRef(0); const { resolvedToggleValue, toggle } = useToggle({ toggleValue, onToggleValueChange: (value) => { if (onCount.current >= 3) { setToggleValue(false); } else { setToggleValue(value); if (value) { onCount.current++; } } }, });
```

Este ejemplo almacena el valor de alternancia en su propio estado y lo pasa a `useToggle`. El parámetro `onToggleValueChange` se maneja actualizando el valor del estado. La lógica para establecer el valor del estado solo permite que sea `true` hasta tres veces.

Por lo tanto, este caso de uso ha anulado el comportamiento predeterminado del interruptor para que solo se pueda establecer en `true` hasta tres veces.

Ahora que entendemos cómo permitir que se controle el estado interno, lo usaremos en nuestro componente de lista de verificación.

#### Permitir que checkedIds sea controlado

Por el momento, nuestro componente de lista de verificación permite seleccionar muchos elementos. Si permitimos que el consumidor controle el estado `checkedIds`, puede cambiar el componente de lista de verificación para que solo pueda seleccionar un único elemento.

Por lo tanto, lleve a cabo los siguientes pasos:

1. Comenzaremos en `useChecked.ts`. Agregue `useEffect` a la declaración de importación de React:

```typescript
import { useState, useEffect } from ‘react’;
```

2. Agregue nuevos parámetros para los IDs marcados controlados y el manejador de cambios:

```typescript
export function useChecked({ checkedIds, onCheckedIdsChange, }: { checkedIds?: IdValue[]; onCheckedIdsChange?: (checkedIds: IdValue[]) => void; }) { ... }
```

3. Actualice el nombre del estado interno a `resolvedCheckedIds` y establézcalo de forma predeterminada en el parámetro `checkedIds` pasado si está definido:

```typescript
export function useChecked({ checkedIds, onCheckedIdsChange, }: Params) { const [resolvedCheckedIds, setResolvedCheckedIds] = useState<IdValue[]>(checkedIds || []); const handleCheckChange = (checkedId: IdValue) => () => { const isChecked = resolvedCheckedIds.includes(checkedId); let newCheckedIds = isChecked ? resolvedCheckedIds.filter( (itemCheckedid) => itemCheckedid !== checkedId ) : resolvedCheckedIds.concat(checkedId); setResolvedCheckedIds(newCheckedIds); }; return { handleCheckChange, resolvedCheckedIds }; }
```

4. Actualice el manejador `handleCheckChange` para llamar al manejador de cambios pasado si está definido:

```typescript
const handleCheckChange = (checkedId: IdValue) => () => { const isChecked = resolvedCheckedIds.includes(checkedId); let newCheckedIds = isChecked ? resolvedCheckedIds.filter( (itemCheckedid) => itemCheckedid !== checkedId ) : resolvedCheckedIds.concat(checkedId); if (onCheckedIdsChange) { onCheckedIdsChange(newCheckedIds); } setResolvedCheckedIds(newCheckedIds); };
```

5. La última tarea en `useChecked.ts` es sincronizar los IDs marcados controlados con el estado interno. Agregue el siguiente Hook `useEffect` para lograr esto:

```typescript
useEffect(() => { const isControlled = checkedIds !== undefined; if (isControlled) { setResolvedCheckedIds(checkedIds); } }, [checkedIds]);
```

6. Ahora, abra `Checklist.tsx` e importe el tipo `IdValue`:

```typescript
import { IdValue } from ‘./types’;
```

7. Agregue las nuevas props para los IDs marcados controlados y el manejador de cambios:

```typescript
type Props<Data> = { data: Data[]; id: keyof Data; primary: keyof Data; secondary: keyof Data; renderItem?: (item: Data) => ReactNode; checkedIds?: IdValue[]; onCheckedIdsChange?: (checkedIds: IdValue[]) => void; } & ComponentPropsWithoutRef<’ul’>; export function Checklist<Data>({ data, id, primary, secondary, renderItem, checkedIds, onCheckedIdsChange, ...ulProps }: Props<Data>) {}
```

8. Pase estas props a `useChecked` y desestructure y use la variable `resolvedCheckedIds`:

```tsx
const { resolvedCheckedIds, handleCheckChange } = useChecked({ checkedIds, onCheckedIdsChange, }); return ( <ul {...ulProps}> {data.map((item) => { ... return ( <li ... > <label> <input type=”checkbox” checked={ resolvedCheckedIds.includes(idValue) } onChange={handleCheckChange(idValue)} /> .. </label> </li> ); })} </ul> );
```

9. Abra `index.ts` en la carpeta `Checklist`. Exporte el tipo `IdValue` porque los consumidores del componente ahora pueden pasar `checkedIds`, que es un array de este tipo:

```typescript
export type { IdValue } from ‘./types’;
```

10. Ahora, abra `App.tsx` e importe `useState` de React, así como el tipo `IdValue`:

```typescript
import { useState } from ‘react’; import { Checklist, IdValue } from ‘./Checklist’; 
```

11. Defina el estado en el componente `App` para el ID marcado único:

```tsx
function App() { const [checkedId, setCheckedId] = useState<IdValue | null>(null); ... }
```

El estado es `null` cuando no hay ningún elemento marcado. Esto no se puede establecer en `undefined` porque `Checklist` pensará que `checkedIds` no está controlado (*uncontrolled*).

12. Cree un manejador para cuando se marque un elemento:

```typescript
function handleCheckedIdsChange(newCheckedIds: IdValue[]) { const newCheckedIdArr = newCheckedIds.filter( (id) => id !== checkedId ); if (newCheckedIdArr.length === 1) { setCheckedId(newCheckedIdArr[0]); } else { setCheckedId(null); } }
```

El manejador almacena el ID marcado en el estado o establece el estado en `null` si el elemento marcado se ha desmarcado.

13. Pase el ID marcado y el manejador de cambios al elemento `Checklist` de la siguiente manera:

```tsx
<Checklist ... checkedIds={checkedId === null ? [] : [checkedId]} onCheckedIdsChange={handleCheckedIdsChange} />;
```

14. Probemos esto. Si la aplicación no se está ejecutando, ejecútela ingresando `npm run dev` en la terminal. Descubrirá que solo se puede marcar un único elemento de la lista.

Eso completa esta sección sobre permitir que se controle el estado interno. He aquí un resumen:
- Este patrón es útil porque cambia el comportamiento del componente.
- El componente debe exponer una prop para controlar el valor del estado y otra para su manejador de cambios.
- Internamente, el componente aún administra el estado y lo sincroniza con el del consumidor usando `useEffect`.
- Si el estado está controlado, se llama al manejador de cambios del consumidor en el manejador de cambios interno.

¡Resumamos el capítulo ahora!

---

### Resumen

En este capítulo, creamos un componente de lista de verificación reutilizable y utilizamos muchos patrones útiles a lo largo del camino.

Comenzamos aprendiendo cómo implementar props genéricas, que permiten que un componente se use con diferentes tipos de datos sin perder el tipado fuerte. Usamos esto para permitir que se pasen diferentes datos al componente de lista de verificación sin sacrificar la seguridad de tipos.

Aprendimos cómo permitir que los consumidores de un componente propaguen props en un elemento interno. Un caso de uso común es propagar props en el elemento contenedor interno para permitir que el consumidor le dé tamaño, que es lo que hicimos con el componente de lista de verificación.

El patrón render prop es uno de los patrones más útiles al desarrollar componentes reutilizables. Aprendimos que permite al consumidor asumir la responsabilidad de renderizar partes del componente. Usamos este patrón para anular el renderizado de elementos de lista en nuestro componente de lista de verificación.

Los Hooks personalizados aíslan la lógica y son útiles para compartir la lógica entre componentes y mantener limpio el código dentro de un componente. Los Hooks personalizados deben llamar a un Hook estándar de React directa o indirectamente. Extrajimos la lógica de marcado de nuestro componente de lista de verificación en un Hook personalizado.

El último patrón que aprendimos fue permitir que se controle el estado interno de un componente. Este poderoso patrón permite al consumidor del componente ajustar su comportamiento. Usamos esto para permitir que solo se marque un único elemento de la lista en nuestro componente de lista de verificación.

En el próximo capítulo, aprenderemos cómo escribir pruebas automatizadas para componentes de React.

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido en este capítulo:

1. El fragmento del siguiente componente representa un componente `Select` que contiene opciones:

```tsx
type Props<TOption> = { options: TOption[]; value: string; label: string; }; export function Select({ options, value, label, }: Props<TOption>) { return ... }
```

Sin embargo, se genera el siguiente error de TypeScript en el parámetro de props del componente: `Cannot find name ‘TOption’`. ¿Cuál es el problema?

2. Las props `value` y `label` del componente de la pregunta 1 solo deben establecerse en un nombre de propiedad en el valor de `options`. ¿Qué tipo podemos darle a `value` y `label` para que TypeScript los incluya en su verificación de tipos?
3. Se ha agregado una prop llamada `option` al componente `Select` de la pregunta anterior de la siguiente manera:

```tsx
type Props<TOption> = { ..., option: ReactNode; }; export function Select<TOption>({ ..., option }: Props<TOption>) { return ( <div> <input /> {options.map((option) => { if (option) { return option; } return ... })} </div> ); }
```

Se supone que la prop `option` permite al consumidor del componente anular el renderizado de las opciones. ¿Puedes detectar el fallo en la implementación?

4. El siguiente es un componente `Field` que representa un elemento `label` y un elemento `input`:

```tsx
type Props = { label: string; } & ComponentPropsWithoutRef<’input’>; export function Field({ ...inputProps, label }: Props) { return ( <> <label>{label}</label> <input {...inputProps} /> </> ); }
```

Sin embargo, hay un problema con la implementación: ¿puedes detectarlo?

5. ¿Cuántas render props puede tener un componente?

---

### Respuestas

1. El tipo genérico debe definirse en la función del componente así como en la prop:

```tsx
export function Select<TOption>({ options, value, label, }: Props<TOption>) { return ... }
```

2. El operador `keyof` se puede utilizar para garantizar que `value` y `label` sean claves en `options`:

```typescript
type Props<TOption> = { options: TOption[]; value: keyof TOption; label: keyof TOption; };
```

3. Es probable que el consumidor necesite los datos para la opción, por lo que la prop debe ser una función que contenga los datos como parámetro:

```tsx
type Props<TOption> = { ..., renderOption: (option: TOption) => ReactNode; }; export function Select<TOption>({ options, value, label, renderOption, }: Props<TOption>) { return ( <div> <input /> {options.map((option) => { if (renderOption) { return renderOption(option); } return ... </div> ); }
```

4. Hay un error de sintaxis porque el parámetro rest es el primer parámetro. El parámetro rest debe ser el último:

```tsx
export function Field({ label, ...inputProps }: Props) { ... }
```

5. No hay límite en la cantidad de render props que puede tener un componente.
