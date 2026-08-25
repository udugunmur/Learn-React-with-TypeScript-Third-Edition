# Parte 1: Introducción

## Capítulo 1: Primeros pasos con React

Facebook se ha convertido en una aplicación increíblemente popular. A medida que su popularidad ha crecido, también lo ha hecho la demanda de nuevas funciones. **React** es la respuesta de Meta para ayudar a que más personas trabajen en la base de código de Facebook y entreguen funciones con mayor rapidez. React ha funcionado tan bien para Facebook que Meta finalmente lo convirtió en código abierto. Hoy en día, React es una biblioteca madura para construir frontends basados en componentes, sumamente popular y con una comunidad y ecosistema masivos.

**TypeScript** es también una biblioteca popular y madura mantenida por otra gran empresa, Microsoft. Permite a los usuarios agregar un sistema de tipos enriquecido a su código JavaScript, ayudándoles a ser más productivos, particularmente en bases de código grandes.

Este libro le enseñará cómo utilizar estas increíbles bibliotecas para crear frontends robustos y fáciles de mantener. Los dos primeros capítulos del libro presentarán React y TypeScript por separado. Luego aprenderá a usar React y TypeScript juntos para componer componentes robustos con un tipado fuerte. Hay un capítulo completo sobre los recientemente lanzados **React Server Components (RSC)**, que ofrecen importantes mejoras de rendimiento y productividad. El libro cubre todos los temas clave que necesitará para crear un frontend web, como estilos, formularios, obtención de datos (*data fetching*) y mutación de datos.

En este capítulo, presentaremos React y comprenderemos sus beneficios. Luego construiremos un componente simple de React, aprenderemos sobre la sintaxis de los componentes y cómo hacerlos configurables. Después de eso, aprenderemos a hacer que un componente sea interactivo usando el estado del componente y los eventos. Al final del capítulo, aprenderemos a utilizar las herramientas de desarrollo de React.

Al final de este primer capítulo, podrá crear componentes simples de React y estará listo para aprender a tiparlos fuertemente con TypeScript.

En este capítulo, cubriremos los siguientes temas:

- Comprender los beneficios de React
- Configurar un proyecto de React
- Comprender la estructura de una aplicación React
- Crear un componente
- Uso de props
- Uso del estado (*state*)
- Uso de eventos
- Uso de las herramientas de desarrollo de React (*React developer tools*)

---

### Beneficios gratuitos con su libro

Su compra incluye una copia gratuita en PDF de este libro junto con otros beneficios exclusivos. Consulte la sección *Beneficios gratuitos con su libro* en el Prefacio para desbloquearlos al instante y maximizar su experiencia de aprendizaje.

---

### Requisitos técnicos

En este capítulo utilizamos las siguientes herramientas:

- **Navegador**: Un navegador moderno como Google Chrome.
- **Terminal**: Usaremos una terminal para ejecutar comandos y crear un proyecto de React. La terminal predeterminada disponible en su sistema operativo funcionará bien.
- **Visual Studio Code**: Necesitamos un editor de código para crear nuestro primer componente de React. Visual Studio Code es un editor popular que utilizaremos a lo largo de este libro. Se puede descargar e instalar desde [https://code.visualstudio.com](https://code.visualstudio.com/).
- **Node.js y npm**: Se requerirá Node.js para compilar nuestra aplicación React y ejecutarla en un servidor de desarrollo. npm es un gestor de paquetes que nos permite instalar fácilmente bibliotecas en nuestra aplicación. Estas herramientas vienen juntas y se pueden descargar e instalar desde [https://nodejs.org/en/download](https://nodejs.org/en/download).

Todos los fragmentos de código de este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter01](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter01)

---

### Comprender los beneficios de React

Antes de comenzar a crear nuestro primer componente de React, en esta sección comprenderemos qué es React y exploraremos algunos de sus beneficios.

React es una biblioteca de frontend increíblemente popular. Ya hemos mencionado que Meta usa React para Facebook, pero muchas otras empresas famosas también lo usan, como Netflix, Uber y Airbnb. La popularidad de React ha dado como resultado un enorme ecosistema a su alrededor que incluye excelentes herramientas, bibliotecas populares y muchos desarrolladores experimentados.

Una de las razones de la popularidad de React es que es simple. Esto se debe a que se enfoca en hacer una sola cosa muy bien: proporcionar un mecanismo poderoso para construir **componentes de interfaz de usuario (UI)**. Los componentes son piezas de la interfaz de usuario que se pueden componer entre sí para crear un frontend. Además, los componentes pueden ser reutilizables para que puedan utilizarse en diferentes pantallas o incluso en otras aplicaciones.

El enfoque específico de React significa que se puede incorporar a una aplicación existente, incluso si utiliza un framework diferente. Esto se debe a que no necesita tomar el control de toda la aplicación para ejecutarse; se integra perfectamente como parte del frontend de una aplicación.

Los componentes de React se muestran de manera eficiente utilizando un **Document Object Model virtual (Virtual DOM)**. Es posible que esté familiarizado con el DOM real: proporciona la estructura de una página web. Sin embargo, los cambios en el DOM real pueden ser costosos, lo que genera problemas de rendimiento en una aplicación interactiva. React resuelve este problema de rendimiento mediante el uso de una representación en memoria del DOM real llamada Virtual DOM. Antes de que React cambie el DOM real, produce un nuevo Virtual DOM y lo compara con el Virtual DOM actual para calcular la cantidad mínima de cambios requeridos en el DOM real. Luego, el DOM real se actualiza únicamente con esos cambios mínimos.

La reciente incorporación de **Server Components** en React permite una obtención de datos de alto rendimiento y reduce la cantidad de JavaScript enviado desde el servidor al navegador. Combine esto con las **React server functions** y también obtendrá una experiencia de desarrollo extremadamente productiva.

**React Native**, un framework basado en React, nos permite crear aplicaciones multiplataforma para iOS y Android, de manera similar a cómo usamos React para crear aplicaciones web. Las habilidades básicas de desarrollo de React son las mismas en ambas tecnologías, y el código también se puede compartir y reutilizar.

El hecho de que Meta use React para Facebook es un gran beneficio porque garantiza que sea de la más alta calidad; ¡que React rompa Facebook no es bueno para Meta! También significa que se dedica mucho cuidado y consideración para garantizar que las nuevas versiones de React sean económicas de adoptar, lo que ayuda a reducir los costos de mantenimiento de una aplicación.

La simplicidad de React significa que es fácil y rápido de aprender. Hay muchos recursos de aprendizaje excelentes, como este libro. También hay una variedad de herramientas que facilitan enormemente la inicialización de una aplicación React; una de esas herramientas se llama **Vite**, sobre la cual aprenderemos más adelante en este capítulo.

Ahora que estamos empezando a comprender React, crearemos nuestro primer proyecto de React en la siguiente sección.

---

### Crear un proyecto de React

En esta sección, crearemos un proyecto de React y configuraremos Visual Studio Code para que funcione de manera óptima con él. También cubriremos cómo ejecutar una aplicación React en modo de desarrollo y cómo generar una compilación para producción.

Crearemos un proyecto de React usando Vite, una popular herramienta de compilación y servidor de desarrollo para aplicaciones React. Lleve a cabo los siguientes pasos:

1. En una terminal, en una carpeta de su elección, ejecute el siguiente comando para indicarle a Vite que cree un proyecto:

```bash
npm create vite@latest
```

2. Aparecerá una solicitud para el nombre del proyecto. El nombre del proyecto será el nombre de la carpeta que contendrá el código del proyecto. Por lo tanto, ingrese el nombre que elija y presione Enter.

*Figura 1.1 – Creación de un proyecto con Vite*

3. Ahora aparece un mensaje para seleccionar el framework del proyecto. Seleccione **React** usando la tecla de flecha hacia abajo para moverse hasta React y presione Enter.

*Figura 1.2 – Selección del framework React*

4. Por último, se solicita la variante. Seleccione **JavaScript**, usando la tecla de flecha hacia abajo, y presione Enter. Tenga en cuenta que exploraremos la opción de TypeScript en el próximo capítulo.

5. El proyecto se crea en el nombre de carpeta que eligió. La terminal enumera los siguientes tres comandos que sugiere ejecutar:
   - `cd <your-project-name>`: Esto cambiará el directorio de trabajo al que se acaba de crear.
   - `npm install`: Esto instalará los paquetes npm de los que depende el proyecto inicial.
   - `npm run dev`: Esto ejecutará la aplicación en modo de desarrollo.

6. Ejecute los dos primeros comandos sugeridos en la terminal para que el proyecto sea el directorio de trabajo y se instalen las dependencias del proyecto.

A continuación, inspeccionaremos el proyecto antes de ejecutar el último comando sugerido para iniciar la aplicación en modo de desarrollo.

#### Entendiendo el proyecto

Ahora que el proyecto está creado, nos tomaremos un tiempo para comprender las carpetas y archivos que ha creado Vite. Lleve a cabo los siguientes pasos:

1. Abra Visual Studio Code en el directorio del proyecto. Esto se puede hacer usando el siguiente comando en la terminal:

```bash
code .
```

2. Observe las carpetas y archivos en el proyecto dentro del panel Explorador a la izquierda. Las siguientes son breves descripciones de estos:
   - **node_modules**: Contiene todos los paquetes npm dependientes y se creó cuando se instalaron en el proyecto mediante el comando `npm install`.
   - **public**: Almacena recursos estáticos como imágenes para servirlos en la ruta raíz (`/`).
   - **src**: Contiene nuestros archivos de código fuente, incluidos los siguientes:
     - `main.jsx`: Contiene la lógica para cargar la aplicación React en la página web raíz, `index.html`.
     - `index.css`: Estilos globales para la aplicación.
     - `App.jsx`: El componente React de nivel superior llamado `App`.
     - `App.css`: Estilos para el componente `App`.
   - **.gitignore**: Especifica qué carpetas y archivos deben ser ignorados por Git.
   - **eslint.config.js**: Este es un archivo de configuración para ESLint, que es una herramienta que verifica el código en busca de posibles errores y desviaciones de los estándares de codificación.
   - **index.html**: Esta es la página web raíz. La aplicación React se carga dentro de esta en tiempo de ejecución.
   - **package.json**: Define el nombre del proyecto, la versión, las dependencias, los scripts y otros metadatos del proyecto.
   - **package-lock.json**: Contiene las versiones exactas de las dependencias, lo que garantiza la coherencia cuando el proyecto se ejecuta en diferentes entornos.
   - **README.md**: Contiene información sobre la plantilla de Vite utilizada para crear el proyecto. Por lo general, se sobrescribe con información sobre la aplicación que se está desarrollando, como una descripción general y los pasos para configurar el entorno de desarrollo.
   - **vite.config.js**: Contiene la configuración para Vite. Para este proyecto, se ha especificado un plugin de Vite para React.

Ahora que estamos empezando a comprender las carpetas y los archivos del proyecto React, configuraremos completamente el linting.

#### Añadir linting a Visual Studio Code

El *linting* es el proceso de verificar el código en busca de posibles problemas. Es una práctica común utilizar herramientas de linting para detectar problemas de forma temprana en el proceso de desarrollo a medida que se escribe el código. ESLint es una herramienta popular que puede realizar linting en código React y TypeScript. Afortunadamente, Vite ya ha instalado y configurado ESLint en nuestro proyecto.

Editores como Visual Studio Code se pueden integrar con ESLint para resaltar problemas potenciales. Lleve a cabo los siguientes pasos para instalar la extensión de ESLint en Visual Studio Code:

1. Abra el área de **EXTENSIONS** en Visual Studio Code. La opción Extensiones se encuentra en el menú Preferencias dentro del menú Archivo en Windows o en el menú Configuración… dentro del menú Code en una Mac.
2. Aparecerá una lista de extensiones en el lado izquierdo y el cuadro de búsqueda ubicado encima de la lista se puede utilizar para buscar una extensión en particular. Ingrese `eslint` en el cuadro de búsqueda.

*Figura 1.3 – Extensión de ESLint para Visual Studio Code*

3. Una extensión de Microsoft llamada **ESLint** debería aparecer en la parte superior de la lista.
4. Haga clic en el botón **Install** para instalar la extensión.
5. Ahora, debemos asegurarnos de que la extensión de ESLint esté configurada para verificar React y TypeScript. Abra el área de Configuración (**Settings**) en Visual Studio Code.
6. En el cuadro de búsqueda de Settings, ingrese `eslint: probe` y seleccione la pestaña **Workspace**:

*Figura 1.4 – Configuración de ESLint Probe en Visual Studio Code*

7. Esta configuración define los lenguajes a utilizar cuando ESLint verifica el código.
8. Asegúrese de que `typescript` y `typescriptreact` estén en la lista. Si no es así, agréguelos mediante el botón **Add Item**. La extensión ESLint para Visual Studio Code ya está instalada y configurada en el proyecto.

Antes de continuar, hay una regla de ESLint que vamos a desactivar, que es la verificación de los tipos de props de los componentes de React (*prop types*). No utilizaremos esta característica de React porque eventualmente tiparemos fuertemente los componentes de React usando TypeScript. Abra `eslint.config.js` y agregue la línea resaltada al campo `rules` para desactivar esta regla:

```javascript
rules: { ..., ‘react/prop-types’: ‘off’, }
```

> [!NOTE]
> Para obtener más información sobre ESLint, consulte el siguiente enlace: [https://eslint.org/](https://eslint.org/).

A continuación, agregaremos el formateo automático de código al proyecto.

#### Añadir formateo de código

La siguiente herramienta que configuraremos formatea automáticamente el código. El formateo automático asegura que el código tenga un formato consistente, lo que ayuda a su legibilidad. Tener un código con un formato consistente también ayuda a los desarrolladores a ver los cambios importantes en una revisión de código, en lugar de diferencias de formato.

Prettier es una herramienta popular capaz de formatear código React y TypeScript. Desafortunadamente, Vite no la instala ni la configura por nosotros. Lleve a cabo los siguientes pasos para instalar y configurar Prettier en el proyecto:

1. Instale Prettier utilizando el siguiente comando en la terminal de Visual Studio Code:

```bash
npm install --save-dev prettier
```

La opción `--save-dev` especifica que Prettier debe instalarse como una dependencia solo de desarrollo. Esto se debe a que Prettier solo es necesario durante el desarrollo y no en tiempo de ejecución.

Una versión abreviada de este comando es la siguiente:

```bash
npm i -D prettier
```

Aquí, `i` es la abreviatura de `install`, y `-D` es la abreviatura de `--save-dev`.

2. Prettier tiene reglas de estilo que se superponen con ESLint, por lo que instalamos la siguiente biblioteca para permitir que Prettier asuma la responsabilidad de las reglas de estilo de ESLint:

```bash
npm i -D eslint-config-prettier
```

3. La configuración de ESLint debe actualizarse para permitir que Prettier gestione las reglas de estilo. Abra el archivo `eslint.config.js`, que se encuentra en la raíz del proyecto, y agregue las siguientes líneas:

```javascript
... import prettier from “eslint-config-prettier”; export default [ ..., prettier ];
```

4. Prettier se puede configurar en un archivo llamado `.prettierrc.json`. Cree este archivo con el siguiente contenido en la carpeta raíz:

```json
{ “printWidth”: 100, “singleQuote”: true, “semi”: true, “tabWidth”: 2, “trailingComma”: “all”, “endOfLine”: “auto” }
```

Hemos especificado lo siguiente:
- Las líneas se ajustan a los 100 caracteres
- Los delimitadores de cadenas son comillas simples
- Los puntos y coma se colocan al final de las sentencias
- El nivel de sangría es de dos espacios
- Se agrega una coma final a los arrays y objetos multilínea
- Se mantienen los saltos de línea existentes

> [!NOTE]
> Se puede encontrar más información sobre las opciones de configuración en el siguiente enlace: [https://prettier.io/docs/en/options.html](https://prettier.io/docs/en/options.html).

Prettier ya está instalado y configurado en el proyecto.

Visual Studio Code puede integrarse con Prettier para formatear automáticamente el código cuando se guardan los archivos fuente. Por lo tanto, instalemos una extensión de Prettier en Visual Studio Code:

1. Abra el área de **EXTENSIONS** en Visual Studio Code e ingrese `prettier` en el cuadro de búsqueda. Una extensión llamada **Prettier - Code formatter** debería aparecer en la parte superior de la lista:

*Figura 1.5 – Extensión Prettier para Visual Studio Code*

2. Haga clic en el botón **Install** para instalar la extensión.
3. A continuación, abra el área de **Settings** en Visual Studio Code. Seleccione la pestaña **Workspace** y asegúrese de que la opción **Format On Save** esté marcada:

*Figura 1.6 – Configuración Format On Save en Visual Studio Code*

Esta configuración le indica a Visual Studio Code que formatee automáticamente el código en los archivos que se guarden.

4. Hay una opción más por configurar. Este es el formateador predeterminado que Visual Studio Code debe usar para formatear el código. Haga clic en la pestaña **Workspace** y asegúrese de que **Default Formatter** esté establecido en **Prettier - Code formatter**:

*Figura 1.7 – Configuración Default Formatter en Visual Studio Code*

La extensión de Prettier para Visual Studio Code ya está instalada y configurada en el proyecto. A continuación, ejecutaremos la aplicación en modo de desarrollo.

#### Iniciar la aplicación en modo de desarrollo

Vite tiene un servidor de desarrollo en el que se puede ejecutar la aplicación del proyecto. Lleve a cabo los siguientes pasos para ejecutar la aplicación en modo de desarrollo:

1. Vite ya ha creado un script npm llamado `dev`, que ejecuta la aplicación en modo de desarrollo. Ejecute este script en la terminal de la siguiente manera:

```bash
npm run dev
```

2. La aplicación comenzará a ejecutarse en el servidor de desarrollo de Vite en `localhost` en el puerto 5173 de forma predeterminada (el puerto se puede cambiar en la configuración de Vite). La URL del navegador para la aplicación aparecerá en la terminal, que es [http://localhost:5173/](http://localhost:5173/) por defecto. Vaya a esta URL en un navegador y verá la aplicación en ejecución:

*Figura 1.8 – La aplicación React ejecutándose en modo de desarrollo*

Vite no solo sirve la aplicación en su servidor de desarrollo, sino que también traspila los componentes de React a código JavaScript que se puede ejecutar en el navegador. ¡Hace todo esto increíblemente rápido!

Haremos un cambio simple en el código ahora mientras la aplicación aún se está ejecutando. En el editor de código, abra el archivo `index.html` en la raíz del proyecto. Busque el elemento `title` de HTML, que especifica el título que aparece en la pestaña del navegador.

Realice un cambio en el contenido del elemento `title` agregando un signo de exclamación al final:

```html
<title>Vite + React!</title>
```

Observe que el título de la pestaña del navegador se actualiza inmediatamente después de guardar los cambios en el archivo `index.html`:

*Figura 1.9 – Título de la aplicación actualizado*

Vite realiza automáticamente cualquier transpilación requerida y recarga la aplicación en el navegador de manera muy eficiente.

Detenga la ejecución de la aplicación antes de continuar. La tecla de acceso rápido para detener la aplicación es `Ctrl + C`.

Hemos visto cómo Vite proporciona una experiencia de desarrollo productiva. A continuación, produciremos una compilación de producción.

#### Producir una compilación de producción

Una compilación de producción traspila los componentes de React a código JavaScript de manera similar a cuando se ejecuta la aplicación en modo de desarrollo. Sin embargo, lleva a cabo varios otros procesos adicionales para que la aplicación se ejecute de manera eficiente en producción.

Uno de los procesos es la **minificación**. La minificación es el proceso de eliminar todos los caracteres innecesarios del código fuente sin afectar su funcionalidad, lo que incluye eliminar espacios en blanco y comentarios, y acortar los nombres de las variables. Esto da como resultado un tamaño de archivo más pequeño, lo que conduce a tiempos de carga más rápidos.

Otro proceso también implica fusionar archivos para que el código se descargue y ejecute de manera eficiente en producción. Este proceso a menudo se denomina **empaquetado (bundling)**, y el archivo de salida a menudo se denomina **bundle**. Los bundles a menudo se separan en fragmentos más pequeños (*chunks*) para reducir el tiempo de carga de la aplicación (por ejemplo, un paquete por página en la aplicación). Los empaquetadores también realizan **tree-shaking** para eliminar el código redundante, manteniendo el tamaño de los bundles lo más pequeño posible para un mejor rendimiento.

Lleve a cabo los siguientes pasos para generar una compilación de producción de nuestra aplicación:

1. Vite ya ha creado un script npm llamado `build` que produce todos los artefactos para el despliegue en producción. Ejecute este script en la terminal de la siguiente manera:

```bash
npm run build
```

2. Después de unos segundos, los artefactos de despliegue se colocarán en una carpeta `dist`.
3. Abra la carpeta `dist`: contiene muchos archivos. El archivo raíz es `index.html`, que hace referencia a los otros archivos JavaScript, CSS e imágenes. Abra algunos de los archivos y vea su contenido; verá que están optimizados para producción sin espacios en blanco y con el JavaScript minificado.

Esto completa la compilación de producción y la configuración del proyecto React con Vite. He aquí un resumen de los puntos clave para crear un proyecto React con Vite:

- Vite puede configurar rápidamente un proyecto de React usando el comando `npm create vite@latest`.
- Vite configura muchas características útiles del proyecto, como el linting. El uso de la extensión de Visual Studio Code para ESLint mejora la experiencia de linting al escribir código.
- Una característica que Vite no configura es el formateo automático de código. Sin embargo, Prettier se puede instalar y configurar para proporcionar esta capacidad.
- La aplicación se puede ejecutar en modo de desarrollo mediante el comando `npm run dev`, y se puede crear una compilación de producción con `npm run build`.

Guarde este proyecto en un lugar seguro porque continuaremos usándolo en la siguiente sección cuando comprendamos la estructura de una aplicación React.

---

### Entender la estructura de una aplicación React

En esta sección, exploraremos el punto de entrada de la aplicación React creada en la sección anterior y cómo se carga en la página HTML. Luego aprenderemos sobre el árbol de componentes de React y cómo se define un componente.

#### Entender el punto de entrada de React

El punto de entrada de esta aplicación React se encuentra en el archivo `main.jsx` dentro de la carpeta `src`. Abra este archivo e inspeccione su contenido. Contiene una llamada a la función `createRoot` de React de la siguiente manera:

```jsx
createRoot(document.getElementById(‘root’)).render( <StrictMode> <App /> </StrictMode>, )
```

Aquí está la explicación de este código:
- Como sugiere el nombre, `createRoot` crea una raíz en el documento HTML para los componentes de React. `createRoot` toma un elemento del DOM donde colocar los componentes de React, que es el elemento que tiene el ID de `'root'` en este caso.
- `createRoot` devuelve un objeto que contiene una función `render`. La función `render` recibe los componentes de React que se mostrarán en el elemento DOM raíz. Este proceso de visualización a menudo se denomina **renderizado (rendering)**. En este caso, los componentes de React a mostrar son un componente `App` dentro de un componente `StrictMode`. La sintaxis para los componentes de React a mostrar es JSX.

> [!NOTE]
> **JSX** significa *JavaScript XML*, que es una extensión de sintaxis para JavaScript que permite a los desarrolladores escribir código similar a HTML dentro de JavaScript/TypeScript. Permite la creación de componentes de React de una manera legible y declarativa, lo que facilita la visualización de la estructura de la UI. JSX debe transpilarse a llamadas a funciones regulares de JavaScript utilizando una herramienta como Babel. Para obtener más información sobre JSX, consulte el siguiente enlace: [https://react.dev/learn/writing-markup-with-jsx](https://react.dev/learn/writing-markup-with-jsx).

El componente `StrictMode` es un componente especial de React que ayuda a identificar problemas potenciales. Activa comprobaciones adicionales y emite advertencias a la consola del navegador en modo de desarrollo.

A continuación, nos tomaremos un tiempo para comprender el árbol de componentes de React.

#### Entender el árbol de componentes de React

Una aplicación de React está estructurada en un **árbol de componentes**. El componente raíz es el componente en la parte superior del árbol. En nuestro proyecto, el componente raíz es el componente `StrictMode`.

Los componentes de React se pueden anidar dentro de otro componente de React. El componente `App` está anidado dentro del componente `StrictMode` en nuestro proyecto. Esto es poderoso porque se puede colocar cualquier componente dentro de `StrictMode`, no necesariamente tiene que ser `App`.

Los componentes de React pueden generar uno o más componentes de React adicionales. El siguiente es un ejemplo de un árbol de componentes de React:

*Figura 1.10 – Un árbol de componentes de React*

Si nuestro componente `App` renderizara otros componentes de React (`Header`, `Main` y `Footer`), el árbol de componentes sería como en la figura anterior.

A continuación, comenzaremos a comprender cómo se define un componente de React.

#### Entender un componente de React

Ahora comprenderemos la implementación de un componente básico de React.

Abra `App.jsx`, que contiene la definición del componente `App`. No entenderemos completamente el componente en esta etapa, pero observe que es simplemente una función regular de JavaScript.

Enfoquémonos en lo que devuelve la función: devuelve JSX que representa la interfaz de usuario. Observe que el JSX hace referencia a elementos HTML como `div`, `a`, `h1`, `button` y `p`. Por lo tanto, JSX puede generar elementos HTML así como otros componentes de React. Actualmente, el componente `App` solo genera elementos HTML y ningún otro componente de React.

Observe el elemento JSX de nivel superior en la declaración `return`, `<>`, que no tiene un nombre. Este es un **Fragmento de React (React fragment)**, que proporciona una forma de agrupar elementos sin crear un elemento en el DOM.

Siguiendo con el JSX, observe el código JavaScript entre llaves. Por ejemplo, mire el JSX del elemento `button`:

```jsx
<button onClick={() => setCount((count) => count + 1)}> count is {count} </button>
```

El atributo `onClick` se establece en una función anónima de JavaScript que llama a otra función llamada `setCount`. Comprenderemos qué hace el atributo `onClick` más adelante en este capítulo; el punto clave por ahora es que JSX puede incluir JavaScript. Observe también que el contenido del botón contiene una referencia a una variable de JavaScript llamada `count`. Hacer referencia a funciones y variables de JavaScript en JSX permite que la salida del componente sea dinámica.

Eso nos lleva al final de esta sección. Recapitulemos:
- El punto de entrada de una aplicación Vite React se encuentra en el archivo `main.jsx`, donde se utiliza la función `createRoot` para renderizar componentes de React.
- Una aplicación React está estructurada en un árbol de componentes.
- Un componente de React es una función JavaScript regular que devuelve JSX que representa la interfaz de usuario dinámica.

A continuación, es hora de crear un componente de React.

---

### Crear un componente

En esta sección, crearemos un componente de React y haremos referencia a él dentro del componente `App`.

#### Crear un componente Alert básico

Vamos a crear un componente que muestre una alerta, al que simplemente llamaremos `Alert`. Consistirá en un icono, un encabezado y un mensaje.

> [!NOTE]
> El nombre de un componente de React debe comenzar con una letra mayúscula. Si el nombre de un componente comienza con una letra minúscula, se trata como un elemento del DOM y no se renderizará correctamente.

Lleve a cabo los siguientes pasos para crear el componente en el proyecto:

1. Cree un nuevo archivo en la carpeta `src` llamado `Alert.jsx`.

> [!NOTE]
> El nombre del archivo para los componentes no es importante para React ni para el transpilador de React. Es una práctica común usar el mismo nombre que el componente, ya sea en PascalCase o snake_case. Sin embargo, la extensión del archivo debe ser `.js` o `.jsx` para que los transpiladores de React los reconozcan como componentes de React.

2. Abra el archivo `Alert.jsx` e ingrese el siguiente código:

```jsx
function Alert() { return ( <div> <div> <span role=”img” aria-label=”Warning”> </span> <span>Oh no!</span> </div> <div>Something went wrong</div> </div> ); }
```

Recuerde que los fragmentos de código están disponibles en línea para copiar. El enlace al fragmento anterior se encuentra en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter01/creating-a-component](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter01/creating-a-component)

El componente renderiza los siguientes elementos:
- Un icono de advertencia (tenga en cuenta que este es un emoji de advertencia)
- Un título: *Oh no!*
- Un mensaje: *Something went wrong*

> [!NOTE]
> Los atributos `role` y `aria-label` se han agregado al elemento `span` que contiene el icono de advertencia para ayudar a los lectores de pantalla a comprender que se trata de una imagen con el título de advertencia.
> - Para más información sobre el rol `img`, consulte: [https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/img_role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/img_role).
> - Para más información sobre el atributo `aria-label`, consulte: [https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-label](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-label).

Alternativamente, un componente de React se puede implementar usando la sintaxis de funciones flecha (*arrow functions*). El siguiente fragmento de código es una versión con sintaxis de flecha del componente `Alert`:

```jsx
const Alert = () => { return ( <div> <div> <span role=”img” aria-label=”Warning”> </span> <span>Oh no!</span> </div> <div>Something went wrong</div> </div> ); };
```

> [!NOTE]
> No existen diferencias significativas entre las funciones flecha y las funciones normales en el contexto de los componentes funcionales de React. Por lo tanto, depende de las preferencias personales cuál elija. Este libro generalmente utiliza la sintaxis de funciones regulares porque tiene menos caracteres que escribir; sin embargo, si lo desea, puede encontrar más información sobre las funciones flecha de JavaScript aquí: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions).

¡Felicitaciones, ha creado su primer componente de React!

Hay un error de linting en el archivo que acabamos de crear, resaltado por ESLint. El error es que el componente `Alert` no se utiliza. Ignore el error por ahora; lo resolveremos más adelante en este capítulo.

*Figura 1.11 – Error de ESLint*

Si la aplicación estuviera ejecutándose, el componente `Alert` aún no aparecería en el navegador. Esto se debe a que aún no se ha agregado al árbol de componentes de React. Haremos esto en la siguiente sección.

#### Añadir Alert al componente App

Volviendo al componente `Alert` en nuestro proyecto, haremos referencia a `Alert` en el componente `App`. También eliminaremos el contenido existente en el componente `App` para que solo renderice la alerta. Para hacer esto, lleve a cabo los siguientes pasos:

1. Primero, debemos exportar el componente `Alert` para que esté disponible en otros archivos. Abra `Alert.jsx` y agregue la palabra clave `export` antes de la función `Alert`:

```jsx
export function Alert() { ... }
```

> [!NOTE]
> Es una práctica común tener cada componente de React en un archivo separado. Esto ayuda a evitar que los archivos se vuelvan demasiado grandes y mejora la legibilidad de la base de código.

Observe que el error de ESLint ahora está resuelto porque `Alert` ahora puede ser utilizado potencialmente por otros archivos.

2. Ahora, podemos importar `Alert` en el archivo `App.jsx`. Abra `App.jsx` y agregue la siguiente declaración de importación en la parte superior del archivo:

```jsx
import { Alert } from ‘./Alert’; 
```

Elimine las otras declaraciones de importación para que la alerta sea la única importación.

3. Ahora podemos hacer referencia a `Alert` en el JSX del componente `App`. Reemplace la definición del componente `App` con lo siguiente para que solo renderice la alerta:

```jsx
function App() { return <Alert />; }
```

4. Ejecute la aplicación en modo de desarrollo ejecutando el comando `npm run dev` en la terminal y abriendo la URL de la aplicación en un navegador. El componente ahora se mostrará en el navegador en la página:

*Figura 1.12 – Componente Alert en la aplicación*

¡Genial! Si ha notado que el componente `Alert` no tiene un estilo visual atractivo, no se preocupe: aprenderemos a aplicarle estilos en el [Capítulo 4](https://subscription.packtpub.com/book/web-development/9781836643173/4), *Enfoques para estilizar frontends en React*.

He aquí un resumen de esta sección:
- Los nombres de los componentes de React comienzan con una letra mayúscula y el nombre del archivo debe tener una extensión `.js` o `.jsx`.
- Creamos un componente `Alert` que muestra un icono de advertencia, un título y un mensaje.
- Por lo general, un componente de React está estructurado en su propio archivo y, por lo tanto, debe exportarse antes de que se haga referencia a él en otro componente de React. Exportamos el componente `Alert`, lo importamos y lo usamos dentro del componente `App`.

A continuación, aprenderemos cómo hacer que el componente `Alert` sea un poco más flexible.

---

### Uso de props

Actualmente, el componente `Alert` es bastante inflexible. Por ejemplo, el consumidor de la alerta no puede cambiar el encabezado ni el mensaje. Por el momento, el encabezado o el mensaje deben modificarse dentro del propio `Alert`. Las **props** resuelven este problema, y aprenderemos sobre ellas en esta sección.

> [!NOTE]
> *Props* es la abreviatura de propiedades (*properties*). La comunidad de React a menudo se refiere a ellas como props, por lo que lo haremos en este libro.

#### Entender las props

El parámetro `props` es un parámetro opcional que se pasa a un componente de React. Este parámetro es un objeto que contiene las propiedades de nuestra elección, lo que permite que un componente padre pase datos. El siguiente fragmento de código muestra un parámetro `props` en un componente `ContactDetails`:

```jsx
function ContactDetails(props) { console.log(props.name); console.log(props.email); ... }
```

El parámetro `props` contiene las propiedades `name` y `email` en el fragmento de código anterior.

> [!NOTE]
> El parámetro no tiene que llamarse `props`, pero es una práctica común.

Las props se pasan a un componente en JSX como atributos. Los nombres de las props deben coincidir con lo definido en el componente. Aquí hay un ejemplo de cómo pasar props al componente `ContactDetails` anterior:

```jsx
<ContactDetails name=”Fred” email=”fred@somewhere.com” />
```

Por lo tanto, las props hacen que la salida del componente sea flexible. Los consumidores del componente pueden pasar las props adecuadas al componente para obtener el resultado deseado.

A continuación, agregaremos algunas props al componente `Alert` en el que hemos estado trabajando.

#### Añadir props al componente Alert

En el proyecto, lleve a cabo los siguientes pasos para agregar props al componente `Alert` y hacerlo más flexible:

1. Comience ejecutando la aplicación en modo de desarrollo si aún no se está ejecutando. Hágalo ejecutando el comando `npm run dev` en la terminal.
2. Abra `Alert.jsx` y agregue un parámetro `props` a la función:

```jsx
export function Alert(props) { ... }
```

Definiremos las siguientes props para la alerta:
- `type`: Será `"information"` o `"warning"` y determinará el icono en la alerta.
- `heading`: Determinará el encabezado de la alerta.
- `children`: Determinará el contenido de la alerta. La prop `children` es en realidad una prop especial utilizada para el contenido principal de un componente.

3. Actualice el JSX del componente `Alert` para usar las props de la siguiente manera:

```jsx
export function Alert(props) { return ( <div> <div> <span role=”img” aria-label={ props.type === ‘warning’ ? ‘Warning’ : ‘Information’ } > {props.type === ‘warning’ ? ‘’ : ‘’} </span> <span>{props.heading}</span> </div> <div>{props.children}</div> </div> ); }
```

Es posible que observe que el componente `Alert` en el navegador ahora no muestra nada más que un icono de información (este es un emoji de información); esto se debe a que el componente `App` aún no le pasa ninguna prop a `Alert`.

4. Abra `App.jsx` y actualice el componente `Alert` en el JSX para pasar props de la siguiente manera:

```jsx
export default function App() { return ( <div className=”App”> <Alert type=”information” heading=”Success”> Everything is really good! </Alert> </div> ); }
```

Observe que el componente `Alert` ya no se cierra automáticamente (*self-closing*), por lo que *Everything is really good!* se puede pasar dentro de su contenido. El contenido se pasa a la prop `children`.

La aplicación ahora muestra el componente `Alert` configurado:

*Figura 1.13 – Componente Alert configurado en la aplicación*

Podemos limpiar un poco el código del componente `Alert` desestructurando el parámetro `props`.

> [!NOTE]
> La **desestructuración** es una característica de JavaScript que permite desempaquetar propiedades de un objeto. Para más información, consulte: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

5. Abra `Alert.jsx` nuevamente, desestructure el parámetro de la función y use las props desempaquetadas de la siguiente manera:

```jsx
export function Alert({ type, heading, children }) { return ( <div> <div> <span role=”img” aria-label={ type === ‘warning’ ? ‘Warning’ : ‘Information’ } > {type === ‘warning’ ? ‘’ : ‘’} </span> <span>{heading}</span> </div> <div>{children}</div> </div> ); }
```

Esto es un poco más limpio porque usamos las props desempaquetadas directamente en lugar de tener que hacer referencia a ellas a través del parámetro `props`.

6. Queremos que la prop `type` tenga como valor predeterminado `"information"`. Defina este valor predeterminado de la siguiente manera:

```jsx
export function Alert({ type = ‘information’, heading, children }) { ... }
```

Eso completa la implementación de las props en el componente `Alert` por ahora. He aquí un breve resumen sobre las props:
- Las props permiten que el JSX consumidor configure un componente y se pasan como atributos de JSX.
- Las props se reciben en la definición del componente en un parámetro de tipo objeto y luego se pueden usar en su JSX.

A continuación, continuaremos haciendo que el componente `Alert` sea más sofisticado permitiendo que el usuario lo cierre.

---

### Uso del estado

El **estado (state)** de un componente de React es una variable especial que puede cambiar a lo largo del ciclo de vida de un componente. En esta sección, aprenderemos sobre la variable de estado y la usaremos dentro de nuestro componente `Alert`. Usaremos el estado para permitir que el usuario cierre la alerta.

#### Entender el estado

No existe una lista predefinida de estados; definimos lo que sea apropiado para un componente dado. Algunos componentes ni siquiera necesitarán ningún estado; el componente `Alert` no ha requerido un estado para los requisitos hasta ahora.

El estado es una parte clave para hacer que un componente sea interactivo. Cuando un usuario interactúa con un componente, es posible que la salida del componente deba cambiar. Un cambio en el estado hace que el componente se actualice, lo que más comúnmente se denomina **re-renderizado (re-render)**.

El estado se define mediante una función `useState` de React. La función `useState` es uno de los **Hooks** de React. Hay un capítulo completo sobre React Hooks en el [Capítulo 3](https://subscription.packtpub.com/book/web-development/9781836643173/3), *Uso de React Hooks*.

La sintaxis para `useState` es la siguiente:

```javascript
const [state, setState] = useState(initialState);
```

Aquí están los puntos clave:
- El valor del estado inicial se pasa a `useState`. Si no se pasa ningún valor, inicialmente será `undefined`.
- `useState` devuelve una tupla que contiene el valor del estado actual y una función para actualizar el valor del estado. La tupla se desestructura en el fragmento de código anterior.
- El nombre de la variable de estado es `state` en el fragmento de código anterior, pero podemos elegir cualquier nombre significativo.
- También podemos elegir el nombre de la función de actualización del estado (*setter*), pero es una práctica común usar el mismo nombre que la variable de estado precedido por `set`.
- Se pueden definir múltiples estados definiendo múltiples instancias de `useState`. Por ejemplo, aquí hay definiciones para estados de carga y de error:

```javascript
const [loading, setLoading] = useState(true); const [error, setError] = useState();
```

A continuación, implementaremos el estado en el componente `Alert` para determinar si es visible o no.

#### Implementar un estado visible en el componente Alert

Comenzaremos implementando una funcionalidad en el componente `Alert` que le permita al usuario cerrarlo. Una parte clave de esa funcionalidad es controlar la visibilidad de la alerta, lo cual haremos con un estado `visible`. Este estado será `true` o `false` e inicialmente se establecerá en `true`.

Siga estos pasos para implementar un estado visible en `Alert`:

1. Si la aplicación aún no se está ejecutando, hágalo ejecutando el comando `npm run dev` en la terminal.
2. Abra `Alert.jsx` en el proyecto.
3. Agregue la siguiente declaración de importación en la parte superior del archivo para importar el Hook `useState` desde React:

```javascript
import { useState } from ‘react’;
```

4. Defina el estado `visible` de la siguiente manera en la definición del componente:

```jsx
export function Alert(...) { const [visible, setVisible] = useState(true); return ( ... ); }
```

5. Después de la declaración de estado, agregue una condición que devuelva `null` si el estado `visible` es `false`. Esto significa que no se renderizará nada:

```jsx
export function Alert(...) { const [visible, setVisible] = useState(true); if (!visible) { return null; } return ( ... ); }
```

El componente se renderizará en la aplicación igual que antes porque el estado `visible` es `true`. Intente cambiar el valor del estado inicial a `false` y verá que desaparece.

Actualmente, el componente `Alert` hace uso del valor del estado `visible` al no renderizar nada si es `false`. Sin embargo, el componente aún no está actualizando el estado `visible`, es decir, `setVisible` no se utiliza por el momento. Actualizaremos el estado `visible` después de implementar un botón de cierre, lo cual haremos a continuación.

#### Añadir un botón de cierre a Alert

Agregaremos un botón de cierre al componente `Alert` para permitir que el usuario lo cierre. Haremos esto configurable para que el consumidor de la alerta pueda elegir si se renderiza el botón de cierre.

Lleve a cabo los siguientes pasos:

1. Comience abriendo `Alert.jsx` y agregue una prop `closable`:

```jsx
export function Alert({ type = “information”, heading, children, closable }) { ... }
```

El consumidor del componente `Alert` usará la prop `closable` para especificar si aparece el botón de cierre.

2. Agregue un botón de cierre entre el encabezado y el contenido de la siguiente manera:

```jsx
export function Alert(...) { ... return ( <div> <div> ... <span>{heading}</span> </div> <button aria-label=”Close”> <span role=”img” aria-label=”Close”></span> </button> <div>{children}</div> </div> ); }
```

Observe que al elemento `span` que contiene el icono de cierre se le asigna un rol `"img"` y una etiqueta `"Close"` para ayudar a los lectores de pantalla. Asimismo, al botón también se le asigna una etiqueta `"Close"` para ayudar a los lectores de pantalla.

El botón de cierre aparece en el componente `Alert` de la siguiente manera:

*Figura 1.14 – El botón de cierre en el componente Alert*

Por el momento, el botón de cierre siempre se renderizará en lugar de hacerlo solo cuando la prop `closable` sea `true`. Podemos usar una expresión de cortocircuito lógico AND de JavaScript (representada por los caracteres `&&`) para renderizar el botón de cierre condicionalmente. Para hacer esto, realice los siguientes cambios:

```jsx
import { useState } from ‘react’; export function Alert(...) { ... return ( <div> <div> ... <span>{heading}</span> </div> {closable && ( <button aria-label=”Close”> <span role=”img” aria-label=”Close”> </span> </button> )} <div>{children}</div> </div> ); }
```

Si `closable` es un valor *falsy*, la expresión se cortocircuitará y, en consecuencia, no renderizará el botón. Sin embargo, si `closable` es *truthy*, el botón se renderizará.

> [!NOTE]
> - Consulte el siguiente enlace para más información sobre expresiones de cortocircuito lógico AND: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND).
> - Consulte los siguientes enlaces para conocer los valores *falsy* de JavaScript: [https://developer.mozilla.org/en-US/docs/Glossary/Falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) y [https://developer.mozilla.org/en-US/docs/Glossary/Truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) para valores *truthy*.

3. Abra `App.jsx` y pase la prop `closable` a `Alert`:

```jsx
export default function App() { return ( <div className=”App”> <Alert type=”information” heading=”Success” closable > Everything is really good! </Alert> </div> ); }
```

Observe que no se ha definido explícitamente un valor en el atributo `closable`. Podríamos haber pasado el valor de la siguiente manera:

```jsx
closable={true}
```

Sin embargo, no es necesario pasar el valor en un atributo booleano. Si el atributo booleano está presente en un elemento, su valor es automáticamente `true`.

Cuando se especifica el atributo `closable`, el botón de cierre aparece en el componente `Alert` como antes, en la Figura 1.13. Cuando no se especifica el atributo `closable`, el botón de cierre no aparece:

*Figura 1.15 – El botón de cierre no está en el componente Alert cuando closable no se especifica*

¡Excelente!

He aquí un breve resumen de lo que hemos aprendido hasta ahora sobre el estado en React:
- El estado se define mediante el Hook `useState` de React.
- El valor inicial del estado se puede pasar al Hook `useState`.
- `useState` devuelve una variable de estado que se puede usar para renderizar elementos condicionalmente.
- `useState` también devuelve una función que se puede utilizar para actualizar el valor del estado.

Es posible que haya notado que el botón de cierre en realidad no cierra la alerta. En la siguiente sección, rectificaremos esto a medida que aprendamos sobre los eventos en React.

---

### Uso de eventos

Los eventos son otra parte clave para permitir que un componente sea interactivo. En esta sección, comprenderemos qué son los eventos de React y cómo usar eventos en elementos del DOM. También aprenderemos a crear nuestros propios eventos de React.

Continuaremos ampliando la funcionalidad del componente `Alert` a medida que aprendamos sobre los eventos. Comenzaremos finalizando la implementación del botón de cierre antes de crear un evento para cuando la alerta se haya cerrado.

#### Entender los eventos

Los eventos del navegador ocurren cuando el usuario interactúa con los elementos del DOM. Por ejemplo, hacer clic en un botón genera un evento `click` desde ese botón.

Se puede ejecutar lógica cuando se genera un evento. Por ejemplo, se puede cerrar una alerta cuando se hace clic en su botón de cierre. Se puede registrar en un elemento una función llamada **manejador de eventos (event handler)** (a veces denominada *event listener*) para un evento que contenga la lógica a ejecutar cuando ocurre ese evento en particular.

> [!NOTE]
> Consulte el siguiente enlace para más información sobre los eventos del navegador: [https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events).

Los eventos en React son muy similares a los eventos nativos del navegador. De hecho, los eventos de React son un contenedor (*wrapper*) sobre los eventos nativos del navegador.

Los manejadores de eventos en React generalmente se registran en un elemento en JSX mediante un atributo. El siguiente fragmento de código registra un manejador de eventos de clic llamado `handleClick` en un elemento `button`:

```jsx
<button onClick={handleClick}>...</button>
```

A continuación, volveremos a nuestro componente `Alert` e implementaremos un manejador de clics en el botón de cierre que cierre la alerta.

#### Implementar un manejador de clics para el botón de cierre en el alert

Por el momento, nuestro componente `Alert` contiene un botón de cierre, pero no sucede nada cuando se hace clic en él. La alerta también contiene un estado `visible` que dicta si se muestra la alerta. Por lo tanto, para finalizar la implementación del botón de cierre, debemos agregar un manejador de eventos cuando se hace clic en él que establezca el estado `visible` en `false`. Lleve a cabo los siguientes pasos para hacer esto:

1. Si la aplicación aún no se está ejecutando, hágalo ejecutando el comando `npm run dev` en la terminal.
2. Abra `Alert.jsx` y registre un manejador de clics en el botón de cierre de la siguiente manera:

```jsx
<button aria-label=”Close” onClick={handleCloseClick}>
```

Hemos registrado un manejador de clics llamado `handleCloseClick` en el botón de cierre.

3. Luego debemos implementar la función `handleCloseClick` en el componente. Cree una función vacía para comenzar, justo encima de la declaración `return`:

```jsx
export function Alert(...) { const [visible, setVisible] = useState(true); if (!visible) { return null; } function handleCloseClick() {} return ( ... ); }
```

Esto puede parecer un poco extraño porque hemos colocado la función `handleCloseClick` dentro de otra función, `Alert`. El manejador debe estar dentro de la función `Alert` para que tenga acceso a las props y al estado.

Se puede utilizar la sintaxis de funciones flecha para los manejadores de eventos si se prefiere. Una versión con función flecha del manejador es la siguiente:

```jsx
export function Alert(...) { const [visible, setVisible] = useState(true); if (!visible) { return null; } const handleCloseClick = () => {} return ( ... ); } 
```

Los manejadores de eventos también se pueden agregar directamente al elemento en JSX de la siguiente manera:

```jsx
<button aria-label=”Close” onClick={() => {}}> 
```

En el componente `Alert`, nos quedaremos con la función de manejador de eventos con nombre `handleCloseClick`.

4. Ahora, podemos usar la función de actualización del estado visible para hacer que el estado `visible` sea `false` en el manejador de eventos:

```javascript
function handleCloseClick() { setVisible(false); }
```

Si hace clic en el botón de cierre de la aplicación, la alerta desaparece. ¡Genial!

Tenga en cuenta que la opción de recarga del navegador se puede utilizar para recargar la aplicación y hacer que el componente `Alert` vuelva a aparecer.

A continuación, ampliaremos el botón de cierre para generar un evento cuando la alerta se cierre.

#### Implementar un evento de cierre en el alert

Ahora crearemos un evento personalizado en el componente `Alert`. El evento se generará cuando se cierre la alerta para que los consumidores puedan ejecutar lógica cuando esto suceda.

Un evento personalizado en un componente se implementa mediante el uso de una prop. La prop es una función a la que se llama para desencadenar el evento.

Para implementar un evento de cierre de alerta, siga estos pasos:

1. Comience abriendo `Alert.jsx` y agregue una prop para el evento:

```jsx
export function Alert({ type = “information”, heading, children, closable, onClose }) {}
```

Hemos llamado a la prop `onClose`.

> [!NOTE]
> Es una práctica común comenzar el nombre de una prop de evento con `on`.

2. En el manejador de eventos `handleCloseClick`, desencadene el evento de cierre después de que el estado `visible` se establezca en `false`:

```javascript
function handleCloseClick() { setVisible(false); if (onClose) { onClose(); } }
```

Observe que solo invocamos `onClose` si está definido y el consumidor lo pasa como prop. Esto significa que no estamos obligando al consumidor a manejar este evento.

3. Ahora podemos manejar cuándo se cierra una alerta en el componente `App`. Abra `App.jsx` y agregue el siguiente manejador de eventos a `Alert` en el JSX:

```jsx
<Alert type=”information” heading=”Success” closable onClose={() => console.log(“closed”)} > Everything is really good! </Alert>;
```

Hemos utilizado un manejador de eventos en línea esta vez.

4. En la aplicación, si hace clic en el botón de cierre y mira la consola, verá que se ha emitido `closed`:

*Figura 1.16 – Salida de la consola después de que la alerta se cierra*

Eso completa el evento de cierre y la implementación de la alerta para este capítulo.

He aquí lo que hemos aprendido sobre los eventos de React:
- Los eventos, junto con el estado, permiten que un componente sea interactivo.
- Los manejadores de eventos son funciones que se registran en elementos en JSX.
- Se puede crear un evento personalizado implementando una prop de tipo función e invocándola para desencadenar el evento.

El componente que creamos en este capítulo es un **componente funcional (function component)**. También puede crear componentes usando clases. Por ejemplo, una versión de componente de clase del componente `Alert` está en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter01/class-component](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter01/class-component). Sin embargo, los componentes funcionales son dominantes en la comunidad de React. Estas son algunas de las razones por las cuales:
- Generalmente, requieren menos código para implementarse.
- La lógica dentro del componente se puede reutilizar más fácilmente.
- Los Hooks de React no se pueden usar en componentes de clase.

Por estas razones, nos centraremos únicamente en componentes funcionales en este libro.

A continuación, aprenderemos a utilizar las herramientas de desarrollo del navegador para React.

---

### Uso de las herramientas de desarrollo de React

**React developer tools** es una extensión de navegador disponible para Chrome, Firefox y Edge. Las herramientas permiten inspeccionar y depurar aplicaciones React. En esta sección, instalaremos y utilizaremos estas herramientas en el componente `Alert` que hemos implementado en este capítulo.

Los enlaces a las extensiones son los siguientes:
- **Chrome**: [https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi](https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- **Firefox**: [https://addons.mozilla.org/en-GB/firefox/addon/react-devtools/](https://addons.mozilla.org/en-GB/firefox/addon/react-devtools/)
- **Edge**: [https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)

Siga las instrucciones en el enlace correspondiente para instalar la extensión en su navegador. Es posible que deba volver a abrir el navegador para que las herramientas estén disponibles.

#### Uso de la herramienta Components

La primera herramienta que vamos a explorar es la herramienta **Components**. Le permite inspeccionar las props actuales y el estado de un componente. Lleve a cabo los siguientes pasos para probar esta herramienta en nuestro componente `Alert`:

1. Si la aplicación no se está ejecutando, iníciela ejecutando `npm run dev` en una terminal.
2. Abra las herramientas de desarrollo del navegador presionando F12 en Windows o Cmd + Option + I en una Mac. Las herramientas de desarrollo de React se pueden encontrar en dos paneles llamados **Components** y **Profiler**.
3. Seleccione el panel **Components**. Aparece el árbol de componentes de React. Al seleccionar el componente `Alert` en el árbol de componentes, se revela información actual sobre él:

*Figura 1.17 – Panel Components de React developer tools*

Un conjunto útil de información del componente son los valores actuales de las props. Puede cambiar estos valores y la salida del componente en el navegador se actualizará. Pruebe esto cambiando la prop `children`. Esta es una excelente manera de probar manualmente que una prop funciona como se espera.

También hay una sección para los valores actuales de los Hooks, que incluye los valores de estado. Sin embargo, observe que el estado no tiene nombre: tiene un nombre genérico, `State`. No obstante, los Hooks aparecen en esta sección en el orden en que aparecen en el código del componente, por lo que podemos deducir cuáles son estos elementos. Además, hay una opción **Parse hook names** (con el icono de una varita mágica) que puede mostrar los nombres de las variables de los Hooks. Haga clic en esta opción para revelar el nombre de la variable de estado entre corchetes:

*Figura 1.18 – Nombre de la variable de estado después de hacer clic en el icono de la varita*

Vamos a observar el cambio en el valor del estado `visible`. Asegúrese de que `closable` esté establecido en el componente (puede establecer esta prop en `true` en la sección de props si no lo está). Al igual que con las props, puede cambiar los valores de estado utilizando las herramientas de desarrollo. Haga clic en la casilla de verificación a la derecha de `State(visible)`. Esto alterna el valor del estado `visible` entre `true` y `false` y actualiza el componente en el navegador en consecuencia.

Esto completa nuestra exploración de la herramienta Components. Presione F5 para actualizar el navegador de modo que el componente `Alert` vuelva a aparecer antes de continuar. A continuación, exploraremos la herramienta Profiler.

#### Uso de la herramienta Profiler

Ahora, exploraremos el panel **Profiler**. Esta herramienta permite perfilar las interacciones, lo cual es útil para rastrear problemas de rendimiento. Lleve a cabo los siguientes pasos para generar un perfil del cierre de la alerta:

1. Antes de comenzar a perfilar, nos aseguraremos de que la herramienta Profiler registre por qué se renderizan los componentes. Seleccione el panel **Profiler** y haga clic en la opción **View settings** (el icono de engranaje). Después de que se abra la configuración de las herramientas de desarrollo de React, haga clic en la pestaña **Profiler** para ver la configuración del Profiler. Asegúrese de que la opción **Record why each component rendered while profiling.** esté marcada.

*Figura 1.19 – Configuración del Profiler*

2. Cierre la configuración y haga clic en la opción **Start profiling**, que es el icono del círculo azul.
3. Haga clic en el botón de cierre en la alerta dentro de la aplicación.
4. Haga clic en la opción **Stop profiling**, que es el icono del círculo rojo. Aparece una línea de tiempo de todos los re-renderizados de los componentes:

*Figura 1.20 – Perfil del cierre de la alerta*

Esto muestra que `Alert` se volvió a renderizar cuando se hizo clic en el botón de cierre, tomando 0.7 milisegundos.

Esta herramienta es útil para detectar rápidamente los componentes lentos de una interacción del usuario.

Esto completa nuestra exploración de las herramientas de desarrollo de React. He aquí un resumen:
- La herramienta de desarrollo **React Components** permite inspeccionar y probar las props y el estado de los componentes.
- La herramienta de desarrollo **React Profiler** permite perfilar las interacciones de usuario de bajo rendimiento para ayudar a identificar la raíz del problema.

Eso nos lleva al final del capítulo. A continuación se muestra un resumen del capítulo.

---

### Resumen

Ahora entendemos que React es una biblioteca popular para crear frontends basados en componentes. En este capítulo, creamos un componente `Alert` usando React.

La salida de los componentes se declara utilizando una mezcla de HTML y JavaScript llamada **JSX**. JSX debe transpilarse a JavaScript antes de que pueda ejecutarse en un navegador.

Las **props** se pueden pasar a un componente como atributos de JSX. Esto permite a los consumidores del componente controlar su salida y comportamiento. Un componente recibe props como un parámetro de objeto. Los nombres de los atributos de JSX forman los nombres de las propiedades del parámetro de objeto. Implementamos una variedad de props en este capítulo en el componente `Alert`.

Los **eventos** se pueden manejar para ejecutar lógica cuando el usuario interactúa con el componente. Creamos un manejador de eventos para el evento de clic del botón de cierre en el componente `Alert`.

El **estado** se puede usar para re-renderizar un componente y actualizar su salida. El estado se define mediante el Hook `useState` y a menudo se actualiza en los manejadores de eventos. Creamos un estado para determinar si la alerta es visible.

Los **eventos personalizados** se pueden implementar como una prop de tipo función. Esto permite a los consumidores del componente ejecutar lógica a medida que el usuario interactúa con él. Implementamos un evento de cierre en el componente `Alert`.

El componente `Alert` es un ejemplo de un componente reutilizable que se puede utilizar en muchos lugares a lo largo de una aplicación grande e incluso en diferentes aplicaciones.

En el próximo capítulo, nos presentaremos a TypeScript. Luego utilizaremos TypeScript para tipar fuertemente el componente `Alert` que comenzamos en este capítulo.

---

### Preguntas

Responda a las siguientes preguntas para reforzar lo que ha aprendido en este capítulo:

1. ¿Qué tiene de incorrecto la siguiente definición de componente?

```jsx
export function important() { return <div>This is really important!</div>; }
```

2. Las props de un componente se pasan a un componente de la siguiente manera:

```jsx
<ContactDetails name=”Fred” email=”fred@somewhere.com” />
```

El componente se define luego de la siguiente manera:

```jsx
export function ContactDetails({ firstName, email }) { return ( <div> <div>{firstName}</div> <div>{email}</div> </div> ); }
```

Sin embargo, el nombre *Fred* no se muestra. ¿Cuál es el problema?

3. ¿Cuál es el valor inicial del estado `loading` definido aquí?

```javascript
const [loading, setLoading] = useState(true);
```

4. ¿Qué tiene de incorrecto cómo se establece el estado en el siguiente componente?

```jsx
export function Agree() { const [agree, setAgree] = useState(); return ( <button onClick={() => agree = true}> Click to agree </button> ); }
```

5. El siguiente componente implementa un evento `Agree` opcional. ¿Qué tiene de incorrecto esta implementación?

```jsx
export function Agree({ onAgree }) { function handleClick() { onAgree(); } return ( <button onClick={handleClick}> Click to agree </button> ); }
```

---

### Respuestas

Aquí están las respuestas a las preguntas anteriores:

1. El problema con la definición del componente es que su nombre está en minúsculas. Las funciones de React deben nombrarse con un primer carácter en mayúscula:

```jsx
export function Important() { ... }
```

2. El problema es que se pasa una prop `name` en lugar de `firstName`. Aquí está el JSX corregido:

```jsx
<ContactDetails firstName=”Fred” email=”fred@somewhere.com” />
```

3. El valor inicial del estado `loading` es `true`.

4. El estado no se actualiza utilizando la función setter del estado. Aquí está la versión corregida de cómo se establece el estado:

```jsx
export function Agree() { const [agree, setAgree] = useState(); return ( <button onClick={() => setAgree(true)}> Click to agree </button> ); }
```

5. El problema es que hacer clic en el botón provocará un error si `onAgree` no se pasa porque será `undefined`. Aquí está la versión corregida del componente:

```jsx
export function Agree({ onAgree }) { function handleClick() { if (onAgree) { onAgree(); } } return ( <button onClick={handleClick}> Click to agree </button> ); }
```
