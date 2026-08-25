# Parte 3: Datos

## Capítulo 9: Trabajo con formularios

Los formularios son extremadamente comunes en las aplicaciones, por lo que es esencial poder implementarlos de manera eficiente. En este capítulo, crearemos un formulario de contacto que se ve a menudo en los sitios web de las empresas en Next.js. Contendrá un puñado de campos y algo de lógica de validación.

Comenzaremos con una implementación básica de formulario utilizando las capacidades nativas del navegador. Revisaremos el envío del formulario para usar un Route Handler y luego una React Server Action introduciendo la validación del servidor. Luego implementaremos un indicador de envío y un mejor manejo de errores utilizando el estado del formulario de React y el estado de la acción. Usaremos una popular biblioteca llamada **React Hook Form** para agregar validación del lado del cliente. Por último, implementaremos actualizaciones optimistas (*optimistic updates*) para una experiencia de usuario aún mejor.

Al final de este capítulo, podrá crear formularios robustos y fáciles de usar en React.

Cubriremos los siguientes temas:

- Uso de formularios básicos
- Uso de un Route Handler para el envío
- Uso de una Server Action para el envío
- Uso de useFormStatus
- Uso de useActionState
- Uso de React Hook Form
- Implementación de actualizaciones optimistas

---

### Requisitos técnicos

Utilizaremos las siguientes herramientas en este capítulo:

- **Navegador**: Un navegador moderno como Google Chrome
- **Node.js y npm**: Puede instalarlos desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
- **Visual Studio Code**: Puede instalarlo desde [https://code.visualstudio.com/](https://code.visualstudio.com/)

Todos los fragmentos de código utilizados en este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter09](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter09)

---

### Uso de formularios básicos

En esta sección, crearemos un proyecto de Next.js y la primera iteración del formulario de contacto. También crearemos una base de datos que eventualmente contendrá los datos del formulario.

#### Creación del proyecto

Comencemos creando un proyecto de Next.js y una base de datos SQLite. Lleve a cabo los siguientes pasos:

1. En una terminal, ejecute el siguiente comando para crear el proyecto:

```bash
npx create-next-app@latest forms --ts --eslint --app --src-dir --import-alias “@/*” --no-tailwind --no-turbopack
```

2. Aún en la terminal, muévase a la carpeta del proyecto y abra Visual Studio Code usando los siguientes comandos:

```bash
cd forms code .
```

Prettier se puede configurar de la misma manera que aprendimos con Vite en el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*. Siéntase libre de agregar el formato de código automático a este proyecto.

3. Instale la dependencia de libSQL ejecutando el siguiente comando en una terminal:

```bash
npm i @libsql/client
```

4. Cree un script que ejecutaremos para crear nuestra base de datos. Cree una carpeta llamada `scripts` en la carpeta `src` y luego un archivo llamado `createDatabase.mjs` en esta carpeta. Copie el script del repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter09/start/src/scripts/createDatabase.mjs](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter09/start/src/scripts/createDatabase.mjs) y péguelo en el archivo.
5. Cree un archivo `.env` en la raíz del proyecto y agregue la URL a la base de datos:

```env
DB_URL=file:src/data/forms.db
```

6. Cree una carpeta en `src` llamada `data` para la ubicación de la base de datos. Para crear la base de datos, ejecute el siguiente comando en la terminal:

```bash
node src/scripts/createDatabase.mjs
```

Este comando ejecuta el script usando Node.js. Una vez completado el comando, aparecerá un archivo de base de datos `forms.db` en la carpeta `data`.

7. Abra `src/app/globals.css` y sobrescriba el contenido con el CSS del siguiente archivo en el repositorio de GitHub:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter09/start/src/app/globals.css](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter09/start/src/app/globals.css).

Esto aplicará estilos atractivos a nuestra aplicación.

8. Limpiemos la página de inicio. Abra `page.tsx` en la carpeta `src/app` y reemplace su contenido con lo siguiente:

```tsx
export default function Home() { return ( <main> </main> ); }
```

9. En la terminal, ejecute el siguiente comando para ejecutar la aplicación en modo de desarrollo:

```bash
npm run dev
```

La aplicación estará disponible en un navegador en [http://localhost:3000](http://localhost:3000/) y mostrará una pantalla en blanco.

Eso completa la configuración del proyecto. A continuación, crearemos un formulario de contacto básico en la aplicación.

#### Creación de un formulario nativo

Lleve a cabo los siguientes pasos para crear un formulario de contacto HTML nativo:

1. Abra `src/app/page.tsx` y agregue los siguientes elementos dentro del elemento `main`:

```tsx
import { ContactForm } from ‘@/components/ContactForm’; export default function Home() { return ( <main> <h2>Contact Us</h2> <p> If you enter your details we will get back to you as soon as we can. </p> <ContactForm /> </main> ); }
```

La página muestra un título, algunas instrucciones y un componente `ContactForm`.

El componente `ContactForm` aún no se ha implementado, por lo que se produce un error de compilación. Crearemos esto en el siguiente paso.

2. Cree una carpeta llamada `components` en la carpeta `src` y un archivo llamado `ContactForm.tsx` en ella. Agregue el siguiente contenido al archivo:

```tsx
export function ContactForm() { return <form></form>; }
```

3. Agregue los siguientes campos dentro del elemento `form`:

```tsx
<form> <div className=”field”> <label htmlFor=”name”>Your name</label> <input type=”text” id=”name” name=”name” /> </div> <div className=”field”> <label htmlFor=”email”>Your email address</label> <input type=”email” id=”email” name=”email” /> </div> <div className=”field”> <label htmlFor=”reason”>Reason you need to contact us </label> <select id=”reason” name=”reason”> <option value=””></option> <option value=”Support”>Support</option> <option value=”Feedback”>Feedback</option> <option value=”Other”>Other</option> </select> </div> <div className=”field”> <label htmlFor=”notes”>Additional notes</label> <textarea id=”notes” name=”notes” /> </div> </form>
```

> [!NOTE]
> Un recordatorio de que el código se puede copiar desde [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter09/basic-form/src/components/ContactForm.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter09/basic-form/src/components/ContactForm.tsx).

Hemos agregado campos para el nombre del usuario, la dirección de correo electrónico, el motivo del contacto y notas adicionales. Cada etiqueta de campo está asociada con su editor estableciendo el atributo `htmlFor` con el valor del `id` del editor. Esto ayuda a las tecnologías de asistencia, como los lectores de pantalla, a leer las etiquetas cuando los campos reciben el foco.

El atributo `name` en los editores de campo permite extraer los valores de los campos en el proceso de envío del formulario.

4. Agregue un botón Submit en la parte inferior del elemento `form` de la siguiente manera:

```tsx
<form> ... <button type=”submit”>Submit</button> </form>
```

Eso completa nuestro formulario básico. En la aplicación en ejecución, complete el formulario y envíelo. Verá los valores agregados a la URL como parámetros de búsqueda. También puede notar que se produce una recarga completa de la página.

*Figura 9.1 – Formulario enviado*

Después de enviar el formulario, no hay ninguna indicación para el usuario de que el proceso fue exitoso. Agregue el siguiente atributo `action` al elemento `form` para navegar a una página Thanks después de enviar el formulario:

```tsx
<form action=”thanks”>
```

5. Creemos la página Thanks creando una nueva carpeta `thanks` en la carpeta `src/app`. Cree un archivo `page.tsx` dentro de esta carpeta con el siguiente contenido:

```tsx
export default async function Thanks({ searchParams, }: { searchParams: Promise<{ [key: string]: string | string[] | undefined; }>; }) { return ( <main> <h2>Form successfully submitted</h2> Thank you {(await searchParams).name}, we will be in touch soon. </main> ); }
```

El componente informa al usuario que el formulario se ha enviado correctamente y le da las gracias. Su nombre se obtiene del parámetro de búsqueda `name`.

6. Complete el formulario nuevamente y envíelo. Será navegado a la página Thanks. Sin embargo, todavía estamos obteniendo una recarga completa de la página; abordaremos esto en el siguiente paso.
7. De vuelta en `ContactForm.tsx`, podemos usar el componente `Form` de Next.js para evitar la recarga completa de la página, de la siguiente manera:

```tsx
import Form from ‘next/form’; export function ContactForm() { return ( <Form ... > ... </Form> ); }
```

8. Complete el formulario una vez más y envíelo. Ya no se produce una recarga completa de la página al navegar a la página Thanks.

Eso completa nuestro formulario básico, que utiliza en gran medida características nativas de HTML. Usamos el componente `Form` de Next.js para optimizar el rendimiento del envío del formulario.

A continuación, integraremos el formulario con un Route Handler de Next.js.

---

### Uso de un Route Handler para el envío

En esta sección, cambiaremos el formulario para enviar los datos a una API web. Usaremos un Route Handler para crear la API, sobre la cual aprendimos en el [Capítulo 8](https://subscription.packtpub.com/book/web-development/9781836643173/8), *Obtención de datos en Client Components y mutaciones con TanStack Query*. Enviar datos a APIs ha sido una práctica común durante muchos años porque evita una recarga completa de la página.

#### Creación de una mutación de base de datos

Crearemos una función que agregue los datos del formulario a nuestra base de datos. Esto eventualmente se usará en el Route Handler. Lleve a cabo los siguientes pasos:

1. Primero, crearemos un tipo para representar los datos. Cree un archivo llamado `schema.ts` en la carpeta `data` con el siguiente contenido:

```typescript
export type Contact = { name: string; email: string; reason: string; notes: string; };
```

2. Cree un nuevo archivo llamado `insertContact.ts` en la carpeta `src/data` que contenga el siguiente contenido:

```typescript
import { createClient, type Client, } from ‘@libsql/client’; import { Contact } from ‘./schema’; export async function insertContact({ name, email, reason, notes, }: Contact) { let client: Client | undefined; let ok = true; }
```

El archivo importa la función `createClient` y el tipo `Client` necesarios del paquete libSQL, así como el tipo `Contact` que acabamos de crear. La implementación de la función `insertContact` se ha iniciado definiendo un parámetro para los datos del formulario. También se han declarado variables para la conexión a la base de datos y si la mutación de la base de datos es exitosa.

3. Después de las declaraciones de variables, conéctese a la base de datos y ejecute el SQL para insertar el registro en la tabla `contact`:

```typescript
export async function insertContact( ... ) { ... try { client = createClient({ url: process.env.DB_URL ?? ‘’, }); await client.execute({ sql: ‘INSERT INTO contact(name, email, reason, notes) VALUES (?, ?, ?, ?)’, args: [name, email, reason, notes], }); } catch { ok = false; } }
```

4. Finalice la implementación de `insertContact` asegurándose de que la conexión a la base de datos esté cerrada y se devuelva el éxito de la operación. El siguiente es el código actualizado:

```typescript
export async function insertContact( ... ) { ... if (client) { client.close(); } return { ok }; }
```

Con esto concluye la función para mutar la base de datos. A continuación, implementaremos el Route Handler.

#### Creación de un Route Handler

Lleve a cabo los siguientes pasos para crear el Route Handler:

1. Cree una carpeta `api` en la carpeta `src/app` y luego un archivo `route.ts` dentro de ella, para que el Route Handler maneje las solicitudes HTTP a la ruta `/api`.
2. Agregue el siguiente código a `route.ts`:

```typescript
import { type NextRequest } from ‘next/server’; import { insertContact } from ‘@/data/insertContact’; export async function POST(request: NextRequest) { const data = await request.json(); const result = await insertContact(data); if (result.ok) { return Response.json( {}, { status: 201 }, ); } return Response.json({}, { status: 500 }); }
```

El Route Handler maneja solicitudes HTTP POST. El manejador obtiene los datos del formulario del cuerpo de la solicitud y llama a la mutación de base de datos `insertContact`, pasando los datos del formulario. Si la mutación es exitosa, se devuelve el código de estado HTTP Created (código de estado 201). Si la mutación falló, se devuelve el código de estado HTTP Internal Server Error (código de estado 500).

Eso completa el Route Handler. A continuación, integraremos este Route Handler en nuestro formulario.

#### Integración del envío del formulario con el Route Handler

Para integrar el Route Handler en el formulario, escribiremos código del lado del cliente para el manejador de envío del formulario, reemplazando la acción del formulario. También eliminaremos el uso del componente `Form` de Next.js, ya que ya no es necesario. Abra `ContactForm.tsx` y lleve a cabo los siguientes pasos:

1. Elimine la importación de `Form` de Next.js y reemplace el componente `Form` en el JSX con un elemento `form`.
2. Haga que el componente sea un Client Component agregando `'use client'` en la parte superior del archivo. Esto se debe a que vamos a implementar un manejador de eventos.
3. Cambie el atributo `action` en el elemento `form` para hacer referencia a un manejador de acción, de la siguiente manera:

```tsx
export function ContactForm() { async function handleAction( formData: FormData) {} return ( <form action={handleAction}> </form> ); }
```

La función `handleAction` ahora se invocará cuando se envíe el formulario. Ahora es nuestra responsabilidad enviar los datos del formulario al servidor.

El parámetro de la función `handleAction` es del tipo `FormData`. `FormData` es una interfaz nativa del navegador que permite el acceso a los valores de un formulario y recibe un elemento de formulario en su parámetro de constructor. Para obtener más información sobre `FormData`, consulte [https://developer.mozilla.org/en-US/docs/Web/API/FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData).

4. Agregue las siguientes líneas al manejador de acción para obtener los datos en un objeto compatible con el Route Handler:

```tsx
import { Contact } from ‘@/data/schema’; ... export function ContactForm() { async function handleAction( ... ) { const contact = Object.fromEntries( formData, ) as Contact; } }
```

Usamos el método `Object.fromEntries` para convertir `formData` en un objeto simple con propiedades que contienen los valores de los campos.

También utilizamos una aserción de tipo para tipar el objeto `contact`, lo cual no es ideal. No es ideal porque en realidad no verifica que `formData` contenga las propiedades y tipos de valores que debería tener `Contact`. Mejoraremos esto más adelante en este capítulo.

5. En las siguientes líneas del manejador de envío, realice una solicitud HTTP a la API del Route Handler y, si se produce un error, genere un error de consola:

```typescript
async function handleAction( ... ) { ... const response = await fetch(‘api’, { method: ‘POST’, body: JSON.stringify(contact), }); if (!response.ok) { console.error(‘Something went wrong’); return; } }
```

6. El paso final en el manejador de envío es navegar a la página Thanks. Podemos realizar la navegación del lado del cliente utilizando el Hook `useRouter` de Next.js, de la siguiente manera:

```tsx
import { useRouter } from ‘next/navigation’; ... export function ContactForm() { const { push } = useRouter(); async function handleAction( ... ) { ... push( ‘/thanks/?name=’ + encodeURIComponent(contact.name), ); } return ... }
```

Usamos la función `push` del Hook `useRouter` para navegar, pasando la ruta a la página Thanks. Incluimos `name` como un parámetro de búsqueda usando `encodeURIComponent` para escapar los caracteres especiales que el usuario pueda haber ingresado en su nombre.

7. En la aplicación en ejecución, complete el formulario y envíelo. Puede verificar que los datos se guardaron en la base de datos utilizando la extensión SQLite de Visual Studio Code que usamos en el [Capítulo 8](https://subscription.packtpub.com/book/web-development/9781836643173/8), *Obtención de datos en Client Components y mutaciones con TanStack Query*.

Eso completa la integración del formulario con un Route Handler. He aquí un resumen rápido:
- Se puede evitar una recarga completa de la página manejando el evento `action` del elemento de formulario.
- La acción puede enviar los datos del formulario a través de una API web, que se puede implementar utilizando un Route Handler de Next.js.
- El Hook `useRouter` de Next.js se puede utilizar para navegar a una página de éxito de envío.

Hay otra forma más moderna de enviar formularios. Cubriremos esto a continuación.

---

### Uso de una Server Action para el envío

En esta sección, comprenderemos qué es una **Server Action**. Luego usaremos una Server Action en nuestro formulario.

#### Comprender las Server Actions

En el [Capítulo 7](https://subscription.packtpub.com/book/web-development/9781836643173/7), *Obtención de datos con Server Components y mutaciones con Server Functions*, aprendimos cómo mutar datos con Server Functions. Podemos usar una Server Function para el envío de formularios. De hecho, las Server Functions utilizadas para el envío de formularios son tan comunes que tienen un nombre especial llamado **Server Actions**.

Se puede pasar una Server Action a un elemento `form` en su prop `action` de la siguiente manera:

```tsx
<form action={serverAction}>
```

Anteriormente en este capítulo, usamos la prop `action` para la ruta de la página a la que enviar. Luego usamos la prop `action` para implementar un manejador de envío del lado del cliente. Aquí, estamos enviando directamente a una Server Action. Por lo tanto, la Server Action deberá contener cualquier navegación de página requerida.

Un beneficio de utilizar una React Server Action para el envío de formularios es que **funciona sin JavaScript**. Esto es útil para aplicaciones web utilizadas en teléfonos móviles en lugares donde la conexión de red es lenta. En esta situación, el formulario puede estar disponible para que el usuario lo complete y lo envíe antes de que se haya hidratado con JavaScript.

Una Server Action debe tener un parámetro de función del tipo `FormData` y no debe devolver nada:

```typescript
export async function serverAction(formData: FormData) { // Guardar los datos // No devolver nada }
```

A continuación, revisaremos nuestro formulario para utilizar una Server Action.

#### Uso de una Server Action en ContactForm

Convertiremos `insertContact` en una Server Function y la usaremos como una Server Action desde nuestro formulario. También agregaremos algo de validación del lado del servidor al formulario. Lleve a cabo los siguientes pasos:

1. Comience eliminando el Route Handler en `src/app/api/route.ts` porque ya no se usará en este capítulo y el código se romperá con los cambios que realizaremos en la función `insertContact`.
2. Abra `insertContact.ts` y agregue una directiva `'use server'` en la parte superior del archivo. Esto marca la función `insertContact` como una Server Function.
3. Ajuste el parámetro de la función `insertContact` para que reciba `FormData` y luego extraiga los valores de los campos de él:

```typescript
export async function insertContact( formData: FormData ) { const { name, email, reason, notes } = Object.fromEntries(formData) as Contact; let client: Client | undefined; ... }
```

Por lo tanto, ahora estamos interactuando con `FormData` en el servidor, en lugar de en el cliente.

4. También necesitamos eliminar la declaración `return` en la función `insertContact`.
5. Necesitamos realizar la navegación en el servidor ahora. Podemos usar la función `redirect` de Next.js para hacer esto. Agregue una llamada a `redirect` al final de la función `insertContact` si la mutación es exitosa:

```typescript
import { redirect } from ‘next/navigation’; ... export async function insertContact( ... ) { ... if (client) { client.close(); } if (ok) { redirect( `/thanks/?name=${encodeURIComponent(name)}`, ); } }
```

6. Abra `ContactForm.tsx`, elimine el uso del Hook `useRouter` y establezca el atributo `action` en el elemento `form` en la Server Function `insertContact`. La función `handleAction` y la importación del tipo `Contact` ahora se pueden eliminar. El archivo ahora debería ser el siguiente:

```tsx
‘use client’; import { insertContact } from ‘@/data/insertContact’; export function ContactForm() { return ( <form action={insertContact}> ... </form> ) }
```

La directiva `'use client'` también podría eliminarse porque ahora no hay ningún manejador de eventos. Sin embargo, la dejaremos en su lugar porque usaremos React Hooks en este componente en la siguiente sección.

7. En la aplicación en ejecución, si completa y envía el formulario, se comportará como antes.
8. Intente desactivar JavaScript en el navegador y complete y envíe el formulario nuevamente. JavaScript se puede desactivar en Chrome abriendo la ventana de comandos y eligiendo el comando *Disable Javascript*. La ventana de comandos se puede abrir presionando `Shift + Command + P` en una Mac y `Shift + Ctrl + P` en Windows.

*Figura 9.2 – Desactivación de JavaScript en Chrome*

Descubrirá que el formulario se envía y se guarda en la base de datos sin problemas.

9. Vuelva a habilitar JavaScript en el navegador antes de continuar usando el comando *Enable Javascript* en la ventana de comandos.
10. Detenga la ejecución de la aplicación presionando `Ctrl + C`.

Eso completa la reelaboración del formulario para utilizar una Server Action. La implementación es mucho más simple que el enfoque anterior de Route Handler.

Por el momento, no hay validación en nuestro formulario. Por ejemplo, podemos enviarlo sin ingresar ninguna información. Abordaremos esto a continuación.

#### Adición de validación en el servidor

La validación del lado del servidor no solo ayuda a los usuarios a completar los formularios correctamente, sino que también ayuda a proteger contra ataques de seguridad como la inyección de SQL y solicitudes con formato incorrecto. Esto se debe a que los usuarios malintencionados pueden manipular, eludir o desactivar por completo la entrada del lado del cliente.

Agregaremos validación del lado del servidor utilizando la biblioteca Zod. Usamos Zod en el [Capítulo 8](https://subscription.packtpub.com/book/web-development/9781836643173/8), *Obtención de datos en Client Components y mutaciones con TanStack Query*, para validar datos de bases de datos y APIs web. Podemos usarlo para validar también los datos del formulario:

1. Instale Zod en el proyecto ejecutando el siguiente comando en una terminal:

```bash
npm i zod
```

2. Abra el archivo `schema.ts`. Reemplace el tipo `Contact` con el siguiente esquema de Zod:

```typescript
import { z } from ‘zod’; export const contactSchema = z.object({ name: z .string() .min(1, { message: ‘You must enter your name’, }) .max(50, { message: ‘The name must be less than 50 characters’, }), email: z.string().email({ message: ‘You must enter a valid email address’, }), reason: z.string().min(1, { message: ‘You must enter a reason’, }), notes: z.string().optional(), });
```

El esquema define los campos de nuestro formulario como cadenas. Todos los campos están definidos como obligatorios, excepto `notes`. El campo `name` admitirá un máximo de 50 caracteres y el campo `email` debe tener un formato de correo electrónico válido.

3. En `insertContact.ts`, elimine la declaración de importación de `Contact` y la aserción de tipo que usa `Contact` en la función `insertContact`.
4. En la función `insertContact`, valide los valores de los campos de la siguiente manera:

```typescript
import { contactSchema } from ‘./schema’; ... export async function insertContact( ... ) { const parsedResult = contactSchema.safeParse( Object.fromEntries(formData), ); if (!parsedResult.success) { return; } const { name, email, reason, notes } = parsedResult.data; ... }
```

Llamamos a la función `safeParse` del esquema para que no se genere automáticamente un error si el formulario no es válido. Esto se debe a que eventualmente manejaremos mejor los errores. Por el momento, si el formulario no es válido, hacemos un cortocircuito en la función y simplemente no devolvemos nada.

Luego extraemos los valores de los campos de la propiedad `data` del objeto devuelto por `safeParse`.

5. Ahora obtenemos un error de tipo en el comando SQL porque la base de datos no acepta `undefined` en el campo `notes`. Para resolver esto, convierta `undefined` en `null` en el argumento `notes` de la siguiente manera:

```typescript
await client.execute({ sql: ..., args: [name, email, reason, notes ?? null], });
```

6. Ejecute la aplicación ingresando el comando `npm run dev` en una terminal. Si no completa el formulario correctamente y lo envía, los datos no se guardarán en la base de datos. Sin embargo, no se muestra ningún error al usuario. Además, estamos haciendo que el usuario vuelva a ingresar todo el formulario. Resolveremos estos problemas más adelante en el capítulo.

Eso completa la implementación de la validación del servidor y también esta sección sobre el uso de Server Actions para el envío de formularios. He aquí un resumen rápido:
- Las Server Actions agilizan el envío de formularios al permitir el manejo de los datos del formulario del lado del servidor, eliminando la necesidad de manejadores de envío del lado del cliente. Estos pueden funcionar sin JavaScript, lo que los convierte en una solución sólida para escenarios de baja conectividad.
- La Server Action debe tener un parámetro de tipo `FormData`, que contendrá los valores de los campos.
- El atributo `action` en el elemento `form` se establece en la Server Action para que el formulario se envíe a ella.
- Zod se usa comúnmente para validar los datos del formulario antes de guardarlos en una base de datos.

Aunque hemos mejorado el formulario agregando validación en el servidor, la experiencia de usuario aún no es excelente. Mejoraremos esto en la siguiente sección.

---

### Uso de useFormStatus

En esta sección, comprenderemos cómo el Hook `useFormStatus` de React puede mejorar la experiencia de usuario y luego lo utilizaremos dentro de nuestro formulario.

#### Comprender useFormStatus

El Hook `useFormStatus` permite el acceso a la información de un envío de formulario en un Client Component. Sus usos principales son implementar un indicador de envío y deshabilitar ciertos elementos del formulario, como el botón Submit, durante el envío. A diferencia de otros Hooks de React, este proviene del paquete `react-dom`.

La sintaxis de `useFormStatus` es la siguiente:

```typescript
const { pending, data } = useFormStatus();
```

El Hook devuelve un objeto que contiene las siguientes propiedades:
- `pending`: Si el envío del formulario está en curso.
- `data`: Da acceso a los valores de campo para el formulario que se envía en una estructura `FormData`. `data` es `null` si el envío no está en curso.

Una restricción del Hook `useFormStatus` es que **debe llamarse en un componente secundario (hijo)** del componente que contiene un elemento `form`. Por lo tanto, el siguiente código no funcionará:

```tsx
function Form() { const { pending } = useFormStatus(); return ( <form> ... {pending && <p>Submitting ...</p>} <button type=”submit” disabled={pending}> Submit </button> </form> ); }
```

En cambio, el uso de `useFormStatus` debe extraerse y colocarse como un hijo:

```tsx
function Form() { return ( <form> ... <SubmitButtion /> </form> ); } function SubmitButtion { const { pending } = useFormStatus(); return ( <> {pending && <p>Submitting ...</p>} <button type=”submit” disabled={pending}> Submit </button> </> ); }
```

Ahora que entendemos cómo usar `useFormStatus`, usaremos este Hook en nuestro formulario.

#### Uso de useFormStatus

Agregaremos un indicador de envío a nuestro formulario usando `useFormStatus`. Abra `ContactForm.tsx` y lleve a cabo los siguientes pasos:

1. Importe `useFormStatus` de React DOM:

```typescript
import { useFormStatus } from ‘react-dom’;
```

2. Cree un nuevo componente en la parte inferior de `ContactForm.tsx`, de la siguiente manera:

```tsx
function SubmitButton() { const { pending } = useFormStatus(); return ( <> {pending && <p role=”alert”>Saving ...</p>} <button type=”submit” disabled={pending}> Submit </button> </> ); }
```

El componente contiene un indicador de envío y un botón Submit. Usamos `pending` de `useFormStatus` para mostrar el indicador de envío y deshabilitar el botón Submit durante el envío del formulario.

3. En el componente `ContactForm`, reemplace el botón Submit actual con nuestra versión mejorada:

```tsx
export function ContactForm() { return ( <form ... > ... <SubmitButton /> </form> ); }
```

4. Intente completar el formulario en la aplicación en ejecución. Para ver el indicador de envío, limite la red (*throttle*) utilizando las herramientas de desarrollo del navegador. El proceso de envío será entonces lo suficientemente lento como para que pueda ver el indicador de envío.

Eso completa la implementación del indicador de envío y esta sección sobre `useFormStatus`. He aquí un resumen rápido:
- El Hook `useFormStatus` se puede utilizar para implementar indicadores de envío en un formulario.
- Una desventaja de `useFormStatus` es que debe estar en un componente hijo del elemento `form`.

Hay un Hook alternativo que podemos usar para implementar un indicador de envío de formulario, sobre el cual aprenderemos en la siguiente sección.

---

### Uso de useActionState

Nuestro formulario todavía no muestra errores de validación y aún borra los valores de los campos después de un envío no válido. En esta sección, comprenderemos cómo el Hook `useActionState` de React puede abordar estos problemas. Luego usaremos este Hook dentro de nuestro formulario.

#### Comprender useActionState

El Hook `useActionState` permite que un Client Component acceda al resultado de una Server Action. Esto permite que se muestren mensajes de error al usuario. También permite que los valores de los campos se mantengan en el formulario después del envío.

La sintaxis de `useActionState` es la siguiente:

```typescript
const [ actionState, formAction, isPending, ] = useActionState(serverAction, initialState);
```

Al Hook se le pasa una referencia a la Server Action y un valor de estado inicial como argumentos.

El Hook devuelve un array que contiene los siguientes elementos ordenados:
- `actionState`: El valor actual del estado de la acción.
- `formAction`: Una acción para vincular al elemento `form`.
- `isPending`: Si la Server Action está en curso.

Los nombres de los elementos del array pueden ser cualquier nombre significativo de nuestra elección. La estructura del estado de la acción también es nuestra elección, pero normalmente contiene al menos un mensaje y una copia de los valores de los campos.

La Server Action debe tener parámetros particulares y debe devolver el nuevo valor de estado cuando se usa con `useActionState`:

```typescript
export async function serverAction( previousState: { message: string, formData: FormData }, formData: FormData, ) { ... return { message, formData } }
```

Los parámetros deben ser el estado anterior, seguido de los datos del formulario. A menudo, el estado anterior no se necesita en la implementación de Server Action, pero React requiere este parámetro.

A continuación, usaremos este Hook en nuestro formulario.

#### Uso de useActionState

Usaremos el Hook `useActionState` en nuestro formulario para manejar mejor los errores y evitar la pérdida de valores de campos ya ingresados. Realizaremos los cambios necesarios en la Server Action `insertContact` antes de reelaborar el componente `ContactForm`.

#### Devolver el estado desde la Server Action

Abra `insertContact.ts` y lleve a cabo los siguientes pasos para devolver el estado desde la Server Action `insertContact`:

1. Comience agregando un tipo para representar el estado de la acción:

```typescript
type ActionState = { ok: boolean; error: string; formData: FormData; };
```

La propiedad `ok` determina si la mutación fue exitosa. La propiedad `error` contendrá el mensaje de error si la validación o la mutación fallan. La propiedad `formData` es una copia del objeto `FormData` del formulario, que contiene los valores de los campos actuales.

2. Agregue un parámetro `previousState` a la función `insertContact`:

```typescript
export async function insertContact( previousState: ActionState, formData: FormData, ) { ... }
```

3. Devuelva el estado de acción correspondiente si los datos del formulario no son válidos:

```typescript
export async function insertContact( ... ) { const parsedResult = contactSchema.safeParse( ... ); if (!parsedResult.success) { return { ok: false, error: ‘Unable to save - invalid field values’, formData, }; } ... }
```

4. Establezca una variable de error si falla la mutación de la base de datos:

```typescript
let error = ‘’; try { client = createClient( ... ); await client.execute( ... ); } catch { ok = false; error = ‘Problem saving form’; }
```

5. Por último, devuelva el estado de la acción al final de la función `insertContact`:

```typescript
if (ok) { redirect( ... ); } return { ok, error, formData };
```

Eso completa los cambios en la Server Action.

#### Adición del estado de la acción al formulario

Haremos cambios en `ContactForm.tsx` ahora, haciendo uso del Hook `useActionState`. Vamos a eliminar el uso de `useFormStatus` y en su lugar usaremos la variable `isPending` de `useActionState`. Abra `ContactForm.tsx` y realice los siguientes cambios:

1. Comience eliminando la declaración de importación de `useFormStatus` e importando el Hook `useActionState` de React:

```typescript
import { useActionState } from ‘react’; 
```

2. Elimine el componente `SubmitButton` de la parte inferior del archivo y reemplace la referencia a él en `ContactForm` con el siguiente elemento de botón:

```tsx
<form ...> ... <button type=”submit”>Submit</button> </form>
```

3. En el componente `ContactForm`, llame a `useActionState`, de la siguiente manera, antes de la sentencia `return`:

```tsx
export function ContactForm() { const [ { ok, error, formData }, formAction, isPending, ] = useActionState(insertContact, { ok: false, error: ‘’, formData: new FormData(), }); return ... }
```

La Server Action y el estado inicial se pasan al Hook. El estado de la acción devuelto se desestructura en las variables `ok`, `error` y `formData`. También desestructuramos la acción del formulario en una variable `formAction` y el indicador de pendiente en una variable `isPending`.

4. La variable `formAction` ahora se puede establecer en el elemento `form`:

```tsx
<form action={formAction}>
```

5. Para retener los valores de los campos, establecemos el atributo `defaultValue` en los elementos del editor en el valor correspondiente del valor `formData` del estado de la acción:

```tsx
<input ... name=”name” defaultValue={ (formData.get(‘name’) ?? ‘’) as string } /> ... <input ... name=”email” defaultValue={ (formData.get(‘email’) ?? ‘’) as string } /> ... <select ... name=”reason” defaultValue={ (formData.get(‘reason’) ?? ‘’) as string } > ... </select> ... <textarea ... name=”notes” defaultValue={ (formData.get(‘notes’) ?? ‘’) as string } />
```

Usamos la función `get` en el objeto de estado de acción `formData` para obtener el valor del campo correspondiente. Si no se ha ingresado un valor de campo, se devolverá `null`, por lo que lo fusionamos con una cadena vacía. Usamos una aserción de tipo string para mantener satisfecho al compilador de TypeScript.

6. Agregue una alerta de error después de los campos, justo encima del botón Submit:

```tsx
{!ok && ( <p role=”alert” className=”error”>{error}</p> )} <button type=”submit”>Submit</button>
```

7. Por último, agregue un indicador de envío después de la alerta de error, justo encima del botón Submit. Además, deshabilite el botón mientras el componente esté en un estado pendiente:

```tsx
{isPending && (<p role=”alert”>Saving ...</p>)} <button type=”submit” disabled={isPending}> Submit </button>
```

8. En la aplicación en ejecución, envíe el formulario sin ingresar nada. Se devuelve el error de validación:

*Figura 9.3 – Error de validación*

9. Intente completar parcialmente el formulario. Descubrirá que los valores de los campos ya no se pierden.
10. Para ver el indicador de envío, limite la red utilizando las herramientas de desarrollo del navegador. El proceso de envío será entonces lo suficientemente lento como para que pueda ver el indicador de envío.
11. También vale la pena comprobar que el formulario sigue funcionando con JavaScript desactivado: ¡descubrirá que sí lo hace!
12. La comprobación final es enviar un formulario válido. Verá que esto navega a la página Thanks como lo hacía antes.

Eso ha mejorado la experiencia de usuario de validación. Continuaremos mejorando esto aún más.

#### Adición de errores de campo

Actualmente, no estamos mostrando errores específicos para cada campo. Extraeremos los errores de campo del error de Zod y los devolveremos en una nueva pieza del estado de acción. Luego podremos renderizar estos errores debajo de cada campo.

Comenzaremos con una función para extraer los errores de campo del error de Zod. Colocaremos esta función en `insertContact.ts`. Lleve a cabo los siguientes pasos:

1. Abra `insertContact.ts` y agregue una declaración de importación para Zod:

```typescript
import { z } from ‘zod’;
```

2. Agregue una propiedad `errors` al tipo `ActionState`, de la siguiente manera. Esto contendrá los errores de validación del campo. Mantendremos la propiedad `error` para errores generales del formulario:

```typescript
type Err = { message: string }; type FieldErrors = { name: Err | null; email: Err | null; reason: Err | null; }; type ActionState = { ... errors: FieldErrors; };
```

Los errores de campo se almacenarán en un objeto con el nombre del campo como nombre de propiedad. El valor del error se establecerá en `null` si no hay ningún error. Si hay un error, el mensaje de error se almacenará en un objeto en una propiedad `message`.

3. Agregue una función en la parte inferior de `insertContact.ts` para extraer errores de campo de un error de Zod:

```typescript
function formatZodErrors(error: z.ZodError) { const formattedErrors: FieldErrors = { name: null, email: null, reason: null, }; }
```

Por el momento, la función inicializa los errores de campo a `null`.

4. Continúe con la implementación de la función e itere a través de los errores de Zod aplanados (*flattened*). Dentro del bucle, abra una rama condicional para errores en una estructura de matriz porque aquí es donde estarán los errores de campo:

```typescript
function formatZodErrors( ... ) { ... for (const [key, value] of Object.entries( error.flatten().fieldErrors, )) { if (Array.isArray(value)) { } } }
```

5. El paso final de implementación en la función es establecer los errores en las propiedades correspondientes en la variable `formattedErrors`:

```typescript
function formatZodErrors(error: unknown) { ... for ( ... ) { if ( ... ) { if (key === ‘name’) { formattedErrors.name = { message: value[0], }; } else if (key === ‘email’) { formattedErrors.email = { message: value[0], }; } else if (key === ‘reason’) { formattedErrors.reason = { message: value[0], }; } } } return formattedErrors; }
```

También devolvemos la variable `formattedErrors` que contiene los errores de campo.

Eso completa la función para extraer errores de campo. Ahora actualizaremos la función `insertContact` para devolver errores de campo en el estado de la acción:

6. Aún en `insertContact.ts`, use la función `formatZodErrors` para devolver errores de campo cuando ocurra un error de validación:

```typescript
export async function insertContact( ... ) { ... if (!parsedResult.success) { return { ok: false, error: ..., formData: ..., errors: formatZodErrors(parsedResult.error), }; } ... } 
```

Hemos dejado la propiedad `error` devuelta con el mensaje de error general para que este mensaje se represente encima del botón Submit.

7. En la parte inferior de la función `insertContact`, incluya la propiedad `errors` sin errores en el objeto devuelto:

```typescript
export async function insertContact( ... ) { ... return { ok, error, formData, errors: { name: null, email: null, reason: null, } }; }
```

La Server Action ahora devuelve errores de campo. Abramos `ContactForm.tsx` y hagamos los cambios necesarios para renderizar los errores de campo:

8. Comience pasando un valor de objeto `errors` inicial al Hook `useActionState` y desestructurando la variable `errors`:

```tsx
const [ { ok, error, errors, formData }, formAction, isPending, ] = useActionState(insertContact, { ok: false, error: ‘’, errors: { name: null, email: null, reason: null, }, formData: new FormData(), });
```

9. Debajo del componente `ContactForm`, agregue un nuevo componente `FieldError` que renderizará un error de validación del servidor para un campo:

```tsx
type Err = { message?: string } | null; function FieldError({ serverError, errorId }: { serverError: Err; errorId: string; }) { if (!serverError) { return null; } return ( <div id={errorId} role=”alert”> {serverError.message} </div> ); }
```

El error de validación se representa condicionalmente en un elemento `div` si el objeto de error tiene un valor. El elemento `div` tiene un atributo de rol de `"alert"` para que un lector de pantalla lo anuncie.

10. Volviendo al componente `ContactForm`, representaremos los errores de validación usando la variable `errors` y el componente `FieldError`. Comience agregando un error de validación debajo del input `name`:

```tsx
<div ... > <label ... >Your name</label> <input ... aria-invalid={errors.name ? ‘true’ : ‘false’} aria-describedby=”name-error” /> <FieldError serverError={errors.name} errorId=”name-error” /> </div>
```

El atributo `aria-describedby` en el elemento de entrada lo asocia con el elemento `div` que contiene el mensaje de error, lo que permite que un lector de pantalla anuncie el error cuando sea apropiado. El atributo `aria-invalid` informa a un lector de pantalla si la entrada está en un estado no válido o no.

11. Usaremos el mismo patrón en los campos `email` y `reason`:

```tsx
<div ... > <label ... >Your email address</label> <input ... aria-invalid={errors.email ? ‘true’ : ‘false’} aria-describedby=”email-error” /> <FieldError serverError={errors.email} errorId=”email-error” /> </div> <div ... > <label ... >Reason you need to contact us</label> <select ... aria-invalid={ errors.reason ? ‘true’ : ‘false’ } aria-describedby=”reason-error” > ... </select> <FieldError serverError={errors.reason} errorId=”reason-error” /> </div>
```

12. En la aplicación en ejecución, envíe el formulario sin ingresar nada. Ahora se devuelven los errores de validación debajo de cada campo no válido:

*Figura 9.4 – Errores de validación de campo*

13. Detenga la ejecución de la aplicación presionando `Ctrl + C`.

Eso completa la representación de los errores de validación de campos y esta sección sobre `useActionState`. He aquí un resumen rápido:
- El Hook `useActionState` se integra con una Server Action y permite representar errores de validación y retener los valores de los campos después del envío.
- El Hook también permite la implementación de un indicador de envío y la deshabilitación de elementos del formulario durante el envío.
- La Server Action debe contener parámetros para el estado de la acción anterior seguidos de los datos del formulario. También debe devolver el nuevo estado de la acción.

Por lo tanto, tanto `useActionState` como `useFormStatus` tienen capacidades superpuestas para implementar indicadores de envío y deshabilitar elementos del formulario durante el envío. Sin embargo, `useFormStatus` solo se puede usar en un componente hijo de un formulario, mientras que `useActionState` solo se puede usar en el mismo componente que el formulario. Por lo tanto, el enfoque de `useActionState` es preferible cuando los elementos del formulario están en el mismo componente, y el enfoque de `useFormStatus` es ideal cuando los elementos del formulario están separados en múltiples componentes.

A continuación, aprenderemos sobre una biblioteca de formularios popular que puede mejorar aún más la experiencia de usuario de nuestro formulario.

---

### Uso de React Hook Form

En esta sección, aprenderemos sobre **React Hook Form** y lo usaremos para mejorar la experiencia de usuario de validación en nuestro formulario de contacto. También aprenderemos sobre sus beneficios en comparación con la validación nativa de formularios HTML.

#### Comprender React Hook Form

Como su nombre indica, React Hook Form es una biblioteca de React para crear formularios. Es muy flexible y se puede utilizar para formularios simples como nuestro formulario de contacto, así como para formularios grandes con lógica de envío y validación compleja. También es muy eficiente y está optimizado para no provocar re-renderizados innecesarios. Es muy popular con decenas de miles de estrellas en GitHub y madura muy bien, habiéndose lanzado por primera vez en 2019.

#### Comprender la validación del lado del cliente

Una característica clave que habilita React Hook Form es la validación del lado del cliente. La validación del lado del cliente mejora la experiencia de usuario al informarle sobre una entrada problemática antes del envío al servidor. También ayuda a la escalabilidad del servidor porque no tendrá que lidiar con tantos envíos de formularios no válidos.

Existen capacidades de validación del cliente nativas del navegador; de hecho, es posible que haya detectado que el campo de correo electrónico utiliza actualmente la validación de correo electrónico del lado del cliente.

*Figura 9.5 – Validación nativa de correo electrónico*

La validación del lado del cliente de correo electrónico se incluye automáticamente en los elementos de entrada de tipo `email`. Podríamos haber agregado un atributo `required` a las entradas de nombre, correo electrónico y motivo para agregarles reglas de validación de campo requeridas del lado del cliente.

La simplicidad de la implementación de la validación estándar de formularios HTML es agradable. Sin embargo, si queremos personalizar la experiencia del usuario de validación, necesitaremos escribir JavaScript para usar la API de validación de restricciones (*constraint validation API*). Por lo tanto, los requisitos comunes, como personalizar el estilo del mensaje de error o cuándo se activa la validación, requieren bastante código. Es por eso que a menudo se prefieren bibliotecas como React Hook Form a la validación de formularios HTML estándar.

> [!NOTE]
> Para obtener información sobre esta API y más información sobre la validación de formularios HTML, consulte el siguiente enlace: [https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation).

#### Comprender el Hook useForm

La parte clave del uso de React Hook Form es el Hook `useForm`, que devuelve funciones útiles y el estado. El siguiente fragmento de código muestra cómo se llama al Hook `useForm`:

```typescript
const { register, handleSubmit, formState: { errors, isSubmitting, isSubmitSuccessful } } = useForm<FieldValues>();
```

`useForm` tiene un parámetro de tipo genérico para el tipo de los valores de campo. En el ejemplo anterior, el tipo de valores de campo es `FieldValues`.

#### Comprender la función register

Una función clave que devuelve `useForm` es una función `register`, que recibe un nombre de campo único y devuelve lo siguiente en una estructura de objeto:
- Un manejador `onChange`, que ocurre cuando cambia el valor del editor de campo
- Un manejador `onBlur`, que ocurre cuando el editor de campo pierde el foco
- Una referencia al elemento editor de campo
- El nombre del campo

Estos elementos devueltos por la función `register` se propagan (*spread*) en el elemento editor de campo para permitir que React Hook Form rastree de manera eficiente su valor. El siguiente fragmento de código permite que React Hook Form rastree un editor de campo de nombre:

```tsx
<input {...register(‘name’)} />
```

Después de que el resultado de `register` se haya propagado al elemento de entrada, contendrá los atributos `ref`, `name`, `onChange` y `onBlur`:

```tsx
<input ref={someVariableInRHF} name=”name” onChange={someHandlerInRHF} onBlur={anotherHandlerInRHF} />
```

Los atributos `ref`, `onChange` y `onBlur` harán referencia al código en React Hook Form que rastrea el valor del elemento de entrada.

#### Especificación de la validación

La validación de campo se define en el campo `register` en un parámetro de opciones, de la siguiente manera:

```tsx
<input {...register(‘name’, {required: true})} />
```

En el ejemplo anterior, se especifica la validación requerida. El mensaje de error asociado se puede definir como una alternativa al indicador `true`, de la siguiente manera:

```tsx
<input {...register(‘name’, { required: ‘You must enter a name’ })} />
```

Existe una variedad de reglas de validación diferentes que se pueden aplicar. Consulte esta página en la documentación de React Hook Form para obtener una lista de todas las reglas disponibles: [https://react-hook-form.com/get-started#Applyvalidation](https://react-hook-form.com/get-started#Applyvalidation).

También se puede utilizar un esquema de Zod para especificar reglas de validación utilizando una opción `resolver` en el Hook `useForm`:

```typescript
const { ... } = useForm({ resolver: zodResolver(schema) });
```

La variable `schema` en el fragmento de código anterior es una definición de esquema de Zod y `zodResolver` es una función de un paquete complementario, `@hookform/resolvers`.

#### Obtención de errores de validación

El Hook `useForm` devuelve una variable de estado llamada `errors`, que contiene los errores de validación del formulario. La variable de estado `errors` es un objeto que contiene mensajes de error de campos no válidos. Por ejemplo, si un campo de nombre no es válido porque se ha violado una regla requerida, el objeto `errors` podría ser el siguiente:

```typescript
{ name: { message: ‘You must enter your email address’, type: ‘required’ } }
```

Los campos en un estado válido no existen en el objeto `errors`, por lo que se puede renderizar condicionalmente un mensaje de error de validación de campo de la siguiente manera:

```tsx
{errors.name && <div>{errors.name.message}</div>}
```

Nuestro formulario ya utiliza este patrón de renderizado, por lo que debería resultarle familiar.

#### Manejo del envío

El Hook `useForm` también devuelve un manejador llamado `handleSubmit` que se puede utilizar para el envío de formularios. `handleSubmit` recibe una función a la que llama React Hook Form cuando ha validado con éxito el formulario. Aquí hay un ejemplo de uso de `handleSubmit`:

```tsx
function onSubmit(data: FormData) { console.log(‘Submitted data:’, data); } return ( <form onSubmit={handleSubmit(onSubmit)}> </form> );
```

En el ejemplo anterior, `onSubmit` solo se llama en el envío cuando el formulario se valida con éxito y no cuando el formulario no es válido.

El estado `isSubmitting` se puede utilizar para deshabilitar elementos mientras se envía el formulario. El siguiente ejemplo deshabilita el botón Submit mientras se envía el formulario:

```tsx
<button type=”submit” disabled={isSubmitting}> Submit </button>
```

`isSubmitSuccessful` se puede utilizar para renderizar condicionalmente un mensaje de envío exitoso:

```tsx
if (isSubmitSuccessful) { return <div>The form was successfully submitted</div>; }
```

Hay muchas más funciones en React Hook Form, pero estas son las funciones y estados que se utilizan habitualmente. Consulte la documentación de React Hook Form para obtener más información en [https://react-hook-form.com/](https://react-hook-form.com/).

Ahora que entendemos cómo usar React Hook Form, lo usaremos en nuestro formulario de contacto.

#### Uso de React Hook Form

Usaremos React Hook Form en el formulario de contacto en el que hemos estado trabajando. Seguiremos usando `useActionState` y la Server Action para el envío del formulario. Utilizaremos principalmente React Hook Form para la validación del cliente. Lleve a cabo los siguientes pasos:

1. Comencemos instalando React Hook Form y su resolución de Zod. Ejecute el siguiente comando en la terminal:

```bash
npm i react-hook-form @hookform/resolvers
```

2. Abra `ContactForm.tsx` e importe el Hook de React Hook Form, la función `zodResolver` y nuestro esquema de Zod. Además, importe `useRef` de React, que necesitaremos para integrar el envío de React Hook Form con la Server Action:

```typescript
import { ..., useRef } from ‘react’; import { useForm } from ‘react-hook-form’; import { zodResolver } from ‘@hookform/resolvers/zod’; import { contactSchema } from ‘@/data/schema’;
```

3. Llame al Hook `useForm` después de llamar al Hook `useActionState`, de la siguiente manera:

```tsx
export function ContactForm() { const [ ... ] = useActionState( ... ); const { handleSubmit, register, formState: { errors: clientErrors }, } = useForm({ resolver: zodResolver(contactSchema), defaultValues: { name: ‘’, email: ‘’, reason: ‘’, notes: ‘’, ...(Object.fromEntries(formData) ?? {}), }, });
```

Le damos un alias a los errores de validación como `clientErrors` para que no colisionen con los errores del servidor que obtenemos del estado de la acción.

Además de pasarle a `useForm` nuestro esquema Zod, también le pasamos valores predeterminados. Especificamos los valores de campo predeterminados como cadenas vacías y luego los sobrescribimos con cualquier valor de campo del estado de la acción de un envío no válido.

4. Después de la llamada a `useForm`, agregue una referencia para el elemento de formulario:

```tsx
const { ... } = useForm( ... ); const formRef = useRef<HTMLFormElement>(null); return ( <form ref={formRef} ... > ... </form> );
```

Necesitaremos la referencia del elemento de formulario para integrar React Hook Form en el envío del formulario.

5. Agregue un manejador de envío de React Hook Form que invoque la Server Action de la siguiente manera:

```tsx
function onSubmit() { if (!formRef.current) { return; } formAction(new FormData(formRef.current)); } return ( <form ref={formRef} action={formAction} onSubmit={handleSubmit(onSubmit)} > ... </form> );
```

El atributo `action` se deja en su lugar para que el envío siga funcionando antes de que se haya completado la hidratación.

El manejador de envío de React Hook Form no tiene el parámetro de datos habitual porque necesitamos pasar los valores de los campos de la Server Action en formato `FormData`, que obtenemos usando la función constructora `FormData` pasando la referencia del formulario.

6. Agregue un atributo `noValidate` al elemento `form` para suprimir la validación nativa de HTML, lo que eliminará la validación de correo electrónico actual en el campo de correo electrónico:

```tsx
<form ... noValidate >
```

7. Actualizaremos el campo `name` para que funcione con React Hook Form junto con el estado de acción actual. Use la función `register` para registrar el campo con React Hook Form y elimine el atributo `name`, porque la función `register` lo establece por nosotros:

```tsx
<input ... {...register(‘name’)} />
```

React Hook Form establecerá el valor predeterminado, pero lo hace mediante JavaScript. Por lo tanto, el atributo `defaultValue` se deja como está para que no perdamos valores después de un envío no válido cuando JavaScript no se haya hidratado.

8. Actualice el componente `FieldError` para incluir un error de cliente, de la siguiente manera:

```tsx
type Err = { message?: string } | null | undefined; function FieldError({ clientError, ...}: { clientError: Err; ... }) { const error = clientError ?? serverError; if (!error) { return null; } return ( <div id={errorId} role=”alert”> {error.message} </div> ); }
```

Se asigna una variable `error` al error del cliente o del servidor y reemplaza a `serverError` en la lógica de la función.

9. Volviendo al campo `name` en el componente `ContactForm`, actualice el mensaje de error para considerar el error del cliente de React Hook Form:

```tsx
<input ... aria-invalid={ (clientErrors.name ?? errors.name) ? ‘true’ : ‘false’ } /> <FieldError clientError={clientErrors.name} ... />
```

10. Continuando con el campo `name`, agregue un atributo `aria-required` para informar a los lectores de pantalla que es un campo obligatorio:

```tsx
<input ... aria-required=”true” />
```

11. Siga el mismo patrón para integrar React Hook Form en el campo `email`:

```tsx
<input ... {...register(‘email’)} aria-required=”true” aria-invalid={ (clientErrors.email ?? errors.email) ? ‘true’ : ‘false’ } /> <FieldError clientError={clientErrors.email} ... />
```

12. Repita lo anterior para el campo `reason`:

```tsx
<select ... {...register(‘reason’)} aria-required=”true” aria-invalid={ (clientErrors.reason ?? errors.reason) ? ‘true’ : ‘false’ } > ... </select> <FieldError clientError={clientErrors.reason} ... />
```

13. Integre el campo `notes` con React Hook Form:

```tsx
<textarea {...register(‘notes’)} />
```

14. Inicie la aplicación ejecutando el comando `npm run dev` en una terminal.
15. Complete el formulario correctamente y envíelo. El envío se realizará correctamente y la aplicación navegará a la página Thanks. Sin embargo, hay una queja de React si mira en la consola del navegador:

*Figura 9.6 – Advertencia de envío de React*

Hemos tomado el control de llamar a la Server Action desde React: el manejador de envío de React Hook Form anulará el atributo `action` cuando se haya hidratado JavaScript. Esto significa que React no puede administrar adecuadamente el estado de la acción. Por lo tanto, nos pide que envolvamos la llamada a la Server Action en una **Transición de React (React Transition)** para que pueda administrar el estado de la acción.

16. Para envolver la llamada de Server Action en una React Transition, primero importe la función `startTransition` de React y luego envuélvala alrededor de la llamada:

```tsx
import { ..., startTransition } from ‘react’; ... export function ContactForm() { ... function onSubmit() { startTransition(() => { if (!formRef.current) { return; } formAction(new FormData(formRef.current)); }); } ... }
```

17. En la aplicación en ejecución, complete el formulario correctamente y envíelo. Esta vez, todo funcionará bien sin ninguna advertencia.
18. Intente enviar un formulario no válido. Se mostrará un error sin ninguna solicitud al servidor: todo sucede en el navegador. Observe también cómo React Hook Form establece automáticamente el foco en el primer campo no válido.
19. Intente desactivar JavaScript y enviar formularios válidos e inválidos. El formulario se comportará como se esperaba, recurriendo a la validación del servidor.
20. Vuelva a habilitar JavaScript en el navegador y detenga la ejecución de la aplicación presionando `Ctrl + C`.

Eso completa la integración de la validación del lado del cliente en nuestro formulario y esta sección sobre React Hook Form. He aquí un resumen rápido:
- React Hook Form es una biblioteca de React popular, de alto rendimiento y flexible para crear formularios que incluyen validación del lado del cliente.
- El Hook `useForm` permite que un esquema Zod defina las reglas de validación. El Hook devuelve una función `register` para rastrear campos y una variable de estado `errors`.
- El estado de la acción de React y el estado de React Hook Form se superponen y, en muchos casos, está bien usar cualquiera de los enfoques para mantener el código simple. Sin embargo, puede tener lo mejor de ambos mundos siguiendo el enfoque de esta sección.

A continuación, aprenderemos sobre las actualizaciones optimistas después del envío de un formulario.

---

### Implementación de actualizaciones optimistas

En esta sección, aprenderemos qué son las actualizaciones optimistas de la interfaz de usuario (*optimistic UI updates*) y cómo usar el Hook `useOptimistic` de React para implementarlas. Luego usaremos este patrón en una nueva página de nuestra aplicación que permite a los usuarios marcar un elemento de contacto como terminado.

#### Comprender useOptimistic

Una **actualización optimista de la interfaz de usuario** es cuando la interfaz de usuario se actualiza inmediatamente después de que se invoca una acción del usuario, antes de que la acción se complete por completo. El patrón hace que la aplicación sea más rápida y con mayor capacidad de respuesta. El Hook `useOptimistic` se puede utilizar para administrar una variable que se espera que cambie durante una acción. Por lo general, la variable contendrá algunos datos del servidor. Aquí está la sintaxis:

```typescript
const [optimisticValue, setOptimisticValue] = useOptimistic(initialValue);
```

La variable que queremos establecer de forma optimista se pasa a `useOptimistic`. Se devuelve una tupla que contiene el valor optimista y una función definidora (*setter*). Aquí hay un componente que usa `useOptimistic`:

```tsx
export function Name() { const [name, setName] = useState(‘’); const [optName, setOptName] = useOptimistic(name); async function updateName(formData: FormData) { const newName = formData.get(‘name’); setOptName(newName); // Send newName to server to update db setName(newName); } return ( <form action={updateName}> <input type=”text” name=”name” defaultValue={name} /> <p>New name: {optName}</p> </form> ); }
```

El componente contiene un formulario que contiene un input `name`. Cuando se envía el formulario, el nombre ingresado se envía al servidor antes de que se actualice el estado `name`. El Hook `useOptimistic` se utiliza para mostrar de forma optimista el nuevo nombre debajo de la entrada de nombre.

Quizás se pregunte por qué no usamos simplemente una variable de estado normal para el estado optimista. Bueno, `useOptimistic` vuelve automáticamente al estado anterior si ocurre un error y maneja automáticamente las condiciones de carrera cuando se activan múltiples acciones rápidamente.

> [!NOTE]
> La función definidora de `useOptimistic` se puede utilizar para acciones no invocadas desde un formulario. Sin embargo, si no se usa en una acción de formulario, debe estar dentro de una React Transition.

Ahora que entendemos el Hook `useOptimistic`, lo usaremos dentro de nuestra aplicación.

#### Uso de useOptimistic

Crearemos una página en nuestra aplicación que enumere todos los elementos de contacto de la base de datos. Cada elemento de la lista contendrá un formulario para hacer notas adicionales y marcar el elemento como terminado. Usaremos `useOptimistic` para un estado de terminado optimista.

#### Adición de una página de contactos no optimista

Comenzaremos agregando una versión de una página de contactos que no implementa un estado completado optimista. Esto utiliza conocimientos cubiertos anteriormente, por lo que copiaremos el contenido del archivo correspondiente del repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter09/use-optimistic](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter09/use-optimistic). Agregue los siguientes archivos del repositorio junto con su estructura de carpetas:
- `src/data/getContacts.ts`: Contiene una función para obtener todos los elementos de contacto.
- `src/data/completeContact.ts`: Contiene una Server Action para actualizar las notas del elemento de contacto y marcarlo como completado en la base de datos.
- `src/data/schema.ts`: Contiene un esquema Zod adicional para la lista de elementos de contacto y la Server Action para completar un elemento. Este archivo ya existe, pero se puede reemplazar con el contenido del repositorio.
- `src/app/contacts/page.tsx`: Esta es la nueva página para los elementos de contacto.
- `src/components/ContactItem.tsx`: Este es el componente en el que trabajaremos, al que se hace referencia en la nueva página. Renderiza detalles de un elemento de contacto con un formulario debajo. El formulario contiene un campo de notas y un botón Submit. El envío del formulario llama a la Server Action `completeContact`. También puede notar un campo oculto que contiene el ID del elemento de contacto, que es un patrón común para pasar datos adicionales a una Server Action.

1. Inicie la aplicación y navegue a la ruta `/contacts` para ver la nueva página representada.

*Figura 9.7 – Página de contactos*

Puede agregar algunos envíos de contactos usando la página raíz si no aparece ningún elemento de contacto.

2. Intente hacer clic en el botón Done para marcar un elemento como completado. Funcionará pero con un retraso molesto.

#### Hacer que completado sea optimista

Abra `ContactItem.tsx` y lleve a cabo los siguientes pasos para mejorar el componente `ContactItem` para que renderice el estado completado de manera optimista:

1. Agregue `useOptimistic` a la declaración de importación de React:

```typescript
import { ..., useOptimistic } from ‘react’;
```

2. Llame al Hook `useOptimistic` después de llamar al Hook `useActionState`, de la siguiente manera:

```tsx
export function ContactItem( ... ) { const [ ... ] = useActionState( ... ); const [optimisticDone, setOptimisticDone] = useOptimistic(done); return ... }
```

La variable `done` se pasa a `useOptimistic`, que proviene de la base de datos del RSC de la página. El `optimisticDone` devuelto contendrá el valor completado optimista, y `setOptimisticDone` es una función definidora para configurarlo.

3. Actualice el elemento de párrafo para usar `optimisticDone` y reducir la opacidad cuando la acción no esté realmente completada:

```tsx
<p style={{ textDecoration: optimisticDone ? ‘line-through’ : ‘none’, opacity: !done && optimisticDone ? 0.5 : 1, }} > <b>{name}</b>, {email}, {reason} </p>
```

4. Cambie la representación condicional del formulario para utilizar el valor completado optimista:

```tsx
{!optimisticDone && ( <form ... > ... </form> )}
```

5. Reelavore el manejador de acciones para configurar `optimisticDone` antes de llamar a la Server Action:

```tsx
<form action={(formData) => { setOptimisticDone(true); return formAction(formData); }} >
```

6. En la aplicación en ejecución, intente hacer clic en el botón Done para marcar un elemento como terminado. El formulario desaparecerá y el elemento se tachará inmediatamente con una opacidad reducida. Después de un segundo, cuando la Server Action se haya completado, el elemento volverá a la opacidad total.

Eso completa la mejora del componente `ContactItem` y esta sección sobre actualizaciones optimistas. En resumen, las actualizaciones optimistas de la interfaz de usuario mejoran la capacidad de respuesta de la aplicación al reflejar inmediatamente las acciones del usuario en la interfaz de usuario, y el Hook `useOptimistic` de React ayuda a implementar este patrón mientras maneja los errores y las condiciones de carrera de manera efectiva.

A continuación, resumiremos lo que hemos aprendido en este capítulo.

---

### Resumen

En este capítulo, aprendimos cómo crear formularios en React, comenzando con un formulario HTML básico con el componente `Form` de Next.js para evitar una recarga completa de la página durante el envío del formulario.

Aprendimos que las Server Actions son Server Functions especiales que se utilizan para el envío de formularios mediante el atributo `action` de un elemento de formulario. Lo bueno de este patrón de envío es que funciona sin JavaScript.

Cubrimos cómo usar el Hook `useFormStatus` para un indicador de envío y deshabilitar elementos de formulario, y comprendimos su requisito de estar en un componente secundario de un elemento de formulario. Aprendimos que el Hook `useActionState` es una forma alternativa de implementar un indicador de envío y deshabilitar elementos de formulario cuando el formulario está en el mismo componente. El Hook `useActionState` también permite la representación de errores de validación del lado del servidor.

Nos introdujimos en una biblioteca de formularios popular llamada React Hook Form para proporcionar validación del lado del cliente con un esquema Zod. Contiene un Hook `useForm` que devuelve una función `register` para registrar campos y una variable de estado `errors` que contiene mensajes de error de validación.

Por último, cubrimos el uso del Hook `useOptimistic` de React para implementar actualizaciones optimistas de la interfaz de usuario.

En este capítulo, usamos el estado del formulario para los valores de los campos, los mensajes de error y si los indicadores de envío están visibles. En el próximo capítulo, aprenderemos sobre otros tipos de estado y también sobre cómo compartir el estado entre componentes.

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido en este capítulo:

1. ¿Cómo mejora el componente `Form` integrado de Next.js el manejo de formularios en comparación con un elemento `form` nativo?
2. ¿Qué argumento se puede colocar en la instrucción `console.log` para mostrar el nombre ingresado durante el envío del formulario?

```tsx
<form action={(data) => console.log())}> <input type=”text” name=”name” /> <button type=”submit”>Submit</button> </form>
```

3. ¿Cuál es el problema con el siguiente formulario que utiliza una React Server Action para el envío?

```tsx
<form onSubmit={someServerAction}> ... </form>
```

4. ¿Cuál es el beneficio de utilizar el atributo `action` del elemento de formulario en lugar de `onSubmit` para el envío del formulario?
5. Considere el siguiente formulario que captura y envía un nombre. Cuando se ingresa y envía un nombre, el resultado de la instrucción `console.log(name)` es `null`. ¿Por qué ocurre esto?

```tsx
function App() { const [name, formAction] = useActionState( updateName, “”, ); return ( <form action={formAction}> <input type=”text” defaultValue={(name ?? “”) as string} /> <button type=”submit”>Submit</button> </form> ); } async function updateName( _: FormDataEntryValue | null, formData: FormData, ) { const name = formData.get(“name”); console.log(name); return name; }
```

---

### Respuestas

1. Utiliza la navegación del lado del cliente en lugar de la navegación de página completa, pero continúa funcionando si JavaScript está deshabilitado.
2. El parámetro `data` en el manejador de acción es un objeto con la interfaz nativa `FormData`. Por lo tanto, el método `get` se puede utilizar de la siguiente manera para obtener el campo de nombre del formulario:

```tsx
<form action={(data) => console.log(data.get(“name”))} > <input type=”text” name=”name” /> <button type=”submit”>Submit</button> </form>
```

3. Se debe utilizar el atributo `action` en lugar de `onSubmit`, de la siguiente manera:

```tsx
<form action={someServerAction}>
```

4. `onSubmit` requiere JavaScript, mientras que `action` puede funcionar sin JavaScript.
5. Falta el atributo `name` en el elemento de entrada (`input`), lo que significa que no se captura en el envío del formulario:

```tsx
<input name=”name” type=”text” defaultValue={(name ?? “”) as string} />
```
