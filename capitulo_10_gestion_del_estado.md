# Parte 4: React Avanzado

## Capítulo 10: Gestión del estado (*State Management*)

Ya hemos utilizado el estado de React muchas veces a lo largo de este libro. En este capítulo, cubriremos el estado de React en profundidad, comenzando por comprender los diferentes tipos de estado y las situaciones en las que podemos evitar escribir código para administrar el estado nosotros mismos.

Luego, el capítulo se centra en el **estado compartido (shared state)**, que es el tipo de estado más complejo de administrar. Exploraremos diferentes enfoques para administrar el estado compartido, analizando los pros y los contras de cada enfoque. Para experimentar los diferentes enfoques, crearemos una aplicación simple que contiene un encabezado que muestra el nombre del usuario, y el contenido principal también hace referencia al nombre del usuario. El nombre del usuario se almacenará en un estado al que deben acceder varios componentes.

Como tal, cubriremos los siguientes temas principales en el capítulo:

- Comprensión de los tipos de estado
- Uso de prop drilling
- Uso de React context
- Uso de Zustand
- Uso de TanStack Query y parámetros de URL

---

### Requisitos técnicos

En este capítulo utilizaremos las siguientes tecnologías:

- **Node.js y npm**: Puede instalarlos desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
- **Visual Studio Code**: Puede instalarlo desde [https://code.visualstudio.com/](https://code.visualstudio.com/)

Todos los fragmentos de código de este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter10](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter10)

---

### Comprensión de los tipos de estado

En esta sección, cubriremos algunos de los diferentes tipos de estado y cómo se pueden administrar. El estado de React aumenta la complejidad del código, por lo que resulta útil poder categorizarlo y, potencialmente, delegar su gestión.

#### Estado del servidor (Server state)

El estado del servidor son los datos obtenidos de un servidor/API externo para representarse en la interfaz de usuario. También se denomina estado remoto (*remote state*) o estado de datos (*data state*).

Aprendimos en el [Capítulo 7](https://subscription.packtpub.com/book/web-development/9781836643173/7), *Obtención de datos con Server Components y mutaciones con Server Functions*, cómo los RSCs eliminan la necesidad del estado del servidor porque los datos se obtienen y renderizan en el servidor.

A veces necesitamos obtener datos en los Client Components, y en el [Capítulo 8](https://subscription.packtpub.com/book/web-development/9781836643173/8), *Obtención de datos en Client Components y mutaciones con TanStack Query*, aprendimos cómo TanStack Query administra el estado del servidor por nosotros y cómo administrar esto nosotros mismos usando `useEffect` y `useState` es problemático.

#### Estado del formulario (Form state)

El estado del formulario incluye valores de campo, mensajes de error de validación y si se representan los indicadores de envío. En el [Capítulo 9](https://subscription.packtpub.com/book/web-development/9781836643173/9), *Trabajo con formularios*, aprendimos cómo se puede administrar este estado utilizando los Hooks `useActionState` y `useFormStatus` de React, así como la popular biblioteca React Hook Form. Es mucho más sencillo utilizar esos enfoques que gestionar el estado con nuestro propio código utilizando `useState`.

#### Estado de la URL (URL state)

Los parámetros de URL son una excelente manera de almacenar pequeños fragmentos de estado de la interfaz de usuario. Usamos parámetros de búsqueda para almacenar criterios de búsqueda en capítulos anteriores, pero también se pueden usar parámetros de ruta. Un beneficio clave de este enfoque es que la URL que contiene el estado se puede compartir con otro usuario para abrir la aplicación representada con ese estado.

#### Estado local (Local state)

El estado local es administrado por un solo componente utilizando los Hooks `useState` o `useReducer` de React, que hemos utilizado a lo largo de este libro.

Un ejemplo de estado local es el estado `visible` en el componente de alerta que construimos a lo largo de varios capítulos. Como recordatorio, aquí hay un fragmento del componente de alerta:

```tsx
export function Alert( ... ) { const [visible, setVisible] = useState(true); if (!visible) { return null; } function handleCloseClick() { setVisible(false); ... } return ( <div> ... {closable && ( <button ... onClick={handleCloseClick}>...</button> )} ... </div> ); }
```

#### Estado derivado (Derived state)

El estado derivado se calcula a partir de otro estado en lugar de almacenarse como estado directamente. Es un patrón que mantiene el código más simple y menos propenso a errores al evitar la duplicación innecesaria de estado.

A continuación, se muestra un ejemplo común de duplicación de estado donde `filteredItems` son los elementos activos del estado `items`:

```tsx
const [items, setItems] = useState([ ... ]); const [filteredItems, setFilteredItems] = useState([]); useEffect(() => { setFilteredItems(items.filter((item) => item.active)); }, [items]);
```

Por lo tanto, `filteredItems` es un estado duplicado que debe sincronizarse mediante un Hook `useEffect`.

Aquí está el mismo ejemplo usando estado derivado:

```tsx
const [items, setItems] = useState([ ... ]); const filteredItems = items.filter((item) => item.active);
```

Es mucho más simple porque no hay lógica de sincronización.

El Hook `useMemo` se puede utilizar para reducir cálculos innecesarios de estado derivado:

```tsx
const filteredItems = useMemo(() => { return items.filter((item) => item.active); }, [items]);
```

#### Estado compartido (Shared state)

Como sugiere el nombre, el estado compartido se comparte entre varios componentes. A veces se le denomina **estado global (global state)**.

El estado compartido puede volverse complicado rápidamente si no se maneja con cuidado. Aquí hay algunas razones:
- Cuando muchos componentes leen y escriben en algún estado compartido, los cambios en un lugar pueden causar efectos secundarios no deseados en otros lugares. Puede resultar difícil determinar qué componente provocó un cambio o por qué se volvió a renderizar un componente.
- Cuando cambia algún estado compartido, muchos componentes pueden volver a renderizarse, incluso aquellos que no usan el estado compartido. Todo este re-renderizado puede causar problemas de rendimiento.
- Se puede colocar demasiado en el estado compartido, incluso estado que podría haber permanecido local. El estado compartido luego se vuelve inflado y difícil de mantener.
- Un componente que utiliza estado compartido es más difícil de probar unitariamente porque el estado compartido es una dependencia externa que debe configurarse en la prueba.

Eso completa esta sección sobre categorías de estados. He aquí un resumen rápido:
- El **estado del servidor** son datos del servidor para renderizar en la interfaz de usuario. Idealmente, se debería utilizar un RSC para evitar el estado del servidor. Alternativamente, se debe utilizar una biblioteca como TanStack Query para gestionar de forma robusta el estado del servidor.
- El **estado del formulario** es para el estado requerido en un formulario. Se recomienda utilizar los Hooks de formulario de React y/o una biblioteca como React Hook Form para administrarlo.
- El **estado de la URL** es cuando el estado se almacena en una ruta de URL o en parámetros de búsqueda. Es una forma sencilla de almacenar fragmentos de estado de la interfaz de usuario.
- El **estado local** es para un solo componente, mientras que el **estado compartido** es para múltiples componentes.
- El **estado derivado** es un patrón para evitar la duplicación y sincronización de estados.

El resto de este capítulo se centra principalmente en diferentes enfoques para implementar el estado compartido. Vale la pena señalar que el estado del servidor y el estado de la URL se pueden utilizar como enfoques para el estado compartido; este capítulo también cubre estos enfoques.

A continuación, cubriremos el enfoque más simple para implementar el estado compartido.

---

### Uso de prop drilling

En esta sección, crearemos la primera iteración de la aplicación, que utilizará una técnica llamada **prop drilling** para compartir el estado entre componentes. Después de crear el proyecto, nos tomaremos el tiempo para comprender qué es el prop drilling y luego lo utilizaremos en la aplicación.

#### Creación del proyecto

La aplicación que crearemos contendrá un encabezado y algo de contenido debajo. Aquí está la estructura de componentes que crearemos:

*Figura 10.1 – Estructura de componentes de la aplicación*

El encabezado tendrá un botón Sign in para autenticar y autorizar a un usuario para obtener su nombre y permisos. Una vez autenticado, el nombre del usuario se mostrará en el encabezado de la aplicación y se le dará la bienvenida en el contenido. Si el usuario tiene permisos de administrador, se mostrará contenido importante.

Lleve a cabo los siguientes pasos para crear las versiones iniciales de los archivos que necesitamos en la aplicación sin compartir ningún estado. Copiaremos y pegaremos el código del repositorio de GitHub para ahorrar tiempo:

1. En una terminal, ejecute el siguiente comando para crear el proyecto:

```bash
npx create-next-app@latest state --ts --eslint --app --src-dir --import-alias “@/*” --no-tailwind --turbopack
```

2. Aún en la terminal, muévase a la carpeta del proyecto y abra Visual Studio Code usando los siguientes comandos:

```bash
cd state code .
```

Prettier se puede configurar de la misma manera que aprendimos con Vite en el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*. Siéntase libre de agregar el formato de código automático a este proyecto.

3. Abra `src/app/globals.css` y sobrescriba el contenido con el CSS del siguiente archivo en el repositorio de GitHub: [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter10/start/src/app/globals.css](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter10/start/src/app/globals.css). Esto estilizará adecuadamente nuestra aplicación.
4. Abra `page.tsx` en la carpeta `src/app` y reemplace su contenido con el contenido del repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter10/start/src/app/page.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter10/start/src/app/page.tsx). Este componente gestiona todo el estado pero actualmente no lo comparte. Hace referencia a los componentes `Header` y `Main` y a las Server Functions `signIn` y `signOut`, que crearemos en el siguiente paso.

Observe que `Home` se declara como un Client Component con la directiva `'use client'` porque gestiona el estado.

5. Cree los siguientes archivos de componentes y copie y pegue el contenido del repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter10/start/src/components](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter10/start/src/components):
   - `src/components/Header.tsx`: Representa botones para iniciar y cerrar sesión. También hay un mensaje para indicar quién ha iniciado sesión, que muestra un `?` porque aún no lo sabe.
   - `src/components/Main.tsx`: Representa mensajes de saludo y de inicio de sesión. Nuevamente, aún no sabe si un usuario ha iniciado sesión, por lo que se muestran ambos mensajes. También renderiza un componente `Content`.
   - `src/components/Content.tsx`: Representa contenido importante y un mensaje de permisos insuficientes. No sabe si el usuario tiene permisos, por lo que actualmente se muestran ambos mensajes.
   - `src/data/auth.ts`: Contiene las Server Functions `signIn` y `signOut`. Estas funciones solo simulan la autenticación para simplificar, de modo que podamos concentrarnos en la gestión del estado en este capítulo. `signIn` devuelve un usuario llamado Bob con permisos de administrador.

Habrá errores de ESLint en `page.tsx` porque las variables de estado no se utilizan actualmente. Esto se resolverá en la siguiente sección.

6. En la terminal, ejecute el siguiente comando para ejecutar la aplicación en modo de desarrollo:

```bash
npm run dev
```

La aplicación estará disponible en un navegador en [http://localhost:3000](http://localhost:3000/) y mostrará lo siguiente:

*Figura 10.2 – Pantalla inicial*

Eso completa la configuración del proyecto. A continuación, aprenderemos qué es el prop drilling.

#### Comprensión y uso de prop drilling

El **prop drilling** es la práctica de pasar datos a través de props desde un componente padre hacia abajo a través de múltiples niveles de componentes intermediarios para llegar a un componente hijo anidado. Este enfoque utiliza características de React que ya conocemos.

En nuestra aplicación, pasaremos las variables de estado en el componente `Home` a sus componentes hijos usando props. También pasaremos las funciones `handleSignIn` y `handleSignOut` como props para que los componentes hijos puedan llamarlas para invocar los procesos de inicio y cierre de sesión.

Haremos cambios en la parte inferior del árbol de componentes y avanzaremos hacia arriba. Lleve a cabo los siguientes pasos:

1. Abra `Content.tsx` y agregue una prop `permissions`. Use `permissions` para representar el mensaje apropiado:

```tsx
export function Content({ permissions, }: { permissions: undefined | string[]; }) { if (permissions === undefined) { return null; } return ( <p> {permissions.includes(‹admin›) ? ‘Some important stuff that only an admin can do’ : ‘Insufficient permissions’} </p> ); }
```

2. Abra `Main.tsx` y agregue las props `userName` y `permissions`. Renderice un mensaje apropiado utilizando la prop `userName` y pase la prop `permissions` a `Content` en el JSX:

```tsx
export function Main({ userName, permissions, }: { userName: string | undefined; permissions: undefined | string[]; }) { return ( <main> <h1>Welcome</h1> <p> {userName ? `Hello ${userName}!` : ‘Please sign in’} </p> <Content permissions={permissions} /> </main> ); } 
```

Observe que el componente no usa `permissions` en su lógica de renderizado; solo lo pasa al componente `Content`.

3. Abra `Header.tsx` y comience agregando las siguientes props:

```tsx
export function Header({ userName, onSignInClick, onSignOutClick, loading, }: { userName: string | undefined; onSignInClick: () => void; onSignOutClick: () => void; loading: boolean; }) { ... }
```

4. Actualice el JSX en `Header` para representar los botones de inicio o cierre de sesión dependiendo de si `userName` está definido. Además, muestre `userName` en el mensaje:

```tsx
<header> {userName ? ( <> <span>{userName} has signed in</span> <button type=”button”>Sign Out</button> </> ) : ( <button type=”button”>Sign in</button> )} </header>
```

5. Conecte los botones a las props `onSignInClick` y `onSignOutClick` y use la prop `loading` para deshabilitar los botones cuando se esté realizando un inicio o cierre de sesión. Además, use la prop `loading` para actualizar el contenido del botón cuando se esté realizando un inicio o cierre de sesión:

```tsx
{userName ? ( <> <span> ... </span> <button ... onClick={onSignOutClick} disabled={loading} > {loading ? ‹...› : ‹Sign out’} </button> </> ) : ( <button ... onClick={onSignInClick} disabled={loading} > {loading ? ‹...› : ‹Sign in’} </button> )}
```

6. Abra `page.tsx` y pase el estado y los manejadores de inicio y cierre de sesión a los componentes hijos de la siguiente manera:

```tsx
<Header userName={userName} onSignInClick={handleSignIn} onSignOutClick={handleSignOut} loading={loading} /> <Main userName={userName} permissions={permissions} />
```

La aplicación en ejecución ahora aparece como se muestra en la captura de pantalla:

*Figura 10.3 – Aplicación antes de iniciar sesión*

7. Haga clic en el botón Sign in. Luego ocurre el proceso de inicio de sesión y, después de un par de segundos, aparece la siguiente pantalla:

*Figura 10.4 – Aplicación después de iniciar sesión*

Lo bueno de este enfoque es que es simple y utiliza características de React con las que ya estamos familiarizados. Una desventaja de este enfoque es que puede obligar a los componentes entre el componente que proporciona el estado y los componentes que acceden al estado a tener una prop para ese estado. Por lo tanto, algunos componentes que no necesitan acceso al estado pueden verse obligados a acceder a él. Un ejemplo es el componente `Main`: el estado `permissions` se ve obligado a pasar a través de él para llegar al componente `Content`.

#### Uso de una mejor composición

A menudo, una mejor composición puede resolver el problema de que un estado pase innecesariamente a través del árbol de componentes mediante prop drilling. Por ejemplo, podemos componer mejor los componentes dentro del componente `Home` para resolver el problema de que el estado `permissions` pase innecesariamente a través de `Main` para llegar a `Content`. Podemos lograr esto haciendo que `Main` represente hijos (`children`) de React en lugar de `Content`. Luego, `Main` puede pasar `Content` como un hijo de `Main`.

Lleve a cabo los siguientes pasos para hacer esto:

1. Abra `Main.tsx` y elimine la declaración de importación para el componente `Content`. Además, agregue una declaración de importación para el tipo `ReactNode`:

```typescript
import type { ReactNode } from ‘react’;
```

2. Aún en `Main.tsx`, elimine la prop `permissions` y agregue una prop `children`. Además, reemplace `Content` con `children` en el JSX:

```tsx
export function Main({ userName, children }: { userName: string | undefined; children: ReactNode; }) { return ( <main> <h1>Welcome</h1> <p>...</p> {children} </main> ); }
```

3. Vaya a `page.tsx` e importe el componente `Content`:

```typescript
import { Content } from ‘@/components/Content’;
```

4. Aún en `page.tsx`, en el JSX del componente `Home`, elimine el atributo `permissions` de `Main` y pase `Content` como un hijo de `Main` de la siguiente manera:

```tsx
<Main userName={userName}> <Content permissions={permissions} /> </Main>
```

La aplicación funcionará como antes.

Eso completa esta sección sobre prop drilling. He aquí un resumen:
- El prop drilling se produce cuando el estado se pasa a través de múltiples capas de componentes a través de props.
- Es el enfoque más simple para compartir estados: utiliza características muy básicas de React y es fácil rastrear cómo fluye el estado a través de la aplicación.
- Funciona bien para compartir estados entre unos pocos componentes adyacentes. Una buena composición de componentes puede ayudar a evitar que el estado se transmita innecesariamente a través de componentes.
- Sin embargo, compartir el estado entre componentes profundamente anidados se vuelve engorroso porque implica que el estado pase innecesariamente a través de componentes. Los componentes también son más difíciles de refactorizar en esta situación debido a dependencias innecesarias.

A continuación, mantenga la aplicación ejecutándose y veremos una solución más adecuada para compartir el estado entre muchos componentes.

---

### Uso de React context

En esta sección, aprenderemos sobre una característica de React llamada **context**. Luego refactorizaremos la aplicación de la última sección para usar React context.

#### Comprender React context

**React context** es un objeto al que pueden acceder los componentes. Este objeto puede contener valores de estado, por lo que proporciona un mecanismo para compartir el estado entre componentes.

Se crea un contexto utilizando una función `createContext` de la siguiente manera:

```typescript
const Context = createContext<ContextType>(defaultValue);
```

Se debe pasar un valor predeterminado para el contexto a `createContext`. También tiene un parámetro de tipo genérico para el tipo que representa el objeto creado por `createContext`.

Se debe colocar un componente proveedor de contexto en el árbol de componentes encima de los componentes que requieren acceso a él. Se puede crear un componente contenedor de proveedor que almacene el estado compartido y lo pase al componente de contexto de la siguiente manera:

```tsx
export function Provider({ children }: Props) { const [someState, setSomeState] = useState(initialState); return ( <Context value={{ someState }}> {children} </Context > ); }
```

El fragmento de código anterior utiliza la variante corta del componente proveedor de contexto, `Context`. La versión más larga es `Context.Provider`.

El Hook `useState` se ha utilizado para el estado en el ejemplo anterior, pero también se podría utilizar `useReducer`.

El componente contenedor de proveedor se puede colocar adecuadamente en el árbol de componentes, encima de los componentes que requieren el estado compartido:

```tsx
function App() { return ( <Provider> <Header /> <Main /> </Provider> ); }
```

React también contiene un Hook `use` que se puede utilizar para obtener valores del contexto:

```typescript
const { someState } = use(Context);
```

El contexto debe pasarse al Hook `use` y las propiedades del objeto de contexto se pueden desestructurar a partir de su resultado.

> [!NOTE]
> Existe un Hook alternativo `useContext` que se puede utilizar para obtener un estado que tiene la misma sintaxis que el Hook `use`. Sin embargo, a diferencia de `useContext`, el Hook `use` se puede utilizar condicionalmente.

Por lo tanto, los componentes que desean acceder al estado compartido pueden acceder a él utilizando el Hook `use` de la siguiente manera:

```tsx
export function SomeComponent() { const { someState } = use(Context); return <div>I have access to {someState}</div>; }
```

Para obtener más información sobre el contexto de React, consulte el siguiente enlace: [https://react.dev/reference/react/createContext](https://react.dev/reference/react/createContext).

Ahora que entendemos el contexto de React, lo usaremos en la aplicación que creamos en la sección anterior.

#### Uso de React context

Rediseñaremos la aplicación de la última sección para usar React context. Comenzaremos creando el contexto y un componente proveedor. Luego, moveremos el estado del componente `Home` al componente proveedor. También agregaremos la capacidad de alternar permisos y observar el impacto en el re-renderizado.

Para hacer esto, lleve a cabo los siguientes pasos:

1. Comience creando un archivo llamado `types.ts` en una nueva carpeta `state` en la carpeta `src`. Agregue el siguiente tipo para representar el estado y las funciones que necesitaremos en nuestro contexto:

```typescript
export type UserState = { userName: undefined | string; permissions: undefined | string[]; loading: boolean; handleSignIn: () => Promise<void>; handleSignOut: () => Promise<void>; togglePermissions: () => void; };
```

2. Cree otro archivo en la carpeta `state` llamado `UserContext.ts`. Agregue el siguiente contenido al archivo para crear un contexto de React que contendrá el estado y las funciones:

```typescript
import { createContext } from ‘react’; import type { UserState } from ‘./types’; export const UserContext = createContext<UserState>({ userName: undefined, permissions: undefined, loading: false, handleSignIn: () => new Promise(() => {}), handleSignOut: () => new Promise(() => {}), togglePermissions: () => {}, });
```

La función `createContext` requiere un estado inicial, por lo que pasamos un objeto que contiene valores de estado `undefined` y funciones vacías.

> [!NOTE]
> React context puede compartir funciones además de estados. Usaremos esta función para compartir las funciones `handleSignIn`, `handleSignOut` y `togglePermissions`.

3. Cree otro archivo llamado `UserProvider.tsx` en la carpeta `state`. Agregue el inicio del componente proveedor de la siguiente manera:

```tsx
‘use client’; import { type ReactNode } from ‘react’; import { signIn, signOut } from ‘@/data/auth’; import { UserContext } from ‘./UserContext’; export function UserProvider({ children, }: { children: ReactNode; }) { return ( <UserContext value={{}} > {children} </UserContext> ); }
```

Por el momento, el componente `UserProvider` solo representa el componente proveedor `UserContext`.

Lo hemos marcado como un Client Component porque eventualmente lo usaremos en el RSC `RootLayout`.

4. Mueva el estado y las funciones `handleSignIn` y `handleSignOut` del componente `Home` al componente `UserProvider`:

```tsx
import { ..., useCallback, useState } from ‘react’; ... export function UserProvider() { const [userName, setUserName] = useState< string | undefined >(); const [permissions, setPermissions] = useState< string[] | undefined >(); const [loading, setLoading] = useState(false); const handleSignIn = useCallback(async () => { setLoading(true); const user = await signIn(); setUserName(user.name); setPermissions(user.permissions); setLoading(false); }, []); const handleSignOut = useCallback(async () => { setLoading(true); await signOut(); setUserName(undefined); setPermissions(undefined); setLoading(false); }, []); return ... }
```

5. Agregue una función `togglePermissions` al contexto también:

```typescript
export function UserProvider( ... ) { ... const togglePermissions = useCallback( () => setPermissions((currPermissions) => currPermissions?.length === 0 ? [‘admin’] : [], ), [], ); }
```

La función alterna entre permisos de administrador y sin permisos.

6. Aún en `UserProvider.tsx`, pase el estado, `handleSignIn`, `handleSignOut` y las funciones `togglePermissions` a `UserContext` en el JSX:

```tsx
<UserContext value={{ userName, permissions, loading, handleSignIn, handleSignOut, togglePermissions }} > {children} </UserContext>
```

7. Abra `layout.tsx` y envuelva `UserProvider` alrededor del contenido del diseño:

```tsx
import { UserProvider } from ‘@/state/UserProvider’; ... export default function RootLayout( ... ) { return ( <html ... > <body ... > <UserProvider>{children}</UserProvider> </body> </html> ); }
```

Esto significa que todo el árbol de componentes tendrá acceso al contexto.

8. Abra `page.tsx`. El estado y las funciones `handleSignIn` y `handleSignOut` deben eliminarse. Las declaraciones de importación de React y `auth` también deben eliminarse, y elimine el paso de props en el JSX. Complete el trabajo en este archivo eliminando la directiva `'use client'` porque ahora puede ser un RSC. El contenido del archivo ahora debería ser el siguiente:

```tsx
import { Header } from ‘@/components/Header’; import { Main } from ‘@/components/Main’; export default function Home() { return ( <> <Header /> <Main> <Content /> </Main> </> ); }
```

9. Abra `Header.tsx` y márquelo como un Client Component porque utilizará React context. Además, importe el Hook `use` de React y el contexto `UserContext` que creamos anteriormente. Elimine las props y obtenga el estado y las funciones del contexto en su lugar:

```tsx
‘use client’; import { use } from ‘react’; import { UserContext } from ‘@/state/UserContext’; export function Header() { const { userName, handleSignIn, handleSignOut, loading, } = use(UserContext); return ... }
```

10. Aún en `Header.tsx`, extraiga `togglePermissions` del contexto y utilícelo cuando se haga clic en el mensaje de inicio de sesión:

```tsx
export function Header() { const { ..., togglePermissions } = use(UserContext); return ( <header> {userName ? ( <> <span onClick={togglePermissions}> {userName} has signed in </span> ... </> ) : ... } </header> ); }
```

Agregar un controlador de clics en un elemento `span` no es una buena práctica, pero es una forma sencilla de experimentar los re-renderizados de React context.

11. Actualice los manejadores `onClick` de los botones para hacer referencia a los manejadores de inicio y cierre de sesión del contexto:

```tsx
return ( <header> {userName ? ( <> ... <button ... onClick={handleSignOut}> ... </button> </> ) : ( <button ... onClick={handleSignIn}> ... </button> )} </header> );
```

12. Abra `Main.tsx` y siga el mismo patrón que en el paso anterior. No pase `permissions` a `Content`; provocará un error de compilación, pero lo resolveremos en el siguiente paso:

```tsx
‘use client’; import { use, type ReactNode } from ‘react’; import { UserContext } from ‘@/state/UserContext’; ... export function Main({ children, }: { children: ReactNode; }) { const { userName } = use(UserContext); return ... }
```

13. Abra `Content.tsx` y siga el mismo patrón nuevamente:

```tsx
‘use client’; import { use } from ‘react’; import { UserContext } from ‘@/state/UserContext’; export function Content() { const { permissions } = use(UserContext); ... }
```

Los errores de compilación en todos los archivos ahora deberían estar resueltos y la aplicación en ejecución se verá y se comportará como antes.

14. Abra las herramientas de desarrollo de React en su navegador y asegúrese de que los resaltados de re-renderizado estén activados (*Components | Settings | Highlight updates when components render*). En la aplicación, inicie sesión y haga clic en el mensaje *Bob has signed in*. Observe que todos los componentes bajo el proveedor de contexto se vuelven a renderizar.

*Figura 10.5 – Componentes volviendo a renderizarse*

15. Detenga la ejecución de la aplicación presionando `Ctrl + C`.

Eso completa la reelaboración de la aplicación para usar React context en lugar de prop drilling.

En comparación con el prop drilling, React context requiere que se escriba más código inicialmente. Sin embargo, permite que los componentes accedan al estado compartido usando un Hook en lugar de pasarlo a través de componentes usando props. Es una solución elegante de estado compartido, especialmente cuando muchos componentes comparten estado. Sin embargo, cuando el estado cambia, todos los componentes debajo del componente proveedor de contexto se vuelven a renderizar.

A continuación, aprenderemos sobre una popular biblioteca de terceros que se puede utilizar para compartir el estado.

---

### Uso de Zustand

En esta sección, aprenderemos sobre **Zustand** antes de usarlo para refactorizar la aplicación en la que hemos estado trabajando.

#### Comprender Zustand

**Zustand** es una biblioteca de administración de estado popular, de alto rendimiento y escalable para React que es increíblemente fácil de usar.

El estado reside en una tienda centralizada (*centralized store*), que se crea utilizando la función `create` de Zustand:

```typescript
const useCountStore = create((set) => ({ count: 0, inc: () => set((state) => ({ count: state.count + 1 })), dec: () => set((state) => ({ count: state.count - 1 })), }));
```

Al igual que React context, una tienda Zustand puede contener funciones además de valores de estado. La tienda del ejemplo anterior contiene el estado `count` con funciones para incrementarlo y decrementarlo.

La función `create` devuelve un Hook que se puede utilizar para acceder a la tienda. En el ejemplo anterior, llamamos a este Hook `useCountStore`.

La función `create` tiene un parámetro genérico opcional para el tipo de la tienda:

```typescript
export const useCountStore = create<{ count: number; inc: () => void; dec: () => void; }>( ... );
```

A diferencia de React context, no se requiere ningún componente proveedor. Simplemente accede a la tienda donde la necesita utilizando el Hook de la tienda:

```tsx
function Add() { const count = useCountStore((state) => state.count); const inc = useCountStore((state) => state.inc); return ( <button type=”button” onClick={inc}> {count} </button> ); }
```

Para obtener más información sobre Zustand, consulte el siguiente enlace: [https://zustand.docs.pmnd.rs/getting-started/introduction](https://zustand.docs.pmnd.rs/getting-started/introduction).

Ahora que entendemos Zustand, lo usaremos en la aplicación que creamos en la sección anterior.

#### Uso de Zustand

Refactorizaremos la aplicación para usar Zustand en lugar de React context. Primero, crearemos una tienda y luego la consumiremos en los componentes `Header`, `Main` y `Content`. Lleve a cabo los siguientes pasos:

1. Comience instalando Zustand ejecutando el siguiente comando en una terminal:

```bash
npm i zustand
```

2. Cree un nuevo archivo en la carpeta `state` llamado `useUserStore.tsx`. Agregue el siguiente contenido para iniciar la implementación de la tienda:

```typescript
import { create } from ‘zustand’; import { UserState } from ‘./types’; export const useUserStore = create<UserState>( (set) => ({ userName: undefined, permissions: undefined, loading: false }), );
```

Hemos creado una tienda para almacenar los valores del estado, que hemos inicializado.

3. Agregue las funciones del manejador de inicio y cierre de sesión a la tienda:

```typescript
import { signIn, signOut } from ‘@/data/auth’; export const useUserStore = create<UserState>( (set) => ({ ..., handleSignIn: async () => { set({ loading: true }); const user = await signIn(); set({ userName: user.name, permissions: user.permissions, loading: false, }); }, handleSignOut: async () => { await signOut(); set({ userName: undefined, permissions: undefined, loading: false, }); }, }), );
```

Los manejadores son similares a los manejadores equivalentes en el contexto de React, excepto que usamos la función `set` de Zustand para actualizar los valores de estado.

4. Agregue también la función `togglePermissions` a la tienda:

```typescript
export const useUserStore = create<UserState>( (set) => ({ ..., togglePermissions: () => set((state) => state.permissions?.length === 0 ? { permissions: [‘admin’] } : { permissions: [] }, ), }), );
```

Eso completa la tienda Zustand.

5. Abra `Header.tsx` y reemplace las llamadas de contexto con una llamada a la tienda que acabamos de crear:

```tsx
import { useUserStore } from ‘@/state/useUserStore’; export function Header() { const userName = useUserStore( (state) => state.userName, ); const loading = useUserStore( (state) => state.loading, ); const handleSignIn = useUserStore( (state) => state.handleSignIn, ); const handleSignOut = useUserStore( (state) => state.handleSignOut, ); const togglePermissions = useUserStore( (state) => state.togglePermissions, ); return ... }
```

Las importaciones de React y de contexto también se pueden eliminar porque son redundantes.

6. Abra `Main.tsx` y reemplace las llamadas de contexto con una llamada a la tienda:

```tsx
import { type ReactNode } from ‘react’; import { useUserStore } from ‘@/state/useUserStore’; ... export function Main() { const userName = useUserStore( (state) => state.userName, ); return ... }
```

Las importaciones de React y de contexto también se pueden eliminar porque son redundantes.

7. Por último, abra `Content.tsx` y reemplace las llamadas de contexto con una llamada a la tienda:

```tsx
import { useUserStore } from ‘@/state/useUserStore’; ... export function Content() { const permissions = useUserStore( (state) => state.permissions, ); if (permissions === undefined) { return null; } return ... }
```

Nuevamente, las importaciones de React y de contexto también se pueden eliminar porque son redundantes.

8. Abra `layout.tsx` y elimine `UserProvider` porque ya no es necesario.
9. Ejecute la aplicación ejecutando `npm run dev` en la terminal. La aplicación se verá y se comportará exactamente como antes.
10. Abra las herramientas de desarrollo de React en su navegador y asegúrese de que los resaltados de re-renderizado estén activados. En la aplicación, inicie sesión y haga clic en el mensaje *Bob has signed in*. Observe que **solo el componente Content se vuelve a renderizar**. Esto se debe a que este es el único componente suscrito al estado `permissions`.

Eso completa la refactorización de la aplicación para usar Zustand en lugar de React context. Como hemos experimentado, es muy sencillo de usar y no tener que utilizar un componente de proveedor es un claro beneficio sobre React context. Cuando un estado en una tienda de Zustand cambia, solo se volverán a renderizar los componentes que se hayan suscrito a ese estado. Esto lo hace más eficiente que React context.

A continuación, recordaremos los parámetros de URL y TanStack Query y cómo se pueden utilizar para compartir el estado entre componentes.

---

### Uso de TanStack Query y parámetros de URL

En esta sección, aprenderemos cómo se puede utilizar TanStack Query para compartir el estado que normalmente proviene de una base de datos del servidor entre diferentes componentes de React. Luego pasaremos a aprender cómo se puede compartir el estado entre componentes utilizando los parámetros de búsqueda de URL.

Ya usamos TanStack Query en el [Capítulo 8](https://subscription.packtpub.com/book/web-development/9781836643173/8), *Obtención de datos en Client Components y mutaciones con TanStack Query*, y usamos parámetros de búsqueda de URL en el [Capítulo 6](https://subscription.packtpub.com/book/web-development/9781836643173/6), *Creación de una aplicación multipágina con Next.js*. Por lo tanto, nos centraremos en los aspectos de intercambio de estados en lugar de en los detalles completos de la implementación.

Utilizaremos una extensión de la aplicación que hemos estado creando para comprender cómo se pueden utilizar tanto TanStack Query como los parámetros de búsqueda de URL para compartir el estado. La aplicación se ha rediseñado para que solo devuelva un ID de usuario a partir de un inicio de sesión y para obtener información sobre el usuario mediante TanStack Query. La información del usuario se muestra en pestañas, con la pestaña activa almacenada como un parámetro de búsqueda de URL.

*Figura 10.6 – Aplicación ampliada*

El código para la aplicación extendida está disponible en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter10/url-react-query](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter10/url-react-query).

#### Uso de TanStack Query

Como se aprendió anteriormente, TanStack Query mantiene una caché del lado del cliente de los datos que obtiene. Estos datos se pueden compartir entre diferentes componentes simplemente usando el Hook `useQuery` con la misma clave de consulta. Cuando se llama a este Hook, los datos se obtendrán de la caché si no están obsoletos. En la aplicación ampliada, los componentes `Header` y `Main` utilizan este enfoque para obtener el nombre de usuario. La llamada a `useQuery` para obtener la información principal del usuario se centraliza en un Hook personalizado `useGetUser` de la siguiente manera:

```typescript
export function useGetUser( userId: string | undefined, ) { return useQuery({ queryKey: [‘user’, userId], queryFn: async () => { const response = await fetch( `/api/users/${userId}`, ); return userSchema.parse( await response.json(), ); }, enabled: userId !== undefined, }); }
```

Aprenderemos más sobre Hooks personalizados en el [Capítulo 11](https://subscription.packtpub.com/book/web-development/9781836643173/11), *Componentes reutilizables*.

La función de consulta llama a un Route Handler y devuelve el cuerpo de la respuesta validada, que se almacenará en la caché de consultas. La opción `enabled` garantiza que la consulta solo se active cuando el usuario haya iniciado sesión y haya un ID de usuario disponible.

Después de que se produzca un inicio de sesión, el componente `Header` se volverá a renderizar, lo que provocará que se active la consulta y que los datos se obtengan y se almacenen en caché. El componente `Main` se volverá a renderizar, lo que provocará que se active la misma consulta. Debido a que la caché para la clave está disponible, los datos almacenados en caché se utilizarán para el componente `Main`.

El uso de TanStack Query es una forma natural de compartir el estado desde el servidor. Usamos el Hook `useQuery` con una función de consulta y una clave adecuada en los componentes que requieren los datos. TanStack Query se encarga de almacenar en caché los datos y de si es necesario realizar una obtención de datos.

A continuación, comprenderemos cómo se pueden utilizar los parámetros de búsqueda de URL para compartir el estado.

#### Uso de parámetros de URL

En la aplicación ampliada, se utiliza un parámetro de búsqueda llamado `tab` para almacenar la pestaña activa. La implementación de las pestañas se divide en muchos componentes, como se muestra en el siguiente diagrama:

*Figura 10.7 – Componentes de pestañas*

Tanto el componente `Tab` como el componente `TabDetail` acceden al estado de la pestaña activa desde la URL. Como se aprendió anteriormente, Next.js tiene un Hook `useSearchParams` para acceder a los valores de los parámetros de búsqueda y un Hook `useRouter` para configurarlos. La obtención y configuración del parámetro de búsqueda `tab` en el componente `Tab` se resalta aquí:

```tsx
export function Tab({ name, label }: { ... }) { const params = useSearchParams(); const activeTab = params.get(‘tab’) ?? ‘address’; const router = useRouter(); return ( <button type=”button” className={ activeTab === name ? ‘active’ : ‘’ } onClick={() => router.push(`/?tab=${name}`)} > {label} </button> ); }
```

La obtención del parámetro de búsqueda `tab` en el componente `TabDetail` se destaca aquí:

```tsx
export function TabDetail() { const params = useSearchParams(); const activeTab = params.get(‘tab’); if (activeTab === ‘profile’) { return <Profile />; } if (activeTab === ‘hobbies’) { return <Hobbies />; } return <Address />; }
```

Este enfoque es extremadamente simple pero muy efectivo. Tampoco se limita solo a Next.js: la mayoría de los frameworks de frontend tienen mecanismos para interactuar con la ruta de URL y los parámetros de búsqueda.

Un error común es duplicar el estado de la URL utilizando el estado de React e intentar sincronizarlos a ambos mediante un efecto de React. Sin embargo, la lógica de sincronización suele ser compleja, lo que genera errores en casos extremos.

Eso completa esta sección sobre el uso de TanStack Query y la URL para compartir el estado. He aquí un resumen rápido:
- TanStack Query almacena en caché los datos del servidor y permite compartir el estado entre componentes mediante el uso del mismo Hook `useQuery` con una clave de consulta compartida.
- Los parámetros de búsqueda de URL pueden administrar y compartir el estado de la interfaz de usuario, como la pestaña activa en una aplicación de varias pestañas.

A continuación, resumiremos el capítulo.

---

### Resumen

Comenzamos este capítulo analizando diferentes categorías de estado. Aprendimos cómo gestionar el estado de forma sólida para cada categoría. Pasamos la mayor parte del capítulo centrándonos en la categoría de estado más compleja: el estado compartido. Creamos una pequeña aplicación de una sola página que contenía componentes que necesitaban compartir el estado.

Comenzamos nuestra exploración del estado compartido utilizando el enfoque de prop drilling. Este es el enfoque más simple e ideal para unos pocos componentes adyacentes. Sin embargo, resulta engorroso para muchos componentes, especialmente si están muy separados en el árbol de componentes.

Pasamos a aprender sobre React context y refactorizamos la aplicación para usarlo. Aprendimos que es más conveniente que el prop drilling para compartir el estado entre muchos componentes. Sin embargo, puede causar problemas de rendimiento porque muchos componentes a menudo se vuelven a renderizar cuando cambia el estado.

A continuación, aprendimos sobre Zustand, que es similar a React context. Una diferencia es que no se requiere ningún componente proveedor, lo que hace que su uso sea más sencillo. Zustand es muy eficiente porque los componentes solo se vuelven a renderizar cuando cambia el estado al que se suscriben.

Por último, aprendimos cómo TanStack Query es una excelente manera de compartir el estado del servidor. Nuestra aplicación utilizó este enfoque para compartir datos de usuario. También aprendimos cómo almacenar cierto estado de la interfaz de usuario en la URL. Nuestra aplicación utilizó este enfoque para almacenar la pestaña activa.

En el próximo capítulo, aprenderemos cómo hacer que los componentes sean altamente reutilizables.

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido en este capítulo:

1. Tenemos un contexto definido de la siguiente manera para mantener el estado del tema para una aplicación:

```typescript
type Theme = { name: string; color: ‘dark’ | ‘light’; }; type ThemeContextType = Theme & { changeTheme: ( name: string, color: ‘dark’ | ‘light’ ) => void; }; const ThemeContext = createContext<ThemeContextType>();
```

Sin embargo, el código no compila. ¿Cuál es el problema?

2. El contexto de la pregunta 1 tiene un contenedor de proveedor llamado `ThemeProvider`, que se agrega al árbol de componentes de la siguiente manera:

```tsx
<ThemeProvider> <Header /> <Main /> </ThemeProvider> <Footer />
```

El estado del tema es `undefined` cuando se desestructura desde `useContext` en el componente `Footer`. ¿Cuál es el problema?

3. ¿Es posible tener dos contextos de React en una aplicación? ¿Es posible tener dos tiendas Zustand en una aplicación? ¿Se pueden tener tanto React context como Zustand en una aplicación?
4. El siguiente botón actualiza el estado en una tienda de Zustand cuando se hace clic en él:

```tsx
<button onClick={() => { const changeTheme = useThemeStore( (state) => state.changeTheme, ); changeTheme(“blue”, “dark”); }} > Change </button>
```

Hay un problema con este código. ¿Cuál es este problema?

5. En un componente de React, ¿es posible utilizar `useState` de React así como el estado de una tienda de Zustand?

---

### Respuestas

1. A `createContext` se le debe pasar un valor predeterminado cuando se utiliza con TypeScript. Aquí está el código corregido:

```typescript
const ThemeContext = createContext<ThemeContextType>({ name: ‘standard’, color: ‘light’, changeTheme: ( name: string, color: ‘dark’ | ‘light’) => {}, });
```

2. `Footer` debe estar anidado dentro de `ThemeProvider` en el árbol de componentes, de la siguiente manera:

```tsx
<ThemeProvider> <Header /> <Main /> <Footer /> </ThemeProvider>
```

3. Sí, no hay límite en la cantidad de contextos de React en una aplicación. Tampoco hay límite en la cantidad de tiendas Zustand en una aplicación. Además, las aplicaciones pueden contener tanto contextos de React como tiendas Zustand.
4. El Hook `useThemeStore` no se puede llamar en un manejador de eventos porque viola las reglas de los Hooks. Este es el código corregido:

```tsx
const changeTheme = useThemeStore( (state) => state.changeTheme, ); return ( ... <button onClick={() => { changeTheme(“blue”, “dark”); }} > Change </button> ... );
```

5. Sí, el estado local definido usando `useState` o `useReducer` se puede usar junto con el estado compartido de una tienda Zustand.
