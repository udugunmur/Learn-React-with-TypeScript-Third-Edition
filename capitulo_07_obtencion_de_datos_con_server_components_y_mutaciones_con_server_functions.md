# Parte 3: Datos

## Capítulo 7: Obtención de datos con Server Components y mutaciones con Server Functions

En este capítulo, aprenderemos cómo React puede obtener datos del servidor utilizando un **Server Component** y analizaremos los beneficios de hacerlo. Mejoraremos la aplicación de publicaciones de blog que creamos en el capítulo anterior para utilizar datos de una base de datos y hacer que las interacciones con los datos sean realistas. Implementaremos indicadores de carga y manejadores de errores a lo largo del camino para garantizar una excelente experiencia de usuario.

Aprenderemos sobre las **React Server Functions** para mutar datos del servidor y utilizaremos este conocimiento para crear nuevos datos de publicaciones de blog en nuestra aplicación. Nuevamente, aseguraremos una excelente experiencia de usuario implementando un indicador de mutación y manejando errores.

Al final de este capítulo, tendrá las habilidades para implementar páginas web con tiempos de carga rápidos y el conocimiento para crear acciones en esas páginas de una manera simple y robusta.

Cubriremos los siguientes temas:

- Comprensión de la obtención de datos en servidor y cliente
- Preparación del entorno
- Obtención de datos usando un Server Component
- Adición de indicadores de carga usando React Suspense
- Manejo de errores con boundaries de error de React
- Mutación de datos usando una Server Function

---

### Requisitos técnicos

En este capítulo utilizaremos las siguientes tecnologías:

- **Navegador**: Un navegador moderno como Google Chrome
- **Node.js y npm**: Puede instalarlos desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
- **Visual Studio Code**: Puede instalarlo desde [https://code.visualstudio.com/](https://code.visualstudio.com/)

Todos los fragmentos de código utilizados en este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter07](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter07)

---

### Comprensión de la obtención de datos en servidor y cliente

Antes de implementar la obtención de datos en nuestra aplicación, aprenderemos sobre las diferencias entre la obtención de datos del lado del cliente (*client-side data fetching*) y del lado del servidor (*server-side data fetching*), así como los beneficios de cada enfoque.

#### Obtención de datos del lado del cliente

La obtención de datos del lado del cliente ocurre después de que un Client Component se renderiza inicialmente. Abordamos el proceso de obtención de datos del lado del cliente en el [Capítulo 5](https://subscription.packtpub.com/book/web-development/9781836643173/5), *Uso de React Server y Client Components*. Como repaso, aquí hay una visualización de los pasos involucrados:

*Figura 7.1 – Pasos de la obtención de datos del lado del cliente*

Estos son los puntos clave:
- Suceden muchas cosas antes de que se obtengan los datos: el HTML, JavaScript y CSS se descargan del servidor, el JavaScript se analiza y se realiza una renderización inicial de los componentes de React.
- Se realizan muchas solicitudes HTTP: una para obtener el HTML, una para el JavaScript, una para el CSS y otra para realizar la consulta a la base de datos. Si varios componentes obtienen datos, se producen más solicitudes HTTP; a esto a menudo se le llama una **cascada de red (network waterfall)**.
- Los componentes de React deben volver a renderizarse antes de que el usuario vea los datos.
- Los datos se pueden actualizar en un momento posterior mediante una solicitud HTTP desde el cliente.

#### Obtención de datos del lado del servidor

La obtención de datos del lado del servidor ocurre cuando se ejecuta un React Server Component (RSC) en el servidor. Nuevamente, abordamos este proceso en el [Capítulo 5](https://subscription.packtpub.com/book/web-development/9781836643173/5), *Uso de React Server y Client Components*. Como repaso, aquí hay una visualización de los pasos involucrados:

*Figura 7.2 – Pasos de la obtención de datos del lado del servidor*

Estos son los puntos clave:
- La consulta a la base de datos ocurre temprano en el proceso mientras el RSC se ejecuta en el servidor.
- Los RSCs generalmente dan como resultado que se descargue menos JavaScript que con los Client Components. Esto no solo significa que el JavaScript se descarga más rápido, sino también que se analiza más rápido.
- Se producen menos solicitudes HTTP. Si varios RSCs obtienen datos, todo esto sucede en el servidor en una sola solicitud HTTP. Por ejemplo, en el siguiente RSC `Home`, se obtiene un usuario de la base de datos y luego se obtienen las tareas del usuario en el RSC secundario `TaskList`. Por lo tanto, ambas consultas a la base de datos ocurren en la misma solicitud HTTP:

```tsx
async function Home() { const user = await fetchUser(); return ( <main> <h2>Welcome, {user.name}</h2> <TaskList userId={user.id} /> </main> ); } async function TaskList({ userId }: ...) { const tasks = await fetchTasks(userId); return <ul> ... </ul>; }
```

- Los componentes de React pueden mostrar datos en su renderizado inicial.
- Los datos no se pueden actualizar en un momento posterior en un RSC sin una recarga completa de la página.

> [!NOTE]
> Vale la pena señalar que los datos podrían obtenerse de otro servidor en lugar de una consulta directa a la base de datos, tanto para la obtención de datos del lado del cliente como del servidor.

#### Comprender los beneficios

Tanto la obtención de datos del lado del servidor como del cliente tienen beneficios y desventajas.

Estos son los **beneficios de la obtención de datos del lado del servidor**:
- El usuario verá los datos en la página más rápido que con la obtención de datos del lado del cliente.
- Los RSCs tienen acceso a las cookies, lo que permite verificaciones de autenticación y autorización basadas en cookies. Los Client Components no tienen acceso a las cookies, por lo que será necesario adoptar un enfoque alternativo de autenticación y autorización.

Estos son los **beneficios de la obtención de datos del lado del cliente**:
- Los datos se pueden actualizar sin una carga completa de la página. Un caso de uso típico donde se requiere la actualización de datos es un panel de control (*dashboard*) que necesita mostrar datos actualizados.
- La paginación de datos se puede realizar sin una carga completa de la página.
- El desplazamiento infinito (*infinite scroll*) solo se puede realizar en un Client Component. Aquí es donde se obtienen datos adicionales a medida que el usuario se desplaza hacia abajo en una lista.
- La composición y reutilización de componentes es más fácil en un Client Component. Esto se debe a que los Client Components pueden obtener sus propios datos, lo que reduce la necesidad de que los datos se pasen a través de props. Como resultado, los desarrolladores pueden componer más fácilmente componentes de alto nivel a partir de componentes de bajo nivel sin acoplarlos estrechamente a una jerarquía específica de obtención de datos. Por el contrario, cuando se utilizan RSCs para obtener datos, los datos se pasan a un Client Component a través de props. Esto introduce un flujo de datos rígido de arriba hacia abajo, donde los RSCs de alto nivel deben conocer los requisitos de datos específicos de sus hijos anidados. Esto rompe la encapsulación y dificulta la reutilización de componentes en diferentes contextos, ya que sus dependencias de datos ya no son internas.
- La obtención de datos del lado del cliente se puede utilizar con cualquier framework de servidor web: no tiene por qué admitir RSCs ni estar basado en JavaScript.

Esto concluye esta sección sobre cómo funciona la obtención de datos del lado del cliente y del servidor. En resumen, la obtención de datos del lado del cliente ocurre después del renderizado inicial e involucra múltiples solicitudes HTTP, lo que resulta en una experiencia de usuario más lenta debido al re-renderizado. La obtención de datos del lado del servidor es más eficiente, muestra datos durante el renderizado inicial y utiliza menos solicitudes HTTP, pero no permite la actualización de datos sin una recarga completa de la página.

A continuación, configuraremos el proyecto para nuestra aplicación y luego implementaremos una obtención de datos del lado del servidor.

---

### Preparación del entorno

En esta sección, prepararemos el código para la aplicación en la que trabajaremos en este capítulo, que es la aplicación de blog del último capítulo. También conectaremos la aplicación a una base de datos SQLite.

#### Creación del proyecto

Lleve a cabo los siguientes pasos para configurar el proyecto. Es el proyecto con el que terminamos en el último capítulo, más algunos estilos adicionales necesarios para este capítulo:

1. En una terminal, ejecute el siguiente comando para crear el proyecto:

```bash
npx create-next-app@latest blog --ts --eslint --app --src-dir --import-alias “@/*” --no-tailwind --turbopack
```

2. Aún en la terminal, muévase a la carpeta del proyecto y abra Visual Studio Code usando los siguientes comandos:

```bash
cd blog code .
```

Prettier se puede configurar de la misma manera que Vite, como cubrimos en el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*. Siéntase libre de agregar el formato de código automático a este proyecto.

3. Copie los siguientes archivos de la carpeta `src` en el repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter07/start/src](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter07/start/src). Agréguelos al proyecto en la misma estructura de carpetas, reemplazando los archivos existentes:
   - `app/layout.tsx`
   - `app/page.tsx`
   - `app/posts/page.tsx`
   - `app/posts/[id]/page.tsx`
   - `app/globals.css`
   - `components/Header.tsx`
   - `data/posts.ts`

El proyecto ahora está como estaba al final del último capítulo, con algunos estilos adicionales.

#### Configuración de la base de datos

SQLite es un motor de base de datos basado en SQL que es fácil de configurar. Usaremos una bifurcación (*fork*) popular de SQLite llamada **libSQL** que funciona muy bien con Next.js. La dependencia para libSQL ya está instalada.

> [!NOTE]
> Se puede encontrar más información sobre libSQL en [https://turso.tech/libsql](https://turso.tech/libsql).

Lleve a cabo los siguientes pasos para crear nuestra base de datos de blog y ver los datos:

1. Instale el cliente de SQLite ejecutando lo siguiente en una terminal:

```bash
npm i @libsql/client
```

2. Cree un script que ejecutaremos para crear nuestra base de datos. Cree una carpeta llamada `scripts` en la carpeta `src` y luego un archivo llamado `createDatabase.mjs` en esta carpeta. Copie el script del repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter07/start/src/scripts/createDatabase.mjs](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter07/start/src/scripts/createDatabase.mjs) y péguelo en el archivo.
3. Para crear la base de datos, ejecute el siguiente comando en la terminal:

```bash
node src/scripts/createDatabase.mjs
```

Este comando ejecuta el script usando Node.js. Una vez completado el comando, aparecerá un archivo de base de datos `blog.db` en la carpeta `data`.

4. Los datos dentro de `blog.db` se pueden ver utilizando una extensión de Visual Studio Code llamada **SQLite3 Editor**.

*Figura 7.3 – Extensión SQLite3 Editor*

Esta extensión también nos permite editar datos, lo que nos resultará útil más adelante en el capítulo. Instale esta extensión y luego podrá hacer clic en el archivo en el panel del Explorador para ver los datos.

*Figura 7.4 – Datos del blog en SQLite3 Editor*

¡Excelente! Eso completa esta sección sobre la configuración del proyecto para este capítulo. He aquí un resumen rápido:
- Creamos un proyecto de Next.js utilizando la herramienta `create-next-app` y copiamos el código de la aplicación del último capítulo con algunos estilos adicionales.
- Creamos una base de datos SQLite para los datos de las publicaciones del blog e instalamos una biblioteca que eventualmente usaremos para conectarnos a ella.

A continuación, implementaremos código de obtención de datos en un RSC.

---

### Obtención de datos usando un Server Component

En esta sección, implementaremos la obtención de datos en los RSCs de la lista de publicaciones de blog y del detalle de la publicación de blog para obtener los datos de nuestra nueva base de datos. Estructuraremos el código que interactúa con la base de datos en funciones separadas; las llamaremos **funciones de consulta (query functions)**.

#### Implementación de funciones de consulta

Primero, implementaremos funciones para conectarnos a la base de datos y consultarla para obtener los datos necesarios. Siga estos pasos:

1. Comenzaremos almacenando la ruta a la base de datos en una variable de entorno. Esta es una práctica común porque permite que la aplicación en diferentes entornos se conecte a diferentes bases de datos. Cree un archivo llamado `.env` en la raíz del proyecto con el siguiente contenido:

```env
DB_URL=file:src/data/blog.db
```

2. Cree un archivo llamado `queries.ts` en la carpeta `src/data`. Agregue el siguiente contenido a `queries.ts` para importar una función de libSQL que nos permitirá conectarnos a la base de datos:

```typescript
import { createClient } from ‘@libsql/client’;
```

3. Cree un tipo para los datos de la publicación de la siguiente manera:

```typescript
type Post = { id: number; title: string; description: string; };
```

Usaremos esto para tipar fuertemente los datos devueltos por las consultas de funciones en los pasos posteriores.

4. Cree una función para obtener todas las publicaciones de blog de la siguiente manera:

```typescript
export async function getAllPosts() { const client = createClient({ url: process.env.DB_URL ?? ‘’, }); const data = await client.execute( ‘SELECT id, title, description FROM posts’, ); client.close(); return data.rows as unknown as Post[]; }
```

Primero, usamos la función `createClient` para conectarnos a la base de datos. Se accede a la variable de entorno URL de la base de datos a través del objeto `process.env`, que es la forma estándar de acceder a las variables de entorno en Next.js. La función devuelve un objeto que contiene una función `execute`, que permite consultar la base de datos.

Usamos la función `execute` para seleccionar los campos `id`, `title` y `description` de la tabla `posts`, que es donde están los datos de nuestras publicaciones de blog.

Luego cerramos la conexión a la base de datos y devolvemos las filas devueltas en la consulta.

Observe que se utiliza una aserción de tipo (*type assertion*) en la sentencia `return` para tipar fuertemente los datos devueltos. Esto es un poco desordenado porque necesitamos asertar a `unknown` antes de asertar a `Post[]`. Limpiaremos esto más adelante en este capítulo, pero mantendremos esto por ahora.

5. Implemente una función estructurada de manera similar para obtener publicaciones de blog filtradas:

```typescript
export async function getFilteredPosts( criteria: string, ) { const client = createClient({ url: process.env.DB_URL ?? ‘’, }); const data = await client.execute({ sql: ‘SELECT id, title, description FROM posts WHERE title LIKE ?’, args: [`%${criteria}%`], }); client.close(); return data.rows as unknown as Post[]; }
```

La principal diferencia esta vez es que pasamos una sentencia SQL y un parámetro SQL a la función `execute` del cliente de base de datos. La sentencia SQL contiene una cláusula `WHERE` que filtra los datos usando el campo `title` y un parámetro (`?` es un parámetro en SQLite). El valor del parámetro SQL se pasa a través de la propiedad `args` en un array. Establecemos el valor del parámetro SQL en el parámetro `criteria` que se pasó a la función dentro de los símbolos `%`, que son comodines (*wildcards*) en SQLite. Los comodines y el operador SQL `LIKE` significan que la consulta devolverá filas con criterios coincidentes dentro de `title`. Vale la pena señalar que SQLite no distingue entre mayúsculas y minúsculas, por lo que el caso de los criterios no necesita coincidir con el campo del título.

6. La última función de consulta es para obtener una sola publicación de blog:

```typescript
export async function getPost(id: number) { const client = createClient({ url: process.env.DB_URL ?? ‘’, }); const data = await client.execute({ sql: ‘SELECT id, title, description FROM posts WHERE id = ?’, args: [id], }); client.close(); if (data.rows.length === 0) { return undefined; } return data.rows[0] as unknown as Post; }
```

Esta vez, pasamos el `id` de la publicación del blog como un parámetro SQL. Si no se encuentran filas, devolvemos `undefined`; de lo contrario, devolvemos la primera fila encontrada.

Con esto quedan implementadas las funciones de consulta. A continuación, llamaremos a estas funciones en los RSCs pertinentes.

#### Llamada a funciones de consulta desde RSCs

Ahora llamaremos a las funciones de consulta que acabamos de implementar en los RSCs de la lista de publicaciones de blog y del detalle de la publicación de blog. Siga estos pasos:

1. Comience abriendo `src/app/posts/page.tsx` y reemplazando la declaración de importación de `posts` con una declaración de importación para importar las funciones de consulta requeridas:

```typescript
import Link from ‘next/link’; import { getAllPosts, getFilteredPosts, } from ‘@/data/queries’;
```

2. Dentro del componente `Posts`, actualice la asignación de `resolvedPosts` para llamar a las funciones de consulta de la siguiente manera:

```typescript
const resolvedPosts = typeof criteria === ‘string’ ? await getFilteredPosts(criteria) : await getAllPosts(); const resolvedHeading = typeof criteria === ‘string’ ? `Posts for ${criteria}` : ‘Posts’; 
```

3. Ahora, abra `src/app/posts/[id]/page.tsx` y realice cambios similares. Comience reemplazando la declaración de importación existente con una declaración de importación para `getPost`:

```typescript
import { getPost } from ‘@/data/queries’;
```

4. A continuación, actualice la asignación de la variable `post` con una llamada a `getPost`:

```typescript
const post = await getPost(id); 
```

5. Ejecute la aplicación ejecutando `npm run dev` en la terminal. La aplicación se comportará como en el último capítulo, pero ahora utiliza una base de datos real. Intente ir a la página de la lista de publicaciones, realizar una búsqueda y luego ir a una publicación de blog en particular para asegurarse de que todo funcione.
6. Detenga la ejecución de la aplicación antes de continuar presionando `Ctrl + C`.

Ahora hemos implementado la obtención de datos de una base de datos en dos RSCs. A continuación, limpiaremos el tipado de TypeScript de los datos de la función de consulta.

#### Adición de seguridad de tipos a una consulta de base de datos

Por el momento, confiamos en que los datos de las consultas de la base de datos sean de tipo `Post[]` para las funciones `getAllPosts` y `getFilteredPosts`, y también de tipo `Post` para la función `getPost`. Sabemos que este es el caso en nuestro ejemplo, pero en el mundo real, los esquemas de bases de datos pueden cambiar sin que el código de conexión se actualice en consecuencia. Esto puede suceder cuando diferentes equipos son propietarios de la base de datos y del código, y un cambio en la base de datos no se comunica adecuadamente.

Si el tipo que representa los datos de la consulta es incorrecto, el código que lo consume no funcionará como se esperaba y puede provocar un error inesperado en tiempo de ejecución. En nuestra aplicación, esto provocaría que la lista de publicaciones y las páginas de detalles no se rendericen correctamente. En aplicaciones más grandes, este tipo de errores pueden ser difíciles de localizar y llevar un tiempo corregirlos.

Para proteger el código contra cambios inesperados en la base de datos, el tipo de datos se puede verificar en tiempo de ejecución para ver si es el esperado. Una biblioteca popular llamada **Zod** puede realizar comprobaciones de validación de esquemas de forma elegante. Si las comprobaciones fallan, se lanza un error. Puede que esto no parezca ideal, pero aporta claridad sobre dónde está el problema, lo que ayuda a garantizar una resolución rápida.

Lleve a cabo los siguientes pasos para agregar validación de esquemas con Zod a nuestras consultas de base de datos:

1. Primero, instale Zod ejecutando el siguiente comando en una terminal:

```bash
npm i zod
```

2. Cree un nuevo archivo en la carpeta `src/data` llamado `schema.ts`. Este contendrá los esquemas de Zod para las consultas a la base de datos. Agregue el siguiente contenido a `schema.ts`:

```typescript
import { z } from ‘zod’; export const postSchema = z.object({ id: z.number(), title: z.string(), description: z.string(), }); export const postsSchema = z.array(postSchema);
```

Hemos definido dos esquemas:
- El primero, `postSchema`, representa una sola publicación de blog. El esquema especifica un objeto con una propiedad numérica `id` y propiedades de cadena `title` y `description`.
- El segundo, `postsSchema`, representa múltiples publicaciones de blog. Se basa en `postSchema`, especificando simplemente que es un array de `postSchema`.

Ambos esquemas se exportan para que podamos usarlos en nuestras consultas de funciones.

3. Abra `queries.ts` y elimine el tipo `Post` existente, ya que será redundante después de los pasos posteriores.
4. Importe los esquemas en `queries.ts`:

```typescript
import { postsSchema, postSchema } from ‘./schema’;
```

5. En las funciones de consulta, valide los datos de la consulta utilizando los esquemas recién importados, eliminando las aserciones de tipo:

```typescript
export async function getAllPosts() { ... return postsSchema.parse(data.rows); } export async function getFilteredPosts( ... ) { ... return postsSchema.parse(data.rows); } export async function getPost( ... ) { ... return postSchema.parse(data.rows[0]); }
```

La función `parse` en los esquemas de Zod realiza la verificación de validación. Se genera un error si la comprobación falla. Si la verificación es exitosa, los datos pasados a `parse` se devuelven y se tipan según el esquema.

6. Abra `src/app/posts/page.tsx` y pase el cursor sobre la variable `resolvedPosts` donde se llaman dos de las funciones de consulta en su asignación. Verá que se ha tipado correctamente a partir del esquema de Zod.

*Figura 7.5 – Variable resolvedPosts correctamente tipada*

7. Ejecute la aplicación ejecutando `npm run dev` en la terminal. La aplicación se comportará como antes.
8. Para ver la validación del esquema de consulta en acción, abra `queries.ts` y convierta temporalmente `id` a texto en la sentencia SQL de la función `getAllPosts`. Esto simula un cambio en la base de datos potencialmente destructivo:

```typescript
export async function getAllPosts() { ... const data = await client.execute( ‘SELECT CAST(id as text) as id, ...’, ); ... }
```

9. Visite la página de la lista de publicaciones en la aplicación en ejecución y verá un error en tiempo de ejecución creado por Zod:

*Figura 7.6 – Error de Zod en tiempo de ejecución*

El mensaje de error es muy claro y nos indica que el campo `id` en cada fila es una cadena en lugar del número esperado. Esto es muy útil para los desarrolladores pero no para los usuarios; mejoraremos la experiencia del error del usuario más adelante en este capítulo.

10. Revierta el cambio en `getAllPosts` antes de continuar para que se resuelva el error.

Eso completa la mejora del tipado de los datos de la consulta. Se puede encontrar más información sobre Zod en [https://zod.dev/](https://zod.dev/).

He aquí un resumen rápido de esta sección:
- Los datos se pueden obtener en un RSC simplemente llamando a la consulta en el cuerpo de la función del RSC antes de la sentencia `return`. La consulta se puede esperar con `await`, y el RSC se puede marcar como `async` si la consulta es asincrónica.
- Zod se puede utilizar para garantizar que los datos de las consultas de bases de datos sean seguros en cuanto a tipos.

A continuación, mejoraremos la experiencia de usuario de obtención de datos añadiendo un indicador de carga.

---

### Adición de indicadores de carga usando React Suspense

En esta sección, aprenderemos sobre **React Suspense** y lo usaremos para implementar un indicador de carga en los RSCs que obtienen datos en nuestra aplicación. Esto mejorará la experiencia de usuario de carga.

#### Comprender la necesidad de indicadores de carga

Actualmente, la experiencia de usuario de obtención de datos en nuestra aplicación es razonable porque el proceso es rápido. Esto se debe a que todo se ejecuta localmente y, por lo tanto, la latencia es baja. Esto también se debe a que la base de datos es pequeña y las consultas son simples, por lo que se ejecutan rápidamente. Por último, somos el único usuario que utiliza la aplicación.

Cuando las aplicaciones se ejecutan en servidores reales con bases de datos más grandes y complejas, las consultas serán un poco más lentas, especialmente cuando muchos usuarios utilizan la aplicación.

Los indicadores de carga permiten al usuario saber que la aplicación está cargando la página y evitan que la aplicación se sienta lenta.

#### Adición de un retraso

Antes de implementar un indicador de carga, simularemos un retraso más significativo en la obtención de datos. Esto nos permitirá sentir la diferencia que hacen los indicadores de carga. Lleve a cabo los siguientes pasos:

1. Abra `queries.ts` y agregue una función `delay` de la siguiente manera:

```typescript
async function delay() { await new Promise((resolve) => setTimeout(resolve, 1000), ); }
```

El retraso es de 1 segundo, que es más largo de lo que esperaríamos que tomara la ejecución típica de una consulta. Sin embargo, esto nos permite experimentar cómo sería una carga lenta.

2. Agregue una llamada a `delay` en la parte superior de las tres consultas:

```typescript
export async function getAllPosts() { await delay(); ... } export async function getFilteredPosts( ... ) { await delay(); ... } export async function getPost(id: number) { await delay(); ... }
```

3. Intente navegar a diferentes páginas en la aplicación. Hacer clic en enlaces que navegan a páginas que contienen datos se sentirá lento.

Eventualmente mejoraremos la experiencia para que la página se cargue inmediatamente con un indicador de carga que informe al usuario que parte del contenido aún se está cargando. Antes de esto, aprenderemos sobre una característica crítica de React necesaria para hacer esto.

#### Comprender React Suspense

**React Suspense** permite que los componentes esperen tareas asincrónicas durante el renderizado. Una tarea asincrónica común para la que se utiliza Suspense es la obtención de datos. Permite suspender la representación de algunos elementos JSX mientras se obtienen los datos, lo que permite que otros elementos se representen normalmente. Además, se puede especificar un **fallback (alternativa)** para los elementos suspendidos, que puede ser un indicador de carga.

El fallback de Suspense se puede mostrar al usuario antes de los elementos suspendidos porque los RSCs que contienen elementos suspendidos se transmiten en flujo (*streamed*) al navegador. Por lo tanto, el fallback de Suspense se enviará al navegador en el primer fragmento (*chunk*), y los elementos suspendidos seguirán cuando estén listos. Los fragmentos del RSC se agregan al DOM y se muestran al usuario a medida que se descargan en el navegador.

En el siguiente ejemplo, el RSC `Page` mostrará inmediatamente un mensaje `Loading ...`. Después de que se haya obtenido el nombre, el indicador de carga se reemplazará con un mensaje `Hello, {fetched name}`:

```tsx
export default function Page() { return ( <Suspense fallback={<div>Loading...</div>}> <Name /> </Suspense> ); } async function Name() { const name = await getName(); return <div>Hello, {name}</div>; }
```

El componente `Suspense` es de React, y forma un **límite de Suspense (Suspense boundary)**. Se suspende la renderización de sus hijos hasta que se completen sus tareas asincrónicas. Su atributo `fallback` permite renderizar un componente alternativo mientras se ejecutan las tareas asincrónicas de los hijos suspendidos.

> [!NOTE]
> Next.js tiene una convención de indicador de carga incorporada que utiliza React Suspense internamente. Sin embargo, todo el componente de la página se reemplaza con el componente de carga. Se puede encontrar más información en [https://nextjs.org/docs/app/api-reference/file-conventions/loading](https://nextjs.org/docs/app/api-reference/file-conventions/loading).

A continuación, agregaremos indicadores de carga en las páginas de lista y detalle de publicaciones de blog.

#### Implementación de indicadores de carga

Usaremos React Suspense para agregar indicadores de carga a partes de los componentes de la página para la lista y los detalles de las publicaciones del blog. Extraeremos las partes asincrónicas de los componentes en componentes secundarios para que puedan envolverse con un componente `Suspense`. Lleve a cabo los siguientes pasos:

1. Comience creando un componente indicador de carga que se pueda reutilizar en ambos componentes de la página. Cree un archivo llamado `Loading.tsx` en `src/components` con el siguiente contenido:

```tsx
export function Loading() { return ( <div className=”skeleton”> <div className=”skeleton-item-title”></div> <div className=”skeleton-item-desc”></div> </div> ); }
```

El componente renderiza elementos `div` que representan visualmente marcadores de posición (*skeletons*) para el título y la descripción de la publicación del blog. El estilo referenciado se puede encontrar en `globals.css`.

2. Extraeremos la obtención de datos y el renderizado de la lista de publicaciones de blog de `Posts` a un componente `PostList`. Comience agregando un nuevo archivo llamado `PostList.tsx` a la carpeta `src/components`. Copie el contenido en este archivo desde el repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter07/fetching-rsc/src/components/PostList.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter07/fetching-rsc/src/components/PostList.tsx). Aquí hay un extracto de este código:

```tsx
export async function PostList({ criteria }: ...) { const resolvedPosts = ... return ( <ul> {resolvedPosts.map((post) => ( <li key={post.id}> <Link href={`/posts/${post.id}`}> {post.title} </Link> <p>{post.description}</p> </li> ))} </ul> ); }
```

3. Abra `src/app/posts/page.tsx` y elimine las importaciones de `Link` y de consultas. Agregue importaciones para los componentes `Suspense`, `Loading` y `PostList` de la siguiente manera:

```typescript
import { Suspense } from ‘react’; import { Loading } from ‘@/components/Loading’; import { PostList } from ‘@/components/PostList’;
```

4. Elimine la variable `resolvedPosts` en el componente `Posts`.
5. Reemplace los elementos `ul` y `li` en el JSX con `PostList` dentro de un componente `Suspense` de la siguiente manera:

```tsx
export default async function Posts( ... ) { const criteria = ... const resolvedHeading = ... return ( <main> <h2>{resolvedHeading}</h2> <Suspense fallback={<Loading />}> <PostList criteria={criteria} /> </Suspense> </main> ); }
```

Eso completa la reelaboración del componente `Posts`.

6. Abra `src/app/posts/[id]/page.tsx`. Extraeremos la obtención de datos y el renderizado de `Post` en un componente `PostDetail`. Comience agregando un nuevo archivo en `src/components/PostDetail.tsx` con el siguiente contenido:

```tsx
import { getPost } from ‘@/data/queries’; export async function PostDetail({ id, }: { id: number; }) { const post = await getPost(id); if (!post) { return <p>Post not found</p>; } return ( <> <h2>{post.title}</h2> <p>{post.description}</p> </> ); }
```

7. Abra `src/app/posts/[id]/page.tsx` y elimine la declaración de importación de `getPost`. Agregue importaciones para los componentes `Suspense`, `Loading` y `PostDetail` de la siguiente manera:

```typescript
import { Suspense } from ‘react’; import { Loading } from ‘@/components/Loading’; import { PostDetail } from ‘@/components/PostDetail’;
```

8. Dentro del componente `Post`, elimine la variable `post` y la cláusula de guarda para el caso de que no se encuentre la publicación. Mantenga la variable `id` en su lugar y su conversión a número. Además, mantenga la cláusula de guarda en su lugar para manejar los casos en que el ID no sea numérico.
9. Reemplace el JSX dentro del elemento `main` con `PostDetail` dentro de un `Suspense` de la siguiente manera:

```tsx
<main> <Suspense fallback={<Loading />}> <PostDetail id={id} /> </Suspense> </main>
```

Eso completa la reelaboración del componente `Post`. El archivo del componente completo está disponible en GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter07/fetching-rsc/src/app/posts/%5Bid%5D/page.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter07/fetching-rsc/src/app/posts/%5Bid%5D/page.tsx).

10. En la aplicación en ejecución, intente hacer clic en enlaces para navegar a diferentes páginas de la aplicación. Las páginas aparecerán inmediatamente con un indicador de carga si es necesario obtener datos:

*Figura 7.7 – Indicador de carga en la página de lista de publicaciones*

11. Detenga la ejecución de la aplicación presionando `Ctrl + C` en la terminal.
12. Elimine los retrasos simulados en las consultas ahora que estamos satisfechos con los indicadores de carga.

Eso completa la implementación de los indicadores de carga. Para obtener más información sobre React Suspense, consulte la página en la documentación de React: [https://react.dev/reference/react/Suspense](https://react.dev/reference/react/Suspense).

He aquí un resumen rápido:
- React Suspense es un componente de React que suspende la renderización de sus hijos mientras se ejecuta una función de consulta de base de datos asincrónica.
- Cuando los hijos están suspendidos, el componente Suspense permite renderizar un componente indicador de carga en su lugar.
- Cuando se ha completado la función de consulta a la base de datos ejecutada, los hijos suspendidos se renderizarán, reemplazando el indicador de carga.
- Este patrón funciona en un RSC porque React Suspense hace que los RSCs se transmitan en flujo (*streamed*) al navegador.

A continuación, aprenderemos cómo mejorar el manejo de errores durante la obtención de datos.

---

### Manejo de errores con boundaries de error de React

En esta sección, aprenderemos sobre el manejo de errores mediante los **React error boundaries**. Con este conocimiento, mejoraremos el manejo de errores en nuestra aplicación.

#### Comprender los React error boundaries

Un **error boundary de React** es un componente que detecta errores en sus componentes hijos durante la renderización. Los boundaries de error de React están disponibles en componentes de clase de React. Para utilizarlos en componentes funcionales, podemos utilizar un paquete llamado `react-error-boundary` que expone un componente `ErrorBoundary`.

Aquí hay un ejemplo de uso del componente `ErrorBoundary`:

```tsx
export function SomeComponent() { return ( <main> <h2>Some heading</h2> <ErrorBoundary FallbackComponent={ErrorAlert} onError={(error, info) => { // Log to error service }} > <SomeChildComponent /> </ErrorBoundary> </main> ); }
```

El atributo `FallbackComponent` permite que un componente de error reemplace a los hijos que no pudieron renderizarse.

El atributo `onError` permite capturar errores, lo que permite registrarlos (*logging*). Vale la pena señalar que este atributo solo se puede utilizar si el componente consumidor es un Client Component y no un RSC porque es un evento.

Aquí está la definición del error fallback del fragmento de código anterior:

```tsx
“use client”; export function ErrorAlert({ error, resetErrorBoundary, }: { error: Error; resetErrorBoundary: () => void; }) { return ( <div role=”alert”> <h3>Something went wrong</h3> <p>{error.message}</p> <button onClick={resetErrorBoundary}>Retry</button> </div> ); }
```

El componente de error fallback debe ser un Client Component. Recibe las siguientes props:
- `error`: Este es el objeto `Error` generado que contiene toda la información sobre el error.
- `resetErrorBoundary`: Esto permite restablecer el estado en el error boundary para volver a intentar el renderizado.

> [!NOTE]
> Next.js tiene una convención de indicador de error boundary incorporada, pero todo el componente de la página se reemplaza con el componente de error. Se puede encontrar más información en [https://nextjs.org/docs/app/api-reference/file-conventions/error](https://nextjs.org/docs/app/api-reference/file-conventions/error).

A continuación, agregaremos boundaries de error en las páginas de lista y detalle de publicaciones de blog.

#### Implementación de error boundaries

Utilizaremos `ErrorBoundary` del paquete `react-error-boundary`. Lo usaremos en las páginas de lista y detalle de publicaciones del blog. Lleve a cabo estos pasos:

1. Instale el paquete `react-error-boundary` ejecutando el siguiente comando en una terminal:

```bash
npm i react-error-boundary
```

2. Comenzaremos agregando un componente de error fallback que muestra el error al usuario. Cree un archivo llamado `ErrorAlert.tsx` en la carpeta `src/components` que contenga lo siguiente:

```tsx
“use client”; export function ErrorAlert({ error, resetErrorBoundary, }: { error: Error; resetErrorBoundary: () => void; }) { return ( <div role=”alert”> <h3>Something went wrong</h3> <p>{error.message}</p> <button onClick={resetErrorBoundary}> Retry </button> </div> ); }
```

El componente muestra el mensaje de error con un botón Retry debajo que restablece el error boundary.

3. Envolveremos el `ErrorBoundary` de `react-error-boundary` para que el fallback y el reporte de errores estén centralizados. Cree un nuevo archivo llamado `ErrorBoundary.tsx` en `src/components` con el siguiente contenido:

```tsx
‘use client’; import type { ReactNode } from ‘react’; import { ErrorBoundary as ReactErrorBoundary, } from ‘react-error-boundary’; import { ErrorAlert } from ‘./ErrorAlert’; export function ErrorBoundary({ children, }: { children: ReactNode; }) { return ( <ReactErrorBoundary FallbackComponent={ErrorAlert} onError={(error, info) => { console.error(‘Unexpected error’, { error, info, }); }} > {children} </ReactErrorBoundary> ); }
```

Llamamos a nuestro componente `ErrorBoundary` y le dimos un alias a `ErrorBoundary` de `react-error-boundary` como `ReactErrorBoundary` para evitar que colisionen.

Hemos especificado `ErrorAlert` como el fallback de error y estamos enviando errores a la consola.

4. Abra `src/app/posts/page.tsx` y envuelva nuestro `ErrorBoundary` alrededor de `PostList`:

```tsx
import { ErrorBoundary } from ‘@/components/ErrorBoundary’; ... export default function Posts( ... ) { ... return ( <main> ... <Suspense ... > <ErrorBoundary> <PostList ... /> </ErrorBoundary> </Suspense> </main> ); }
```

5. Abra `src/app/posts/[id]/page.tsx` y envuelva nuestro `ErrorBoundary` alrededor de `PostDetail`:

```tsx
import { ErrorBoundary } from ‘@/components/ErrorBoundary’; ... export default async function Post( ... ) { ... return ( <main> <Suspense ...> <ErrorBoundary> <PostDetail ... /> </ErrorBoundary> </Suspense> </main> ); }
```

Los error boundaries ya están implementados.

6. Ejecute la aplicación ejecutando `npm run dev` en una terminal, navegue a la página de la lista de publicaciones del blog, abra las herramientas de desarrollo del navegador y vaya al panel **Components**. Podemos forzar a `PostList` a un estado de error buscando el componente en el árbol, seleccionando `PostList` en el árbol y seleccionando la opción que contiene el icono del signo de exclamación.

*Figura 7.8 – Forzar un componente a un estado de error*

7. Luego aparecerá el error, reemplazando la lista de publicaciones del blog:

*Figura 7.9 – Componente PostList en estado de error*

8. Haga clic en el botón Retry para restablecer el estado de error. El componente `PostList` se renderizará con éxito.
9. Repita los dos pasos anteriores para el componente `PostDetail` en la página de detalles de la publicación del blog. Descubrirá que el error boundary también funciona bien allí.

Eso completa los boundaries de error en nuestra aplicación y esta sección. He aquí un resumen rápido:
- Un boundary de error de React es un componente que detecta errores de sus componentes secundarios.
- El componente de boundary de error puede renderizar un componente de alerta de error personalizado cuando se encuentra en un estado de error.
- El componente de boundary de error también puede registrar errores en un servicio de errores, lo que ayuda a la solución de problemas en producción.

> [!NOTE]
> Para obtener más información sobre los boundaries de error de React, consulte la siguiente página en la documentación de React: [https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary).

A continuación, aprenderemos cómo mutar datos.

---

### Mutación de datos usando una Server Function

En esta sección, aprenderemos cómo mutar datos de la base de datos a través de una **React Server Function**. Crearemos un botón en nuestra aplicación para crear una nueva publicación de blog aprovechando una Server Function. También aprenderemos a implementar un indicador de progreso para la Server Function y a manejar cualquier error.

#### Comprender una Server Function

Una **React Server Function** es exactamente lo que su nombre indica: es una función que se ejecuta en el servidor. Un caso de uso común es ejecutar una mutación de base de datos.

Tradicionalmente, una aplicación React llamaría a una API web para ejecutar código en el servidor. Las Server Functions simplifican esta tarea.

Aquí hay un ejemplo de una Server Function utilizada dentro de un RSC. Renderiza un botón que, cuando se hace clic en él, elimina un producto de una base de datos:

```tsx
export function DeleteButton({id}: {id: number}) { async function deleteProduct() { ‘use server’; const client = createClient({ url: process.env.DB_URL ?? ‘’ }); await client.execute({ sql: ‘DELETE FROM products WHERE id = ?’, args: [id], }); client.close(); } return ( <button type=”button” onClick={deleteProduct}> Delete </button> ); }
```

La directiva `'use server'` marca la función `deleteProduct` como una Server Function. Esta función nunca se descarga en el navegador; solo existe en el servidor. Esto significa que la información confidencial sobre la base de datos, como sus credenciales de conexión, no se filtrará al navegador.

Quizás esté pensando que el fragmento de código anterior no puede ser un RSC porque contiene un manejador de clics de botón. Anteriormente aprendimos que los RSCs no son capaces de manejar eventos porque necesitan ejecutarse en el cliente. En Next.js, este código funciona porque los RSCs pueden pasar Server Functions al cliente en ciertos manejadores de eventos, y `onClick` es uno que admite esta capacidad; `onSubmit` es otro que se usa comúnmente. Como se indicó anteriormente, no se envía toda la Server Function al cliente, solo una referencia a ella.

Las Server Functions también se pueden llamar desde Client Components, pero deben estar en un archivo diferente. Aquí está el mismo ejemplo, pero esta vez `DeleteButton` es un Client Component:

Aquí está el archivo `DeleteButton.tsx`:

```tsx
‘use client’; import { deleteProduct } from ‘@/data/deleteProduct’; export function DeleteButton({ id }: { id: number }) { return ( <button type=”button” onClick={() => deleteProduct(id)} > Delete </button> ); }
```

La Server Function `deleteProduct` se importa y se llama como una función normal.

Aquí está `deleteProduct.ts`:

```typescript
‘use server’; import { createClient } from ‘@libsql/client’; export async function deleteProduct(id: number) { const client = createClient({ url: process.env.DB_URL ?? ‘’ }); await client.execute({ sql: ‘DELETE FROM posts WHERE id = ?’, args: [id], }); client.close(); }
```

La directiva `'use server'` en la parte superior del archivo marca las funciones exportadas en el archivo como Server Functions.

Ahora que entendemos cómo implementar una Server Function, usaremos una para crear nuevas publicaciones de blog en nuestra aplicación.

#### Creación de una Server Function

En esta sección, crearemos un botón en la página de lista de publicaciones de blog que crea una nueva publicación de blog. Usaremos una Server Function para implementar esto. Lleve a cabo los siguientes pasos:

1. Comenzaremos implementando la Server Function que inserta una nueva publicación de blog en la base de datos. Cree un nuevo archivo llamado `createPost.ts` en la carpeta `src/data` y agréguele el siguiente contenido:

```typescript
‘use server’; import { revalidatePath } from ‘next/cache’; import { createClient } from ‘@libsql/client’; export async function createPost( title: string, description: string, ) { const client = createClient({ url: process.env.DB_URL ?? ‘’, }); await client.execute({ sql: ‘INSERT INTO posts(title, description) VALUES (?, ?)’, args: [title, description], }); client.close(); revalidatePath(‘/posts’); }
```

La función se conecta a la base de datos y ejecuta una consulta SQL para insertar un nuevo registro en la tabla `posts` con el título y la descripción pasados a la función.

La función `revalidatePath` invalida la caché de Next.js para que la nueva publicación aparezca en la lista.

La directiva `'use server'` significa que la función estará disponible como una Server Function.

2. Ahora crearemos un componente Client Component de botón que llama a `createPost`. Cree un archivo llamado `NewPost.tsx` en la carpeta `src/components` con el siguiente contenido:

```tsx
‘use client’; import { createPost } from ‘@/data/createPost’; export function NewPost() { async function handleClick() { await createPost( ‘New Post’, ‘New Post Description’, ); } return ( <div className=”actions”> <button type=”button” onClick={handleClick}> Create New Post </button> </div> ); }
```

El componente renderiza un botón que llama a la Server Function `createPost` en su manejador de clics. Hemos codificado de forma rígida el título y la descripción de la publicación del blog como `New Post` y `New Post Description`, respectivamente.

Se podría utilizar un formulario para capturar el título y la descripción del usuario antes de enviarlo a la Server Function. Cubriremos esto en detalle en el [Capítulo 9](https://subscription.packtpub.com/book/web-development/9781836643173/9), *Trabajo con formularios*.

A medida que escribe el código para llamar a `createPost`, tómese un momento para apreciar el IntelliSense. Intente también pasar argumentos que no sean cadenas y experimente la verificación de tipos. ¡Estas características funcionan a través del límite de red cliente/servidor, como si no hubiera ningún límite!

3. El último paso de implementación es hacer referencia al componente `NewPost` en la página `Posts`. Abra `src/app/posts/page.tsx` y agregue `NewPost` de la siguiente manera:

```tsx
import { NewPost } from ‘@/components/NewPost’; ... export default async function Posts( ... ) { const criteria = ... const resolvedHeading = ... return ( <main> <h2>{resolvedHeading}</h2> <NewPost /> <Suspense ...> ... </Suspense> </main> ); }
```

4. Ejecute la aplicación ejecutando `npm run dev` en una terminal y luego navegue a la página de lista de publicaciones del blog. El botón **Create New Post** aparece encima de la lista de publicaciones del blog:

*Figura 7.10 – Botón Create New Post en la página de lista de publicaciones del blog*

5. Abra las herramientas de desarrollo del navegador, vaya al panel **Network** y borre las llamadas de red existentes que se muestran. Usaremos este panel para comprender mejor cómo se llama a la Server Function en el paso 6.
6. Haga clic en el botón **Create New Post** y la nueva publicación del blog aparecerá en la parte inferior de la lista. Puede utilizar la extensión SQLite de Visual Studio Code para verificar que el registro se agrega a la tabla `posts` en la base de datos.
7. En el panel Network de las herramientas de desarrollo del navegador, busque la solicitud a la ruta `posts` y observe el método de solicitud HTTP, un encabezado de solicitud HTTP llamado `Next-Action` y la carga útil (*payload*) de la solicitud.

*Figura 7.11 – Solicitud HTTP de la Server Function*

Estos son los puntos clave sobre cómo se invoca la Server Function:
- Se invoca mediante una solicitud HTTP **POST** a la página en la que se renderiza el componente.
- El encabezado HTTP `Next-Action` de la solicitud es una referencia indirecta a la función `createPost` en el servidor. Esto se establece en una cadena similar a un GUID en este ejemplo.
- La carga útil es un array que contiene los argumentos que se van a pasar a la Server Function.

Esto completa la implementación y el consumo de nuestra Server Function. Para obtener más información sobre las Server Functions, consulte la siguiente página en la documentación de React: [https://react.dev/reference/rsc/server-functions](https://react.dev/reference/rsc/server-functions).

A continuación, agregaremos un indicador de progreso al componente `NewPost`.

#### Adición de un indicador de progreso

En el componente `NewPost`, utilizaremos una variable de estado de React `isMutating` para renderizar un indicador de progreso. Lleve a cabo los siguientes pasos:

1. Abra `NewPost.tsx` y agregue el siguiente código resaltado:

```tsx
import { useState } from ‘react’; ... export function NewPost() { const [isMutating, setIsMutating] = useState(false); async function handleClick() { setIsMutating(true); await createPost( ... ); setIsMutating(false); } return ( <div ... > <button ... > {isMutating ? ‘Creating...’ : ‘Create New Post’} </button> </div> ); }
```

Establecemos `isMutating` en `true` antes de la llamada a la Server Function y luego en `false` cuando ha terminado de ejecutarse. El contenido del botón cambia a `Creating...` durante la mutación, formando el indicador de progreso.

2. Haga clic en el botón **Create New Post** para ver el indicador de progreso. Probablemente aparecerá y desaparecerá rápidamente, por lo que puede usar el panel Network de las herramientas de desarrollo del navegador para ralentizar (*throttle*) la red para que el indicador de progreso aparezca durante más tiempo.

Eso completa el indicador de progreso. A continuación, mejoraremos el manejo de errores en torno a la Server Function.

#### Manejo de errores

Si la Server Function falla, no se muestra ninguna notificación de error al usuario. En su lugar, la operación parecerá quedarse congelada. Como desarrolladores, veremos una alerta de error que muestra Next.js, pero este no será el caso en una compilación de producción.

Lleve a cabo los siguientes pasos para mejorar el manejo de errores:

1. Comenzaremos mejorando la Server Function para detectar errores en las llamadas a la base de datos. Abra `createPost.ts` e importe los siguientes tipos de LibSQL:

```typescript
... import { ..., type Client, type ResultSet, } from ‘@libsql/client’;
```

2. Dentro de `createPost`, agregue una declaración `try-catch` alrededor de la creación del cliente de base de datos y la ejecución de la consulta SQL de la siguiente manera:

```typescript
export async function createPost( ... ) { let client: Client | undefined; let result: ResultSet | undefined; try { client = createClient( ... ); await client.execute( ... ); } catch { return { ok: false }; } finally { if (client) { client.close(); } } revalidatePath(‘/posts’); }
```

3. Agregue una sentencia `return` para devolver si la mutación fue exitosa, junto con el `id` de la nueva publicación:

```typescript
export async function createPost( ... ) { ... revalidatePath(‘/posts’); return { ok: true, id: result ? result.lastInsertRowid : undefined, }; }
```

4. Ahora podemos usar el objeto devuelto de `createPost` en el componente `NewPost` para establecer algún estado, que a su vez mostrará un mensaje de error o éxito. Abra `NewPost.tsx` y comience estableciendo un estado `status` a partir de la llamada a `createPost`:

```tsx
export function NewPost() { ... const [status, setStatus] = useState< ‘pending’ | ‘error’ | ‘success’ >(‘pending’); async function handleClick() { setIsMutating(true); const result = await createPost( ‘New Post’, ‘New Post Description’, ); setStatus(result.ok ? ‘success’ : ‘error’); setIsMutating(false); } ... }
```

5. Por último, podemos usar `status` para renderizar un mensaje de éxito o error de la siguiente manera:

```tsx
export function NewPost() { ... return ( <div ... > <button ...> ... </button> {status === ‘error’ && ( <span role=”alert”> An unexpected error occurred </span> )} {status === ‘success’ && ( <span role=”alert” className=”success”> Post successfully created </span> )} </div> ); }
```

Tenga en cuenta que estamos renderizando un mensaje de error general en esta situación. En el [Capítulo 9](https://subscription.packtpub.com/book/web-development/9781836643173/9), *Trabajo con formularios*, aprenderemos cómo renderizar mensajes de error más específicos desde una Server Function.

6. En la aplicación en ejecución, ahora aparece un mensaje de éxito cuando se hace clic en el botón **Create New Post** y la Server Function `createPost` tiene éxito. El nombre de la tabla en la instrucción SQL en `createPost` se puede ajustar para probar un error.

*Figura 7.12 – Mensaje de error cuando createPost falla*

No olvide corregir la sentencia SQL antes de continuar.

Eso completa el manejo de errores y esta sección sobre Server Functions. He aquí un resumen rápido:
- Las Server Functions permiten ejecutar código de servidor desde un componente de React de una manera simple y con seguridad de tipos.
- Se puede implementar un indicador de progreso de Client Component utilizando el estado de React que se actualiza antes y después de la ejecución de la Server Function.
- Se puede utilizar una declaración `try-catch` para manejar errores en una Server Function. El hecho de que haya ocurrido un error se puede devolver al componente de React para que se renderice en un estado de éxito o de error.

A continuación, resumiremos lo que hemos aprendido en este capítulo.

---

### Resumen

En este capítulo, aprendimos que la obtención de datos del lado del servidor puede mejorar el rendimiento de la carga inicial de una página debido a la reducción de llamadas de red y a la menor cantidad de JavaScript del lado del cliente.

Utilizamos la obtención de datos del lado del servidor en nuestra aplicación, en un RSC, consultando una base de datos SQLite para obtener los datos. Usamos React Suspense para implementar un indicador de carga y un error boundary de React para manejar y reportar errores.

Aprendimos que las Server Functions son un enfoque simple y con seguridad de tipos para mutar datos. Finalmente, usamos una Server Function en nuestra aplicación para agregar una nueva publicación de blog a nuestra base de datos.

Ahora tiene las habilidades para hacer que las páginas web se carguen a una velocidad increíble y el conocimiento para implementar rápidamente acciones mantenibles en esas páginas.

En el próximo capítulo, aprenderemos cómo implementar la obtención de datos y mutaciones del lado del cliente utilizando una popular biblioteca de terceros.

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido en este capítulo:

1. Tenemos el siguiente RSC para una página. La página tarda un poco porque la función `getPeople` es algo lenta. ¿Qué podemos hacer para mejorar la experiencia de usuario además de mejorar el rendimiento de la función `getPeople`?

```tsx
export default async function People() { const people = await getPeople(); return ( <ul> {people.map((person) => ( <li key={person}> <span>{person}</span> </li> ))} </ul> ); }
```

2. Considere el siguiente RSC. Cuando se lanza un error en la función `getPeople`, toda la aplicación de React falla al renderizarse. ¿Cómo podemos mejorar esta situación para que solo el componente `PeopleList` falle al renderizarse?

```tsx
export async function PeopleList() { const people = await getPeople(); return ( <ul> {people.map((person) => ( <li key={person}> <span>{person}</span> </li> ))} </ul> ); }
```

3. ¿Cuál es la ventaja de utilizar React Server Functions sobre las rutas de API tradicionales en Next.js?
4. ¿Por qué no se recomienda utilizar React Server Functions en Next.js para la obtención de datos?
5. El siguiente componente genera un error de compilación en Next.js. ¿Cuál es el problema?

```tsx
“use client”; import { useState } from “react”; export function Counter() { const [count, setCount] = useState(1); async function saveCount(count: number) { use server”; db.count.save(count); } function handleClick() { setCount((prev) => { const newCount = prev + 1; saveCount(newCount); return newCount; }); } return ( <button onClick={handleClick}> {count} </button> ); }
```

---

### Respuestas

1. Se puede utilizar **React Suspense** con la lista de personas para proporcionar un indicador de carga mientras se obtienen sus datos. Primero, la lista de personas debe extraerse en su propio componente:

```tsx
export async function PeopleList() { const people = await getPeople(); return ( <ul> {people.map((person) => ( <li key={person}> <span>{person}</span> </li> ))} </ul> ); }
```

El componente `People` puede luego hacer referencia a `PeopleList` dentro de un componente `Suspense` con un fallback de indicador de carga:

```tsx
export default function People() { return ( <main> <Suspense fallback={<div>Loading...</div>}> <PeopleList /> </Suspense> </main> ); }
```

Si se utiliza Next.js y `People` es un componente de nivel de página, el archivo convencional `loading.tsx` se puede utilizar como una solución alternativa.

2. Un componente `ErrorBoundary` (del paquete `react-error-boundary`) puede envolver a `PeopleList` en el árbol de componentes para detectar errores y renderizar un componente fallback:

```tsx
<ErrorBoundary FallbackComponent={ErrorFallback}> <PeopleList /> </ErrorBoundary>
```

El componente fallback debe ser un Client Component:

```tsx
“use client”; export function ErrorFallback() { return <div role=”alert”>An error occurred</div>; }
```

3. Las React Server Functions permiten ejecutar código de servidor desde un componente de React utilizando menos código y una mejor seguridad de tipos que un manejador de ruta de API de Next.js.
4. Las React Server Functions en Next.js utilizan una solicitud HTTP **POST** en lugar de HTTP **GET**, por lo que no utilizarán la caché del navegador ni de la CDN.
5. Los Client Components no pueden contener Server Functions en línea (*inline Server Functions*). Por lo tanto, la función `saveCount` debe extraerse en un archivo separado con la directiva `"use server"` en la parte superior:

```typescript
// saveCount.ts “use server”; export async function saveCount(count: number) { db.count.save(count); } // Counter.tsx “use client”; import { useState } from “react”; import { saveCount } from “./saveCount”; export function Counter() { const [count, setCount] = useState(1); function handleClick() { setCount((prev) => { const newCount = prev + 1; saveCount(newCount); return newCount; }); } return ( <button onClick={handleClick}> {count} </button> ); }
```
