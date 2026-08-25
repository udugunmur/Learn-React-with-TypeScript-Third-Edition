# Parte 2: Fundamentos de la Aplicación

## Capítulo 6: Creación de una aplicación multipágina con Next.js

Hasta ahora en este libro, hemos creado aplicaciones con una sola pantalla. En este capítulo, aprenderemos a crear aplicaciones con múltiples pantallas con **Next.js**, que se presentó en el capítulo anterior. React no incluye gran parte de la funcionalidad necesaria para crear aplicaciones de varias páginas, y Next.js es una opción popular que llena ese vacío.

Crearemos una aplicación de publicaciones de blog que contiene tres pantallas: una página de **Inicio (Home)**, una página de **Publicaciones (Posts)** y una página de **Publicación individual (Post)**. Crearemos un encabezado compartido en las pantallas y opciones de navegación entre ellas. También crearemos una función de búsqueda y aprenderemos todo sobre los parámetros de búsqueda (*search parameters*).

Al final de este capítulo, sabrá cómo crear aplicaciones de varias páginas utilizando Next.js.

Cubriremos los siguientes temas:

- Creación de rutas
- Creación de navegación
- Creación de un layout compartido
- Creación de rutas dinámicas
- Uso de parámetros de búsqueda

---

### Requisitos técnicos

En este capítulo utilizaremos las siguientes tecnologías:

- **Navegador**: Un navegador moderno como Google Chrome
- **Node.js y npm**: Puede instalarlos desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
- **Visual Studio Code**: Puede instalarlo desde [https://code.visualstudio.com/](https://code.visualstudio.com/)

Todos los fragmentos de código utilizados en este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter06](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter06)

---

### Creación de rutas

En esta sección, crearemos un proyecto de Next.js. Entenderemos qué son las rutas y cómo crearlas. Usaremos este conocimiento para crear una ruta que enumere todas las publicaciones de nuestro blog.

#### Creación del proyecto

Comencemos creando un proyecto de Next.js. Esto se cubrió en el capítulo anterior, por lo que las explicaciones de los pasos son breves:

1. En una terminal, ejecute el siguiente comando para crear el proyecto:

```bash
npx create-next-app@latest blog --ts --eslint --app --src-dir --import-alias “@/*” --no-tailwind --turbopack
```

2. Aún en la terminal, muévase a la carpeta del proyecto y abra Visual Studio Code usando los siguientes comandos:

```bash
cd blog code .
```

Prettier se puede configurar de la misma manera que aprendimos con Vite en el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*. Siéntase libre de agregar el formateo automático de código a este proyecto.

3. En la terminal, ejecute el siguiente comando para ejecutar la aplicación en modo de desarrollo:

```bash
npm run dev
```

La aplicación estará disponible en un navegador en [http://localhost:3000](http://localhost:3000/).

4. Abra `src/app/globals.css` y sobrescriba el contenido con el CSS del siguiente archivo en el repositorio de GitHub:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter06/creating-project/src/app/globals.css](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter06/creating-project/src/app/globals.css)

Esto le dará un estilo agradable a nuestra aplicación.

5. Por último, limpiemos la página de inicio. Abra `page.tsx` en la carpeta `src/app` y reemplace su contenido con lo siguiente:

```tsx
export default function Home() { return ( <main> <h2> Welcome to my blog! </h2> </main> ); }
```

La página muestra un bonito encabezado centrado en la parte superior de la página:

*Figura 6.1 – Página de inicio*

Eso completa la configuración del proyecto. A continuación, comprenderemos cómo crear nuevas páginas.

#### Comprender las rutas

Una **ruta (route)** es una ruta en una aplicación web que corresponde a una URL. Next.js tiene dos tipos de sistemas de enrutamiento: el *Page Router* y el *App Router*. En este libro, nos centraremos en el **App Router**, lanzado más recientemente, con el que se ha configurado nuestro proyecto.

Las rutas se definen mediante la estructura de carpetas en la carpeta `app` y un archivo especial llamado `page.tsx`. Ya tenemos una ruta de inicio definida para la ruta `/`, que es el archivo `src/app/page.tsx`. Para definir una ruta de configuración (*settings*), implementaríamos un archivo `src/app/settings/page.tsx`.

El contenido de `page.tsx` es un componente de React. Puede ser un RSC o un Client Component. El nombre del componente puede ser un nombre significativo de nuestra elección; sin embargo, es importante que sea la **exportación por defecto (default export)**. Debe ser la exportación por defecto para permitir que Next.js detecte y renderice automáticamente el componente adecuado para una ruta; si fuera una exportación con nombre, no sabría cuál usar.

Ahora que entendemos las rutas y cómo crearlas, crearemos una ruta para las publicaciones (*posts*).

#### Creación de una ruta de posts

Lleve a cabo los siguientes pasos para crear una ruta de posts en nuestra aplicación:

1. Cree una carpeta en la carpeta `src/app` llamada `posts` con un archivo `page.tsx` dentro.
2. Agregue el siguiente contenido al archivo `page.tsx` que acaba de crear:

```tsx
export default function Posts() { return ( <main> <h2>Posts</h2> <ul></ul> </main> ); }
```

El componente genera un encabezado y una lista vacía en esta etapa.

3. Vaya a la URL [http://localhost:3000/posts](http://localhost:3000/posts) en un navegador. Esto llegará a la ruta que acabamos de crear, mostrando el componente `Posts`.

*Figura 6.2 – Ruta posts*

4. Ahora agregaremos algunos datos de publicaciones de blog para mostrar en esta página. Comience creando una carpeta `data` en la carpeta `src` con un archivo llamado `posts.ts` en ella. Copie el contenido del archivo en el repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter06/creating-routes/src/data/posts.ts](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter06/creating-routes/src/data/posts.ts) y péguelo en `posts.ts`. Contiene una variable exportada llamada `posts` que contiene un array de publicaciones de blog:

```typescript
export const posts = [ { Id: 1, title: ‘Understanding React Hooks’, description: ‘A comprehensive guide to React Hooks and how they simplify state management in functional components’, }, ... ];
```

5. Ahora usaremos la variable `posts` en la página `Posts` para mostrar una lista de publicaciones de blog. Abra `app/posts/page.tsx` y agregue las siguientes líneas resaltadas:

```tsx
import { posts } from ‘@/data/posts’; export default function Posts() { return ( <main> <h2>Posts</h2> <ul> {posts.map((post) => ( <li key={post.id}> <span>{post.title}</span> <p>{post.description}</p> </li> ))} </ul> </main> ); }
```

La variable `posts` se importa y se usa en el JSX para iterar a través de su array y generar elementos de lista. El uso de `Array.map` es una práctica común para la lógica de bucle en JSX.

Observe el atributo `key` en los elementos de los elementos de lista. React requiere esto en elementos dentro de un bucle para actualizar los elementos del DOM correspondientes de manera eficiente. El valor del atributo `key` debe ser único y estable dentro del array, por lo que hemos utilizado el ID de la publicación.

6. Si observa la ruta `posts` en el navegador, ahora contiene una lista de publicaciones de blog.

*Figura 6.3 – Ruta posts actualizada*

Eso completa esta sección sobre la creación de rutas. El punto clave es que las rutas en Next.js se crean mediante la estructura de carpetas de `app` y los archivos `page.tsx`.

> [!NOTE]
> Para obtener más información sobre las rutas, consulte la siguiente página en la documentación de Next.js: [https://nextjs.org/docs/app/getting-started/layouts-and-pages](https://nextjs.org/docs/app/getting-started/layouts-and-pages).

A continuación, aprenderemos sobre un componente de Next.js que puede realizar navegación.

---

### Creación de navegación

Next.js tiene dos formas de implementar la navegación, que cubriremos en esta sección. Como parte del proceso de aprendizaje, actualizaremos la lista de publicaciones de blog para incluir enlaces a la página de publicación asociada.

#### Uso del componente Link

El componente `Link` es la forma recomendada de realizar la navegación en Next.js. Lleve a cabo los siguientes pasos para utilizar este componente en la página `Posts`:

1. Abra `app/posts/page.tsx` y agregue una declaración de importación en la parte superior del archivo para importar `Link`:

```typescript
import Link from ‘next/link’;
```

2. En el JSX del componente `Posts`, cambie el elemento que contiene el título de la publicación del blog a `Link` de la siguiente manera:

```tsx
<li key={post.id}> <Link href={`/posts/${post.id}`}> {post.title} </Link> <p>{post.description}</p> </li>
```

También hemos definido la ruta a la que debe ir el enlace mediante el atributo `href`.

3. La ruta a la que acabamos de hacer referencia aún no existe, así que vamos a crearla. Dentro de la carpeta `posts`, cree una carpeta llamada `1` (el número 1). Luego, cree un archivo `page.tsx` dentro de ella que contenga lo siguiente:

```tsx
export default function Post() { return <main>Blog post one</main>; }
```

Esto implementa la ruta `posts/1`, que satisfará el primer elemento de la lista de publicaciones de blog. Mejoraremos esta ruta más adelante en este capítulo para que todas las publicaciones del blog estén vinculadas.

4. Ejecute la aplicación ejecutando `npm run dev` en una terminal si no se está ejecutando.
5. Ahora, inspeccione la lista de publicaciones de blog utilizando las herramientas de desarrollo del navegador cuando esté en la página `Posts`:

*Figura 6.4 – Inspección del componente Link*

Podemos ver que el componente `Link` se renderiza como un elemento de anclaje (`<a>`) HTML.

6. Mientras todavía está en las herramientas de desarrollo del navegador, vaya al panel **Components** en las herramientas de desarrollo de React. Busque el componente `Posts` y confirme que es un RSC:

*Figura 6.5 – RSC Posts*

Por lo tanto, el punto clave es que `Link` se puede utilizar en RSCs.

7. Haga clic en el primer enlace de publicación de blog en la aplicación: lo dirigirá a la página de la publicación:

*Figura 6.6 – Navegación con Link*

Es posible que observe que no se produjo una recarga completa de la página. En su lugar, se realiza una **navegación del lado del cliente** para maximizar el rendimiento.

Eso completa el uso del componente `Link` por ahora. Lo usaremos nuevamente más adelante en este capítulo. Para obtener más información sobre el componente `Link`, consulte la siguiente página en la documentación de Next.js: [https://nextjs.org/docs/app/api-reference/components/link](https://nextjs.org/docs/app/api-reference/components/link).

A continuación, comprenderemos una forma diferente de navegar.

#### Uso de useRouter

El Hook `useRouter` de Next.js permite la **navegación programática**. A diferencia de `Link`, no se puede usar en un RSC, por lo que el componente consumidor debe ser un Client Component.

El hook devuelve un objeto que contiene funciones de enrutamiento útiles como las siguientes:
- `push`: Realiza la navegación del lado del cliente, agregando una nueva entrada al historial del navegador
- `replace`: Realiza la navegación del lado del cliente, sin agregar una nueva entrada al historial del navegador
- `refresh`: Actualiza la ruta actual sin perder ningún estado

El siguiente es un fragmento de código que incluye la navegación usando `useRouter`:

```tsx
import { useRouter } from ‘next/navigation’; function SomeComponent() { const router = useRouter(); ... function handleClick() { if (someCheck()) { router.push(‘/some-path’); } else router.push(‘/some-other-path’); } } return <button onClick={handleClick}>Action</button>; }
```

Aquí hay un desglose del fragmento de código:
- El hook `useRouter` se llama al inicio del componente y se asigna a una variable `router`. Por lo tanto, todas las funciones de enrutamiento están disponibles en la variable `router`.
- Se llama a la función `push` del router en el manejador de clics del botón pasando la ruta. Hay una llamada a la función `someCheck` para determinar la ruta a la que navegar.

> [!NOTE]
> Para obtener más información sobre el hook `useRouter`, consulte la siguiente página en la documentación de Next.js: [https://nextjs.org/docs/app/api-reference/functions/use-router](https://nextjs.org/docs/app/api-reference/functions/use-router).

Eso completa esta sección sobre navegación. En resumen, el componente `Link` es la forma recomendada de navegar en Next.js y se puede utilizar en RSCs así como en Client Components. El hook `useRouter` permite la navegación programática en Client Components.

A continuación, cubriremos cómo implementar componentes de layout compartidos.

---

### Creación de un layout compartido

En esta sección, crearemos un encabezado para nuestra aplicación que contendrá enlaces a las páginas Home y Posts. Utilizaremos las capacidades de layout compartido de Next.js para implementar esto.

#### Comprender los componentes de layout

En Next.js, un **layout compartido (shared layout)** se define en un archivo especial llamado `layout.tsx`. Los layouts compartidos se pueden definir en cualquier ruta. Nuestra aplicación actual tiene un layout compartido en la ruta raíz en la carpeta `src/app`. A continuación se muestran algunos de los contenidos de este archivo:

```tsx
... export default function RootLayout({ children }: ...) { return ( <html lang=”en”> <body ... > {children} </body> </html> ); }
```

El contenido del archivo es un componente de React establecido como exportación por defecto. El nombre del componente puede ser un nombre significativo de nuestra elección. Este componente se llama sensatamente `RootLayout` porque se renderizará para cada ruta.

Los componentes de layout pueden ser RSCs o Client Components. En nuestra aplicación, `RootLayout` es un RSC.

El componente de página para la ruta renderizada se pasará a la prop `children` del componente de layout. Por lo tanto, se pasará `Home` para la ruta `/` y `Posts` para la ruta `posts/`.

Ahora que entendemos cómo funcionan los componentes de layout compartidos, crearemos un encabezado compartido.

#### Creación de un encabezado

Crearemos un componente `Header` en nuestra aplicación que contenga enlaces a las páginas Home y Posts. Luego agregaremos `Header` a `RootLayout` para que sea visible en todas las rutas. Lleve a cabo los siguientes pasos para hacer esto:

1. Cree una carpeta `components` en la carpeta `src` y luego un archivo llamado `Header.tsx` dentro de ella. Agregue el siguiente contenido dentro de `Header.tsx`:

```tsx
import Link from ‘next/link’; export function Header() { return ( <header> <Link href=”/”>Home</Link> <Link href=”/posts”>Posts</Link> </header> ); }
```

Dentro de un elemento `header`, hemos agregado enlaces a las rutas `home` y `posts` utilizando el componente `Link`.

2. Abra `src/app/layout.tsx` y agregue `Header` dentro del elemento `body` de la siguiente manera:

```tsx
... import { Header } from ‘@/components/Header’; ... export default function RootLayout( ... ) { return ( <html ... > <body ... > <Header /> {children} </body> </html> ); }
```

3. Ejecute la aplicación ejecutando `npm run dev` en una terminal si no se está ejecutando. Mire la aplicación en el navegador: verá el encabezado que acabamos de agregar tanto en la página de inicio como en la de publicaciones:

*Figura 6.7 – Encabezado en la página de inicio*

4. Vamos a mejorar un poco el estilo de los enlaces del encabezado. Le daremos estilo al enlace activo para que se destaque. Usaremos un hook llamado `usePathname` de Next.js para obtener la ruta activa y compararla con la ruta del enlace para determinar si está activo. Para empezar, realice los siguientes cambios resaltados en `Header.tsx` para obtener la ruta activa:

```tsx
‘use client’; import Link from ‘next/link’; import { usePathname } from ‘next/navigation’; export function Header() { const pathname = usePathname(); return ... }
```

Necesitamos convertir `Header` en un Client Component porque `usePathname` no se puede utilizar en un RSC.

5. Ahora use la variable `pathname` para establecer condicionalmente una clase CSS `active` en los enlaces:

```tsx
<header> <Link href=”/” className={pathname === ‘/’ ? ‘active’ : ‘’} > Home </Link> <Link href=”/posts” className={ pathname === ‘/posts’ ? ‘active’ : ‘’ } > Posts </Link> </header>
```

Si mira la aplicación en ejecución, el enlace activo estará en negrita:

*Figura 6.8 – Enlace activo en el encabezado*

Eso completa nuestro encabezado compartido y esta sección sobre layouts compartidos.

En resumen, un componente de layout compartido se define en `layout.tsx`. El layout raíz se comparte en todas las rutas y está en la carpeta `app`.

> [!NOTE]
> Para obtener más información sobre el archivo `layout.tsx`, consulte la siguiente página en la documentación de Next.js: [https://nextjs.org/docs/app/api-reference/file-conventions/layout](https://nextjs.org/docs/app/api-reference/file-conventions/layout).

Actualmente, la ruta de la publicación del blog solo funciona para la primera. Abordaremos esto en la siguiente sección.

---

### Creación de rutas dinámicas

Aprenderemos sobre rutas dinámicas en esta sección y las usaremos para implementar completamente la ruta de publicaciones de blog.

#### Comprender las rutas dinámicas

En Next.js, una **ruta dinámica (dynamic route)** le permite crear páginas que pueden responder a diferentes parámetros de URL. Esto permite que el contenido de la página se muestre a partir de datos variables en la URL. La parte dinámica de la URL se define entre corchetes `[]`.

Un ejemplo es nuestra página de publicación en las rutas `posts/1`, `posts/2`, y así sucesivamente, que es `posts/[id]` en su forma dinámica. En esta ruta dinámica, `id` se denomina **parámetro de ruta (route parameter)**.

Los parámetros de ruta se pasan al componente de página en una prop `params`. Por ejemplo, se podría usar un parámetro `id` en la página `Post` de la siguiente manera:

```tsx
export default async function Post({ params, }: { params: Promise<{ id: string }>; }) { const id = (await params).id; return <main>Blog post {id}</main>; }
```

Se utiliza una anotación de tipo para tipar fuertemente los parámetros de la ruta. Tenga en cuenta que la prop `params` es asincrónica, lo que significa que el componente debe declararse con la palabra clave `async`.

Si desea utilizar un parámetro de ruta en una parte inferior del árbol de componentes, Next.js tiene un hook `useParams` que se puede utilizar. Esto solo está disponible en Client Components y no en RSCs. `useParams` tiene un parámetro genérico para tipar fuertemente los parámetros de ruta. Aquí hay un ejemplo de uso de un parámetro `id`:

```tsx
‘use client’; import { useParams } from ‘next/navigation’; export function SomeComponent() { const params = useParams<{id: string}>(); return <h3>Blog post {params.id}</h3> }
```

Ahora que entendemos las rutas dinámicas y cómo implementarlas, implementaremos por completo nuestra ruta de publicaciones de blog.

#### Creación de una ruta dinámica para las publicaciones de blog

Lleve a cabo los siguientes pasos para hacer que la ruta de la publicación del blog sea dinámica. También mostraremos el título y la descripción de la publicación del blog en su componente de página:

1. Comience cambiando el nombre de la carpeta `1` dentro de la carpeta `src/app/posts` a `[id]`. Esto hace que la ruta sea dinámica con un parámetro de ruta `id`.
2. Abra `page.tsx` dentro de la carpeta `[id]` y agregue los cambios resaltados:

```tsx
export default async function Post({ params, }: { params: Promise<{ id: string }>; }) { const id = (await params).id; return <main>Blog post {id}</main>; }
```

El componente ahora está utilizando el parámetro de ruta.

3. Ejecute la aplicación ejecutando `npm run dev` en una terminal si no se está ejecutando.
4. Vaya a la aplicación en el navegador, vaya a la lista de publicaciones de blog y haga clic en diferentes enlaces de publicaciones de blog. Verá que la página `Post` muestra correctamente el parámetro de ruta `id`:

*Figura 6.9 – Página Post mostrando su parámetro de ruta id*

5. Agreguemos algo de validación para garantizar que el parámetro de ruta `id` sea numérico. Si no es numérico, informaremos al usuario que no se encontró la página:

```tsx
import { notFound } from “next/navigation”; export default async function Post( ... ) { const id = Number((await params).id); if (!Number.isInteger(id)) { notFound(); } return <main>Blog post {id}</main>; }
```

La función `notFound` de Next.js mostrará el componente de página no encontrada predeterminado de Next.js.

6. Queremos mostrar el título y la descripción de la publicación del blog en la página `Post`. Primero, accedamos a la variable `posts` y busquemos la publicación relevante usando el parámetro de ruta `id`. Realice las siguientes adiciones en `src/app/posts/[id]/page.tsx`:

```tsx
import { posts } from ‘@/data/posts’; export default async function Post( ... ) { ... const post = posts.find( (post) => post.id === Number(id), ); return <main>Blog post {params.id}</main>; }
```

7. Si no se encuentra la publicación, muestre un mensaje útil agregando las líneas resaltadas:

```tsx
export default async function Post( ... ) { ... const post = posts.find( ... ); if (!post) { notFound(); } return <main>Blog post {params.id}</main>; }
```

8. Finalmente, reemplace el contenido del elemento `main` con el título y la descripción de la publicación del blog de la siguiente manera:

```tsx
export default async function Post( ... ) { ... return ( <main> <h2>{post.title}</h2> <p>{post.description}</p> </main> ); }
```

9. Ahora vaya a diferentes publicaciones de blog en la aplicación en ejecución: verá el título y la descripción mostrados:

*Figura 6.10 – Página Post con título y descripción*

Si introduce un parámetro de ruta `id` no válido en la URL del navegador, se mostrará un mensaje indicando que no se ha encontrado.

Eso completa esta sección sobre rutas dinámicas. He aquí un resumen rápido:
- Las rutas dinámicas en Next.js le permiten crear páginas que responden a los parámetros de URL. Usamos esta función para mostrar diferentes publicaciones de blog según el parámetro de ruta `id`.
- La prop `params` o el hook `useParams` en Client Components pueden acceder a los parámetros de ruta y actualizar dinámicamente el contenido en función de la URL.

> [!NOTE]
> Para obtener más información sobre las rutas dinámicas, consulte la siguiente página en la documentación de Next.js: [https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes).

A continuación, aprenderemos sobre el otro tipo de parámetro de URL: un parámetro de búsqueda (*search parameter*).

---

### Uso de parámetros de búsqueda

En esta sección, aprenderemos sobre los parámetros de búsqueda en Next.js y los usaremos para implementar una función de búsqueda en la aplicación.

#### Comprender los parámetros de búsqueda

Los **parámetros de búsqueda (search parameters)** son parte de una URL que viene después del carácter `?` y se separan por el carácter `&`. Los parámetros de búsqueda a veces se denominan parámetros de consulta (*query parameters*). En la siguiente URL, `type` y `when` son parámetros de búsqueda: `https://somewhere.com/?type=sometype&when=recent`.

En Next.js, se puede acceder a los parámetros de búsqueda a través de una prop `searchParams` de la siguiente manera:

```tsx
export default async function Page({ searchParams, }: { searchParams: Promise<{ [key: string]: string | string[] | undefined; }>; }) { const params = await searchParams; return ( <main> Searching: {params.type}, {params.when} </main> ); }
```

Tenga en cuenta que `searchParams` es asincrónico, por lo que su acceso debe ser esperado con `await` y el componente debe declararse como asincrónico.

La anotación de tipo para `searchParams` es un poco compleja, así que vamos a desglosarla:
- `[key: string]` es una firma de índice (*index signature*) que representa cualquier nombre de propiedad. Esto se debe a que se puede agregar cualquier parámetro de búsqueda a la URL aunque nuestros componentes solo usen `type` y `when`.
- La unión que sigue a la firma de índice representa todos los tipos que pueden tener los parámetros de búsqueda. Nuevamente, no podemos controlar completamente lo que un usuario pone en la URL, por lo que esto representa lo que podría suceder.
- El tipo se envuelve en el tipo `Promise` porque `searchParams` es asincrónico.

Si desea utilizar un parámetro de búsqueda en un nivel inferior del árbol de componentes, Next.js tiene un hook `useSearchParams` que se puede utilizar. Esto solo está disponible en Client Components y no en RSCs. Aquí hay un ejemplo de acceso a los parámetros `type` y `when`:

```tsx
‘use client’; import { useSearchParams } from ‘next/navigation’; export function SomeComponent() { const searchParams = useSearchParams(); const type = searchParams.get(‘type’); const when = searchParams.get(‘when’); ... }
```

Por lo tanto, `useSearchParams` es un poco diferente a `useParams` para parámetros de ruta. Devuelve una interfaz estándar de JavaScript `URLSearchParams`, que proporciona funciones para acceder a los parámetros de búsqueda. Una función `get` en la interfaz permite el acceso a un valor de parámetro de búsqueda en particular. Para obtener más información sobre `URLSearchParams`, consulte el siguiente enlace: [https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams).

A continuación, agregaremos funcionalidad de búsqueda a nuestra aplicación.

#### Añadir funcionalidad de búsqueda a la aplicación

Agregaremos un input de búsqueda al encabezado de la aplicación. Al enviar una búsqueda, el usuario accederá a la página `Posts` con un conjunto filtrado de publicaciones de blog que coincidan con los criterios de búsqueda. Lleve a cabo los siguientes pasos:

1. Primero, abra `src/components/Header.tsx` y agregue un formulario de búsqueda como se muestra aquí:

```tsx
import Form from ‘next/form’; export function Header() { ... return ( <header> <Link ... >Home</Link> <Link ... >Posts</Link> <Form action=”/posts”> <input type=”search” name=”criteria” placeholder=”Search” aria-label=”Search blog posts” /> </Form> </header> ); }
```

`Form` es un componente de Next.js que extiende el elemento `form` HTML nativo. Permite enviar el formulario sin una recarga completa de la página.

El atributo `action` en `Form` provocará una navegación a la ruta `posts`. La navegación incluirá un parámetro de búsqueda `criteria` con el valor del input `criteria`.

2. Ejecute la aplicación ejecutando `npm run dev` en una terminal si no se está ejecutando.
3. Vaya a la aplicación en el navegador, ingrese algunos criterios en el input de búsqueda en el encabezado y presione Enter. Se produce la navegación a la página `Posts` con un parámetro de búsqueda `criteria`. La lista de publicaciones del blog aún no está filtrada; comenzaremos a implementar esto en el siguiente paso.
4. Ahora abra `src/app/posts/page.tsx`. Agregue una prop `searchParams` al componente `Posts` y haga que el componente sea asincrónico de la siguiente manera:

```tsx
export default async function Posts({ searchParams, }: { searchParams: Promise<{ [key: string]: string | string[] | undefined; }>; }) { ... }
```

5. Dentro del componente `Posts`, filtre las publicaciones del blog utilizando los criterios de búsqueda:

```tsx
export default async function Posts( ... ) { const criteria = (await searchParams).criteria; const resolvedPosts = typeof criteria === ‘string’ ? posts.filter((post) => post.title .toLowerCase() .includes(criteria.toLowerCase()), ) : posts; return ... }
```

Si se define un criterio de búsqueda, se produce un filtro que no distingue entre mayúsculas y minúsculas (*case-insensitive*). De lo contrario, se utilizan las publicaciones sin filtrar.

6. También incluyamos los criterios de búsqueda en el encabezado si están definidos. Crearemos una variable para contener el encabezado:

```tsx
export default async function Posts( ... ) { const criteria = ... const resolvedPosts = ... const resolvedHeading = typeof criteria === ‘string’ ? `Posts for ${criteria}` : ‘Posts’; return ... }
```

7. Finalmente, podemos usar estas variables en el JSX de la siguiente manera:

```tsx
export default async function Posts( ... ) { ... return ( <main> <h2>{resolvedHeading}</h2> <ul> {resolvedPosts.map((post) => ( ... ))} </ul> </main> ); }
```

8. En la aplicación en ejecución, si hacemos una búsqueda, la lista de publicaciones de blog se filtra:

*Figura 6.11 – Lista de publicaciones de blog filtrada*

Eso completa la aplicación. Detenga la ejecución de la aplicación con `Ctrl + C` en la terminal.

Eso completa esta sección sobre parámetros de búsqueda. En resumen, en Next.js se accede a los parámetros de búsqueda mediante una prop `searchParams` o el hook `useSearchParams`.

> [!NOTE]
> Para obtener más información sobre los parámetros de búsqueda, consulte la siguiente página en la documentación de Next.js: [https://nextjs.org/docs/app/api-reference/functions/use-search-params](https://nextjs.org/docs/app/api-reference/functions/use-search-params).

A continuación, resumiremos lo que hemos aprendido en este capítulo.

---

### Resumen

Next.js nos ofrece un conjunto completo de funciones para crear aplicaciones de varias páginas. Las diferentes rutas se definen mediante carpetas y un archivo especial `page.tsx`. Las rutas dinámicas se definen mediante corchetes que contienen el parámetro de la ruta. Creamos una ruta estática para una lista de publicaciones de blog y una ruta dinámica para cada publicación de blog.

Un componente de layout compartido se define en `layout.tsx` en la carpeta correspondiente. Usamos el layout raíz para compartir un componente `Header`.

El componente `Link` es la forma recomendada de navegar en Next.js y se puede utilizar en RSCs así como en Client Components. Usamos esto en la lista de publicaciones del blog, así como en el encabezado de la aplicación. El hook `useRouter` permite la navegación programática en Client Components.

Se puede acceder a los parámetros de ruta y búsqueda a través de las props `params` y `searchParams`, respectivamente. También se puede acceder a ellos en Client Components a través de los hooks `useParams` y `useSearchParams`. Usamos un parámetro de ruta `id` en la ruta dinámica de la publicación del blog. También usamos un parámetro de búsqueda `criteria` en la ruta de la lista de publicaciones del blog.

El conocimiento que ha aprendido en este capítulo le ha brindado las habilidades necesarias para escribir aplicaciones con varias páginas.

En el próximo capítulo, mejoraremos la aplicación que creamos en este capítulo para usar datos de una base de datos, a medida que aprendemos cómo React puede interactuar con los datos del servidor.

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido en este capítulo:

1. En el App Router de Next.js, ¿qué archivo define el layout de un segmento de ruta que persiste en varias páginas?
2. Al navegar a la URL `/home` en una aplicación Next.js, se devuelve un mensaje de error 404. Estos son los contenidos de la carpeta `src/app`:

```text
app/ ├── globals.css ├── layout.tsx ├── page.tsx └── home.tsx
```

¿Cuál podría ser el problema?

3. ¿Se puede utilizar un componente `Link` de Next.js en un RSC?
4. En Next.js, es necesario colocar un componente `Header` en cada página. ¿Qué enfoque recomendaría?
5. Al navegar a la URL `/customers/10` en una aplicación Next.js, se devuelve un mensaje de error 404. Estos son los contenidos de la carpeta `src/app/customers`:

```text
app/ ├── customers └── id └── page.tsx
```

¿Cuál es el problema?

---

### Respuestas

1. `layout.tsx`.
2. La ruta `/home` renderizará el archivo `app/home/page.tsx`, no `app/home.tsx`.
3. Sí, se puede.
4. `Header` se puede agregar al layout raíz (`RootLayout`) de la siguiente manera:

```tsx
export default function RootLayout( ... ) { return ( <html lang=”en”> <body ... > <Header /> {children} </body> </html> ); }
```

5. El nombre de la carpeta `id` debe tener corchetes para formar una ruta dinámica (es decir, `[id]`).
