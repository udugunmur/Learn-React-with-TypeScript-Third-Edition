# Parte 2: Fundamentos de la Aplicación

## Capítulo 5: Uso de React Server y Client Components

En este capítulo, aprenderemos todo sobre los **React Server Components (RSC)** y los **Client Components**. Comenzaremos por comprender el problema que los Server Components intentan resolver antes de explorar sus capacidades y cuándo utilizarlos. Además, aprenderemos cómo anidarlos entre sí para crear una aplicación de alto rendimiento con las funciones que necesitamos.

También comenzaremos a utilizar un framework de aplicaciones basado en React llamado **Next.js** y obtendremos experiencia práctica creando tanto Server como Client Components.

Cubriremos los siguientes temas:

- Comprensión de las SPAs
- Comprensión de los Server Components
- Creación de Server Components
- Exploración de los Client Components
- Composición de Server y Client Components

---

### Requisitos técnicos

En este capítulo utilizaremos las siguientes tecnologías:

- **Navegador**: Un navegador moderno como Google Chrome
- **Node.js y npm**: Puede instalarlos desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
- **Visual Studio Code**: Puede instalarlo desde [https://code.visualstudio.com/](https://code.visualstudio.com/)

Todos los fragmentos de código utilizados en este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter05](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter05)

---

### Comprensión de las SPAs

Antes de sumergirnos en los Server Components, comprendamos los problemas que pretenden resolver, todos los cuales provienen de las aplicaciones de una sola página (*single-page applications* o **SPAs**).

#### Comprender el problema de las SPAs

El problema de las SPAs ocurre cuando se carga la primera página. El siguiente diagrama visualiza los pasos para cargar la primera página en una SPA:

*Figura 5.1 – Pasos de carga de una SPA en React*

Aquí hay una descripción de los pasos:

1. El proceso comienza cuando el usuario solicita una página al servidor y se descarga una página HTML. Una SPA típica carga una página HTML con muy poco contenido aparte de referencias a archivos JavaScript y CSS. Por lo general, el HTML no contendrá ningún contenido significativo para el usuario.
2. El JavaScript y el CSS a los que se hace referencia en el HTML se solicitan y descargan desde el servidor. El JavaScript contiene código para renderizar la aplicación porque los elementos se renderizan en el cliente. En una aplicación React, este JavaScript contendrá tanto a React como a la aplicación, lo que significa que puede haber una gran cantidad de JavaScript. El CSS contiene estilos para los elementos renderizados.
3. Una vez descargado el JavaScript, se analiza (*parse*) y ejecuta, lo que da como resultado que el árbol de componentes se renderice en una aplicación React. El proceso de renderizado a menudo se denomina renderizado del lado del cliente (*client-side rendering* o **CSR**) en una SPA.
4. Por lo general, una aplicación obtendrá algunos datos de una base de datos para mostrarlos en la primera página. Por lo tanto, después del renderizado inicial en una aplicación React, se producirá un proceso de obtención de datos (*data fetching*), seguido de un re-renderizado de componentes para mostrar los datos en la página.

El principal problema de las SPAs es que suceden muchas cosas antes de que el usuario vea contenido significativo en la página: ¡por lo general, al menos tres solicitudes de red! Este proceso ineficiente es un problema potencial de rendimiento. Si alguno de los pasos lleva mucho tiempo, el problema puede ser significativo. Por ejemplo, descargar, analizar y ejecutar una gran cantidad de JavaScript puede ser problemático. Un rendimiento de carga lento no solo frustra a los usuarios, sino que puede afectar el SEO.

Vale la pena señalar que los empaquetadores como Vite pueden dividir la aplicación en paquetes (*bundles*) separados para mitigar el problema de rendimiento de la primera carga. Los paquetes se cargan bajo demanda a medida que el usuario interactúa con la aplicación. Sin embargo, esta solución puede causar un problema derivado: interacciones lentas del usuario a medida que los paquetes se cargan de forma diferida (*lazy loading*).

#### Comprender los beneficios de las SPAs

Antes de comenzar a cubrir los **React Server Components (RSCs)**, es importante comprender que las SPAs no son del todo malas; han sido una arquitectura popular durante más de una década y todavía ofrecen beneficios significativos en la actualidad. De hecho, las aplicaciones que hemos creado hasta ahora en este libro son SPAs. Estas son algunas de las ventajas de las SPAs:

- Una vez que se carga y ejecuta el JavaScript inicial, las SPAs son rápidas. Los elementos de la interfaz de usuario se actualizan en el cliente y cualquier obtención de datos ocurre sin una recarga completa de la página. Además, la navegación por la página ocurre en el cliente sin una solicitud al servidor.
- Escala bien porque la carga del servidor no contiene ningún procesamiento de HTML o JavaScript; ese trabajo se distribuye a cada cliente.
- La separación entre frontend y backend es sencilla, lo que significa que el backend puede ser una tecnología distinta de JavaScript.
- Alojar una SPA es simple y económico porque es solo un conjunto de recursos estáticos. Se puede utilizar una CDN, lo que permite una distribución global y potencialmente reduce los costos aún más debido a la reducción de solicitudes al servidor y al menor uso de ancho de banda.

A continuación, es hora de aprender sobre los RSCs y cómo abordan el problema de las SPAs.

---

### Comprensión de los Server Components

En esta sección, aprenderemos qué son los RSCs y sus beneficios.

#### Comprender qué es un Server Component

Los RSCs se introdujeron por primera vez en la versión experimental de React 18 y se lanzaron por completo en **React 19**. Los componentes de React anteriores a esta versión eran todos Client Components.

Los componentes de React que hemos creado hasta ahora en este libro no son RSCs; en su lugar, son Client Components. Profundizaremos en cómo los Client Components pueden funcionar junto con los RSCs más adelante en este capítulo.

Aquí hay un ejemplo de un RSC:

```tsx
export async function Person({ id }: { id: string }) { const result = await db .select({ name: people.name, notes: people.notes }) .from(people) .where(eq(people.id, id)); if (result.length === 0) { return null; } const person = result[0]; return ( <main> <h1>{person.name}</h1> <p>{person.notes}</p> </main> ); }
```

A primera vista, un RSC parece un componente regular de React, similar a lo que ya hemos estado construyendo. Tenga en cuenta lo siguiente en particular:
- El componente es una función que recibe props; en este caso, `id`.
- El componente devuelve JSX.

Sin embargo, existen algunas diferencias obvias:
- La función es **asincrónica (`async`)**. Los Client Components solo pueden ser sincrónicos; los RSCs pueden ser asincrónicos o sincrónicos.
- La función accede a una base de datos en el servidor. Los Client Components no pueden acceder directamente a los recursos del servidor.

Aquí hay algunos otros puntos clave sobre los RSCs que no son evidentes en el fragmento de código anterior:
- Se ejecutan exclusivamente en el servidor.
- No se vuelven a renderizar (*re-render*). Se ejecutan una vez en el servidor para generar la interfaz de usuario, que luego se descarga en el cliente.
- No pueden ser muy interactivos: no se permiten el estado de React (`useState`), los efectos (`useEffect`) ni los manejadores de eventos. Sin embargo, se puede utilizar la interactividad nativa de HTML, como los formularios básicos. No se pueden utilizar interacciones que requieran JavaScript.
- El paquete de JavaScript no incluye el código JavaScript del componente que se ejecuta en el proceso de renderizado. Solo se descarga al cliente el resultado de la llamada a la función del RSC, que es la interfaz de usuario generada.

Los RSCs han tardado un par de años en lanzarse por completo y estar disponibles después de haber sido introducidos en una versión experimental. Esto se debe a que requieren elementos externos a React, como un empaquetador (*bundler*) y un servidor web compatible con RSCs.

Esto nos lleva a un requisito arquitectónico para los RSCs: se requiere un servidor web capaz de ejecutar un RSC. Por lo tanto, los servidores que no están basados en JavaScript requerirán un servidor adicional para los RSCs. Además, incluso si el servidor está basado en JavaScript, el framework web utilizado debe admitir RSCs, y no muchos lo hacen en el momento de escribir este libro.

Ahora que hemos comenzado a comprender qué son los RSCs, a continuación, cubriremos sus beneficios en profundidad.

#### Comprender cómo los RSCs abordan el problema de las SPAs

El siguiente diagrama visualiza los pasos para cargar la primera página donde se utiliza un RSC:

*Figura 5.2 – Pasos de carga con RSC*

Aquí hay una descripción de los pasos:

1. El proceso comienza cuando el usuario solicita una página al servidor. El servidor ejecuta el RSC, ejecutando cualquier consulta a la base de datos. Luego se descarga la página HTML procesada. Por lo tanto, es probable que el HTML contenga contenido significativo para el usuario, a diferencia de una SPA.
2. El JavaScript y el CSS a los que se hace referencia en el HTML se solicitan y descargan desde el servidor. La cantidad de JavaScript generalmente será menor que la que recibiría de una SPA porque parte de ella ya se habrá ejecutado y no será necesaria en el cliente. El JavaScript contendrá Client Components, algo que cubriremos más adelante en este capítulo.
3. Una vez descargado el JavaScript, se analiza y ejecuta, lo que da como resultado la **hidratación (hydration)** de los Client Components. Cubriremos la hidratación más adelante en este capítulo, pero es importante tener en cuenta que requiere menos trabajo que renderizar completamente los componentes.

Ahora que hemos comenzado a comprender los RSCs y cómo abordan el problema de las SPAs, exploremos más a fondo sus beneficios.

#### Comprender los beneficios de los Server Components

Los RSCs ofrecen beneficios significativos. Estas son algunas tareas valiosas que puede realizar un RSC:
- Obtener datos directamente de una base de datos
- Llamar a otro servicio web que requiera credenciales secretas
- Comprobar si un usuario está autorizado para acceder a un recurso del servidor
- Convertir contenido markdown en HTML sin tener que descargar el código del convertidor al navegador

En términos generales, existen tres categorías de beneficios:

1. **Rendimiento**: El rendimiento se ve afectado positivamente de las siguientes maneras:
   - Se reduce la cantidad de JavaScript descargada en el navegador
   - Se reduce la cantidad de llamadas de red desde el navegador cuando se carga la aplicación
   - Se reduce el renderizado de componentes cuando se carga la aplicación

2. **Productividad del desarrollador**: Veamos cómo se ve afectada la productividad del desarrollador:
   - Siempre hemos podido escribir código de servidor para consultar bases de datos y realizar comprobaciones de autorización de usuarios. Sin embargo, los RSCs le permiten hacer esto utilizando el paradigma de React.
   - Los RSCs permiten coubicar (*colocate*) el código de frontend y backend, así como disponer de un tipado fuerte a través del límite de red.
   - Los RSCs reducen el estado de React necesario en una aplicación porque no es necesario para los datos del servidor: estos datos simplemente se renderizan directamente en el servidor. El estado es uno de los aspectos más complejos de React, por lo que reducir el estado reduce la complejidad.

3. **Seguridad**: Dado que los componentes del servidor nunca se ejecutan en el cliente, pueden contener de forma segura lógica confidencial, como reglas de acceso condicional o claves de API.

Esto concluye esta sección sobre la comprensión de los RSCs. En resumen, los RSCs se ejecutan exclusivamente en el servidor y pueden ser asincrónicos, a diferencia de los Client Components. Pueden obtener datos directamente de bases de datos e interactuar con los recursos del servidor, pero no pueden realizar interacciones de usuario basadas en JavaScript. Los RSCs ofrecen un buen rendimiento, productividad y beneficios de seguridad en comparación con los Client Components.

A continuación, implementaremos nuestro primer RSC.

---

### Creación de Server Components

En esta sección, crearemos un nuevo proyecto en **Next.js**, un popular framework basado en React que admite RSCs. Luego, crearemos algunos RSCs y consolidaremos nuestra comprensión de ellos.

#### Creación del proyecto

Next.js es un framework maduro que fue uno de los primeros en adoptar RSCs. Lo usaremos en este capítulo y a lo largo del resto de este libro. Lleve a cabo los siguientes pasos para crear un proyecto Next.js:

1. En una terminal, ejecute el siguiente comando para crear el proyecto:

```bash
npx create-next-app@latest app --ts --tailwind --eslint --app --src-dir --import-alias “@/*” --turbopack
```

Aquí hay una explicación del comando:
- `create-next-app`: La herramienta que crea el proyecto
- `app`: El nombre de la aplicación
- `--ts`: Una opción que configura el proyecto para que use TypeScript
- `--tailwind`: Una opción que configura el proyecto para que use estilos de Tailwind
- `--eslint`: Una opción que configura ESLint para el linting
- `--app`: Una opción que configura el proyecto para que use la última variante de enrutador, llamada **App Router**
- `--src-dir`: Una opción que configura si el código fuente está en una carpeta `src`
- `--import-alias`: Una opción que configura los alias de rutas de importación. Lo hemos establecido en el valor predeterminado `"@/*"`
- `--turbopack`: Una opción que especifica si Turbopack debe usarse para el desarrollo

El comando también instala las dependencias del proyecto, algo que tardará unos segundos.

Se puede encontrar más información sobre la creación de una aplicación Next.js en [https://nextjs.org/docs/app/api-reference/cli/create-next-app](https://nextjs.org/docs/app/api-reference/cli/create-next-app).

2. Aún en la terminal, muévase a la carpeta del proyecto y abra Visual Studio Code ejecutando los siguientes comandos:

```bash
cd app code .
```

Si no está utilizando Visual Studio Code, el comando `code .` no abrirá su editor; puede omitir ese comando y abrir su editor manualmente.

Prettier se puede configurar de la misma manera que Vite, algo que aprendimos en el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*. Siéntase libre de agregar el formato de código automático a este proyecto.

3. En la terminal, ejecute el siguiente comando para ejecutar la aplicación en modo de desarrollo:

```bash
npm run dev
```

La aplicación estará disponible en su navegador en [http://localhost:3000](http://localhost:3000/):

*Figura 5.3 – Página de inicio de la aplicación de plantilla de Next.js*

4. A continuación, vaya al código en Visual Studio Code y abra `layout.tsx` en la carpeta `src/app`. Este es el componente raíz de React, que se renderiza en todas las rutas de la aplicación. Agregue la siguiente línea resaltada antes de la sentencia `return`:

```tsx
export default function RootLayout( ... ) { console.log(‘Am I on the server or client?’); return ... }
```

La salida de la consola nos ayudará a determinar si el componente es un RSC.

Como experimentamos anteriormente con Vite, la aplicación se reconstruirá y recargará automáticamente en el navegador de manera eficiente.

5. Abra la consola del navegador en el área de herramientas de desarrollo del navegador. Verá el siguiente mensaje, junto con una insignia (*badge*) **Server**, lo que indica que provino del servidor:

*Figura 5.4 – Salida de la consola del navegador*

6. Ahora, abra la terminal que está ejecutando la aplicación Next.js. Veremos el siguiente mensaje:

*Figura 5.5 – Salida de consola del servidor Next.js*

A partir de esto, podemos ver que el componente `Layout` es un RSC.

7. Ahora, abra `page.tsx`. Este es el componente que se renderiza dentro de `Layout` en la ruta raíz de la aplicación. Agregue una salida de consola similar a este componente:

```tsx
export default function Home() { console.log(‘Is Home on the server or client?’); return ... }
```

Determinará que este también es un RSC.

8. En el área de herramientas de desarrollo del navegador, vaya al panel **Components** y observe la lista de componentes de React. Verá una insignia **Server** junto a los RSCs. Esta es otra forma de determinar si un componente es un RSC:

*Figura 5.6 – Insignia de Server en la lista de componentes de React*

En Next.js, los componentes son RSCs por defecto; es decir, no tiene que hacer nada para declarar que un componente es un RSC. De hecho, no puede especificar que un componente sea un RSC; solo puede especificar que un componente es un Client Component. Más adelante en este capítulo, exploraremos cómo se hace esto.

En la siguiente sección, crearemos nuestro propio RSC en el proyecto Next.js.

#### Creación de un RSC

En esta sección, crearemos un RSC `Header` y lo anidaremos dentro del RSC `Home` que observamos en la sección anterior. El componente `Header` simplemente contendrá el nombre de la aplicación. Lleve a cabo los siguientes pasos:

1. Cree una carpeta en la carpeta `src` llamada `components`. Luego, cree un archivo llamado `Header.tsx` en esa carpeta `components`.

> [!NOTE]
> Es una práctica común aislar los componentes de enrutamiento en la carpeta `app` de otros componentes mediante una carpeta `components`. Aprenderemos más sobre los componentes de enrutamiento en el [Capítulo 6](https://subscription.packtpub.com/book/web-development/9781836643173/6), *Creación de una aplicación multipágina con Next.js*.

2. Agregue el siguiente contenido a `Header.tsx`:

```tsx
export function Header() { console.log(“Is Header an RSC?”); return ( <header className=”flex w-full items-center justify-between”> <span className=”text-lg font-bold”> My app </span> </header> ); }
```

Esto genera un elemento `header` con el texto `My app` en su interior.

3. Abra `page.tsx` y agregue una declaración de importación para importar `Header`:

```typescript
import { Header } from ‘@/components/Header’;
```

Aquí, `@/` es un alias de ruta de importación para la carpeta `src`. Usar un alias de ruta de importación es una alternativa a usar una ruta relativa, que se vería así:

```typescript
import { Header } from ‘../components/Header’;
```

Los beneficios de los alias de rutas de importación sobre las rutas relativas incluyen la legibilidad y la reducción de cambios cuando se mueven archivos.

4. Agregue `Header` dentro del elemento `div`, justo encima del elemento `main`:

```tsx
return ( <div ... > <Header /> <main ... > ... </div> );
```

Al mirar la consola del navegador y la terminal, se revelará que `Header` es un RSC:

*Figura 5.7 – Confirmación en la terminal de que Header es un RSC*

Ese es nuestro primer RSC creado. ¡Excelente!

En la siguiente sección, comenzaremos a comprender qué sucede internamente en un RSC.

#### Comprender cómo funcionan los Server Components

En esta sección, nos tomaremos el tiempo para comprender cómo se ejecuta el componente `Header` en el servidor y luego se muestra en el navegador. Lleve a cabo los siguientes pasos:

1. Primero, ampliaremos el componente `Header` para que incluya algún cómputo. Una vez que hayamos hecho esto, podremos determinar si el cómputo se realizó en el servidor o en el cliente. Abra `Header.tsx` y agregue un total calculado, de la siguiente manera:

```tsx
export function Header() { console.log(‘Is Header an RSC?’); const total = 99 + 99; return ( <header ... > <span ... >My app</span> <span>{total}</span> </header> ); }
```

2. En el área de herramientas de desarrollo del navegador, en el panel Elements, busque `198`. Habrá dos instancias de `198`. La primera estará dentro del elemento `span`, mientras que la segunda estará dentro de un elemento `script`, dentro de una estructura JSON, como se muestra en la siguiente captura de pantalla:

*Figura 5.8 – RSC serializado*

Tenga en cuenta que el JSON del elemento `script` puede ser diferente para usted en comparación con lo que se muestra en la captura de pantalla anterior.

El JSON en el elemento `script` comienza a revelar cómo funcionan los RSCs. Una vez que los RSCs se han ejecutado en el servidor, la salida del RSC se **serializa** junto con la información para renderizarlos. Es importante tener en cuenta que solo se serializa el resultado del RSC, no el código de la función. En este ejemplo, solo se serializa `198`, no el cálculo. Por lo tanto, los detalles confidenciales en el RSC nunca se filtrarán al cliente a menos que se incluyan en la salida del RSC.

Eso nos lleva al final de esta sección sobre la creación de RSCs. He aquí un breve resumen:
- Next.js es un framework de aplicaciones que admite RSCs. Lo usamos para crear nuestro proyecto de React.
- En un proyecto de Next.js, los componentes son RSCs por defecto, por lo que `Layout` y `Page` son RSCs. Creamos un RSC `Header` dentro del RSC `Page`.
- Usamos la consola para confirmar que los componentes eran RSCs e inspeccionamos los elementos HTML en el área de herramientas de desarrollo del navegador para ver su formato serializado.

Ahora que tenemos una buena comprensión de los RSCs, a continuación, aprenderemos sobre los Client Components.

---

### Exploración de los Client Components

En esta sección, comenzaremos por comprender los Client Components con respecto a los RSCs. Luego, crearemos un Client Component en nuestro proyecto que alternará la aplicación entre el modo oscuro y el claro.

#### Comprender los Client Components

Para las aplicaciones interactivas, los Client Components son esenciales. De hecho, cada componente de React era un Client Component antes de la introducción de los RSCs.

Estas son algunas de las características que tienen los Client Components que los RSCs no tienen:
- Tienen React Hooks, como `useState`, `useRef` y `useEffect`
- Proporcionan manejo de eventos del navegador, como `onClick` en un botón
- El estado y las funciones se comparten con el contexto de React (`useContext`)
- Se proporciona acceso a las APIs del navegador como `window.localStorage`

A continuación, aprenderemos cómo se renderizan los Client Components en Next.js.

#### Comprender el renderizado de Client Components

Como sugiere el nombre, los Client Components se ejecutan en el cliente. Sin embargo, también pueden ejecutarse en el servidor durante la carga inicial para **pre-renderizar** las partes que se pueden pre-renderizar, que es lo que sucede en Next.js. En Next.js, después de la carga inicial, los Client Components se **hidratan** con las partes no cubiertas por el pre-renderizado, que generalmente es la interactividad. Este proceso es complejo, pero acelera el renderizado inicial de los Client Components.

Para comprender mejor el proceso de renderizado de los Client Components de Next.js, considere el siguiente componente `Button`:

```jsx
function Button() { return ( <button onClick={() => { console.log(“click”); }}> Click </button> ); }
```

Estos son los pasos que tienen lugar en el proceso de renderizado:
1. Primero, el componente se renderiza en el servidor. Sin embargo, no se puede renderizar por completo porque el servidor no puede adjuntar el manejador de clics. Por lo tanto, el resultado de este primer proceso de renderizado es un botón HTML sin un manejador de clics.
2. El botón HTML se envía al cliente, junto con un script de hidratación.
3. Finalmente, el cliente agrega el botón HTML al DOM y ejecuta el script de hidratación para adjuntar el manejador de clics del botón.

Antes de crear un Client Component en nuestro proyecto, aprenderemos cómo especificar un Client Component en Next.js.

#### Especificar Client Components

Como se mencionó anteriormente en este capítulo, en Next.js, los componentes son RSCs por defecto. Para especificar un Client Component, el archivo debe contener `'use client'` en la parte superior. Sin embargo, los componentes que se importan en un archivo de Client Component se convierten automáticamente en Client Components, lo que significa que `'use client'` no es obligatorio en ellos.

¡No se preocupe si esto es un poco confuso en esta etapa! En la siguiente sección, agregaremos un Client Component a la aplicación para afianzar este conocimiento.

#### Creación de Client Components

En esta sección, crearemos un Client Component que alternará nuestra aplicación entre el modo oscuro y el claro. Nos tomaremos el tiempo para experimentar qué sucede si el componente no está marcado como Client Component y también demostraremos que se ejecuta en el servidor así como en el cliente. Lleve a cabo los siguientes pasos:

1. Cree un nuevo archivo llamado `ColorModeToggle.tsx` en la carpeta `src/components` con el siguiente contenido:

```tsx
export function ColorModeToggle() { console.log(‘Does ColorModeToggle run on the Server and Client?’); function handleClick() {} return ( <button onClick={handleClick} className=”flex rounded bg-blue-500 px-4 py-2 text-white”> </button> ); }
```

El componente renderiza un botón azul con un manejador de clics que aún no hace nada.

2. Abra `Header.tsx` y agregue `ColorModeToggle` después del elemento `<span>{total}</span>`:

```tsx
import { ColorModeToggle } from ‘./ColorModeToggle’; export function Header() { ... return ( <header ...> ... <span>{total}</span> <ColorModeToggle /> </header> ); }
```

Se producirá un error en tiempo de ejecución:

*Figura 5.9 – Error por falta de "use client"*

Actualmente, `ColorModeToggle` es un RSC y tenemos un manejador de eventos, lo cual no está permitido.

3. De vuelta en `ColorModeToggle.tsx`, resuelva el error especificando que el componente es un Client Component en la parte superior del archivo:

```typescript
‘use client’;
```

4. Aún en `ColorModeToggle.tsx`, usaremos el estado para almacenar información sobre si estamos en modo oscuro o no. Actualizaremos esto en el manejador de clics del botón y renderizaremos el modo actual como el texto del botón. Agregue las siguientes líneas de código resaltadas:

```tsx
‘use client’; import { useState } from ‘react’; export function ColorModeToggle() { ... const [colorMode, setColorMode] = useState<’dark’ | ‘light’>(‘light’); function handleClick() { const newColorMode = colorMode === ‘dark’ ? ‘light’ : ‘dark’; setColorMode(newColorMode); } return ( <button ... > {colorMode === ‘dark’ ? ‘Light’ : ‘Dark’} </button> ); }
```

El botón de alternancia del modo de color ahora aparece a la derecha del encabezado de la aplicación:

*Figura 5.10 – El botón de alternancia del modo de color a la derecha*

5. Hacer clic en el botón de alternancia del modo de color no alterna entre el modo oscuro y el claro por el momento. Para que esto funcione, necesitamos agregar un nombre de clase CSS `dark` al elemento `body`. También necesitamos actualizar las variables CSS `--background` y `--foreground`. Podemos usar un efecto para sincronizar el estado `colorMode` con estas:

```tsx
import { ..., useEffect } from ‘react’; export function ColorModeToggle() { ... useEffect(() => { if (colorMode === ‘dark’) { document.body.classList.add(‘dark’); document.documentElement.style.setProperty( ‘--background’, ‘#0a0a0a’, ); document.documentElement.style.setProperty( ‘--foreground’, ‘#ededed’, ); } else { document.body.classList.remove(‘dark’); document.documentElement.style.setProperty( ‘--background’, ‘#ffffff’, ); document.documentElement.style.setProperty( ‘--foreground’, ‘#171717’, ); } }, [colorMode]); return ... }
```

El botón de alternancia del modo de color ahora funciona cuando se hace clic en él.

6. Hagamos una mejora más que predeterminará el modo de color a lo especificado en el sistema operativo o navegador. Agregue el siguiente efecto para hacer esto:

```tsx
export function ColorModeToggle() { ... useEffect(() => { const mediaQuery = window.matchMedia( ‘(prefers-color-scheme: dark)’, ); setColorMode( mediaQuery.matches ? ‘dark’ : ‘light’ ); }, []); return ... }
```

Estamos utilizando un efecto aquí porque solo queremos que este código se ejecute en el cliente, donde se puede acceder a `window.matchMedia`. Recuerde que los Client Components pueden ejecutarse inicialmente en el servidor, pero el servidor no tiene acceso al objeto `window` del navegador. Los efectos nunca se ejecutan en el servidor: solo se ejecutan en el cliente.

Este patrón es muy útil para el acceso al DOM, que debe ocurrir cuando el componente se está cargando.

7. Ahora, comprobemos dónde se ejecuta el componente `ColorModeToggle`. Primero, verifique la terminal para confirmar que se ejecuta inicialmente en el servidor:

*Figura 5.11 – Confirmación de que ColorModeToggle se ejecuta en el servidor*

8. Por último, verifiquemos la consola del navegador:

*Figura 5.12 – Confirmación de que ColorModeToggle se ejecuta en el cliente*

Eso nos lleva al final de esta sección sobre Client Components. He aquí un resumen:
- Los Client Components son necesarios para aplicaciones interactivas, ya que admiten características como React Hooks, manejo de eventos y acceso al DOM. Inicialmente se ejecutan en el servidor y luego se hidratan con interactividad en el cliente.
- Un Client Component requiere una directiva `'use client'` en la parte superior de su archivo. Otros componentes que se importan en un archivo de Client Component se convierten automáticamente en Client Components, lo que significa que `'use client'` no es necesario para los Client Components posteriores.
- Finalmente, `ColorModeToggle` es el primer Client Component en nuestra aplicación. Contiene un estado, un efecto y manejadores de eventos. Por lo tanto, debe ser un Client Component, en lugar de un RSC.

Eso completa nuestra exploración de los Client Components. A continuación, aprenderemos cómo se pueden componer los Server y Client Components.

---

### Composición de Server y Client Components

Esta sección explicará el proceso de pensamiento detrás de la elección entre Server y Client Components. Luego, comprenderemos qué es un límite de cliente (*client boundary*) y cómo inyectar un RSC en él.

#### RSCs versus Client Components

Ya hemos cubierto los beneficios y las diferentes capacidades de los RSCs y Client Components. Como repaso, aquí hay una tabla comparativa sucinta:

| Característica / Aspecto | RSC | Client Component |
| :--- | :--- | :--- |
| **Carga de la primera página** | Rápida | Más lenta |
| **Acceso a recursos seguros (bases de datos, APIs de terceros)** | Directo | A través de una llamada HTTP a un servidor seguro |
| **Interactividad** | No admitida | Interactividad total |
| **React Hooks** | Muy limitados | Soporte total |
| **Acceso a APIs del navegador** | No admitido | Acceso total |

*Tabla 5.1: Diferencias clave entre RSCs y Client Components*

A continuación, cubriremos una estrategia para elegir un RSC o un Client Component.

#### Comprender cuándo usar un RSC o Client Component

Si está creando una nueva página de Next.js, debe comenzar el árbol de componentes con RSCs. Esto se debe a que es el valor predeterminado en Next.js, y los RSCs tienden a ser más simples y de mayor rendimiento. Cambie un componente a un Client Component si requiere funcionalidad fuera de los RSCs, como la interactividad. Para maximizar el rendimiento, aísle solo las partes que los RSCs no pueden hacer en Client Components. Esto es exactamente lo que hicimos con `ColorModeToggle` en la sección anterior.

A continuación, comprenderemos exactamente cuándo necesitamos usar la directiva `'use client'`.

#### Comprender los límites de cliente

Cada archivo que se importa después de una directiva `'use client'` es automáticamente un Client Component. Esto se conoce como un **límite de cliente (client boundary)**.

El siguiente diagrama visualiza un límite de cliente en una página de una aplicación:

*Figura 5.13 – Límite de cliente en una aplicación*

Los componentes `Page` y `Header` son RSCs. Tenga en cuenta que `ContactForm` contiene una directiva `'use client'`, por lo que es un Client Component. Aquí, `Name`, `Details` y `Submit` son todos Client Components y no necesitan una directiva `'use client'`. Por lo tanto, se forma un límite de cliente alrededor de todo lo que se importa en `ContactForm`.

Podría pensar que no es posible renderizar un RSC dentro de un Client Component. En la siguiente sección, aprenderemos cómo hacer esto.

#### Renderizar un RSC en ColorModeToggle

En esta sección, agregaremos un icono a nuestro componente `ColorModeToggle`, que, como recordará, es un Client Component. Aprenderemos cómo el icono se puede convertir en un RSC.

Los Client Components no pueden importar un RSC. Sin embargo, se puede pasar un RSC a un Client Component como una **prop**.

Usando este concepto clave, agregaremos un icono RSC a nuestro componente `ColorModeToggle`. Siga estos pasos:

1. Comience creando un archivo para el icono SVG llamado `colorModeIcon.svg` en la carpeta `src/components`. Copie el contenido del SVG en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter05/rendering-rsc-in-client-component/src/components/colorModeIcon.svg](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter05/rendering-rsc-in-client-component/src/components/colorModeIcon.svg) y péguelo en este archivo.
2. A continuación, cree un componente de React para el icono. Cree un archivo llamado `ColorModeIcon.tsx` con el siguiente contenido:

```tsx
import Image from “next/image”; import colorModeSvg from “./colorModeIcon.svg”; export function ColorModeIcon() { console.log(“Is ColorModeIcon an RSC?”); return ( <Image src={colorModeSvg} alt=”Color mode icon” className=”mr-2 h-6 w-6” /> ); }
```

El componente renderiza el SVG en un componente `Image` de Next.js con un tamaño fijo.

3. Ahora, pongamos el icono dentro de `ColorModeToggle`. Primero, intentaremos importarlo y usarlo en `ColorModeToggle.tsx`:

```tsx
import { ColorModeIcon } from “./ColorModeIcon”; export function ColorModeToggle() { ... return ( <button ...> <ColorModeIcon /> {colorMode === ‘dark’ ? ‘Light’ : ‘Dark’} </button> ); } 
```

4. Si la aplicación no se está ejecutando, puede ejecutarla ingresando `npm run dev` en la terminal. Eche un vistazo a la consola del navegador para determinar si `ColorModeIcon` es un Client Component:

*Figura 5.14 – Confirmación de que ColorModeIcon es un Client Component*

El hecho de que el mensaje `Is ColorModeIcon an RSC?` se envíe a la consola del navegador sin una insignia **Server** confirma que es un Client Component. Esto confirma lo que aprendimos en la sección anterior: un componente que se importa a un Client Component será automáticamente un Client Component.

5. A continuación, probaremos un enfoque diferente para agregar `ColorModeIcon` a `ColorModeToggle`. Esta vez, lo pasaremos como una prop. Comience eliminando la declaración de importación de `ColorModeIcon`.
6. Ahora, agregue una prop `icon` a `ColorModeToggle` y muestre esto en lugar de `ColorModeIcon`:

```tsx
import { ..., type ReactNode } from ‘react’; export function ColorModeToggle({ icon, }: { icon: ReactNode; }) { ... return ( <button ...> {icon} {colorMode === ‘dark’ ? ‘Light’ : ‘Dark’} </button> ); }
```

En el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*, aprendimos que los componentes se pueden pasar a otros componentes usando una prop especial `children`. Podemos crear nuestra propia prop que acepte un componente, algo que hemos hecho aquí con la prop `icon`. Hemos establecido su tipo en `ReactNode` para que acepte cualquier componente de React.

7. Por último, abra `Header.tsx` y pase `ColorModeIcon` a `ColorModeToggle` usando la prop `icon`:

```tsx
... import { ColorModeIcon } from ‘./ColorModeIcon’; export function Header() { ... return ( <header ...> ... <ColorModeToggle icon={<ColorModeIcon />} /> </header> ); }
```

Ahora, si mira en la consola del navegador, verá que el mensaje `Is ColorModeIcon an RSC?` tiene una insignia **Server** porque `ColorModeIcon` es un RSC.

Antes de completar esta sección, apreciemos algunos beneficios de productividad del uso de RSCs con Client Components. Aunque estos componentes se ejecutan en computadoras completamente diferentes, los desarrollamos utilizando el mismo paradigma. Abra `Header.tsx` y lleve a cabo los siguientes pasos:

1. Pase el cursor sobre `ColorModeToggle` en el JSX. Tenga en cuenta que obtenemos IntelliSense a través del límite servidor/cliente:

*Figura 5.15 – IntelliSense a través del límite servidor/cliente*

2. Elimine temporalmente el atributo `icon` del JSX. Tenga en cuenta que obtenemos un error de tipo.
3. Haga clic derecho en `ColorModeToggle` y elija la opción **Go to Definition**. Accederá a la definición de `ColorModeToggle`.

Sorprendentemente, este tipo de características funcionan en estos dos mundos: nos brindan grandes beneficios de productividad. ¡No parecen dos mundos diferentes en absoluto!

Eso concluye esta sección sobre la composición de RSCs con Client Components. He aquí un resumen:
- Se recomienda iniciar una nueva página de Next.js con RSCs por simplicidad y rendimiento. Utilice Client Components solo cuando se necesite funcionalidad adicional, como la interactividad.
- Se crea un límite de cliente cuando un componente tiene una directiva `'use client'`. Todos los archivos que se importan después se convierten en Client Components.
- Si bien un Client Component no puede importar directamente un RSC, se puede pasar un RSC como una prop.

A continuación, resumiremos lo que hemos aprendido en este capítulo.

---

### Resumen

En este capítulo, aprendimos que los RSCs se lanzaron por completo en React 19 y se ejecutan exclusivamente en el servidor. A diferencia de los Client Components, los RSCs pueden ser asincrónicos e interactuar directamente con los recursos del servidor, como las bases de datos.

Los principales beneficios de los RSCs incluyen un rendimiento mejorado, ya que reducen la cantidad de JavaScript que se envía al cliente y disminuyen la cantidad de llamadas de red realizadas desde el navegador. La productividad del desarrollador también mejora porque los RSCs permiten coubicar el código de frontend y backend, eliminando la necesidad de cambiar entre diferentes paradigmas.

Los Client Components son esenciales para la interactividad, ya que admiten React Hooks y acceso a APIs del navegador. Se ejecutan en el cliente después del renderizado inicial del servidor. Los desarrolladores pueden declarar un componente como Client Component agregando la directiva `'use client'`. Los RSCs se pueden anidar dentro de Client Components pasándolos como props.

Next.js es un framework popular basado en React que admite RSCs, donde los componentes son RSCs por defecto. Creamos una aplicación Next.js y creamos un RSC `Header` con un Client Component `ColorModeToggle` anidado en su interior. Luego anidamos un RSC `ColorModeIcon` dentro de `ColorModeToggle` pasándolo como una prop.

En el próximo capítulo, aprenderemos más sobre Next.js para que podamos crear una aplicación de varias páginas.

---

### Lecturas adicionales

Para obtener más información sobre los RSCs, consulte la documentación de React en [https://react.dev/reference/rsc/server-components](https://react.dev/reference/rsc/server-components).

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido en este capítulo:

1. Tenemos una aplicación Next.js que tiene el siguiente RSC:

```tsx
import Counter from “@/components/Counter”; export default function Home() { return ( <div> <Counter /> </div> ); }
```

El componente `Counter` es el siguiente:

```tsx
import { useState } from “react”; export default function Counter() { const [count, setCount] = useState(1); return ( <button onClick={() => setCount((prev) => prev + 1)} > {count} </button> ); }
```

Se produce un error de compilación. ¿Cuál es el problema?

2. ¿Se ejecuta un RSC en el cliente? ¿Se ejecuta un Client Component en el servidor?

3. Tenemos el siguiente componente, pero se produce un error cuando se renderiza. ¿Cuál es el problema?

```tsx
“use client”; export async function PeopleList() { const people = await getPeople(); return ( <ul> {people.map((person) => ( <li key={person}> <span>{person}</span> </li> ))} </ul> ); }
```

4. En una aplicación Next.js, ¿es posible anidar un RSC dentro de un Client Component?

5. En Next.js, ¿cuál de los siguientes elementos no está permitido dentro de un RSC?
   - `fetch`
   - `useEffect`
   - `async` / `await`

---

### Respuestas

1. Aquí, `Counter.tsx` debe ser un Client Component porque requiere estado (`useState`). Sin embargo, es un RSC porque se importa en un archivo RSC. Por lo tanto, se debe colocar la siguiente directiva en la parte superior del archivo para convertirlo en un Client Component:

```typescript
“use client”;
```

2. Un RSC solo se ejecuta en el servidor; no se ejecuta en el cliente. Un Client Component puede ejecutarse en el servidor (para el pre-renderizado) y luego hidratarse en el cliente.

3. Los Client Components no pueden ser asincrónicos (`async`). Por lo tanto, el componente se puede cambiar a un RSC si se importa a un RSC. Si necesita ser un Client Component, la llamada a `getPeople` se puede realizar utilizando el Hook `useEffect`. Un ejemplo sencillo se muestra aquí:

```tsx
export function PeopleList() { const [people, setPeople] = useState<string[]>([]); useEffect(() => { getPeople().then((p) => { setPeople(p); }); }, []); return ...; }
```

4. Sí, si el RSC se pasa al Client Component como una prop. No se puede importar un RSC directamente en un archivo de Client Component.

5. El Hook `useEffect`, porque `useEffect` solo se ejecuta en el cliente.
