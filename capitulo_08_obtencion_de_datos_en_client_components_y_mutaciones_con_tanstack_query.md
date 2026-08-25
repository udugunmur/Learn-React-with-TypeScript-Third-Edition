# Parte 3: Datos

## Capítulo 8: Obtención de datos en Client Components y mutaciones con TanStack Query

En este capítulo, aprenderemos cómo React puede obtener datos desde un **Client Component**, rediseñando la aplicación de publicaciones de blog del capítulo anterior.

Comenzaremos explorando los desafíos de usar `useEffect` para la obtención de datos del lado del cliente. Luego pasaremos a utilizar una biblioteca popular llamada **TanStack Query** para la obtención de datos del lado del cliente y experimentaremos cómo simplifica esta tarea. Mantendremos la excelente experiencia de usuario creada en el capítulo anterior, utilizando las capacidades de TanStack Query para implementar indicadores de carga y manejo de errores. También utilizaremos TanStack Query para rediseñar el código de mutación.

Cubriremos el uso de React Server Functions para la obtención de datos del lado del cliente y comprenderemos las desventajas de este enfoque. También aprenderemos cómo se compara un **Next.js Route Handler** con las React Server Functions para la obtención de datos del lado del cliente.

Al final del capítulo, tendrá el conocimiento necesario para implementar mutaciones y obtención de datos del lado del cliente de forma mantenible y robusta.

Cubriremos los siguientes temas:

- Obtención de datos mediante TanStack Query
- Uso de un Route Handler con TanStack Query
- Mutación de datos mediante una mutación de TanStack Query

---

### Requisitos técnicos

En este capítulo utilizaremos las siguientes tecnologías:

- **Navegador**: Un navegador moderno como Google Chrome
- **Node.js y npm**: Puede instalarlos desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
- **Visual Studio Code**: Puede instalarlo desde [https://code.visualstudio.com/](https://code.visualstudio.com/)

Todos los fragmentos de código utilizados en este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter08](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter08)

---

### Obtención de datos mediante TanStack Query

En esta sección, comenzaremos comprendiendo cómo obtener datos utilizando el Hook `useEffect` de React y los desafíos de este enfoque. Luego exploraremos cómo obtener datos utilizando una biblioteca popular llamada TanStack Query y la utilizaremos en nuestra aplicación, experimentando sus beneficios.

#### Comprender los desafíos con useEffect para la obtención de datos

En esta sección, exploraremos la obtención de datos utilizando el Hook `useEffect` de React y los desafíos de este enfoque.

Usamos el Hook `useEffect` en el [Capítulo 3](https://subscription.packtpub.com/book/web-development/9781836643173/3), *Uso de React Hooks*, para obtener el nombre de una persona. Aquí hay un recordatorio del código:

```tsx
const [name, setName] = useState<string | undefined>(); const [loading, setLoading] = useState(true); useEffect(() => { getPerson().then((person) => { setLoading(false); setName(person.name); }); }, []); if (loading) { return <div>Loading ...</div>; }
```

Observe que necesitamos administrar el estado de los datos obtenidos y del indicador de carga. Si se agrega el manejo de errores, se requiere más estado y el código se vuelve un poco más complejo:

```tsx
... const [error, setError] = useState<Error | undefined>(); useEffect(() => { getPerson() .then((person) => { ... }) .catch(e => { setError(e); setLoading(false); }); }, []); ...
```

En realidad, necesitaríamos pasar el ID de la persona a la función de obtención, lo que hace que las cosas sean aún más complejas:

```tsx
useEffect(() => { getPerson(personId) ... }, [personId]);
```

Ahora, se producirá una nueva obtención (*refetch*) cada vez que cambie `personId`. Puede ocurrir una **condición de carrera (race condition)** si `personId` cambia mientras una obtención aún está en progreso. Además, si el componente se desmonta mientras una obtención aún está en progreso, se producirá un error cuando se complete la obtención y se intente establecer el estado de los datos.

La complejidad de implementar este código aumenta rápidamente a medida que se implementan más casos extremos. La siguiente publicación de blog de Dominik Dorfmeister, uno de los mantenedores de TanStack Query, profundiza un poco más en la complejidad: [https://tkdodo.eu/blog/why-you-want-react-query](https://tkdodo.eu/blog/why-you-want-react-query).

Eso completa esta sección sobre la obtención de datos con el Hook `useEffect`. En resumen, se pueden obtener datos con el Hook `useEffect`, pero no es recomendable.

Afortunadamente, TanStack Query reduce la complejidad de la obtención de datos y es una opción muy popular en la comunidad de React. Aprenderemos sobre esto a continuación.

#### Comprender TanStack Query

**TanStack Query** simplifica el código de obtención de datos administrando los diferentes estados por nosotros, como los estados de carga y error. En su núcleo hay una caché de datos inteligente para los datos obtenidos. Cuando los datos almacenados en caché se vuelven obsoletos (*stale*), automáticamente vuelve a obtener los datos.

Aquí hay un fragmento de código simple de TanStack Query en un componente que obtiene datos de un producto y muestra su nombre y descripción:

```tsx
function Product({ id }: { id: number }) { const { data, error, isPending } = useQuery({ queryKey: [‘products’, id], queryFn: () => fetch( `https://some-server.com/products/${id}`, ).then((res) => res.json()), }); if (isPending) return ‘Loading... ‘; if (error) return ‘Error: ‘ + error.message; return ( <div> <h2>{data.name}</h2> <p>{data.description}</p> </div> ); }
```

TanStack Query contiene un Hook `useQuery` que administra el proceso de obtención de datos y los diferentes estados. Las opciones se pasan a `useQuery` en un objeto, y las dos opciones más importantes son las siguientes:
- `queryKey`: Una clave única para los datos. La caché de TanStack Query es capaz de almacenar datos de muchas consultas diferentes, por lo que esta clave identifica los datos en la caché. La clave en este ejemplo es un array que contiene la palabra `"products"` y el ID numérico del producto en este caso.

> [!NOTE]
> Usar un array para una clave es la mejor práctica porque le permite a TanStack Query diferenciar fácilmente entre elementos individuales con la clave y tomar decisiones basadas en ellos. Por ejemplo, si se invalidan los datos para la clave `['products']`, los datos para las claves `['products', 1]`, `['products', 2]`, ... se invalidarían automáticamente.

- `queryFn`: Una función que realmente realiza la obtención de datos. La función `fetch` del navegador obtiene el producto de la API en este ejemplo.

El Hook `useQuery` devuelve un objeto que contiene muchas variables útiles, incluidas las siguientes:
- `data`: Los datos obtenidos.
- `isPending`: Si los datos aún no se han obtenido. Esto se puede utilizar para implementar un indicador de carga, como en el ejemplo anterior.
- `isSuccess`: Cuando los datos se han obtenido con éxito.
- `isError`: Cuando la obtención de datos produjo un error. Esto se puede utilizar para implementar una alerta de error, como en el ejemplo anterior.
- `error`: La instancia de `Error` si la obtención de datos produjo un error.

El Hook `useQuery` solo se puede usar dentro de un componente `QueryClientProvider` en el árbol de componentes de React. Esto permite utilizar la misma caché de datos en toda la aplicación. El componente `QueryClientProvider` recibe una instancia de `QueryClient`, como en el siguiente ejemplo:

```tsx
function App() { const [queryClient] = useState(() => new QueryClient()); return ( <QueryClientProvider client={queryClient}> ... </QueryClientProvider> ); }
```

La instancia de `QueryClient` se mantiene en el estado para que se reutilice la misma instancia después de un re-renderizado.

`QueryClient` también se puede utilizar para acceder a la caché de datos; un caso de uso común es invalidarla para que se obtengan datos nuevos. El siguiente fragmento de código invalida la caché para la clave `['products']`:

```typescript
queryClient.invalidateQueries({ queryKey: [‘products’] });
```

A continuación, configuraremos el proyecto que utilizaremos en este capítulo.

#### Configuración del proyecto

El proyecto que necesitamos para este capítulo es el que terminamos al final del capítulo anterior. Se puede encontrar una copia de esto en el repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter08/start](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter08/start).

A continuación, rediseñaremos nuestra aplicación para usar TanStack Query para la obtención de datos.

#### Obtención de datos de publicaciones de blog

Comenzaremos rediseñando la obtención de datos en la página de lista de publicaciones en nuestra aplicación para usar TanStack Query. En el camino, crearemos un indicador de carga y manejaremos errores. Lleve a cabo los siguientes pasos:

1. Instale TanStack Query ejecutando el siguiente comando en la terminal:

```bash
npm i @tanstack/react-query
```

2. Crearemos un Client Component `Providers` que contiene el `QueryClientProvider` de TanStack Query. Luego haremos referencia a esto en el layout raíz, en la parte superior del árbol de componentes de React, para que todos nuestros Client Components puedan acceder a la caché. Cree un nuevo archivo llamado `Providers.tsx` en la carpeta `src/components` con el siguiente contenido:

```tsx
‘use client’; import { QueryClient, QueryClientProvider, } from ‘@tanstack/react-query’; import { ReactNode, useState } from ‘react’; export function Providers({ children, }: { children: ReactNode; }) { const [queryClient] = useState( () => new QueryClient(), ); return ( <QueryClientProvider client={queryClient}> {children} </QueryClientProvider> ); }
```

3. Abra `src/app/layout.tsx` y agregue `Providers.tsx` a `RootLayout` de la siguiente manera:

```tsx
import { Providers } from ‘@/components/Providers’; ... export default function RootLayout( ... ) { return ( <html ... > <body ... > <Providers> <Header /> {children} </Providers> </body> </html> ); }
```

Todos los Client Components tienen acceso a la caché ahora.

4. Expondremos las consultas de la base de datos como Server Functions para que se puedan llamar desde los Client Components. Abra `queries.ts` y agregue una directiva `'use server'` en la parte superior del archivo.
5. Abra `ErrorAlert.tsx`. Simplificaremos el componente `ErrorAlert` porque los reintentos son automáticos en TanStack Query. Elimine el botón Retry y la prop `resetErrorBoundary`. El componente ahora debería quedar de la siguiente manera:

```tsx
export function ErrorAlert({ error, }: { error: Error; }) { return ( <div role=”alert”> <h3>Something went wrong</h3> <p>{error.message}</p> </div> ); }
```

6. Ahora convertiremos `PostList` en un Client Component y rediseñaremos la obtención de datos para usar TanStack Query. Abra `PostList.tsx` y realice los siguientes cambios resaltados:

```tsx
‘use client’; import { useQuery } from ‘@tanstack/react-query’; ... export function PostList( ... ) { const { data: resolvedPosts } = useQuery({ queryKey: [‘posts’, criteria], queryFn: () => { if (typeof criteria === ‘string’) { return getFilteredPosts(criteria); } return getAllPosts(); }, }); return ... }
```

No olvide eliminar la palabra clave `async` de la función.

Hemos establecido la clave de consulta en `['posts', criteria]` para que todas las publicaciones y las publicaciones filtradas se almacenen en caché por separado. Le dimos un alias a los datos obtenidos como `resolvedPosts` para que no se requieran cambios en el JSX.

7. Agregue un indicador de carga y manejo de errores a `PostList` de la siguiente manera:

```tsx
import { Loading } from ‘./Loading’; import { ErrorAlert } from ‘./ErrorAlert’; export function PostList( ... ) { const { ..., isPending, error, } = useQuery( ... ); if (isPending) { return <Loading />; } if (error) { return <ErrorAlert error={error} />; } return ... }
```

8. Abra `src/app/posts/page.tsx` y elimine React Suspense y los boundaries de error porque la obtención de datos ya no ocurre desde los RSCs. La importación del indicador de carga también se puede eliminar. El JSX en `Posts` ahora debería ser el siguiente:

```tsx
<main> <h2>{resolvedHeading}</h2> <NewPost /> <PostList criteria={criteria} /> </main>
```

9. Ejecute la aplicación ejecutando `npm run dev` en la terminal, si aún no se está ejecutando. Abra las herramientas de desarrollo del navegador, vaya al panel Network y navegue hasta la página `Posts`. La última solicitud HTTP será la solicitud de obtención de datos.

*Figura 8.1 – Solicitud HTTP de obtención de datos del lado del cliente*

Observe que la solicitud es un HTTP POST en lugar de una solicitud GET. Esto se debe a que todas las Server Functions en Next.js son solicitudes HTTP POST.

> [!NOTE]
> Es posible que se pierda el indicador de carga porque la obtención de datos es rápida, ya que todo se ejecuta localmente. Puede cambiar la configuración de Throttle en el panel Network de las herramientas de desarrollo del navegador para ralentizar el proceso de obtención de datos.

10. Provocaremos un error temporalmente para comprobar que el código de manejo de errores funciona. Abra `queries.ts` y, en la función `getAllPosts`, cambie `posts` por `postsX` en la sentencia SQL. Actualice la página `Posts` en la aplicación en ejecución y verá varias solicitudes HTTP para obtener los datos que devuelven el estado 500 antes de que se muestre el error. Este es el comportamiento de **reintento predeterminado** en TanStack Query. Revierta el cambio antes de continuar para que la consulta vuelva a tener éxito.
11. Experimentaremos otra agradable característica predeterminada en TanStack Query. Primero, asegúrese de que la página `Posts` esté activa en la aplicación y luego cambie a una pestaña diferente en el navegador.
12. Ahora simulemos que un usuario diferente agrega una publicación de blog. Inserte un registro en la tabla `posts` utilizando la extensión de Visual Studio Code para SQLite.

*Figura 8.2 – Agregar un registro en la tabla posts*

13. Vuelva a la aplicación en el navegador y verá la nueva publicación de blog en la lista. TanStack Query obtiene automáticamente una copia nueva de los datos si la ventana del navegador pierde y recupera el foco (*focus*).

*Figura 8.3 – Nueva publicación agregada automáticamente al final de la página Posts*

14. Ahora rediseñaremos la obtención de datos de la página `Post` en nuestra aplicación para usar TanStack Query. Nuevamente crearemos un indicador de carga y manejaremos errores. Lleve a cabo los siguientes pasos:
    - Comenzaremos convirtiendo `PostDetail` en un Client Component y rediseñaremos su obtención de datos para usar TanStack Query. Abra `PostDetail.tsx` y realice los siguientes cambios resaltados:

```tsx
‘use client’; import { useQuery } from ‘@tanstack/react-query’; ... export function PostDetail( ... ) { const { data: post } = useQuery({ queryKey: [‘post’, id], queryFn: () => { return getPost(id); }, }); if (!post) { ... } return ... }
```

No olvide eliminar la palabra clave `async` de la función.

Hemos establecido la clave de consulta en `['post', id]` para que las diferentes publicaciones se almacenen en caché por separado. Le dimos un alias a los datos obtenidos como `post` para que no se requieran cambios en el JSX.

15. Agregue un indicador de carga y manejo de errores a `PostDetail` de la siguiente manera:

```tsx
import { Loading } from ‘./Loading’; import { ErrorAlert } from ‘./ErrorAlert’; ... export function PostDetail( ... ) { const { ..., isPending, error } = useQuery( ... ); if (isPending) { return <Loading />; } if (error) { return <ErrorAlert error={error} />; } if (!post) { ... } return ... }
```

16. Por último, abra `src/app/posts/[id]/page.tsx` y elimine React Suspense, el error boundary y el indicador de carga porque la obtención de datos ya no ocurre desde los RSCs. El JSX en `Post` ahora debería ser el siguiente:

```tsx
<main> <PostDetail id={id} /> </main>
```

La página `Post` ahora utilizará la obtención de datos del lado del cliente en la aplicación en ejecución. Puede experimentar el manejo de errores en la página utilizando el mismo enfoque que antes.

Es posible que haya notado que las nuevas publicaciones de blog creadas con el botón **Create New Post** no aparecen en la lista. Resolveremos esto más adelante en este capítulo cuando veamos las mutaciones de TanStack.

Eso completa el rediseño de la obtención de datos para usar TanStack Query. He aquí un resumen de esta sección:
- TanStack Query simplifica la obtención de datos del lado del cliente con características como la gestión automática de los estados de carga y error, almacenamiento en caché, reactualización de datos obsoletos y reintento de consultas fallidas.
- Para usar TanStack Query, `QueryClientProvider` se coloca en un nivel alto en el árbol de componentes de React, lo que permite el acceso global a la caché de datos y proporciona Hooks como `useQuery` para obtener y administrar los estados de las consultas.
- La configuración predeterminada de TanStack Query significa que las consultas erróneas se reintentan y los datos se vuelven a obtener cuando la ventana del navegador recupera el foco.

A continuación, rediseñaremos la obtención de datos para utilizar un Route Handler de Next.js.

---

### Uso de un Route Handler con TanStack Query

En esta sección, aprenderemos sobre los **Route Handlers** de Next.js y los beneficios de usarlos para la obtención de datos en lugar de las Server Functions. Luego rediseñaremos nuestra aplicación para usar Route Handlers.

#### Comprender los beneficios de los Route Handlers

Un Route Handler de Next.js permite crear un punto de conexión (*endpoint*) de API. Aunque el enfoque de Server Functions es simple y proporciona seguridad de tipos a través del límite de red, tiene algunas desventajas, como las siguientes:
- Las Server Functions se ejecutan secuencialmente. Por lo tanto, si un componente llama a una Server Function para obtener datos y un componente secundario llama a otra Server Function, la segunda llamada debe esperar a que se complete la primera. Por el contrario, los Route Handlers se ejecutan en **paralelo**.
- Las Server Functions utilizan un HTTP POST en lugar de un GET, que se usa comúnmente para obtener datos. HTTP POST se utiliza generalmente para mutaciones.
- Los equipos de Next.js o React no recomiendan las Server Functions para la obtención de datos.

Ahora que entendemos las razones para usar Route Handlers, los usaremos en nuestra aplicación.

#### Uso de Route Handlers

Reemplazaremos el uso actual de Server Functions en nuestra aplicación con Route Handlers. Lleve a cabo los siguientes pasos para crear Route Handlers para las rutas `api/posts` y `api/posts/[id]`:

1. Comience eliminando la directiva `'use server'` en la parte superior de `queries.ts` para que sus funciones exportadas ya no estén disponibles como Server Functions.
2. Los Route Handlers se definen en la carpeta `src/app/api`. Los manejadores para diferentes rutas se definen de manera similar a las páginas con un archivo `route.ts` en lugar de un archivo `page.tsx`. Cree un archivo `route.ts` en la carpeta `src/app/api/posts` y agregue el siguiente contenido:

```typescript
import { type NextRequest } from ‘next/server’; import { getAllPosts, getFilteredPosts, } from ‘@/data/queries’; export async function GET(request: NextRequest) { const criteria = request.nextUrl.searchParams.get(‘criteria’); if (typeof criteria === ‘string’) { return Response.json( await getFilteredPosts(criteria), ); } return Response.json(await getAllPosts()); }
```

El código maneja solicitudes GET a la ruta `api/posts`. Extrae `criteria` de los parámetros de búsqueda y devuelve los datos de la consulta `getFilteredPosts` si está definida. Si no se especifican criterios, se llama a la consulta `getAllPosts` para obtener los datos que se devolverán.

3. Pruebe a acceder a la API en un navegador ingresando [http://localhost:3000/api/posts](http://localhost:3000/api/posts) como dirección del navegador. Se devolverán los datos JSON para todas las publicaciones.
4. Cree un archivo `route.ts` en la carpeta `src/app/api/posts/[id]` y agregue el siguiente contenido:

```typescript
import { getPost } from ‘@/data/queries’; export async function GET( _: Request, { params }: { params: Promise<{ id: string }> } ) { const id = Number((await params).id); if (!Number.isInteger(id)) { return Response.json( { message: ‘Post not found’ }, { status: 404 } ); } const data = await getPost(id); if (!data) { return Response.json( { message: ‘Post not found’ }, { status: 404 } ); } return Response.json(data); }
```

El código maneja solicitudes GET a las rutas `api/posts/[id]`. Extrae el parámetro de ruta `id` y devuelve los datos de la consulta `getPost`. Si `id` no es numérico o la consulta no devuelve ninguna fila, se devuelve el código de estado HTTP 404.

5. Pruebe a acceder a la API en un navegador ingresando [http://localhost:3000/api/posts/1](http://localhost:3000/api/posts/1) como dirección del navegador. Se devolverán los datos JSON para la publicación 1.
6. Ahora integraremos los Route Handlers en los componentes `PostList` y `PostDetail`. Comience con `PostList` abriendo `PostList.tsx` y elimine las consultas importadas `getAllPosts` y `getFilteredPosts`. Por último, actualice `useQuery` para usar el Route Handler de la siguiente manera:

```tsx
const { ... } = useQuery({ queryKey: ..., queryFn: async () => { const path = typeof criteria === ‘string’ ? `/api/posts/?criteria=${ encodeURIComponent(criteria)}` : ‘/api/posts/’; const response = await fetch(path); if (!response.ok) { throw new Error(‘Problem fetching data’); } return await response.json(); }, });
```

Llamamos a la función `fetch` del navegador para realizar una solicitud HTTP a la ruta correspondiente en función de si hay un parámetro de búsqueda `criteria`. Si la respuesta devuelve un código de estado de error HTTP, lanzamos un error para poner la consulta en un estado de error.

Notará un error de tipo en la función `map` de la variable `resolvedPosts` porque solo se puede inferir que tiene el tipo `any`. Resolveremos este error más adelante en este capítulo. Sin embargo, el código funcionará bien si navegamos a la página `Posts` en la aplicación en ejecución.

7. Ahora rediseñaremos el componente `PostDetail`. Abra `PostDetail.tsx` y elimine la función `getPost` importada. Por último, actualice `useQuery` para usar el Route Handler de la siguiente manera:

```tsx
const { ... } = useQuery({ queryKey: ..., queryFn: async () => { const response = await fetch( `/api/posts/${id}`, ); if (!response.ok) { throw new Error( response.status === 404 ? “Blog post not found” : “Problem fetching data”, ); } return await response.json(); }, });
```

Llamamos a la función `fetch` del navegador para realizar una solicitud HTTP a la ruta correspondiente utilizando el parámetro de ruta `id`. Si la respuesta devuelve un código de estado de error HTTP, lanzamos un error para poner la consulta en un estado de error. Capturamos específicamente el código de estado 404 para que podamos lanzar un mensaje de error apropiado.

Nuevamente, se infiere que los datos de TanStack Query tienen el tipo `any`, lo cual no es ideal. Sin embargo, el código funcionará bien si navegamos a la página `Post` en la aplicación en ejecución.

Eso completa el rediseño para cambiar al uso de Next.js Route Handlers. Para obtener más información sobre los Route Handlers, consulte la siguiente página: [https://nextjs.org/docs/app/building-your-application/routing/route-handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers).

A continuación, resolveremos el problema de seguridad de tipos con los datos de TanStack Query.

#### Adición de seguridad de tipos a la respuesta de la API

Podríamos usar una aserción de tipo de TypeScript para hacer que el código de obtención de datos sea un poco más seguro en cuanto a tipos de la siguiente manera:

```typescript
return response.json() as Promise<Posts>
```

Sin embargo, estamos asumiendo el esquema del cuerpo de la respuesta de la API. Esto está bien si controlamos la API, como en nuestra aplicación. Sin embargo, ¿qué pasa si un equipo independiente desarrolla esa API? ¿Qué pasa si es una API de terceros que controla una empresa diferente? Esta suposición puede ser arriesgada.

En lugar de una aserción de tipo, usaremos Zod para validar el esquema del cuerpo de la respuesta de la API, tal como lo hicimos para los resultados de las consultas SQL anteriormente en este capítulo. Usaremos el mismo esquema de Zod que usamos para la validación de consultas SQL porque el esquema no cambia después de ser devuelto por la consulta. Lleve a cabo los siguientes pasos:

1. Abra `PostList.tsx` y agregue una verificación de esquema Zod de la siguiente manera:

```tsx
import { postsSchema } from ‘@/data/schema’; export function PostList( ... ) { const { ... } = useQuery({ ..., queryFn: async () => { ... return postsSchema.parse( await response.json(), ); }, }); ... }
```

El error de tipo ahora está resuelto y, si pasa el cursor sobre la variable `resolvedPosts`, su tipo ahora se inferirá correctamente.

2. Abra `PostDetail.tsx` y agregue una verificación de esquema Zod de la siguiente manera:

```tsx
import { postSchema } from ‘@/data/schema’; export function PostDetail( ... ) { const { ... } = useQuery({ ..., queryFn: async () => { ... return postSchema.parse( await response.json(), ); }, }); ... }
```

Si pasa el cursor sobre la variable `post`, su tipo ahora se inferirá correctamente.

Con esto se completa la mejora de seguridad de tipos y el final de esta sección sobre el uso de Route Handlers. He aquí un resumen:
- Generalmente se prefieren los Route Handlers a las Server Functions para la obtención de datos. Esto se debe a que mejoran el rendimiento al permitir solicitudes de API paralelas y utilizar solicitudes HTTP GET.
- Una desventaja de utilizar Route Handlers para la obtención de datos es la falta de seguridad de tipos a través del límite de red. Zod se puede utilizar para cerrar esta brecha con un esquema que valide el cuerpo de la respuesta de la API.

A continuación, usaremos TanStack Query para mutaciones.

---

### Mutación de datos mediante una mutación de TanStack Query

En esta sección, comprenderemos y luego utilizaremos una mutación de TanStack Query en el componente `NewPost`. También resolveremos el problema de que las nuevas publicaciones no aparezcan en la página `Posts`.

#### Comprender las mutaciones de TanStack Query

El Hook `useMutation` de TanStack Query administra el proceso de mutación, incluidas variables de estado útiles. También proporciona un mecanismo para actualizar la caché después de una mutación. Aquí hay un ejemplo:

```tsx
const { mutate, isPending, isError } = useMutation({ mutationFn: (newProduct) => createProduct(newProduct), onSuccess: async () => { await queryClient.invalidateQueries({ queryKey: [‘products’], }), }, }); async function handleClick() { mutate({ name: ‘New product’, description: ‘New product description’, }); } if (isPending) return ‘Mutating... ‘; if (isError) return ‘An unexpected error occurred’;
```

El Hook `useMutation` recibe un objeto de opciones como argumento. Aquí hay una descripción de las opciones utilizadas en este ejemplo:
- `mutationFn`: La función que llama al servidor para realizar la mutación. En este ejemplo, llamamos a una Server Function llamada `createProduct`.
- `onSuccess`: Una función para llamar cuando la mutación es exitosa. En este ejemplo, invalidamos la caché de `products`.

El Hook `useMutation` devuelve un objeto. Aquí hay una descripción de los miembros del objeto utilizados en este ejemplo:
- `mutate`: Esta es la función que inicia el proceso de mutación. En este ejemplo, esta función se utiliza en un manejador de clics.
- `isPending`: Esto indica si la mutación se está ejecutando actualmente. Lo usamos en este ejemplo para renderizar un indicador de mutación.
- `isError`: Esto indica que la mutación ha fallado. Lo usamos en este ejemplo para renderizar una alerta de error.

A continuación, usaremos `useMutation` en nuestra aplicación.

#### Uso de useMutation

Simplificaremos la gestión del estado en el componente `NewPost` utilizando `useMutation`. También invalidaremos la caché de publicaciones después de que la mutación haya sido exitosa. Lleve a cabo los siguientes pasos:

1. Abra `NewPost.tsx` y comience eliminando las llamadas existentes a `useState` de `isMutating` y `status`. La importación de `useState` de React también se puede eliminar.
2. Importe el Hook `useMutation` y llámelo de la siguiente manera:

```tsx
import { useMutation } from ‘@tanstack/react-query’; ... export function NewPost() { const { mutate, isPending, isError, isSuccess } = useMutation({ mutationFn: ({ title, description, }: { title: string; description: string; }) => createPost(title, description), }); async function handleClick() { ... } return ... }
```

La función de mutación llama a la Server Function `createPost`, pasando el título y la descripción de la nueva publicación de blog. Hemos desestructurado la función `mutate`, así como las variables de estado `isPending`, `isError` e `isSuccess`.

> [!NOTE]
> Podríamos haber utilizado un Route Handler para la mutación en lugar de una Server Function. Sin embargo, las Server Functions son más sencillas de implementar y ofrecen mayor seguridad de tipos.

3. Llame a `mutate` en el manejador de clics y elimine también las referencias a las variables de estado antiguas. Además, la función ya no necesita estar marcada con la palabra clave `async`. El manejador de clics ahora debería quedar de la siguiente manera:

```typescript
function handleClick() { mutate({ title: ‘New Post’, description: ‘New Post Description’, }); }
```

4. En el JSX, reemplace `isMutating` con `isPending`. Además, reemplace los condicionales en la antigua variable `status` con `isError` e `isSuccess`:

```tsx
<div ... > <button ... > {isPending ? ‘Creating...’ : ‘Create New Post’} </button> {isError && ( <span ... >An unexpected error occurred</span> )} {isSuccess && ( <span ... >Post successfully created</span> )} </div>
```

Si prueba el botón **Create New Post** en la aplicación en ejecución, se comportará como antes. Esto significa que la nueva publicación aún no aparece en la lista después de que la mutación se haya completado con éxito.

5. Resolvamos este problema invalidando la caché en la opción de función `onSuccess` de la siguiente manera:

```tsx
import { ..., useQueryClient, } from ‘@tanstack/react-query’; ... export function NewPost() { const queryClient = useQueryClient(); const { ... } = useMutation({ mutationFn: ..., onSuccess: async () => { queryClient.invalidateQueries({ queryKey: [‘posts’], }); }, }); ... }
```

Usamos el Hook `useQueryClient` para acceder a `queryClient` de modo que podamos llamar a su función `invalidateQueries`. Pasamos a la función `invalidateQueries` la clave que queremos invalidar.

6. Si prueba el botón **Create New Post** en la aplicación en ejecución, la nueva publicación ahora aparecerá en la lista después de que la mutación se haya completado con éxito.

Eso completa esta sección sobre mutaciones con TanStack Query. He aquí un resumen rápido:
- El Hook `useMutation` de TanStack Query simplifica la gestión del estado al gestionar las mutaciones, proporcionando variables de estado de ayuda como `isPending`, `isSuccess` e `isError`.
- Al llamar a `QueryClient.invalidateQueries` con la clave de consulta adecuada en la función `onSuccess`, se pueden invalidar los datos en la caché afectados por la mutación.

> [!NOTE]
> Para obtener más información sobre TanStack Query, consulte su documentación en [https://tanstack.com/query/latest](https://tanstack.com/query/latest).

A continuación, resumiremos lo que hemos aprendido en este capítulo.

---

### Resumen

En este capítulo, comenzamos explorando el uso del Hook `useEffect` para la obtención de datos del lado del cliente y lo difícil que es escribir código robusto. Rápidamente cambiamos a utilizar TanStack Query y experimentamos cómo simplificó el código y habilitó funciones como el reintento de una obtención de datos cuando falla. Experimentamos cómo actualizó automáticamente los datos cuando la pestaña del navegador de nuestra aplicación recuperó el foco, algo que no es posible con la obtención de datos del lado del servidor.

Aprendimos que el uso de una Server Function tiene algunas desventajas para la obtención de datos del lado del cliente, como el uso de una solicitud HTTP POST en lugar de un HTTP GET. Cambiamos a utilizar un Route Handler de Next.js debido a estas desventajas.

Aprendimos que las Server Functions son ideales para mutar datos debido a su simplicidad y seguridad de tipos. Usamos una Server Function en nuestra aplicación para agregar una nueva publicación de blog a nuestra base de datos. Usamos TanStack Query con una Server Function para mutar datos y aprendimos que la caché de TanStack Query debe invalidarse después de una mutación exitosa.

Ahora tiene las habilidades para implementar mutaciones y obtención de datos del lado del cliente robustas de una manera muy fácil de mantener.

En el próximo capítulo, aprenderemos más sobre las mutaciones a medida que exploremos los formularios en profundidad.

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido en este capítulo:

1. ¿Cuál es el problema con el siguiente código de obtención de datos?

```tsx
useEffect(() => { fetch(“/api/data”) .then((res) => res.json()) .then((data) => setData(data)); }, []);
```

2. ¿Por qué no se recomienda utilizar una Server Function para la obtención de datos en Next.js?
3. ¿Cuál será la salida de la siguiente validación de esquema Zod?

```typescript
const userSchema = z.object({ name: z.string(), age: z.number().min(18), }); const result = userSchema.safeParse({ name: “Alice”, age: 16, }); console.log(result.success);
```

4. ¿Qué hace la siguiente mutación cuando se envía el formulario?

```tsx
const mutation = useMutation({ mutationFn: (newUser) => fetch(“/api/users”, { method: “POST”, body: JSON.stringify(newUser), }), onSuccess: () => { console.log(“User created!”); }, }); <form onSubmit={(e) => { e.preventDefault(); mutation.mutate({ name: “Jane” }); }} > <button type=”submit”>Create User</button> </form>;
```

5. ¿Qué hace la opción `staleTime` en el siguiente código?

```typescript
useQuery([“todos”], fetchTodos, { staleTime: 10000 });
```

---

### Respuestas

1. Si el componente se desmonta antes de que finalice la obtención de datos, puede intentar actualizar el estado, provocando una advertencia (*warning*). Se puede utilizar un objeto `AbortController` que se aborte cuando el componente se desmonte para solucionar el problema:

```tsx
useEffect(() => { const controller = new AbortController(); fetch(“/api/data”, { signal: controller.signal }) .then((res) => res.json()) .then((data) => setData(data)) .catch((err) => { if (err.name !== “AbortError”) { console.error(err); } }); return () => controller.abort(); }, []);
```

2. Las Server Functions se ejecutan en secuencia en lugar de en paralelo, lo que puede causar un problema de rendimiento cuando se llaman múltiples Server Functions. También utilizan un HTTP POST en lugar de un HTTP GET.
3. La validación fallará porque el esquema requiere que `age` sea un número mayor o igual a 18, pero se proporciona 16. Por lo tanto, `safeParse` devolverá un objeto con una propiedad `success` establecida en `false`. Por lo tanto, la salida de la instrucción `console.log` será `false`.
4. Envía una solicitud HTTP POST y muestra `User created!` en la consola cuando tiene éxito.
5. La opción `staleTime` es la cantidad de milisegundos que los datos de una consulta se consideran frescos (*fresh*). Durante este tiempo, TanStack Query no volverá a obtener los datos automáticamente en segundo plano, incluso si el componente se vuelve a montar o la ventana vuelve a enfocar. Por lo tanto, en el fragmento de código, los datos obtenidos se considerarán frescos durante diez segundos.
