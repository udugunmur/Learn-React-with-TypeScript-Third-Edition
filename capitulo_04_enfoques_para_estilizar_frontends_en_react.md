# Parte 2: Fundamentos de la Aplicación

## Capítulo 4: Enfoques para estilizar frontends en React

React no ofrece un mecanismo de estilos estándar, y la comunidad ha desarrollado muchos enfoques diferentes. En este capítulo, aprenderemos algunos de los enfoques de diseño y estilos más populares.

Utilizaremos diferentes enfoques para diseñar el componente alert en el que trabajamos en capítulos anteriores. Primero, usaremos **CSS puro (plain CSS)** y comprenderemos los pros y los contras de este enfoque. Luego, pasaremos a utilizar **módulos CSS (CSS modules)**, que resolverán el problema principal del CSS puro. A continuación, utilizaremos una biblioteca llamada **Tailwind CSS**, comprendiendo nuevamente sus ventajas y desventajas. Por último, abordaremos algunos otros enfoques de estilos notables.

Además, aprenderemos a utilizar **SVGs** en aplicaciones de React y los utilizaremos en el componente alert para los iconos de información y advertencia.

Al final de este capítulo, tendrá el conocimiento necesario para elegir un enfoque de estilos adecuado para los futuros proyectos de React en los que trabaje.

Cubriremos los siguientes temas:

- Uso de CSS puro (*plain CSS*)
- Uso de módulos CSS (*CSS modules*)
- Uso de Tailwind CSS
- Uso de SVGs
- Otros enfoques de estilos

---

### Requisitos técnicos

En este capítulo utilizaremos las siguientes tecnologías:

- **Navegador**: Un navegador moderno como Google Chrome.
- **Node.js y npm**: Puede instalarlos desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/).
- **Visual Studio Code**: Puede instalarlo desde [https://code.visualstudio.com/](https://code.visualstudio.com/).

Todos los fragmentos de código utilizados en este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04)

---

### Uso de CSS puro

Diseñaremos el componente alert que creamos en el [Capítulo 2](https://subscription.packtpub.com/book/web-development/9781836643173/2), *Primeros pasos con TypeScript*, utilizando CSS puro. Analizaremos uno de los desafíos del CSS puro y descubriremos cómo podríamos mitigarlo.

#### Creación del proyecto

Usaremos el componente alert que completamos al final del [Capítulo 2](https://subscription.packtpub.com/book/web-development/9781836643173/2), *Primeros pasos con TypeScript*. Crearemos un nuevo proyecto de React y TypeScript con Vite y copiaremos el componente alert en él. Llevemos a cabo los siguientes pasos:

1. En una terminal, en una carpeta de su elección, ejecute el siguiente comando para indicarle a Vite que cree un proyecto de React y TypeScript:

```bash
npm create vite@latest alert -- --template react-ts
```

2. El proyecto se crea. Ejecute los siguientes comandos en la terminal para cambiar el directorio de trabajo a la carpeta `alert`, instalar las dependencias del proyecto y abrir el proyecto en Visual Studio Code:

```bash
cd alert npm i code .
```

Siéntase libre de agregar formateo automático de código. Cubrimos este tema con Prettier en el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*.

3. Ejecute la aplicación en modo de desarrollo ejecutando el siguiente comando en una terminal:

```bash
npm run dev
```

4. Cree un nuevo archivo en la carpeta `src` llamado `Alert.tsx` y copie y pegue el contenido de [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter04/start/Alert.tsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter04/start/Alert.tsx). Esta es la última versión del componente alert en la que trabajamos.

5. Abra `App.tsx` y reemplace el contenido con lo siguiente:

```tsx
import { Alert } from ‘./Alert’; import ‘./App.css’; export default function App() { return ( <Alert heading=”Success” closable> Everything is really good! </Alert> ); }
```

El proyecto ya está creado y ejecutándose en modo de desarrollo. A continuación, nos tomaremos un tiempo para comprender cómo utilizar CSS puro en los componentes de React.

#### Comprender cómo hacer referencia a CSS

Vite ya ha habilitado el uso de CSS puro y ya utiliza parte de él en el proyecto. Lleve a cabo los siguientes pasos para explorar cómo se hace referencia a CSS puro en una aplicación React:

1. Abra el archivo `main.tsx` y observe la declaración de importación de `index.css`:

```typescript
import ‘./index.css’
```

La declaración de importación es un poco diferente de importar un módulo de JavaScript o TypeScript: importa todo el CSS del archivo en lugar de parte del código del archivo.

2. Abra `index.css` y observe que contiene reglas CSS dirigidas a elementos fuera de la aplicación React. Por ejemplo, hay reglas CSS dirigidas al elemento `body`. Otro archivo CSS utilizado es `App.css`, que se importa en `App.tsx`. La mayoría de los estilos en `App.css` son redundantes porque se dirigen a elementos que eliminamos. Por ejemplo, la clase CSS `logo` ahora es redundante.

Mantendremos el CSS redundante en su lugar por ahora. Eventualmente, verificaremos si el proceso de compilación lo elimina.

3. Abra `App.css` y agregue las siguientes propiedades resaltadas a la clase `card` para agregar un borde redondeado:

```css
.card { padding: 2em; border-radius: 8px; border: 1px solid #c1c1c1; }
```

4. Usaremos la clase CSS `card` en el componente `App`. Abra `App.tsx` y envuelva un elemento `div` con una clase CSS `card` alrededor de `Alert` de la siguiente manera:

```tsx
function App() { return ( <div className=”card”> <Alert ... >...</Alert> </div> ); }
```

React utiliza un atributo `className` en lugar de `class` porque `class` es una palabra reservada en JavaScript. El atributo `className` se convierte en un atributo `class` durante la transpilación.

5. En la aplicación en ejecución, ahora aparece un borde alrededor de la alerta:

*Figura 4.1 – Alerta con un borde alrededor*

6. Ahora, detenga la ejecución de la aplicación presionando `Ctrl + C` en la terminal.
7. Ejecute el siguiente comando en la terminal para producir una compilación de producción:

```bash
npm run build
```

Después de unos segundos, los artefactos de compilación aparecerán en una carpeta `dist` en la raíz del proyecto.

8. Abra `index.html` en la carpeta `dist`. Busque el elemento `link` que hace referencia al archivo CSS y anote la ruta; será algo similar a `/assets/index-DFxdEdRD.css`.
9. Abra el archivo CSS referenciado. Se han eliminado todos los espacios en blanco porque está optimizado para producción. Observe que contiene todo el CSS de `index.css` y `App.css`, incluida la clase CSS redundante `logo`:

*Figura 4.2 – El archivo CSS empaquetado, incluida la clase CSS redundante logo*

El punto clave aquí es que Vite no elimina ningún CSS redundante: incluirá todo el contenido de todos los archivos CSS que se hayan importado.

A continuación, aplicaremos estilos al componente alert con CSS puro.

#### Uso de CSS puro en el componente alert

Ahora que entendemos cómo usar CSS puro dentro de React, apliquemos estilos al componente alert. Lleve a cabo los siguientes pasos:

1. Agregue un archivo CSS llamado `Alert.css` en la carpeta `src`. Está disponible para copiar en GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter04/using-plain-css/src/Alert.css](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter04/using-plain-css/src/Alert.css).

Vamos a agregar las clases CSS paso a paso y a comprender los estilos de cada clase.

2. Comience agregando una clase `container` en `Alert.css`:

```css
.container { display: inline-flex; flex-direction: column; text-align: left; padding: 1em; border-radius: 4px; border: 1px solid transparent; }
```

Esto se utilizará en el elemento `div` exterior. El estilo utiliza un flexbox en línea (*inline flexbox*), con los elementos fluyendo verticalmente y alineados a la izquierda. También hemos agregado un bonito borde redondeado y un poco de relleno (*padding*) entre el borde y los elementos secundarios.

3. Agregue las siguientes clases adicionales que se pueden usar dentro de `container`:

```css
.container.warning { color: #e7650f; background-color: #f3e8da; } .container.information { color: #118da0; background-color: #dcf1f3; }
```

Usaremos estas clases para los diferentes tipos de alertas para colorearlas apropiadamente.

4. Agregue la siguiente clase para el elemento contenedor del encabezado:

```css
.header { display: flex; align-items: center; margin-bottom: 0.1em; }
```

Esto se aplicará al elemento que contiene el icono, el encabezado y el botón de cierre. Utiliza un flexbox que fluye horizontalmente con elementos secundarios centrados verticalmente. También agrega un pequeño espacio en la parte inferior antes del mensaje de alerta.

5. Ahora, agregue la siguiente clase para que el icono tenga un ancho de 22px:

```css
.header-icon { width: 22px; }
```

6. A continuación, agregue la siguiente clase para aplicar al encabezado y ponerlo en negrita:

```css
.header-text { font-weight: bold; }
```

7. Agregue la siguiente clase para aplicar al botón de cierre:

```css
.close-button { margin-left: auto; border: none; display: flex; align-items: center; justify-content: center; background: transparent; height: 24px; width: 24px; padding: 0px; cursor: pointer; }
```

Esto elimina el borde y el fondo. Alinea el botón a la derecha del encabezado y le da un cursor de ratón tipo puntero. También centra el contenido dentro de él.

8. Agregue la siguiente clase para el elemento de contenido:

```css
.content { margin: 0 1.2em 0 1.4em; color: #000; }
```

Esto agrega algo de margen para que el mensaje se alinee horizontalmente con el encabezado y establece el color del texto en negro.

Eso completa todas las definiciones de clases CSS.

9. Abra `Alert.tsx` y agregue una declaración de importación en la parte superior del archivo para el archivo CSS que acabamos de crear:

```typescript
import ‘./Alert.css’;
```

10. Ahora, vamos a hacer referencia a las clases CSS que acabamos de crear en los elementos del componente alert. Agregue las siguientes referencias de nombres de clases CSS resaltadas en el JSX de la alerta:

```tsx
<div className={`container ${type}`}> <div className=”header”> <span ... className=”header-icon”> {type === “warning” ? “” : “”} </span> <span className=”header-text”>{heading}</span> {closable && ( <button ... className=”close-button”> ... </button> )} </div> <div className=”content”>{children}</div> </div>
```

Los elementos del componente alert ahora están siendo estilizados por las clases CSS en el archivo CSS importado.

11. Inicie la aplicación en modo de desarrollo ejecutando `npm run dev` en la terminal. Si visita la aplicación en el navegador, aparecerá un componente alert mejorado:

*Figura 4.3 – Un componente alert estilizado con CSS puro*

Eso completa el diseño del componente alert, pero continuemos para que podamos observar una desventaja del CSS puro.

#### Experimentar conflictos de CSS

Ahora veremos un ejemplo de conflicto entre el CSS de diferentes componentes. Mantenga la aplicación ejecutándose en modo de desarrollo y siga estos pasos:

1. Abra `App.tsx` y cambie la clase CSS referenciada de `"card"` a `"container"` en el elemento `div`:

```tsx
<div className=”container”> <Alert ...> ... </Alert> </div>
```

2. Abra `App.css` y cambie el nombre de la clase CSS `card` a `container`:

```css
.container { padding: 2em; border-radius: 8px; border: 1px solid #c1c1c1; }
```

3. Ahora, mire la aplicación en ejecución y observe que la alerta ahora tiene un borde gris, sus esquinas son un poco más redondeadas y hay un poco más de relleno.
4. Inspeccione los elementos utilizando las herramientas de desarrollo del navegador. Observe que los estilos de la clase CSS `container` en `App.css` se están filtrando en el componente alert, lo que hace que se sobrescriban su relleno y su borde.

*Figura 4.4 – Clases CSS en conflicto*

El punto clave aquí es que las clases de CSS puro tienen un alcance global en toda la aplicación y no solo en el archivo en el que se importan. Esto significa que las clases CSS pueden entrar en conflicto si tienen el mismo nombre, como acabamos de experimentar.

Una solución a los conflictos de CSS es nombrarlas cuidadosamente usando **BEM**. Por ejemplo, `container` en el componente `App` podría llamarse `App__container`, y `container` en el componente `Alert` podría llamarse `Alert__container`. Sin embargo, esto requiere disciplina por parte de todos los miembros de un equipo de desarrollo.

> [!NOTE]
> **BEM** significa *Block, Element, and Modifier* (Bloque, Elemento y Modificador) y es una convención de nomenclatura popular para nombres de clases CSS. Se puede encontrar más información en el siguiente enlace: [https://css-tricks.com/bem-101/](https://css-tricks.com/bem-101/).

He aquí un breve resumen de esta sección:
- El uso de CSS puro es simple y familiar para la mayoría de los desarrolladores y funciona sin ninguna configuración adicional en un proyecto de Vite.
- Los archivos CSS se pueden importar en archivos de componentes de React utilizando la sintaxis `import 'path-to-css-file.css'`.
- Todos los estilos en un archivo CSS importado se aplican a la aplicación: no hay ámbito local ni eliminación de estilos redundantes.

A continuación, aprenderemos sobre un enfoque de diseño que no sufre conflictos de CSS entre componentes.

---

### Uso de módulos CSS

En esta sección, comenzaremos comprendiendo los módulos CSS (*CSS modules*) y los usaremos dentro del componente alert en el que hemos estado trabajando.

#### Comprender los módulos CSS

**CSS modules** es una biblioteca de código abierto disponible en GitHub en [https://github.com/css-modules/css-modules](https://github.com/css-modules/css-modules), que se puede agregar al proceso de empaquetado para facilitar la asignación automática de ámbito (*scoping*) a los nombres de clases CSS.

Un módulo CSS es un archivo CSS, al igual que en la sección anterior; sin embargo, el nombre del archivo tiene una extensión `.module.css` en lugar de `.css`. Esta extensión especial permite a Vite diferenciar un archivo de módulo CSS de un archivo CSS puro para que pueda procesarse de manera diferente.

Un archivo de módulo CSS se importa en un archivo de componente de React de la siguiente manera:

```typescript
import styles from ‘./styles.module.css’;
```

Esto es similar a la sintaxis de importación de CSS puro, pero se define una variable para contener la información de mapeo de nombres de clases CSS. En el fragmento de código anterior, la información del nombre de la clase CSS se importa en una variable llamada `styles`, pero el nombre de la variable puede ser cualquiera que elijamos.

La variable de información de mapeo de nombres de clases CSS es un objeto que contiene nombres de propiedades correspondientes a los nombres de clases CSS. Cada propiedad de nombre de clase contiene un valor de un nombre de clase con ámbito que se utilizará en un componente de React. Aquí hay un ejemplo del objeto de mapeo que contiene los nombres de clases CSS `container` y `error` que se han importado a un componente:

```json
{ container: “_container_16mbb_1”, error: “_error_16mbb_7” }
```

El nombre de la clase CSS con ámbito incluye el nombre de la clase CSS original, seguido de una cadena aleatoria. Esta construcción de nombres evita que los nombres de las clases entren en conflicto.

Los estilos dentro de un módulo CSS se referencian en el atributo `className` de un componente de la siguiente manera:

```tsx
<span className={styles.error}>A bad error</span>
```

El nombre de la clase CSS en el elemento luego se resolvería en el nombre de la clase con ámbito. En los fragmentos de código anteriores, `styles.error` se resolvería en `_error_16mbb_7`. Por lo tanto, los estilos en la aplicación en ejecución serán los nombres de estilos con ámbito y no los nombres de clases originales.

Los proyectos creados con Vite ya tienen módulos CSS instalados y configurados. Esto significa que no tenemos que instalar módulos CSS para comenzar a usarlos en nuestro proyecto.

A continuación, usaremos módulos CSS en el componente alert en el que hemos trabajado.

#### Uso de módulos CSS en el componente alert

Ahora que comprendemos los módulos CSS, usémoslos en el componente alert. Lleve a cabo los siguientes pasos:

1. Comience cambiando el nombre de `Alert.css` a `Alert.module.css`; este archivo ahora se puede usar como un módulo CSS.
2. Abra `Alert.module.css` y cambie los nombres de las clases CSS a camelCase en lugar de kebab-case. Esto nos permitirá hacer referencia a los nombres de clases CSS con ámbito más fácilmente en el componente, por ejemplo, `styles.headerText` en lugar de `styles["header-text"]`. Los cambios son los siguientes:

```css
... .headerIcon { ... } .headerText { ... } .closeButton { ... }
```

3. Ahora, abra `Alert.tsx` y cambie la declaración de importación de CSS para importar el módulo CSS de la siguiente manera:

```typescript
import styles from ‘./Alert.module.css’;
```

4. En el JSX, cambie las referencias de nombres de clase para usar los nombres con ámbito del módulo CSS:

```tsx
<div className={`${styles.container} ${styles[type]}`}> <div className={styles.header}> <span ... className={styles.headerIcon}> {type === “warning” ? “” : “”} </span> {heading && ( <span className={styles.headerText}> {heading} </span> )} {closable && ( <button ... className={styles.closeButton}> ... </button> )} </div> <div className={styles.content}>{children}</div> </div>
```

5. Si la aplicación no se está ejecutando, iníciela ejecutando `npm run dev` en la terminal. Esta vez, la alerta ya no tendrá su propio borde gris, lo cual es una señal de que los estilos ya no entran en conflicto.
6. Inspeccione los elementos en el DOM utilizando las DevTools del navegador. Verá que el componente alert ahora está usando nombres de clases CSS con ámbito. Esto significa que los estilos del contenedor de la alerta ya no entran en conflicto con los estilos del contenedor de la aplicación.

*Figura 4.5 – Los nombres de clase con ámbito del módulo CSS*

7. Detenga la aplicación en ejecución antes de continuar presionando `Ctrl + C`.

Para redondear nuestra comprensión de los módulos CSS, veamos qué sucede con el CSS en una compilación de producción. Sin embargo, antes de hacer eso, agreguemos una clase CSS redundante en la parte inferior de `Alert.module.css`:

```css
... .content { margin-left: 1.4em; color: #000; } .redundant { color: red; }
```

8. Ahora, cree una compilación de producción ejecutando `npm run build` en la terminal. Después de unos segundos, los artefactos de compilación se crean en la carpeta `dist`.
9. Abra el archivo CSS empaquetado y notará los siguientes puntos:
   - Contiene todo el CSS de `index.css`, `App.css` y el módulo CSS que acabamos de crear.
   - Los nombres de clase del módulo CSS tienen ámbito asignado. Esto asegurará que los estilos en producción no entren en conflicto, al igual que en el modo de desarrollo.
   - Contiene el nombre de la clase CSS redundante del módulo CSS.

*Figura 4.6 – La clase CSS redundante incluida en el paquete CSS*

Eso completa la refactorización del componente alert para usar módulos CSS.

> [!NOTE]
> Para obtener más información sobre los módulos CSS, visite el repositorio de GitHub en [https://github.com/css-modules/css-modules](https://github.com/css-modules/css-modules).

He aquí un resumen de lo que hemos aprendido sobre los módulos CSS:
- Los módulos CSS permiten asignar automáticamente un ámbito a los nombres de clases CSS para un componente de React. Esto evita que los estilos de diferentes componentes de React entren en conflicto.
- Los módulos CSS no son una característica estándar del navegador; en su lugar, es una biblioteca de código abierto que se puede agregar al proceso de empaquetado. Sin embargo, viene preinstalada y preconfigurada en proyectos creados con Vite.
- Dentro de un archivo de módulo CSS, escribe CSS puro, que es familiar para la mayoría de los desarrolladores.
- Al igual que en CSS puro, las clases CSS redundantes no se eliminan del paquete CSS de producción.

A continuación, aprenderemos sobre otro enfoque para aplicar estilos a las aplicaciones React.

---

### Uso de Tailwind CSS

En esta sección, comenzaremos comprendiendo Tailwind CSS y sus beneficios. A continuación, refactorizaremos el componente alert que hemos estado usando para usar Tailwind y observaremos en qué se diferencia de otros enfoques que hemos probado.

#### Comprender Tailwind CSS

**Tailwind** es un conjunto de clases CSS predefinidas que se pueden utilizar para dar estilo a una aplicación. Se conoce como un **framework CSS utility-first (utilidad primero)** porque las clases predefinidas se pueden considerar como utilidades flexibles.

Un ejemplo de clase CSS es `bg-white`, que define el fondo de un elemento como blanco (`bg` es la abreviatura de *background*). Otro ejemplo es `bg-orange-500`, que establece el color de fondo en un tono 500 de naranja. Tailwind contiene una bonita paleta de colores que se puede personalizar.

Las clases de utilidad se pueden usar juntas para diseñar un elemento. El siguiente ejemplo da estilo a un elemento de botón en JSX:

```jsx
<button className=”border-none rounded-md bg-emerald-700 text-white cursor-pointer”> ... </button>
```

Aquí hay una explicación de las clases utilizadas en el ejemplo anterior:
- `border-none` elimina el borde de un elemento.
- `rounded-md` redondea las esquinas del borde de un elemento. `md` significa mediano (*medium*). Alternativamente, podría usar `lg` (*large*) o incluso `full`, para bordes más redondeados.
- `bg-emerald-700` establece el color de fondo del elemento en un tono 700 de esmeralda (*emerald*).
- `text-white` establece el color del texto del elemento en blanco.
- `cursor-pointer` establece el cursor del elemento como un puntero.

Las clases de utilidad son de bajo nivel y se centran en aplicar estilo a una cosa muy específica. Esto hace que las clases sean flexibles, lo que les permite ser altamente reutilizables.

Tailwind puede especificar que se debe aplicar una clase cuando el elemento está en estado de desplazamiento (*hover*) prefijándolo con `hover:`. El siguiente ejemplo establece el fondo del botón en un tono más oscuro de esmeralda cuando se pasa el cursor sobre él:

```jsx
<button className=”md border-none rounded-md bg-emerald-700 text-white cursor-pointer hover:bg-emerald-800”> ... </button>
```

Por lo tanto, un punto clave de Tailwind es que no escribimos nuevas clases CSS para cada elemento al que queremos aplicar estilo; en su lugar, usamos una amplia gama de clases existentes bien pensadas. Un beneficio de este enfoque es que ayuda a que una aplicación se vea atractiva y consistente.

> [!NOTE]
> Para obtener más información sobre Tailwind, consulte su sitio web en el siguiente enlace: [https://tailwindcss.com/](https://tailwindcss.com/). El sitio web de Tailwind es un recurso crucial para buscar y comprender todas las diferentes clases de utilidad que están disponibles.

A continuación, instalaremos y configuraremos Tailwind en el proyecto que contiene el componente alert en el que hemos estado trabajando.

#### Instalar y configurar Tailwind CSS

Ahora que entendemos Tailwind, instalémoslo y configurémoslo en el proyecto del componente alert. Para hacer esto, lleve a cabo los siguientes pasos:

1. En el proyecto de Visual Studio, comience instalando Tailwind ejecutando el siguiente comando en una terminal:

```bash
npm i -D tailwindcss @tailwindcss/vite
```

La biblioteca Tailwind se instala como una dependencia de desarrollo porque no es necesaria en tiempo de ejecución.

2. Abra `vite.config.ts` y agregue el plugin `tailwindcss`:

```typescript
... import tailwindcss from “@tailwindcss/vite”; export default defineConfig({ plugins: [react(), tailwindcss()], });
```

3. Ahora, abra `index.css` en la carpeta `src` y reemplace el contenido con la siguiente línea para agregar el CSS base de Tailwind:

```css
@import ‘tailwindcss’;
```

4. A continuación, agregaremos una extensión de Visual Studio Code que habilita IntelliSense para las clases de Tailwind CSS. Abra las extensiones de Visual Studio Code, busque la extensión **Tailwind CSS IntelliSense** e instálela.

*Figura 4.7 – Extensión de Tailwind para Visual Studio Code*

Los siguientes dos pasos solo son relevantes si utiliza Prettier para formatear el código.

5. Agreguemos un complemento de Prettier para ordenar sensatamente los nombres de las clases CSS a las que se hace referencia en el atributo `className` durante el formateo del código. Comience instalando `prettier-plugin-tailwindcss` ejecutando el siguiente comando en la terminal:

```bash
npm i -D prettier-plugin-tailwindcss
```

6. Abra el archivo de configuración de Prettier y agregue la siguiente línea resaltada para configurar Prettier para que use este plugin:

```json
{ ..., “plugins”: [“prettier-plugin-tailwindcss”] }
```

Tailwind ya está instalado y listo para usar. A continuación, utilizaremos Tailwind para darle estilo al componente alert en el que hemos estado trabajando.

#### Uso de Tailwind CSS

Ahora, usemos Tailwind para diseñar el componente alert. Reemplazaremos el uso de módulos CSS con nombres de clases de utilidad de Tailwind en el atributo `className` de JSX. Para hacer esto, lleve a cabo los siguientes pasos:

1. Abra `App.tsx` y elimine el atributo `className` en el elemento `div` más externo. Esto elimina el CSS puro del componente `App`.
2. Abra `Alert.tsx` y comience eliminando la declaración de importación de `Alert.module.css` de la parte superior del archivo.
3. Actualice el atributo `className` en el elemento `div` más externo de la siguiente manera:

```tsx
<div className={`border-1 inline-flex flex-col rounded-md border- transparent p-3 text-left`} > ... </div>
```

Aquí hay una explicación de las clases de utilidad que se acaban de utilizar:
- `inline-flex` y `flex-col` crean un flexbox en línea que fluye verticalmente.
- `text-left` alinea los elementos a la izquierda.
- `p-3` agrega tres unidades de espaciado de relleno (*padding*).
- Nos hemos encontrado con `rounded-md` antes: esto redondea las esquinas del elemento `div`.
- `border-1` y `border-transparent` agregan un borde transparente de 1px.

> [!NOTE]
> Las unidades de espaciado se definen en Tailwind y son una escala proporcional. Una unidad de espaciado equivale a 0.25rem, lo que se traduce aproximadamente en 4px.

Observe el útil IntelliSense a medida que se ingresan los nombres de las clases:

*Figura 4.8 – IntelliSense para los nombres de clases de Tailwind*

Observe también que los nombres de las clases se ordenarán según el fragmento de código anterior, independientemente del orden en que se ingresen.

4. Aún en el elemento `div` más externo, agregue los siguientes estilos condicionales utilizando interpolación de cadenas:

```tsx
<div className={`border-1 inline-flex flex-col rounded-md border-transparent p-3 text-left ${ type === ‘warning’ ? ‘text-amber-900’ : ‘text-teal-900’ } ${ type === ‘warning’ ? ‘bg-amber-50’ : ‘bg-teal-50’}`} > ... </div>
```

El color del texto se establece en un tono ámbar 900 para alertas de advertencia y un tono verde azulado (*teal*) 900 para alertas de información. El color de fondo se establece en un tono ámbar 50 para alertas de advertencia y un tono verde azulado 50 para alertas de información.

5. A continuación, actualice el atributo `className` en el contenedor del encabezado de la siguiente manera:

```tsx
<div className=”mb-1 flex items-center”> <span role=”img” ... > ... </span> <span ... >{heading}</span> {closable && ...} </div>
```

Aquí hay una explicación de las clases de utilidad que se acaban de utilizar:
- `mb-1` agrega un margen de una unidad de espaciado en la parte inferior del elemento.
- `flex` e `items-center` crean un flexbox que fluye horizontalmente donde los elementos están centrados verticalmente.

6. Actualice el atributo `className` en el icono de la siguiente manera:

```tsx
<span role=”img” ... className=”w-7”> {type === ‘warning’ ? ‘’ : ‘’} </span>
```

La instancia `w-7` establece el elemento en un ancho de siete unidades de espaciado.

7. Actualice el atributo `className` en el encabezado de la siguiente manera:

```tsx
<span className=”font-bold”>{heading}</span>
```

La instancia `font-bold` establece el grosor de la fuente en negrita en el elemento.

8. Actualice el atributo `className` en el botón de cierre de la siguiente manera:

```tsx
{closable && ( <button ... className=”ml-auto flex h-6 w-6 cursor-pointer items-center justify-center border-none bg-transparent p-0” > ... </button> )}
```

Aquí, `border-none` elimina el borde del elemento y `bg-transparent` hace que el fondo del elemento sea transparente. La instancia `ml-auto` establece el margen izquierdo en automático, lo que alinea el elemento a la derecha. Las instancias `flex`, `items-center` y `justify-center` centran el contenido dentro del botón. Las instancias `h-6` y `w-6` dimensionan el botón, y `p-0` elimina su relleno. La instancia `cursor-pointer` establece el cursor del ratón en un puntero.

9. Finalmente, actualice el atributo `className` en el contenedor de mensajes de la siguiente manera:

```tsx
<div className=”ml-7 pr-5 text-black”> {children} </div>
```

La instancia `ml-7` establece el margen izquierdo en el elemento en siete unidades de espaciado, `pr-5` establece el relleno derecho en cinco unidades de espaciado y `text-black` establece el color del texto en negro.

10. Ejecute la aplicación ejecutando `npm run dev` en la terminal. Después de unos segundos, la aplicación aparecerá en el navegador.
11. Inspeccione los elementos en el DOM mediante las DevTools del navegador. Observe las clases de utilidad de Tailwind que se están utilizando. Un punto clave a tener en cuenta es que no se produce ninguna asignación de ámbito a los nombres de clases CSS. No hay necesidad de ningún ámbito porque las clases son generales y reutilizables y no específicas de ningún componente.

*Figura 4.9 – Una alerta estilizada con Tailwind*

12. Detenga la aplicación en ejecución antes de continuar presionando `Ctrl + C`.

Para redondear nuestra comprensión de Tailwind, veamos qué sucede con el CSS en una compilación de producción. Primero, cree una compilación de producción ejecutando `npm run build` en la terminal. Después de unos segundos, los artefactos de compilación se crean en la carpeta `dist`.

13. Abra el archivo CSS empaquetado desde la carpeta `dist/assets`. Observe los estilos base de Tailwind y todas las clases de Tailwind que usamos en este archivo.

*Figura 4.10 – Clases de Tailwind CSS en un archivo CSS empaquetado*

> [!NOTE]
> Un punto importante es que Tailwind no agrega todas sus clases CSS; ¡eso produciría un archivo CSS masivo! En su lugar, solo agrega las clases CSS utilizadas en la aplicación.

Eso completa el proceso de refactorización del componente alert para usar Tailwind.

He aquí un resumen de lo que aprendimos sobre Tailwind:
- Tailwind requiere instalación y configuración en un proyecto de Vite.
- Tailwind es una colección bien pensada de clases CSS reutilizables que se pueden aplicar a elementos de React. Hay mucho que aprender, pero la documentación es muy útil.
- Una desventaja de Tailwind es que el JSX puede ser más difícil de leer cuando contiene muchas referencias a clases CSS de Tailwind.
- Tailwind tiene una bonita paleta de colores predeterminada y una escala de espaciado de 4px, lo que le da una apariencia agradable.
- Solo las clases utilizadas en los elementos de React se incluyen en el paquete de compilación CSS. Por esta razón, y por su alta reutilización, el paquete CSS es generalmente muy pequeño.

A continuación, haremos que los iconos en el componente alert se vean un poco mejor.

---

### Uso de SVGs

En esta sección, aprenderemos cómo usar archivos SVG en React y cómo usarlos para los iconos en el componente alert.

#### Comprender cómo usar SVGs en React

**SVG** significa *Scalable Vector Graphics* (Gráficos Vectoriales Escalables) y está compuesto por puntos, líneas, curvas y formas basadas en fórmulas matemáticas en lugar de píxeles específicos. Esto les permite escalar cuando se redimensionan sin distorsión. Es importante lograr la calidad adecuada de los iconos: si están distorsionados, hacen que toda la aplicación parezca poco profesional. El uso de SVGs para iconos es común en el desarrollo web moderno.

La forma más sencilla de utilizar SVGs en React es agregar un elemento `svg` directamente en JSX. El siguiente ejemplo es un botón de carga que contiene un SVG de flecha hacia arriba:

```jsx
function Upload() { return ( <div className=... > <button className=... > <svg width=”24” height=”24” viewBox=”0 0 24 24” fill=”none” xmlns=”http://www.w3.org/2000/svg” > <path d=”M12 4L4 12H9V20H15V12H20L12 4Z” fill=”white” /> </svg> </button> </div> ); }
```

La desventaja de este enfoque es que los elementos `svg` pueden contener mucho marcado (*markup*), lo que dificulta la lectura del componente de React. Es más conveniente hacer referencia a un archivo SVG que se encuentre fuera del archivo del componente de React. Vite permite importar archivos SVG sin ninguna configuración adicional. De hecho, se hizo referencia a un par de SVGs en el componente `App` de la plantilla de Vite React de la siguiente manera:

```tsx
import reactLogo from ‘./assets/react.svg’ import viteLogo from ‘./vite.svg’; ... function App() { ... return ( ... <img src={viteLogo} ... /> ... <img src={reactLogo} ... /> ... ); } export default App;
```

En el ejemplo anterior, `viteLogo` se importa como una ruta al archivo SVG, que luego se utiliza en el atributo `src` en el elemento `img` para mostrar el SVG.

Observe la ruta en la declaración de importación de `viteLogo` y dónde está el archivo `vite.svg` en el proyecto: está en la carpeta `public`. La carpeta `public` es una carpeta especial donde los recursos se sirven en la ruta raíz (`/`), y está diseñada para recursos a los que no se hace referencia en el código fuente o para recursos cuyos nombres deben permanecer sin cambios durante el proceso de empaquetado.

Una desventaja de hacer referencia a un SVG en un elemento `img` es que se pierde la capacidad de aplicarle estilos al SVG. Otro enfoque es crear un componente de React SVG reutilizable con el SVG incrustado. El siguiente ejemplo es un componente de React para un icono de flecha hacia arriba:

```tsx
export function UpArrow() { return ( <svg width=”24” height=”24” viewBox=”0 0 24 24” fill=”none” xmlns=”http://www.w3.org/2000/svg” > <path d=”M12 4L4 12H9V20H15V12H20L12 4Z” fill=”white” /> </svg> ); }
```

El componente de React SVG se puede consumir en otros componentes sin que el SVG sobrecargue el marcado, de la siguiente manera:

```tsx
import { UpArrow } from “/icons/UpArrow”; function Upload() { return ( <div className=... > <button className=... > <UpArrow /> </button> </div> ); }
```

De hecho, hay un plugin de Vite llamado `vite-plugin-svgr` que le permite importar archivos SVG como componentes de React, como en el siguiente ejemplo:

```tsx
import UpArrow from “/icons/uparrow.svg?react”; function Upload() { return ( <div className=... > <button className=... > <UpArrow /> </button> </div> ); }
```

Consulte el repositorio de GitHub de `vite-plugin-svgr` para obtener más detalles: [https://github.com/pd4d10/vite-plugin-svgr](https://github.com/pd4d10/vite-plugin-svgr).

A continuación, usaremos SVGs en el componente alert.

#### Añadir SVGs al componente alert

Lleve a cabo los siguientes pasos para reemplazar los iconos de emoji en el componente alert con SVGs:

1. Primero, cree tres archivos vacíos llamados `cross.svg`, `info.svg` y `warning.svg` en la carpeta `src/assets`. Luego, copie y pegue el contenido de estos desde el repositorio de GitHub en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04/using-svgs/src/assets](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04/using-svgs/src/assets).
2. Abra `Alert.tsx` y agregue las siguientes declaraciones de importación para importar los SVGs:

```typescript
import crossIcon from ‘./assets/cross.svg’; import infoIcon from ‘./assets/info.svg’; import warningIcon from ‘./assets/warning.svg’;
```

Hemos asignado alias con nombres apropiados a los componentes SVG.

3. Reemplace el elemento `span` que contiene los iconos de emoji con un elemento `img` que haga referencia condicionalmente a los SVGs de la siguiente manera:

```tsx
<div className=”mb-1 flex items-center”> <img src={type === ‘warning’ ? warningIcon : infoIcon} alt={type === ‘warning’ ? ‘Warning’ : ‘Information’} className=”mr-1 h-6 w-6” /> <span className=”font-bold”>{heading} </span> </div>
```

Hemos utilizado Tailwind para dimensionar los iconos adecuadamente y agregar un espacio entre ellos y el encabezado.

4. A continuación, reemplace el icono de cierre de emoji con el icono de cierre SVG de la siguiente manera:

```jsx
<button ... > <img src={crossIcon} alt=”Close” /> </button>
```

5. Ejecute la aplicación ejecutando `npm run dev`. La aplicación en el navegador contendrá el componente alert con los iconos SVG.

*Figura 4.11 – Una alerta con un icono SVG*

Eso completa el componente alert: se ve mucho mejor ahora.

He aquí un breve resumen de lo que aprendimos sobre el uso de SVGs en aplicaciones React:
- Los SVGs se utilizan comúnmente para iconos en una aplicación para garantizar un aspecto profesional.
- Los archivos SVG se pueden importar a un componente de React y luego se puede hacer referencia a ellos dentro de un elemento `img`.

A continuación, abordaremos algunos otros enfoques de estilos.

---

### Otros enfoques de estilos

En esta sección, cubriremos otros enfoques de diseño populares.

#### Uso de estilos en línea

El atributo `style` se puede utilizar para dar estilo a los elementos JSX. Se establece en un objeto JavaScript, donde las propiedades CSS se escriben en camelCase con valores de cadena o numéricos.

Aquí hay un ejemplo del contenedor de la alerta estilizado con este enfoque:

```jsx
<div style={{ display: “inline-flex”, flexDirection: “column”, textAlign: “left”, padding: “1em”, borderRadius: “4px”, border: “1px solid transparent”, backgroundColor: type === “warning” ? “#f3e8da” : “#dcf1f3”, color: type === “warning” ? “#e7650f” : “#118da0”, }} > ... </div>;
```

Los beneficios de este enfoque son los siguientes:
- Es simple y familiar para la mayoría de los desarrolladores.
- No se requiere ningún paso de compilación.
- Los estilos tienen un ámbito limitado al componente.

Sin embargo, existen algunos inconvenientes:
- Las pseudoclases CSS (como `:hover`) y los pseudoelementos (como `::before`) no se pueden utilizar.
- Por lo general, es menos eficiente porque no se puede almacenar en caché ninguna hoja de estilos. También puede provocar una duplicación innecesaria de estilos, lo que aumenta el tamaño del DOM.

Aquí hay un enlace al código completo para el componente alert diseñado con estilos en línea: [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04/using-inline-styles](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04/using-inline-styles).

#### Uso de SCSS

**SCSS** es un superconjunto de CSS que históricamente ha sido muy popular debido a características como variables, anidamiento y mixins. Sin embargo, las variables y el anidamiento ahora están disponibles en CSS nativo, por lo que SCSS es una opción menos popular para los nuevos proyectos de React.

Aquí está el contenedor de la alerta estilizado utilizando la función de anidamiento en SCSS:

```scss
container { display: inline-flex; flex-direction: column; text-align: left; padding: 1em; border-radius: 4px; border: 1px solid transparent; &.warning { color: #e7650f; background-color: #f3e8da; } &.information { color: #118da0; background-color: #dcf1f3; } }
```

Vite admite SCSS, pero es necesario instalar un paquete `sass-embedded`. El SCSS se coloca en un archivo con una extensión `.scss` y se importa a un componente de la misma manera que un archivo `.css`.

SCSS no asigna un ámbito automático a los estilos, por lo que sufre el mismo problema de conflicto que el CSS puro.

Aquí hay un enlace al código para el componente alert estilizado con SCSS: [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04/using-scss](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04/using-scss).

Para obtener más información sobre SCSS, consulte la documentación en [https://sass-lang.com](https://sass-lang.com/).

#### Uso de CSS-in-JS

**CSS-in-JS** es una técnica de diseño donde los estilos se escriben directamente en JSX. Por lo tanto, es similar al CSS en línea, pero un paso de compilación traslada los estilos en línea de los elementos HTML a un elemento `style`. Los elementos HTML luego hacen referencia a los estilos utilizando un nombre de clase CSS con ámbito.

La biblioteca `styled-components` es una biblioteca CSS-in-JS muy popular. Aquí está el contenedor de la alerta usando `styled-components`:

```tsx
const Container = styled.div<{ type: string }>` display: inline-flex; flex-direction: column; text-align: left; padding: 1em; border-radius: 4px; border: 1px solid transparent; ${(props) => props.type === “warning” && ` color: #e7650f; background-color: #f3e8da; `} ${(props) => props.type === “information” && ` color: #118da0; background-color: #dcf1f3; `} `;
```

El componente `Container` es un componente regular de React que puede recibir props; en este caso, una prop `type` aplica el contenedor para el tipo de alerta especificado. Utiliza una plantilla literal para definir un elemento `div` estilizado con CSS directamente en el componente de React.

Aquí hay un enlace al código para el componente alert diseñado con `styled-components`: [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04/using-css-in-js](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter04/using-css-in-js).

Una desventaja de `styled-components` es que los estilos se aplican en tiempo de ejecución en lugar de en tiempo de compilación, lo que genera una penalización menor en el rendimiento. Otra desventaja es que no se puede utilizar en React Server Components, que cubriremos en el [Capítulo 5](https://subscription.packtpub.com/book/web-development/9781836643173/5), *Uso de React Server y Client Components*. El proyecto también se ha puesto recientemente en modo de mantenimiento.

Para obtener más información sobre `styled-components`, consulte su documentación en [https://styled-components.com](https://styled-components.com/).

Las bibliotecas CSS-in-JS más nuevas, como **StyleX** ([https://stylexjs.com](https://stylexjs.com/)) y **Panda** ([https://panda-css.com](https://panda-css.com/)), abordan las desventajas de `styled-components` pero no han ganado tanta tracción en el momento de escribir este libro.

A continuación, resumiremos lo que hemos aprendido en este capítulo.

---

### Resumen

En este capítulo, aprendimos tres métodos de diseño y sus pros y contras.

Primero, aprendimos que se puede usar CSS puro para dar estilo a las aplicaciones de React, pero todos los estilos en el archivo CSS importado se empaquetan independientemente de si se usa un estilo o no. Además, los estilos no tienen un ámbito limitado a un componente específico: observamos que los nombres de clase CSS `container` entraban en conflicto con los componentes `App` y `Alert`.

A continuación, aprendimos sobre los módulos CSS, que nos permiten escribir archivos CSS puros importados de una manera que asigna un ámbito a los estilos del componente, lo que permite su uso en cualquier parte de la aplicación. Aprendimos que CSS modules es una biblioteca de código abierto preinstalada y preconfigurada en proyectos creados con Vite. Vimos cómo esto resolvió el problema de conflictos de CSS pero no eliminó los estilos redundantes.

Analizamos el diseño con la popular biblioteca Tailwind CSS. Aprendimos que Tailwind proporciona un conjunto de clases CSS reutilizables que se pueden aplicar a elementos de React, incluida una atractiva paleta de colores predeterminada y una escala de espaciado de 4px, las cuales se pueden personalizar. Aprendimos que solo las clases de Tailwind utilizadas se incluyen en la compilación de producción.

Abordamos algunos otros enfoques de diseño. El CSS en línea es conveniente pero tiene penalizaciones de rendimiento y no permite pseudoclases ni pseudoelementos. SCSS y CSS-in-JS fueron muy populares en el pasado, por lo que es posible que los use en bases de código existentes.

Finalmente, aprendimos que Vite permite el uso de archivos SVG. Los SVGs se pueden importar y referenciar como una ruta en un elemento `img`.

Una nota importante es que el estilo no afecta la reutilización de los componentes. Nuestro componente alert aún se puede usar en muchas partes de una aplicación e incluso en diferentes aplicaciones.

En el próximo capítulo, veremos los React Server Components.

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido sobre el diseño en React:

1. ¿Por qué el siguiente uso de CSS puro podría ser problemático?

```jsx
<div className=”wrapper”></div>
```

2. Tenemos un componente diseñado usando módulos CSS de la siguiente manera:

```tsx
import styles from ‘./styles3.module.css’; function ComponentThree() { return <div className={styles.wrapper}> </div> }
```

Los estilos en `styles3.module.css` son los siguientes:

```css
.wrap { display: flex; align-items: center; background: #e7650f; }
```

Los estilos no se aplican cuando se ejecuta la aplicación. ¿Cuál es el problema?

3. Estamos diseñando un elemento de botón usando Tailwind. Actualmente está diseñado de la siguiente manera:

```jsx
<button className=”bg-blue-500 text-white font-bold py-2 px-4 rounded”> Button </button>
```

¿Cómo podemos mejorar el estilo haciendo que el fondo del botón sea un tono 700 de azul cuando el usuario pasa el cursor sobre él?

4. Se hace referencia a un logotipo SVG de la siguiente manera:

```tsx
import Logo from ‘./logo.svg’; function SomeComponent() { return ( <div> <Logo /> </div> ); }
```

Sin embargo, el logo no se renderiza. ¿Cuál es el problema?

5. Estamos diseñando un elemento de botón usando Tailwind que tiene una prop `color` para determinar su color y está diseñado de la siguiente manera:

```jsx
<button className={`bg-${color}-500 text-white font-bold py-2 px-4 rounded`}> Button </button>
```

Sin embargo, el color del botón no funciona. ¿Cuál es el problema?

---

### Respuestas

1. La clase CSS `wrapper` podría entrar en conflicto con otras clases. Para reducir este riesgo, el nombre de la clase se puede limitar manualmente al ámbito del componente:

```jsx
<div className=”card-wrapper”></div>
```

2. Se hace referencia al nombre de clase incorrecto en el componente; debería ser `wrap` en lugar de `wrapper`:

```tsx
import styles from ‘./styles3.module.css’; function ComponentThree() { return <div className={styles.wrap}> </div> }
```

3. El estilo se puede ajustar de la siguiente manera para incluir el estilo de hover:

```jsx
<button className=”bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded”> ... </button>
```

4. La instancia `Logo` contendrá la ruta al SVG en lugar de un componente. La declaración de importación se puede ajustar de la siguiente manera para importar un componente de React si se utiliza el plugin `vite-plugin-svgr`:

```tsx
import Logo from ‘./logo.svg?react’; function SomeComponent() { return ( <div> <Logo /> </div> ); }
```

5. El nombre de la clase `bg-${color}-500` es problemático ya que solo se puede resolver en tiempo de ejecución debido a la variable `color`. Las clases de Tailwind utilizadas se determinan en tiempo de compilación y se agregan al paquete, lo que significa que las clases de color de fondo relevantes no se empaquetarán. Esto significa que el estilo de color de fondo no se aplicará al botón.
