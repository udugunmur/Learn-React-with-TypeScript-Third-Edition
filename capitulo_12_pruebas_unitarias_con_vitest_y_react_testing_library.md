# Parte 4: React Avanzado

## Capítulo 12: Pruebas unitarias con Vitest y React Testing Library

En este capítulo, aprenderemos a utilizar Vitest y React Testing Library, dos herramientas de pruebas automatizadas muy populares que se pueden utilizar juntas en aplicaciones de React. Crearemos pruebas en el componente de lista de verificación que creamos en el [Capítulo 11](https://subscription.packtpub.com/book/web-development/9781836643173/11), *Componentes reutilizables*.

Comenzaremos centrándonos en **Vitest** y usándolo para probar funciones puras, aprendiendo sobre las funciones de coincidencia (*matcher functions*) comunes de Vitest para escribir expectativas (*expectations*) y cómo ejecutar pruebas para verificar si pasan.

Luego pasaremos a aprender sobre las **pruebas de componentes** utilizando la biblioteca **React Testing Library**. Entenderemos los diferentes tipos y variantes de consultas (*queries*) y cómo nos ayudan a crear pruebas sólidas.

Después de eso, aprenderemos sobre la forma más precisa de simular interacciones del usuario utilizando un paquete complementario de React Testing Library. Usamos esto para crear pruebas para elementos que se marcan en el componente de lista de verificación.

Al final del capítulo, aprenderemos cómo determinar qué código está cubierto por las pruebas y, lo que es más importante, qué código no está cubierto. Usaremos la herramienta de **cobertura de código (code coverage)** de Vitest para hacer esto y comprender todas las diferentes estadísticas de cobertura que nos brinda.

Por lo tanto, en este capítulo cubriremos los siguientes temas:

- Pruebas de funciones puras
- Pruebas de componentes
- Simulación de interacciones del usuario
- Obtención de cobertura de código

---

### Requisitos técnicos

En este capítulo utilizaremos las siguientes tecnologías:

- **Node.js y npm**: Puede instalarlos desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/)
- **Visual Studio Code**: Puede instalarlo desde [https://code.visualstudio.com/](https://code.visualstudio.com/)

Comenzaremos con una versión modificada del código que terminamos en el capítulo anterior. El código modificado contiene lógica extraída en funciones puras, que será ideal para usar en las primeras pruebas que escribamos. Este código se puede encontrar en línea en [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter12/start](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter12/start).

Lleve a cabo los siguientes pasos para descargarlo en su computadora local:

1. Vaya a [https://download-directory.github.io/](https://download-directory.github.io/) en un navegador.
2. En el cuadro de texto de la página web, ingrese la siguiente URL: [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter12/start](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter12/start).
3. Presione la tecla Enter. Ahora se descargará un archivo ZIP que contiene la carpeta de inicio.
4. Extraiga el archivo ZIP en una carpeta de su elección y abra esa carpeta en Visual Studio Code.
5. Vaya a esta carpeta en una terminal y ejecute el siguiente comando para instalar todas las dependencias:

```bash
npm i
```

Ahora está listo para comenzar a escribir pruebas para el componente de lista de verificación.

---

### Pruebas de funciones puras

En esta sección, comenzaremos por comprender las partes fundamentales de una prueba de Vitest. Luego, pondremos esto en práctica implementando pruebas en una función pura en el componente de lista de verificación.

Una **función pura** tiene un valor de salida constante para un conjunto determinado de valores de parámetros. Estas funciones dependen únicamente de los parámetros de la función y de nada fuera de la función, y tampoco cambian ningún valor de argumento que se les pase. Por lo tanto, las funciones puras son excelentes para aprender a escribir pruebas porque no tienen efectos secundarios difíciles de manejar.

En esta sección, también cubriremos cómo probar excepciones, lo cual resulta útil para probar funciones de aserción de tipos. Finalmente, al final de esta sección, aprenderemos a ejecutar las pruebas en una suite de pruebas.

#### Comprender una prueba

Vitest busca pruebas en archivos con extensiones particulares. Estas extensiones de archivo son `.test.ts` para pruebas en funciones puras y `.test.tsx` para pruebas en componentes. Alternativamente, se podría utilizar una extensión de archivo `.spec.*`.

Una prueba se define mediante la función `test` de Vitest:

```typescript
test(‘your test name’, () => { // your test implementation });
```

La función `test` tiene dos parámetros para el nombre y la implementación de la prueba. Es una práctica común que la implementación de la prueba sea una función anónima. La implementación de la prueba puede ser asíncrona colocando la palabra clave `async` delante de la función anónima:

```typescript
test(‘your test name’, async () => { // your test implementation });
```

La implementación de la prueba consistirá en llamar a la función con los argumentos que se están probando y verificar que el resultado sea el esperado:

```typescript
test(‘your test name’, async () => { const someResult = yourFunction(‘someArgument’); expect(someResult).toBe(‘something’); });
```

La función `expect` de Vitest se utiliza para definir nuestras expectativas. El resultado de la llamada a la función se pasa a `expect` y devuelve un objeto que contiene métodos que podemos utilizar para definir expectativas específicas para el resultado. Estos métodos se denominan **matchers**. Si la expectativa falla, Vitest fallará la prueba.

La prueba anterior utiliza el matcher `toBe`. El matcher `toBe` comprueba que los valores primitivos sean iguales, y la prueba anterior lo utiliza para comprobar que la variable `someResult` es igual a `'something'`. Otros matchers comunes son los siguientes:
- `toStrictEqual`: para comprobar los valores de un objeto o matriz. Esto comprueba recursivamente cada propiedad en el objeto o matriz. Aquí hay un ejemplo:

```typescript
expect(someResult).toStrictEqual({ field1: ‘something’, field2: ‘something else’ });
```

- `not`: para comprobar lo contrario de un matcher. Aquí hay un ejemplo:

```typescript
expect(someResult).not.toBe(‘something’);
```

- `toMatch`: para comparar cadenas con expresiones regulares (regex). Aquí hay un ejemplo:

```typescript
expect(someResult).toMatch(/error/);
```

- `toContain`: para comprobar si un elemento está en una matriz. Aquí hay un ejemplo:

```typescript
expect(someResult).toContain(99);
```

Se puede encontrar una lista completa de todos los matchers estándar en la documentación de Vitest en [https://vitest.dev/api/expect.html#expect](https://vitest.dev/api/expect.html#expect).

Ahora que entendemos los conceptos básicos de una prueba de Vitest, crearemos nuestra primera prueba de Vitest.

#### Probando isChecked

La primera función que probaremos es `isChecked`. Esta función tiene dos parámetros:
- `checkedIds`: este es un array de IDs que están actualmente marcados.
- `idValue`: este es el ID para determinar si está marcado.

Escribiremos una prueba para cuando el elemento de la lista esté marcado y otra para cuando no esté marcado:

1. Comience instalando Vitest ingresando el siguiente comando en una terminal:

```bash
npm i -D vitest
```

2. Configuraremos Vitest para que podamos usar sus funciones como `test` y `expect` globalmente, sin tener que importarlas. Abra el archivo de configuración de Vite, `vite.config.ts`, que se encuentra en la raíz del proyecto. Necesitamos crear una configuración de Vitest y fusionarla con la configuración de Vite. Entonces, importe una función `mergeConfig` de Vite y una función `defineConfig` de Vitest de la siguiente manera:

```typescript
import { defineConfig, mergeConfig } from “vite”; import { defineConfig as defineVitestConfig } from “vitest/config”;
```

Hemos creado un alias para la función `defineConfig` de Vitest para que no colisione con la de Vite.

3. Ajuste la configuración de Vite para que se asigne a una variable y cree la configuración de Vitest:

```typescript
const viteConfig = defineConfig({ plugins: [react()], }); const vitestConfig = defineVitestConfig({ test: { globals: true, }, });
```

Hemos configurado las APIs de Vitest para que estén disponibles globalmente configurando `test.globals` en `true`.

4. El último paso en el archivo `vite.config.ts` es fusionar las configuraciones y exportarlas:

```typescript
export default mergeConfig(viteConfig, vitestConfig);
```

5. Para que TypeScript reconozca las APIs globales, debemos actualizar el archivo de configuración de TypeScript. Abra `tsconfig.app.json` desde la raíz del proyecto y agregue los tipos globales de Vitest de la siguiente manera:

```json
{ “compilerOptions”: { ..., «types»: [«vitest/globals»] }, ... }
```

6. Pasemos a escribir una prueba ahora. Cree un archivo llamado `isChecked.test.ts` en la carpeta `src/Checklist` que contendrá las pruebas.

> [!NOTE]
> La mejor práctica es colocar los archivos de prueba adyacentes al archivo fuente que se está probando. Esto permite a los desarrolladores navegar a la prueba de una función rápidamente.

7. Abra `isChecked.test.ts` e importe la función `isChecked`:

```typescript
import { isChecked } from ‘./isChecked’;
```

8. Comience a crear la primera prueba de la siguiente manera:

```typescript
test(‘’, () => { });
```

9. Agregue el nombre de la prueba de la siguiente manera:

```typescript
test(‘should return true when in checkedIds’, () => { ); 
```

Formar una convención de nomenclatura para los nombres de las pruebas es una buena práctica para que sean consistentes y fáciles de entender. Aquí hemos utilizado la siguiente estructura de nomenclatura:
`should {salida esperada / comportamiento} when {condición de entrada / estado}`

10. Ahora, comencemos a implementar la lógica dentro de la prueba. El primer paso en la prueba es llamar a la función que se está probando con los argumentos que queremos probar:

```typescript
test(‘should return true when in checkedIds’, () => { const result = isChecked([1, 2, 3], 2); });
```

11. El segundo (y último) paso de la prueba es comprobar que el resultado sea el que esperamos, que es `true` para esta prueba:

```typescript
test(‘should return true when in checkedIds’, () => { const result = isChecked([1, 2, 3], 2); expect(result).toBe(true); });
```

Dado que el resultado es un valor primitivo (un booleano), utilizamos el matcher `toBe` para verificar el resultado.

12. Agregue una segunda prueba para cubrir el caso cuando el ID no está en los IDs marcados:

```typescript
test(‘should return false when not in checkedIds’, () => { const result = isChecked([1, 2, 3], 4); expect(result).toBe(false); });
```

Eso completa las pruebas en la función `isChecked`. A continuación, aprenderemos a probar las excepciones que se generan. Comprobaremos que nuestras pruebas funcionan después de eso.

#### Probando excepciones

Vamos a probar la función de aserción de tipos `assertValueCanBeRendered`. Esto es un poco diferente de la última función que probamos porque queremos probar si se genera una excepción, en lugar del valor devuelto.

Vitest tiene un matcher `toThrow` que se puede usar para verificar si se ha generado una excepción. Para que esto capture excepciones, la función que se está probando debe ejecutarse dentro de la expectativa, de la siguiente manera:

```typescript
test(‘some test’, () => { expect(() => { someAssertionFunction(someValue); }).toThrow(‘some error message’); });
```

Usaremos este enfoque para agregar tres pruebas en la función de aserción de tipos `assertValueCanBeRendered`. Lleve a cabo los siguientes pasos:

1. Cree un archivo llamado `assertValueCanBeRendered.test.ts` en la carpeta `src/Checklist` para las pruebas. Importe la función de aserción de tipo `assertValueCanBeRendered`:

```typescript
import { assertValueCanBeRendered } from ‘./assertValueCanBeRendered’;
```

2. La primera prueba que agregaremos es verificar si se genera una excepción cuando el valor no es una cadena o un número:

```typescript
test(‘should raise exception when not a string or number’, () => { expect(() => { assertValueCanBeRendered( true ); }).toThrow( ‘value is not a string or a number’ ); });
```

Pasamos el valor booleano `true`, lo que debería provocar un error.

3. A continuación, probaremos si no se genera una excepción cuando el valor es una cadena:

```typescript
test(‘should not raise exception when string’, () => { expect(() => { assertValueCanBeRendered( ‘something’ ); }).not.toThrow(); });
```

Usamos el matcher `not` con `toThrow` para verificar que no se genere una excepción.

4. La prueba final verificará que no se genere ninguna excepción cuando el valor sea un número:

```typescript
test(‘should not raise exception when number’, () => { expect(() => { assertValueCanBeRendered( 99 ); }).not.toThrow(); });
```

Eso completa las pruebas para la función de aserción de tipos `assertValueCanBeRendered`.

Ahora que hemos implementado algunas pruebas, aprenderemos cómo ejecutarlas a continuación.

#### Ejecución de pruebas

Para ejecutar las pruebas, simplemente ejecutamos Vitest. Después de ejecutar las pruebas, un observador (*watcher*) volverá a ejecutar las pruebas cuando cambie el código fuente o el código de prueba.

Lleve a cabo los siguientes pasos para ejecutar todas las pruebas:

1. Primero, agregue el siguiente script de prueba al archivo `package.json` en la sección de `scripts`. Este comando npm ejecutará Vitest, que ejecutará las pruebas:

```json
{ ..., “scripts”: { ... «test»: «vitest» }, ... }
```

2. Abra la terminal y ejecute el siguiente comando:

```bash
npm run test
```

`test` es un script npm muy común, por lo que se puede omitir la palabra clave `run`. Además, `test` se puede abreviar como `t`. Entonces, una versión abreviada del comando anterior es la siguiente:

```bash
npm t
```

Se ejecutarán las pruebas y aparecerá el siguiente resumen en la terminal:

*Figura 12.1 – Primera ejecución de prueba*

Observe que no hay un símbolo del sistema en la terminal como suele haber después de que un comando ha terminado de ejecutarse. Esto se debe a que el comando no se ha completado por completo ya que el observador de pruebas se está ejecutando; esto se llama **modo de observación (watch mode)**. El comando no se completará hasta que se salga del modo de observación usando la tecla `Q`. Deje la terminal en modo de observación y continúe con el siguiente paso.

Todas las pruebas pasan por el momento. Ahora, haremos que una prueba falle deliberadamente para que podamos ver la información que nos proporciona Vitest. Entonces, abra `assertValueCanBeRendered.ts` y cambie el mensaje de error esperado en la primera prueba de la siguiente manera:

```typescript
test(‘should raise exception when not a string or number’, () => { expect(() => { assertValueCanBeRendered(true); }).toThrow(‘value is not a string or a numberX’); });
```

Tan pronto como se guarda el archivo de prueba, las pruebas se vuelven a ejecutar y se informa de una prueba fallida de la siguiente manera:

*Figura 12.2 – Prueba fallida*

Vitest proporciona información valiosa sobre la falla que nos ayuda a resolver rápidamente los fallos de las pruebas. Nos dice esto:
- Qué prueba falló
- Cuál era el resultado esperado, en comparación con el resultado real
- La línea en nuestro código donde ocurrió el fallo

Resuelva el fallo de la prueba revirtiendo la prueba para verificar el mensaje de error correcto. La prueba debería ser la siguiente ahora:

```typescript
test(‘should raise exception when not a string or number’, () => { expect(() => { assertValueCanBeRendered(true); }).toThrow(‘value is not a string or a number’); });
```

Eso completa nuestra exploración de la ejecución de pruebas de Vitest y esta sección sobre pruebas de funciones puras. He aquí un resumen rápido de los puntos clave:
- Las pruebas se definen mediante la función `test` de Vitest.
- Las expectativas dentro de la prueba se definen utilizando la función `expect` de Vitest en combinación con uno o más matchers.
- El argumento de la función `expect` puede ser una función que ejecuta la función que se está probando. Esto es útil para probar excepciones con el matcher `toThrow`.

A continuación, aprenderemos cómo probar componentes de React.

---

### Pruebas de componentes

Probar los componentes es importante porque esto es con lo que interactúa el usuario. Tener pruebas automatizadas en los componentes nos da la confianza de que la aplicación funciona correctamente y ayuda a prevenir regresiones cuando cambiamos el código.

En esta sección, aprenderemos cómo probar componentes con Vitest y React Testing Library. Luego, crearemos algunas pruebas en el componente de lista de verificación que desarrollamos en el último capítulo.

#### Comprender React Testing Library

**React Testing Library** es una biblioteca popular para probar componentes de React. Proporciona funciones para renderizar componentes y luego seleccionar elementos internos. Luego, esos elementos internos se pueden verificar mediante matchers especiales proporcionados por otra biblioteca complementaria, llamada **jest-dom**.

#### Una prueba de componente básica

Aquí hay un ejemplo de una prueba de componente:

```tsx
test(‘should render heading when content specified’, () => { render(<Heading>Some heading</Heading>); const heading = screen.getByText(‘Some heading’); expect(heading).toBeInTheDocument(); });
```

Expliquemos la prueba:
- La función `render` de React Testing Library procesa el componente que queremos probar. Pasamos todos los atributos y contenidos apropiados para que el componente esté en el estado requerido para las comprobaciones. En esta prueba, hemos especificado algún texto en el contenido.
- La siguiente línea selecciona un elemento interno del componente. Hay muchos métodos en el objeto `screen` de React Testing Library que permiten la selección de elementos. Estos métodos se conocen como **consultas (queries)**. `getByText` selecciona un elemento haciendo coincidir el contenido de texto especificado. En esta prueba, se seleccionará un elemento con el contenido de texto `Some heading` y se asignará a la variable `heading`.
- La última línea de la prueba es la expectativa. El matcher `toBeInTheDocument` es un matcher especial de `jest-dom` que comprueba si el elemento en la expectativa está en el DOM.

#### Comprender las consultas (queries)

Una consulta de React Testing Library es un método que selecciona un elemento DOM dentro del componente que se está renderizando. Existen muchas consultas diferentes que encuentran el elemento de diferentes maneras:

- `ByRole`: Consulta elementos por su rol.

> [!NOTE]
> Los elementos DOM tienen un atributo `role` que permite a las tecnologías de asistencia, como los lectores de pantalla, comprender qué son. Muchos elementos DOM tienen este atributo preestablecido; por ejemplo, el elemento `button` tiene automáticamente la función de `'button'`. Para obtener más información sobre roles, consulte [https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles).

- `ByLabelText`: Consulta elementos por su etiqueta asociada. Consulte esta página en la documentación de React Testing Library para conocer las diferentes formas en que los elementos se pueden asociar con una etiqueta: [https://testing-library.com/docs/queries/bylabeltext](https://testing-library.com/docs/queries/bylabeltext).
- `ByPlaceholderText`: Consulta elementos por su texto de marcador de posición (*placeholder*).
- `ByText`: Consulta elementos por su contenido de texto.
- `ByDisplayValue`: Consulta elementos `input`, `textarea` y `select` por su valor.
- `ByAltText`: Consulta elementos `img` por su atributo `alt`.
- `ByTitle`: Consulta elementos por su atributo `title`.
- `ByTestId`: Consulta elementos por su ID de prueba (el atributo `data-testid`).

También existen diferentes tipos de consultas que se comportan de manera ligeramente diferente en el elemento encontrado. Cada tipo de consulta tiene un prefijo particular en el nombre del método de consulta:
- `getBy`: Produce un error si no se encuentra un solo elemento. Esto es ideal para obtener sincrónicamente un solo elemento.
- `getAllBy`: Produce un error si no se encuentra al menos un elemento. Esto es ideal para obtener sincrónicamente múltiples elementos.
- `findBy`: Produce un error si no se encuentra un solo elemento. La búsqueda de un elemento se repite durante un cierto período de tiempo (un segundo de forma predeterminada). Por lo tanto, esto es ideal para obtener de forma asíncrona un solo elemento que podría no estar inmediatamente en el DOM.
- `findAllBy`: Produce un error si no se encuentra al menos un elemento dentro de un cierto período de tiempo (un segundo de forma predeterminada). Esto es ideal para obtener de forma asíncrona múltiples elementos que podrían no estar inmediatamente en el DOM.
- `queryBy`: Esto devuelve `null` si no se encuentra un elemento. Esto es ideal para verificar que un elemento no existe.
- `queryAllBy`: Es lo mismo que `queryBy`, pero devuelve un array de elementos. Esto es ideal para comprobar que no existen varios elementos.

Por lo tanto, la consulta `getByText` que usamos en la prueba anterior encuentra el elemento por el contenido de texto especificado y genera un error si no se encuentran elementos.

Para obtener más información sobre las consultas, consulte la siguiente página en la documentación de React Testing Library: [https://testing-library.com/docs/queries/about/](https://testing-library.com/docs/queries/about/).

Observe que ninguna de estas consultas hace referencia a detalles de implementación, como un nombre de elemento, ID o clase CSS. Si esos detalles de implementación cambian debido a la refactorización del código, las pruebas no deberían romperse, que es precisamente lo que queremos.

Ahora que entendemos qué es la biblioteca React Testing Library, la usaremos para escribir nuestra primera prueba de componentes.

#### Implementación de pruebas del componente checklist

La primera prueba de componente que escribiremos es verificar que los elementos de la lista se procesen correctamente. La segunda prueba de componentes verificará que los elementos de la lista se procesen correctamente cuando se rendericen de forma personalizada. Lleve a cabo los siguientes pasos:

1. Comience instalando React Testing Library y `jest-dom` ejecutando el siguiente comando en una terminal:

```bash
npm i -D @testing-library/react @testing-library/dom @testing-library/jest-dom jsdom
```

2. Ahora necesitamos configurar Vitest para usar un entorno DOM para las pruebas en lugar del entorno de Node predeterminado. Abra el archivo de configuración de Vite, `vite.config.ts`, y configure Vitest para usar la biblioteca `jsdom` que simula el DOM:

```typescript
const vitestConfig = defineVitestConfig({ test: { ..., environment: “jsdom”, }, });
```

3. En la configuración, también importaremos automáticamente la biblioteca `jest-dom` para que no necesitemos hacer esto en cada archivo de prueba. Agregue un archivo de configuración para que se ejecute antes de las pruebas en el archivo de configuración de Vite de la siguiente manera:

```typescript
const vitestConfig = defineVitestConfig({ test: { ..., setupFiles: ‘./vitest.setup.ts’, }, });
```

4. Cree el archivo `vitest.setup.ts` en la raíz del proyecto con la declaración de importación de `jest-dom`:

```typescript
import ‘@testing-library/jest-dom/vitest’;
```

5. Abra `tsconfig.app.json` y haga que TypeScript reconozca los tipos `jest-dom` de la siguiente manera:

```json
{ “compilerOptions”: { ... “types”: [ “vitest/globals”, “@testing-library/jest-dom” ] }, ... }
```

6. Cree un nuevo archivo en la carpeta `src/Checklist` llamado `Checklist.test.tsx` y agregue las siguientes declaraciones de importación:

```typescript
import { expect, test } from “vitest”; import { render, screen } from ‘@testing-library/react’; import { Checklist } from ‘./Checklist’;
```

7. Comience a crear la prueba de la siguiente manera:

```typescript
test(‘should render correct list items when data specified’, () => { });
```

8. En la prueba, renderice `Checklist` con algunos datos:

```tsx
test(‘should render correct list items when data specified’, () => { render( <Checklist data={[{ id: 1, name: ‘Lucy’, role: ‘Manager’ }]} id=”id” primary=”name” secondary=”role” /> ); });
```

Hemos renderizado un único elemento de lista que debe tener el texto principal Lucy y el texto secundario Manager.

9. Comprobemos que Lucy ha sido renderizada:

```tsx
test(‘should render correct list items when data specified’, () => { render( ... ); expect( screen.getByText(‘Lucy’) ).toBeInTheDocument(); });
```

Hemos seleccionado el elemento mediante la consulta `getByText` y lo hemos introducido directamente en la expectativa. Usamos el matcher `toBeInTheDocument` para verificar que el elemento encontrado esté en el DOM.

10. Complete la prueba agregando una expectativa similar para verificar a Manager:

```tsx
test(‘should render correct list items when data specified’, () => { render( ... ); expect( ... ).toBeInTheDocument(); expect( screen.getByText(‘Manager’) ).toBeInTheDocument(); });
```

Eso completa nuestra primera prueba de componentes.

11. Agregaremos la segunda prueba de una sola vez, de la siguiente manera:

```tsx
test(‘should render correct list items when renderItem specified’, () => { render( <Checklist data={[{ id: 1, name: ‘Lucy’, role: ‘Manager’ }]} id=”id” primary=”name” secondary=”role” renderItem={(item) => ( <li key={item.id}> {item.name}-{item.role} </li> )} /> ); expect( screen.getByText(‘Lucy-Manager’) ).toBeInTheDocument(); });
```

Representamos un único elemento de la lista con los mismos datos que la prueba anterior. Sin embargo, esta prueba renderiza de forma personalizada los elementos de la lista con un guión entre el nombre y la función. Usamos la misma consulta `getByText` para verificar que el elemento de la lista con el texto correcto se encuentre en el DOM.

12. Si las pruebas no se ejecutan automáticamente, ejecútelas ejecutando `npm test` en la terminal. Ambas nuevas pruebas deberían pasar:

*Figura 12.3 – Pruebas de componentes que pasan*

Eso completa nuestras dos primeras pruebas de componentes. ¡Vea lo fácil que lo hace React Testing Library!

#### Uso de test IDs

La siguiente prueba que implementaremos es verificar que un elemento de la lista esté marcado cuando se especifique. Esta prueba será un poco más complicada y requiere un test ID en las casillas de verificación. Lleve a cabo los siguientes pasos:

1. Comience abriendo `Checklist.tsx` y observe el siguiente ID de prueba en el elemento `input`:

```tsx
<input ... data-testid={`Checklist__input__${ idValue.toString()}`} />
```

Los test IDs se agregan a los elementos mediante un atributo `data-testid`. El ID del elemento de la lista se incluye en el ID para que sea único para cada elemento de la lista.

2. Ahora, regrese al archivo `Checklist.test.tsx` y comience a escribir la siguiente nueva prueba:

```tsx
test(‘should render correct checked items when specified’, () => { render( <Checklist data={[{ id: 1, name: ‘Lucy’, role: ‘Manager’ }]} id=”id” primary=”name” secondary=”role” checkedIds={[1]} /> ); });
```

Hemos renderizado la lista de verificación con los mismos datos que las pruebas anteriores. Sin embargo, hemos especificado que el elemento de la lista esté marcado mediante la prop `checkedIds`.

3. Ahora, pasemos a la expectativa para la prueba:

```tsx
test(‘should render correct checked items when specified’, () => { render( <Checklist data={[{ id: 1, name: ‘Lucy’, role: ‘Manager’ }]} id=”id” primary=”name” secondary=”role” checkedIds={[1]} /> ); expect( screen.getByTestId(‘Checklist__input__1’) ).toBeChecked(); });
```

Seleccionamos la casilla de verificación por su test ID mediante la consulta `getByTestId`. Luego usamos el matcher `toBeChecked` para verificar que la casilla de verificación esté marcada. `toBeChecked` es otro matcher especial del paquete `vitest-dom` / `jest-dom`.

Esta nueva prueba debería pasar, dejándonos con tres pruebas superadas en `Checklist`:

*Figura 12.4 – Las tres pruebas de componentes aprobadas*

Detenga el ejecutor de pruebas presionando la tecla `Q`.

Eso completa esta sección sobre pruebas de componentes. He aquí un resumen rápido:
- React Testing Library contiene muchas consultas útiles para seleccionar elementos DOM. Diferentes tipos de consultas encontrarán elementos únicos o múltiples y generarán o no errores si no se encuentra un elemento. Incluso hay un tipo de consulta para buscar repetidamente elementos representados de forma asíncrona.
- `jest-dom` contiene muchos matchers útiles para comprobar elementos DOM. Un comparador común es `toBeInTheDocument`, que verifica que un elemento esté en el DOM. Sin embargo, `jest-dom` contiene muchos otros comparadores útiles, como `toBeChecked` para comprobar si un elemento está marcado o no.

A continuación, aprenderemos cómo simular interacciones de los usuarios en las pruebas.

---

### Simulación de interacciones del usuario

Hasta ahora, nuestras pruebas simplemente han renderizado el componente de lista de verificación con varias props configuradas. Los usuarios pueden interactuar con el componente de lista de verificación marcando y desmarcando elementos. En esta sección, primero aprenderemos cómo simular interacciones de los usuarios en las pruebas. Luego usaremos este conocimiento para probar si los elementos de la lista se marcan cuando se hace clic en ellos y que se genera `onCheckedIdsChange`.

#### Comprender fireEvent y user-event

React Testing Library tiene una función `fireEvent` que puede generar eventos en elementos DOM. El siguiente ejemplo genera un evento de clic en un botón Save:

```tsx
render(<button>Save</button>); fireEvent.click(screen.getByText(‘Save’));
```

Esto está bien, pero ¿qué pasa si la lógica se implementó mediante un evento `mousedown` en lugar de `click`? La prueba tendría que ser la siguiente:

```tsx
render(<button>Save</button>); fireEvent.mouseDown(screen.getByText(‘Save’));
```

Afortunadamente, existe un enfoque alternativo para realizar interacciones del usuario en las pruebas. El enfoque alternativo es utilizar el paquete **user-event**, que es un paquete complementario de React Testing Library que simula interacciones del usuario en lugar de eventos DOM específicos de bajo nivel. La misma prueba usando `user-event` se ve así:

```tsx
const user = userEvent.setup(); render(<button>Save</button>); await user.click(screen.getByText(‘Save’));
```

El paquete `user-event` enviará los eventos DOM de bajo nivel apropiados, en el orden apropiado. Por lo tanto, la prueba cubriría la lógica implementada mediante un evento de clic o un evento de pulsación del ratón (*mousedown*). Por lo tanto, está menos acoplado a los detalles de implementación, lo cual es bueno. Por esta razón, usaremos el paquete `user-event` para escribir pruebas interactivas en nuestro componente de lista de verificación.

El paquete `user-event` puede simular otras interacciones además de los clics. Consulte la documentación en el siguiente enlace para obtener más información: [https://testing-library.com/docs/user-event/intro](https://testing-library.com/docs/user-event/intro).

#### Implementación de pruebas de lista de verificación para marcar elementos

Ahora escribiremos dos pruebas interactivas en el componente de lista de verificación. La primera prueba comprobará que los elementos estén marcados cuando se hace clic en ellos. La segunda prueba comprobará que se llame a `onCheckedIdsChange` cuando se haga clic en los elementos. Lleve a cabo los siguientes pasos:

1. Instale el paquete `user-event` ejecutando el siguiente comando en una terminal:

```bash
npm i -D @testing-library/user-event
```

2. Agregaremos las pruebas interactivas en el mismo archivo de prueba que las otras pruebas de componentes. Por lo tanto, abra `Checklist.test.tsx` y agregue una declaración de importación para `user-event`:

```typescript
import userEvent from ‘@testing-library/user-event’;
```

3. La primera prueba comprobará que los elementos estén marcados cuando se hace clic en ellos. Comience a implementar esto de la siguiente manera en la parte inferior del archivo:

```typescript
test(‘should check items when clicked’, async () => { });
```

Hemos marcado la prueba como asíncrona porque las interacciones de usuario simuladas en `user-event` son asíncronas.

4. A continuación, inicialice la simulación del usuario de la siguiente manera:

```typescript
test(‘should check items when clicked’, async () => { const user = userEvent.setup(); });
```

5. Ahora podemos renderizar un elemento de la lista como lo hemos hecho en pruebas anteriores. También obtendremos una referencia a la casilla de verificación en el elemento de la lista representado y comprobaremos que no esté marcada:

```tsx
test(‘should check items when clicked’, async () => { const user = userEvent.setup(); render( <Checklist data={[{ id: 1, name: ‘Lucy’, role: ‘Manager’ }]} id=”id” primary=”name” secondary=”role” /> ); const lucyCheckbox = screen.getByTestId( ‘Checklist__input__1’ ); expect(lucyCheckbox).not.toBeChecked(); });
```

6. Ahora, pasemos a la interacción del usuario. Simule que el usuario hace clic en el elemento de la lista llamando al método `click` en el objeto `user`; la casilla de verificación en la que se debe hacer clic debe pasarse al argumento de `click`:

```tsx
test(‘should check items when clicked’, async () => { const user = userEvent.setup(); render( ... ); const lucyCheckbox = screen.getByTestId( ‘Checklist__input__1’ ); expect(lucyCheckbox).not.toBeChecked(); await user.click(lucyCheckbox); });
```

7. El último paso de la prueba es comprobar que la casilla de verificación ahora esté marcada:

```tsx
test(‘should check items when clicked’, async () => { const user = userEvent.setup(); render( ... ); const lucyCheckbox = screen.getByTestId( ‘Checklist__input__1’ ); expect(lucyCheckbox).not.toBeChecked(); await user.click(lucyCheckbox); expect(lucyCheckbox).toBeChecked(); });
```

8. La siguiente prueba comprobará que la función asignada a la prop `onCheckedIdsChange` se llame cuando se hace clic en un elemento de la lista. Aquí está la prueba:

```tsx
test(‘should call onCheckedIdsChange when clicked’, async () => { const user = userEvent.setup(); let calledWith: IdValue[] | undefined = undefined; render( <Checklist data={[{ id: 1, name: ‘Lucy’, role: ‘Manager’ }]} id=”id” primary=”name” secondary=”role” onCheckedIdsChange={(checkedIds) => (calledWith = checkedIds) } /> ); await user.click(screen.getByTestId( ‘Checklist__input__1’)); expect(calledWith).toStrictEqual([1]); });
```

Establecemos una variable `calledWith` en el valor del parámetro `onCheckedIdsChange`. Después de hacer clic en el elemento de la lista, verificamos el valor de la variable `calledWith` usando el matcher `toStrictEqual`. El matcher `toStrictEqual` es un matcher estándar que es ideal para verificar matrices y objetos.

9. La segunda prueba hace referencia al tipo `IdValue`, así que agregue una declaración de importación para esto:

```typescript
import { IdValue } from ‘./types’;
```

10. Ejecute las pruebas ejecutando `npm test` en la terminal. Ahora deberíamos tener cinco pruebas de componentes aprobadas:

*Figura 12.5 – Cinco pruebas de componentes aprobadas*

Detenga el ejecutor de pruebas presionando la tecla `Q`.

Eso completa las pruebas para hacer clic en elementos y esta sección sobre cómo simular interacciones de los usuarios. Aprendimos que la función `fireEvent` de React Testing Library genera un evento particular que acopla las pruebas a los detalles de implementación. Un mejor enfoque es utilizar el paquete `user-event` para simular interacciones del usuario, lo que potencialmente puede generar varios eventos en el proceso.

A continuación, aprenderemos cómo determinar rápidamente cualquier código que no esté cubierto por las pruebas.

---

### Obtención de cobertura de código

La **cobertura de código (code coverage)** es la forma en que nos referimos a cuánto del código de nuestra aplicación está cubierto por pruebas unitarias. A medida que escribimos nuestras pruebas unitarias, tendremos una idea bastante clara de qué código está cubierto y cuál no, pero a medida que la aplicación crezca y pase el tiempo, perderemos el rastro de esto.

En esta sección, aprenderemos cómo usar la opción de cobertura de código de Vitest para no tener que mantener lo que está cubierto en nuestras cabezas. Usaremos la opción de cobertura de código para determinar la cobertura de código en el componente de lista de verificación y comprender todas las diferentes estadísticas del informe. Usaremos el informe de cobertura de código para encontrar algún código no cubierto en nuestro componente de lista de verificación. Luego extenderemos las pruebas en el componente de lista de verificación para lograr una cobertura de código completa.

#### Instalación de la herramienta de cobertura de código

La herramienta de cobertura de código predeterminada para Vitest es `coverage-v8`. Para instalar esto en nuestro proyecto, ejecute el siguiente comando en una terminal:

```bash
npm i -D @vitest/coverage-v8
```

#### Ejecución de la cobertura de código

Para obtener la cobertura del código, ejecutamos el comando de prueba con una opción `--coverage`. También incluimos una opción `--watch=false` que le indica a Vitest que no se ejecute en modo de observación. Entonces, ejecute el siguiente comando en una terminal para determinar la cobertura de código en nuestra aplicación:

```bash
npm test -- --coverage --watch=false
```

Las pruebas tardan un poco más en ejecutarse debido a los cálculos de cobertura del código. Cuando finalizan las pruebas, se muestra un informe de cobertura de código en la terminal con los resultados de las pruebas:

*Figura 12.6 – Informe de cobertura de código de terminal*

A continuación, nos tomaremos un tiempo para comprender este informe de cobertura de código.

#### Comprensión del informe de cobertura de código

El informe de cobertura enumera la cobertura de cada archivo y agrega la cobertura en una carpeta para todos los archivos del proyecto. Por lo tanto, toda la aplicación tiene una cobertura de código de entre el 62,79% y el 80,95%, según la estadística que tomemos.

Aquí hay una explicación de todas las columnas de estadísticas:
- **% Stmts**: Esta es la cobertura de sentencias (*statement coverage*), que es cuántas sentencias de código fuente se han ejecutado durante la ejecución de la prueba.
- **% Branch**: Esta es la cobertura de ramas (*branch coverage*), que es cuántas ramas de la lógica condicional se han ejecutado durante la ejecución de la prueba.
- **% Funcs**: Esta es la cobertura de funciones (*function coverage*), que es cuántas funciones se han llamado durante la ejecución de la prueba.
- **% Lines**: Esta es la cobertura de líneas (*line coverage*), que es cuántas líneas de código fuente se han ejecutado durante la ejecución de la prueba.

La columna del extremo derecho del informe es muy útil. Proporciona las líneas de código fuente que no están cubiertas por las pruebas. Por ejemplo, el archivo `getNewCheckedIds.ts` en el componente de lista de verificación tiene la línea 6, que no está cubierta.

Hay otra versión del informe que se genera en formato HTML. Este archivo se genera automáticamente cada vez que se ejecuta una prueba con la opción `--coverage`. Entonces, este informe ya se ha generado porque acabamos de ejecutar las pruebas con la opción `--coverage`. Lleve a cabo los siguientes pasos para explorar el informe HTML:

1. El informe se puede encontrar en un archivo `index.html` en una carpeta `coverage`. Haga doble clic en el archivo para que se abra en un navegador:

*Figura 12.7 – Informe de cobertura HTML*

El informe contiene los mismos datos que el informe de la terminal, pero este es interactivo.

2. Haga clic en el enlace `src/Checklist` en la segunda fila del informe. La página ahora muestra la cobertura de los archivos en el componente de lista de verificación:

*Figura 12.8 – Informe de cobertura para archivos de componentes de lista de verificación*

3. Haga clic en el enlace `getNewCheckedIds.ts` para profundizar en la cobertura de ese archivo:

*Figura 12.9 – Informe de cobertura para getNewCheckedIds.ts*

Podemos ver que la línea 6 descubierta está claramente resaltada en el archivo `getNewCheckedIds.ts`.

Por lo tanto, el informe de cobertura HTML es útil en una base de código grande porque comienza con una cobertura de alto nivel y le permite profundizar en la cobertura en carpetas y archivos específicos. Al ver un archivo en el informe, podemos determinar rápidamente dónde está el código no cubierto porque está claramente resaltado.

A continuación, actualizaremos nuestras pruebas para que la línea 6 en `getNewCheckedIds.ts` quede cubierta.

#### Obtención de cobertura completa en el componente checklist

La lógica que actualmente no están verificando las pruebas es la lógica utilizada cuando se hace clic en un elemento de la lista pero ya ha sido marcado. Ampliaremos la prueba `should check items when clicked` para cubrir esta lógica. Lleve a cabo los siguientes pasos:

1. Abra `Checklist.test.tsx` y cambie el nombre de la prueba `should check items when clicked` de la siguiente manera:

```typescript
test(‘should check and uncheck items when clicked’, async () => { ... });
```

2. Agregue las siguientes líneas al final de la prueba para hacer clic en la casilla de verificación por segunda vez y verificar que esté desmarcada:

```tsx
test(‘should check and uncheck items when clicked’, async () => { const user = userEvent.setup(); render( ... ); const lucyCheckbox = screen.getByTestId( ‘Checklist__input__1’ ); expect(lucyCheckbox).not.toBeChecked(); await user.click(lucyCheckbox); expect(lucyCheckbox).toBeChecked(); await user.click(lucyCheckbox); expect(lucyCheckbox).not.toBeChecked(); });
```

3. En la terminal, vuelva a ejecutar las pruebas con cobertura:

```bash
npm run test -- --coverage --watch=false
```

Todas las pruebas aún pasan y la cobertura en el componente de lista de verificación ahora se informa como 100% en todas las estadísticas:

*Figura 12.10 – Cobertura del 100% en el componente de lista de verificación*

El componente de lista de verificación ahora está bien cubierto. Sin embargo, resulta un poco molesto que `index.ts` y `types.ts` aparezcan en el informe con una cobertura de cero. Resolveremos esto a continuación.

#### Ignorar archivos en el informe de cobertura

Eliminaremos `index.ts` y `types.ts` del informe de cobertura porque no contienen ninguna lógica y crean ruido innecesario. Lleve a cabo los siguientes pasos:

1. Abra el archivo `vite.config.ts`. Podemos configurar la cobertura en un campo `test.coverage`, y hay una opción `exclude` para eliminar archivos del informe de cobertura. Agregue las siguientes líneas para ignorar los archivos que no queremos incluir en el informe de cobertura:

```typescript
const vitestConfig = defineVitestConfig({ test: { ..., coverage: { exclude: [ “**/types.ts”, “**/index.ts”, “vite.config.ts”, “eslint.config.js”, ], }, }, });
```

2. En la terminal, vuelva a ejecutar las pruebas con cobertura:

```bash
npm run test -- --coverage --watch=false
```

Los archivos `types.ts` e `index.ts` se eliminan del informe de cobertura:

*Figura 12.11 – Archivos types.ts e index.ts eliminados del informe de cobertura*

Eso completa esta sección sobre cobertura de código. He aquí un resumen rápido:
- La opción `--coverage` genera un informe de cobertura de código después de que se hayan ejecutado las pruebas.
- Se genera un informe interactivo de cobertura de código HTML además del de la terminal. Esto es útil en un conjunto de pruebas grande para profundizar en el código no cubierto.
- Ambos formatos de informe resaltan el código no cubierto, lo que nos brinda información valiosa para mejorar nuestro conjunto de pruebas.

Es hora de resumir el capítulo.

---

### Resumen

En este capítulo, creamos pruebas en un componente de lista de verificación utilizando Vitest y React Testing Library. Además, aprendimos sobre matchers comunes en el paquete principal de Vitest y matchers útiles para pruebas de componentes en un paquete complementario llamado `jest-dom`.

Aprendimos sobre la amplia variedad de consultas disponibles en React Testing Library para seleccionar elementos de diferentes maneras. Usamos la consulta `getByText` ampliamente en las pruebas de la lista de verificación. También creamos un test ID en las casillas de verificación de los elementos de la lista para que la consulta `getByTestId` pudiera usarse para seleccionarlas de forma única.

Aprendimos que el paquete `user-event` es una excelente manera de simular interacciones de usuario desacopladas de la implementación. Usamos esto para simular a un usuario haciendo clic en una casilla de verificación de un elemento de la lista.

Finalmente, aprendimos cómo generar informes de cobertura de código y comprendimos todas las estadísticas del informe. El informe incluyó información sobre el código no cubierto, que utilizamos para obtener una cobertura del 100% en el componente de lista de verificación.

Por tanto, hemos llegado al final de este libro. Ahora se sentirá cómodo tanto con React como con TypeScript y tendrá un conocimiento excelente en áreas fuera de React core, como los estilos y el popular framework Next.js. Podrá desarrollar componentes que sean reutilizables en diferentes páginas e incluso en diferentes aplicaciones. Además de eso, ahora podrá escribir un conjunto de pruebas sólido para que pueda implementar nuevas funciones con confianza.

En resumen, el conocimiento de este libro le permitirá crear de manera eficiente el frontend de aplicaciones grandes y complejas con React y TypeScript. ¡Espero que haya disfrutado leyendo este libro tanto como yo escribiéndolo!

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido en este capítulo:

1. ¿Por qué no pasa la siguiente expectativa? ¿Cómo podría resolverse esto?

```typescript
expect({ name: ‘Bob’ }).toBe({ name: ‘Bob’ });
```

2. ¿Qué matcher se puede utilizar para comprobar que una variable no es `null`?
3. Aquí hay una expectativa que verifica si un botón Guardar está deshabilitado:

```typescript
expect( screen.getByText(‘Save’).hasAttribute(‘disabled’) ).toBe(true);
```

La expectativa pasa como se esperaba, pero ¿hay un matcher diferente que pueda usarse para simplificar esto?

4. Tenemos un elemento de formulario que contiene un botón Save solo cuando los datos se han cargado en campos desde una API de servidor. Hemos utilizado el tipo de consulta `findBy` para que la consulta vuelva a intentar hasta que se hayan obtenido los datos:

```typescript
expect(screen.findByText(‘Save’)).toBeInTheDocument();
```

Sin embargo, la expectativa no funciona: ¿puedes detectar el problema?

5. La siguiente expectativa intenta comprobar que un botón Save no está en el DOM:

```typescript
expect(screen.getByText(‘Save’)).toBe(null);
```

Sin embargo, esto no funciona como se esperaba. En su lugar, se produce un error porque no se puede encontrar el botón Save. ¿Cómo se puede resolver esto?

---

### Respuestas

1. El matcher `toBe` solo debe usarse para verificar valores primitivos como números y cadenas; este es un objeto. Se debe usar el matcher `toStrictEqual` para verificar objetos porque verifica los valores de todas sus propiedades en lugar de la referencia del objeto:

```typescript
expect({ name: ‘Bob’ }).toStrictEqual({ name: ‘Bob’ });
```

2. Los matchers `not` y `toBeNull` se pueden combinar para comprobar que una variable no sea `null`:

```typescript
expect(something).not.toBeNull();
```

3. El matcher `toBeDisabled` se puede utilizar desde `jest-dom`:

```typescript
expect(screen.getByText(‘Save’)).toBeDisabled();
```

4. El tipo de consulta `findBy` requiere `await` porque es asíncrono:

```typescript
expect( await screen.findByText(‘Save’) ).toBeInTheDocument();
```

5. Se puede utilizar el tipo de consulta `queryBy` porque no genera una excepción cuando no se encuentra un elemento. Además, los matchers `not` y `toBeInTheDocument` se pueden utilizar para comprobar que el elemento no esté en el DOM:

```typescript
expect( screen.queryByText(‘Save’) ).not.toBeInTheDocument();
```
