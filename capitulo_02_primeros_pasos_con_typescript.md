# Parte 1: Introducción

## Capítulo 2: Primeros pasos con TypeScript

En este capítulo, comenzaremos comprendiendo qué es TypeScript y cómo proporciona un sistema de tipos mucho más enriquecido sobre JavaScript. Aprenderemos sobre los tipos básicos en TypeScript, como números y cadenas de texto, y luego cómo crear nuestros propios tipos para representar objetos y arrays utilizando diferentes características de TypeScript. Comprenderemos el compilador de TypeScript y sus opciones clave en una aplicación React. Finalmente, modificaremos el componente alert que construimos en el capítulo anterior para usar TypeScript.

En este capítulo, cubriremos los siguientes temas:

- Comprender los beneficios de TypeScript
- Comprender los tipos de JavaScript
- Uso de tipos básicos de TypeScript
- Crear tipos en TypeScript
- Uso del compilador de TypeScript
- Crear un componente con React y TypeScript

Al final de este capítulo, podrá crear componentes simples de React con tipado seguro (*type-safe*).

---

### Requisitos técnicos

En este capítulo utilizaremos el siguiente software:

- **Navegador**: Un navegador moderno como Google Chrome.
- **TypeScript Playground**: Este es un sitio web en [https://www.typescriptlang.org/play/](https://www.typescriptlang.org/play/) que le permite experimentar y comprender las características de TypeScript sin necesidad de instalarlo.
- **Terminal**: Usaremos una terminal para ejecutar comandos y crear un proyecto de TypeScript. La terminal predeterminada disponible en su sistema operativo funcionará bien.
- **Visual Studio Code**: Necesitaremos un editor de código para explorar TypeScript. Si no lo instaló en el capítulo anterior, se puede instalar desde [https://code.visualstudio.com/](https://code.visualstudio.com/).
- **Node.js y npm**: TypeScript depende de estas piezas de software. Puede instalarlas desde [https://nodejs.org/en/download/](https://nodejs.org/en/download/).

Todos los fragmentos de código de este capítulo se pueden encontrar en línea en:
[https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter02](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/tree/main/Chapter02)

---

### Comprender los beneficios de TypeScript

En esta sección, comenzaremos comprendiendo qué es TypeScript, cómo se relaciona con JavaScript y cómo TypeScript permite a los equipos ser más productivos.

#### Entendiendo TypeScript

TypeScript se lanzó por primera vez en 2012 y todavía se encuentra en continuo desarrollo, con nuevas versiones que se publican cada pocos meses. Pero, ¿qué es TypeScript y cuáles son sus beneficios?

A TypeScript a menudo se le conoce como un **superconjunto (superset)** o extensión de JavaScript porque cualquier característica de JavaScript está disponible en TypeScript. A diferencia de JavaScript, TypeScript no se puede ejecutar directamente en un navegador; primero debe transpilarse a JavaScript.

> [!NOTE]
> Vale la pena tener en cuenta que se está considerando una propuesta que permitiría ejecutar TypeScript directamente en un navegador sin transpilación. Consulte el siguiente enlace para más información: [https://github.com/tc39/proposal-type-annotations](https://github.com/tc39/proposal-type-annotations).

TypeScript añade un sistema de tipos enriquecido a JavaScript. Se utiliza a menudo con frameworks de frontend como Angular, Vue y React. TypeScript también se puede utilizar para construir un backend con Node.js, o incluso con entornos de ejecución más nuevos como Bun o Deno. Esto demuestra lo flexible que es el sistema de tipos de TypeScript.

Cuando una base de código JavaScript crece, puede volverse difícil de leer y mantener. El sistema de tipos de TypeScript resuelve este problema. TypeScript utiliza el sistema de tipos para permitir que los editores de código detecten errores de tipo a medida que los desarrolladores escriben código problemático. Los editores de código también utilizan el sistema de tipos para proporcionar funciones de productividad, como una navegación de código robusta y refactorización de código.

A continuación, veremos un ejemplo paso a paso de cómo TypeScript detecta un tipo de error que JavaScript no puede.

#### Detectar errores de tipo de forma temprana

La información de tipos ayuda al compilador de TypeScript a detectar errores de tipo. En editores de código como Visual Studio Code, un error de tipo se subraya en rojo inmediatamente después de que el desarrollador comete un error de tipo. Lleve a cabo los siguientes pasos para experimentar un ejemplo de TypeScript detectando un error de tipo:

1. Abra Visual Studio Code en una carpeta de su elección.
2. Cree un nuevo archivo llamado `calculateTotalPrice.js` eligiendo la opción **New File** en el panel Explorador.

*Figura 2.1 – Creación de un nuevo archivo en Visual Studio Code*

3. Ingrese el siguiente código en el archivo:

```javascript
function calculateTotalPriceJS( product, quantity, discount, ) { const priceWithoutDiscount = product.price * quantity; const discountAmount = priceWithoutDiscount * discount; return ( priceWithoutDiscount - discountAmount ); }
```

La función calcula el precio total de un producto, así como la cantidad y el descuento que se le pasan.

Recuerde que los fragmentos de código están disponibles en línea para copiar. El enlace al fragmento anterior es [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter02/understanding-typescript/calculateTotalPrice.js](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter02/understanding-typescript/calculateTotalPrice.js).

Hay un error en el código que podría ser difícil de detectar y Visual Studio Code no lo resaltará. El error se aclarará en el paso 5 después de que el código se haya convertido a TypeScript.

4. Ahora, cree una copia del archivo pero con una extensión `.ts` en lugar de `.js`. Se puede copiar un archivo haciendo clic derecho sobre él en el panel Explorador y seleccionando la opción **Copy**. Luego, haga clic derecho nuevamente en el panel Explorador y seleccione la opción **Paste** para crear el archivo copiado.

> [!NOTE]
> Una extensión de archivo `.ts` denota un archivo de TypeScript. Esto significa que un compilador de TypeScript realizará la verificación de tipos en este archivo.

5. En el archivo `calculateTotalPrice.ts`, elimine `JS` al final del nombre de la función y realice las siguientes actualizaciones en el código:

```typescript
function calculateTotalPrice( product: { name: string; unitPrice: number }, quantity: number, discount: number ) { const priceWithoutDiscount = product.price * quantity; const discountAmount = priceWithoutDiscount * discount; return priceWithoutDiscount - discountAmount; }
```

Aquí, hemos añadido anotaciones de tipo de TypeScript a los parámetros de la función. Aprenderemos sobre las anotaciones de tipo en detalle en la siguiente sección.

El punto clave es que el error de tipo ahora se resalta mediante un subrayado ondulado rojo:

*Figura 2.2 – Error de tipo resaltado*

El error radica en que la función hace referencia a una propiedad `price` en el objeto `product` que no existe. La propiedad a la que se debería hacer referencia es `unitPrice`.

Detectar estos problemas de forma temprana en el proceso de desarrollo aumenta el rendimiento del equipo y es una cosa menos que el control de calidad debe detectar. Podría ser peor: el error podría haber llegado a la aplicación en producción y haber causado una mala experiencia a los usuarios.

Mantenga estos archivos abiertos en Visual Studio Code porque a continuación veremos un ejemplo de cómo TypeScript mejora la experiencia del desarrollador.

#### Mejorar la experiencia y la productividad del desarrollador con IntelliSense

IntelliSense es una función de los editores de código que brinda información útil sobre los elementos del código y permite completarlo rápidamente. Por ejemplo, IntelliSense puede proporcionar la lista de propiedades disponibles en un objeto.

Lleve a cabo los siguientes pasos para experimentar cómo TypeScript funciona mejor con IntelliSense que JavaScript y cómo esto impacta positivamente en la productividad. Como parte de este ejercicio, corregiremos el error de `price` de la sección anterior:

1. Abra `calculateTotalPrice.js`, y en la línea 2, donde se hace referencia a `product.price`, elimine `price`. Luego, con el cursor después del punto (`.`), presione `Ctrl + barra espaciadora`. Esto abre el IntelliSense de Visual Studio Code:

*Figura 2.3 – IntelliSense en un archivo JavaScript*

Visual Studio Code solo puede adivinar el nombre potencial de la propiedad, por lo que lista los nombres de variables y nombres de funciones que ha visto en el archivo. Desafortunadamente, IntelliSense no ayuda en este caso porque el nombre de propiedad correcto, `unitPrice`, no aparece en la lista.

2. Ahora, abra `calculateTotalPrice.ts`, elimine `price` de `product.price` y presione `Ctrl + barra espaciadora` para abrir IntelliSense nuevamente:

*Figura 2.4 – IntelliSense en un archivo TypeScript*

Esta vez, Visual Studio Code muestra las propiedades correctas.

3. Seleccione `unitPrice` de IntelliSense para resolver el error de tipo.

IntelliSense es solo una de las herramientas que proporciona TypeScript. También puede proporcionar funciones sólidas de refactorización, como cambiar el nombre de los componentes de React, y ayuda con una navegación precisa por el código, como ir a la definición de una función.

En resumen, aprendimos lo siguiente en esta sección:
- La función de verificación de tipos de TypeScript ayuda a detectar problemas más temprano en el proceso de desarrollo.
- TypeScript permite que los editores de código ofrezcan funciones de productividad como IntelliSense.
- Estas ventajas proporcionan beneficios significativos cuando se trabaja en bases de código más grandes.

A continuación, aprenderemos sobre el sistema de tipos en JavaScript. Esto subrayará aún más la necesidad de TypeScript en una gran base de código.

---

### Comprender los tipos de JavaScript

Antes de comprender el sistema de tipos en TypeScript, exploremos brevemente el sistema de tipos en JavaScript. Para hacer esto, abra un navegador y lleve a cabo los siguientes pasos:

1. Abra las herramientas de desarrollo del navegador (F12 en Windows o Cmd + Option + I en Mac) y vaya al panel **Console**.
2. Ingrese las siguientes líneas en la consola:

```javascript
let firstName = “Fred”; console.log(typeof firstName); let score = 9; console.log(typeof score); let date = new Date(2022, 10, 1); console.log(typeof date);
```

El código asigna tres variables a varios valores. El código también muestra los valores de las variables en la consola, junto con su tipo de JavaScript.

Aquí está la salida de la consola:

*Figura 2.5 – Algunos tipos de JavaScript*

No es de extrañar que `firstName` sea un `string` y `score` sea un `number`. Sin embargo, es un poco sorprendente que `date` sea un `object` en lugar de algo más específico, como una fecha.

Agreguemos otro par de líneas de código a la consola:

```javascript
score = “ten”; console.log(typeof score);
```

Nuevamente, la salida de la consola es un poco sorprendente:

*Figura 2.6 – Variable cambiando de tipo*

¡La variable `score` ha cambiado de un tipo `number` a un tipo `string`! Esto se debe a que JavaScript tiene un tipado débil (*loosely typed*).

Un punto clave es que JavaScript solo tiene un conjunto mínimo de tipos, como `string`, `number` y `boolean`. Vale la pena señalar que todos los tipos de JavaScript están disponibles en TypeScript porque TypeScript es un superconjunto de JavaScript.

Además, JavaScript permite que una variable cambie su tipo, lo que significa que el motor de JavaScript no arrojará un error si una variable se cambia a un tipo completamente diferente. Este tipado débil hace que sea imposible para los editores de código detectar errores de tipo.

> [!NOTE]
> Para obtener más información sobre los tipos de JavaScript, consulte: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures).

Ahora que comprendemos las limitaciones del sistema de tipos en JavaScript, aprenderemos sobre el sistema de tipos de TypeScript, comenzando con los tipos básicos.

---

### Uso de tipos básicos de TypeScript

En esta sección, comenzaremos comprendiendo cómo se pueden declarar los tipos de TypeScript y cómo se infieren a partir de los valores asignados. Luego aprenderemos los tipos básicos comúnmente utilizados en TypeScript que no están disponibles en JavaScript y comprenderemos casos de uso útiles.

#### Uso de anotaciones de tipo

Las **anotaciones de tipo (type annotations)** de TypeScript permiten declarar variables con tipos específicos. Estas permiten que el compilador de TypeScript verifique que el código se adhiera a estos tipos. En resumen, las anotaciones de tipo permiten a TypeScript detectar errores en los que nuestro código utiliza el tipo incorrecto mucho antes de lo que lo haríamos si estuviéramos escribiendo nuestro código en JavaScript.

Abra **TypeScript Playground** en [https://www.typescriptlang.org/play](https://www.typescriptlang.org/play) y lleve a cabo los siguientes pasos para explorar las anotaciones de tipo:

1. Elimine cualquier código existente en el panel izquierdo e ingrese la siguiente declaración de variable:

```typescript
let unitPrice: number;
```

La anotación de tipo viene después de la declaración de la variable. Comienza con dos puntos seguidos del tipo que queremos asignar a la variable. En este caso, `unitPrice` va a ser de tipo `number`. Recuerde que `number` es un tipo en JavaScript, lo que significa que también está disponible para que lo usemos en TypeScript.

El JavaScript transpilado aparece en el panel derecho de la siguiente manera:

```javascript
let unitPrice;
```

Sin embargo, observe que la anotación de tipo ha desaparecido. Esto se debe a que las anotaciones de tipo no existen en JavaScript.

> [!NOTE]
> Es posible que también vea `"use strict";` en la parte superior del JavaScript transpilado. Esto significa que el JavaScript se ejecutará en modo estricto de JavaScript, lo que detectará más errores de codificación. Para más información sobre el modo estricto de JavaScript, consulte: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode).

2. Agregue una segunda línea al programa:

```typescript
unitPrice = “Table”;
```

Observe que aparece una línea roja debajo de `unitPrice` en esta línea. Si pasa el cursor sobre el `unitPrice` subrayado, se describe un error de tipo:

*Figura 2.7 – Detección de un error de tipo*

El error de tipo también aparece en la pestaña **Errors** en el panel derecho. Hay una insignia roja que contiene un `1` notificándonos que hay un error. Haga clic en la pestaña Errors para descubrir el mismo mensaje de error que se informó en el paso anterior.

*Figura 2.8 – Error de tipo informado en la pestaña Errors*

También puede agregar anotaciones de tipo a los parámetros de una función y al valor de retorno de una función utilizando la misma sintaxis que para anotar una variable. Como ejemplo, ingrese la siguiente función en TypeScript Playground:

```typescript
function getTotal( unitPrice: number, quantity: number, discount: number ): number { const priceWithoutDiscount = unitPrice * quantity; const discountAmount = priceWithoutDiscount * discount; return priceWithoutDiscount - discountAmount; }
```

Hemos declarado los parámetros `unitPrice`, `quantity` y `discount`, todos con un tipo `number`. La anotación del tipo de retorno viene después de los paréntesis de la función, que también es de tipo `number` en el ejemplo anterior.

> [!NOTE]
> Hemos utilizado tanto `const` como `let` para declarar variables en diferentes ejemplos. `let` permite que la variable cambie de valor después de la declaración, mientras que las variables `const` no pueden cambiar. En la función anterior, `priceWithoutDiscount` y `discountAmount` nunca cambian de valor después de la asignación inicial, por lo que hemos utilizado `const`.

3. Agregue otra línea de código para llamar a `getTotal` con un tipo incorrecto para `quantity`. Asigne el resultado de la llamada a `getTotal` a una variable con un tipo incorrecto:

```typescript
let total: string = getTotal(500, “one”, 0.1);
```

Ambos errores se detectan y resaltan inmediatamente:

*Figura 2.9 – Detección de ambos errores de tipo*

Esta fuerte verificación de tipos es algo que no obtenemos en JavaScript, y es muy útil en bases de código grandes porque nos ayuda a detectar de inmediato errores de tipo.

A continuación, aprenderemos cómo TypeScript no siempre necesita anotaciones de tipo para verificar los tipos en el código.

#### Uso de la inferencia de tipos

Las anotaciones de tipo son realmente valiosas, pero requieren que se escriba código adicional. Este código extra lleva tiempo escribirlo. Afortunadamente, el potente sistema de **inferencia de tipos** de TypeScript significa que no es necesario especificar anotaciones de tipo todo el tiempo. TypeScript infiere el tipo de una variable a partir de su valor asignado.

Explore la inferencia de tipos llevando a cabo los siguientes pasos en TypeScript Playground:

1. Primero, elimine cualquier código anterior y luego agregue la siguiente línea:

```typescript
let flag = false;
```

2. Pase el cursor sobre la variable `flag`. Aparecerá una información sobre herramientas que muestra el tipo al que se ha inferido `flag`:

*Figura 2.10 – Al pasar el cursor sobre una variable se revela su tipo*

3. Agregue otra línea debajo para establecer incorrectamente `flag` en un valor no válido:

```typescript
flag = “table”;
```

Un error de tipo se detecta de inmediato, al igual que cuando usamos una anotación de tipo para asignar un tipo a una variable.

La inferencia de tipos es una excelente característica de TypeScript y evita la sobrecarga de código que traería una gran cantidad de anotaciones de tipo. Por lo tanto, es una práctica común utilizar la inferencia de tipos y solo recurrir al uso de anotaciones de tipo donde la inferencia no sea posible.

A continuación, veremos el tipo `Date` en TypeScript.

#### Uso del tipo Date

Ya sabemos que no existe un tipo `Date` en JavaScript, pero afortunadamente, sí existe un tipo `Date` en TypeScript. El tipo `Date` de TypeScript es una representación del objeto `Date` de JavaScript.

> [!NOTE]
> Consulte el siguiente enlace para más información sobre el objeto `Date` de JavaScript: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date).

Para explorar el tipo `Date` de TypeScript, lleve a cabo los siguientes pasos en TypeScript Playground:

1. Primero, elimine cualquier código anterior y luego agregue las siguientes líneas:

```typescript
let today: Date; today = new Date();
```

Se declara una variable llamada `today` con un tipo `Date` asignado. El valor de la variable se establece en la fecha de hoy.

2. Refactorice estas dos líneas en la siguiente línea única que utiliza la inferencia de tipos en lugar de una anotación de tipo:

```typescript
let today = new Date();
```

3. Compruebe que a `today` se le ha asignado el tipo `Date` pasando el cursor sobre él y verificando la información sobre herramientas:

*Figura 2.11 – Confirmación de que today ha inferido el tipo Date*

4. Ahora, verifique que IntelliSense funcione agregando `today.` en una nueva línea:

*Figura 2.12 – IntelliSense funcionando correctamente en una fecha*

5. Elimine esta línea y agregue una línea de código ligeramente diferente:

```typescript
today.addMonths(2);
```

No existe una función `addMonths` en el objeto `Date`, por lo que se genera un error de tipo:

*Figura 2.13 – Error de tipo detectado en una fecha*

En resumen, el tipo `Date` tiene todas las características que esperamos: inferencia, IntelliSense y verificación de tipos, que son realmente útiles cuando se trabaja con fechas.

A continuación, aprenderemos sobre una vía de escape del sistema de tipos de TypeScript.

#### Uso del tipo any

¿Qué sucede si declaramos una variable sin anotación de tipo y sin valor? ¿Qué inferirá TypeScript como tipo? Averigüémoslo ingresando el siguiente código en TypeScript Playground:

```typescript
let flag;
```

Ahora, pase el mouse sobre `flag`:

*Figura 2.14 – Variable a la que se le asigna el tipo any*

Por lo tanto, TypeScript le da a una variable sin anotación de tipo y sin un valor asignado inmediatamente el tipo `any`. Es una forma de optar por no realizar la verificación de tipos en una variable en particular y se usa comúnmente para contenido dinámico o valores de bibliotecas de terceros. Sin embargo, el sistema de tipos cada vez más potente de TypeScript significa que hoy en día necesitamos usar `any` con menos frecuencia.

En su lugar, existe una alternativa mejor: el tipo `unknown`.

#### Uso del tipo unknown

`unknown` es un tipo que podemos usar cuando no estamos seguros del tipo pero queremos interactuar con él de una manera fuertemente tipada. Lleve a cabo los siguientes pasos para explorar cómo esta es una mejor alternativa al tipo `any`:

1. En TypeScript Playground, elimine cualquier código anterior e ingrese lo siguiente:

```typescript
fetch(“https://swapi.dev/api/people/1”) .then((response) => response.json()) .then((data) => { console.log(“firstName”, data.firstName); });
```

El código obtiene un personaje de Star Wars de una API web. No se generan errores de tipo, por lo que el código parece estar bien.

2. Ahora, haga clic en la opción **Run** para ejecutar el código:

*Figura 2.15 – La propiedad firstName tiene un valor undefined*

La propiedad `firstName` no parece estar en los datos obtenidos porque es `undefined` cuando se imprime en la consola.

¿Por qué no se generó un error de tipo en la línea 4 donde se hizo referencia a `firstName`? Bueno, `data` es de tipo `any`, lo que significa que no se realizará ninguna comprobación de tipos sobre ella. Puede pasar el cursor sobre `data` para confirmar que se le ha asignado el tipo `any`.

3. Asigne a `data` la anotación de tipo `unknown`:

```typescript
fetch(“https://swapi.dev/api/people/1”) .then((response) => response.json()) .then((data: unknown) => { console.log(“firstName”, data.firstName); });
```

Ahora se genera un error de tipo donde se hace referencia a `firstName`:

*Figura 2.16 – Error de tipo en el parámetro data con unknown*

El tipo `unknown` es lo opuesto al tipo `any`, ya que no contiene nada dentro de su tipo. Un tipo que no contiene nada puede parecer inútil. Sin embargo, el tipo de una variable se puede ampliar (*widen*) si se realizan comprobaciones que permitan a TypeScript ampliarlo.

4. Antes de darle a TypeScript información para ampliar `data`, cambie la propiedad a la que se hace referencia dentro de ella de `firstName` a `name`:

```typescript
fetch(“https://swapi.dev/api/people/1”) .then((response) => response.json()) .then((data: unknown) => { console.log(“name”, data.name); });
```

`name` es una propiedad válida, pero aún se produce un error de tipo. Esto se debe a que `data` sigue siendo `unknown`.

5. Ahora, realice los cambios resaltados en el código para ampliar el tipo de datos:

```typescript
fetch(“https://swapi.dev/api/people/1”) .then((response) => response.json()) .then((data: unknown) => { if (isCharacter(data)) { console.log(“name”, data.name); } }); function isCharacter( character: any ): character is { name: string } { return “name” in character; }
```

El fragmento de código se puede copiar desde: [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter02/using-basic-typescript-types/using-the-unknown-type/code.ts](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter02/using-basic-typescript-types/using-the-unknown-type/code.ts).

La sentencia `if` utiliza una función llamada `isCharacter` para verificar que el objeto contiene una propiedad `name`. El resultado de esta llamada es `true` en este ejemplo, por lo que la lógica fluirá hacia la rama `if`.

Observe el tipo de retorno de `isCharacter`, que es el siguiente:

```typescript
character is { name: string }
```

Este es un **predicado de tipo (type predicate)**. TypeScript restringirá o ampliará el tipo de `character` a `{ name: string }` si la función devuelve `true`. El predicado de tipo es `true` en este ejemplo, por lo que `character` se amplía a un objeto con una propiedad de cadena `name`.

6. Pase el cursor sobre la variable `data` en cada línea donde se hace referencia a ella. `data` comienza con el tipo `unknown` donde se asigna con una anotación de tipo. Luego, se amplía a `{name: string}` dentro de la rama `if`:

*Figura 2.17 – Tipo ampliado asignado a data*

Observe que el error de tipo también ha desaparecido. ¡Genial!

7. A continuación, ejecute el código. Verá a *Luke Skywalker* impreso en la consola.

En resumen, el tipo `unknown` es una excelente opción para datos de cuyo tipo no está seguro. Sin embargo, no puede interactuar con variables `unknown`: la variable debe ampliarse a un tipo diferente antes de cualquier interacción.

A continuación, cubramos los arrays.

#### Uso de arrays

Los arrays son estructuras que TypeScript hereda de JavaScript. Agregamos anotaciones de tipo a los arrays como de costumbre, pero con corchetes, `[]`, al final para denotar que este es un tipo de array.

Exploremos un ejemplo en TypeScript Playground:

1. Elimine cualquier código existente e ingrese lo siguiente:

```typescript
const numbers: number[] = [];
```

Alternativamente, se puede utilizar la sintaxis de tipo genérico `Array`:

```typescript
const numbers: Array<number> = [];
```

Aprenderemos sobre los genéricos en TypeScript en el [Capítulo 11](https://subscription.packtpub.com/book/web-development/9781836643173/11), *Componentes Reutilizables*.

2. Agregue `1` al array utilizando la función `push` del array:

```typescript
numbers.push(1);
```

3. Ahora agregue una cadena al array:

```typescript
numbers.push(“two”);
```

Se genera un error de tipo como esperaríamos:

*Figura 2.18 – Error de tipo al agregar un tipo string a un array de number*

4. Ahora reemplace todo el código con lo siguiente:

```typescript
const numbers = [1, 2, 3];
```

5. Pase el cursor sobre `numbers` para verificar que TypeScript haya inferido que su tipo es `number[]`.

*Figura 2.19 – Inferencia de tipo de array*

Excelente: ¡podemos ver que la inferencia de tipos de TypeScript funciona con arrays!

Los arrays son uno de los tipos más comunes utilizados para estructurar datos. En los ejemplos anteriores, solo hemos utilizado un array con elementos que tienen un tipo `number`, pero se puede utilizar cualquier tipo para los elementos, incluidos objetos, que tienen sus propias propiedades.

He aquí un resumen de todos los tipos básicos sobre los que hemos aprendido en esta sección:
- TypeScript agrega muchos tipos útiles a los tipos de JavaScript, como `Date`, y es capaz de representar arrays.
- TypeScript puede inferir el tipo de una variable a partir de su valor asignado. Se puede utilizar una anotación de tipo donde la inferencia de tipos no proporcione el tipo deseado.
- No se produce ninguna comprobación de tipos en variables con el tipo `any`, por lo que este tipo debe evitarse.
- El tipo `unknown` es una alternativa fuertemente tipada a `any`, pero las variables `unknown` deben ampliarse para interactuar con ellas.
- Los tipos de array se pueden definir usando corchetes después del tipo de elemento del array.

En la siguiente sección, aprenderemos a crear nuestros propios tipos.

---

### Crear tipos en TypeScript

La última sección mostró que TypeScript tiene un excelente conjunto de tipos estándar. En esta sección, aprenderemos a crear nuestros propios tipos. Comenzaremos aprendiendo cómo crear tipos para objetos antes de aprender cómo crear tipos para variables que contienen un rango de valores.

#### Uso de tipos de objetos

Los objetos son muy comunes en los programas de JavaScript, por lo que aprender a representarlos en TypeScript es realmente importante. De hecho, ya usamos un tipo de objeto anteriormente en este capítulo para el parámetro `product` en la función `calculateTotalPrice`. Aquí hay un recordatorio de la anotación de tipo del parámetro `product`:

```typescript
function calculateTotalPrice( product: { name: string; unitPrice: number }, ... ) { ... }
```

Un tipo de objeto en TypeScript se representa de forma similar a un literal de objeto de JavaScript. Sin embargo, en lugar de valores de propiedad, se especifican tipos de propiedad. Las propiedades en las definiciones de objetos se pueden separar por puntos y coma o comas, pero usar un punto y coma es una práctica común.

Borre cualquier código existente en TypeScript Playground y siga este ejemplo para explorar los tipos de objetos:

1. Ingrese la siguiente asignación de variable a un objeto:

```typescript
let table = {name: “Table”, unitPrice: 450};
```

Si pasa el cursor sobre la variable `table`, verá que se infiere que es del siguiente tipo:

```typescript
{ name: string; unitPrice: number; }
```

Por lo tanto, la inferencia de tipos funciona muy bien para los objetos.

2. Ahora, en la línea siguiente, intente establecer una propiedad `discount` en `10`:

```typescript
table.discount = 10;
```

Sin embargo, no existe una propiedad `discount` en el tipo; solo existen las propiedades `name` y `unitPrice`. Por lo tanto, ocurre un error de tipo.

3. Supongamos que queremos representar un objeto de producto que contenga las propiedades `name` y `unitPrice`, pero queremos que `unitPrice` sea opcional. Elimine el código existente y reemplácelo con lo siguiente:

```typescript
const table: { name: string; unitPrice: number } = { name: “Table”, };
```

Esto genera un error de tipo porque `unitPrice` es una propiedad requerida en la anotación de tipo. Podemos usar el símbolo `?` de la siguiente manera para que sea opcional en lugar de requerida:

```typescript
const table: { name: string; unitPrice?: number } = { name: “Table”, };
```

El error de tipo desaparece.

> [!NOTE]
> El símbolo `?` se puede utilizar en funciones para parámetros opcionales, por ejemplo, `myFunction(requiredParam: string, optionalParam?: string)`.

Ahora, aprendamos una forma de simplificar las definiciones de tipos de objetos.

#### Crear alias de tipo

La anotación de tipo que usamos en el último ejemplo era bastante extensa y sería más larga para estructuras de objetos más complejas. Además, tener que escribir la misma estructura de objeto para asignarla a diferentes variables resulta un poco frustrante:

```typescript
const table: { name: string; unitPrice?: number } = ...; const chair: { name: string; unitPrice?: number } = ...;
```

Los **alias de tipo (type aliases)** resuelven estos problemas. Como sugiere el nombre, un alias de tipo se refiere a otro tipo, y la sintaxis es la siguiente:

```typescript
type YourTypeAliasName = AnExistingType;
```

Abra TypeScript Playground y siga los pasos para explorar los alias de tipo:

1. Comience creando un alias de tipo para la estructura de objeto de producto que usamos en el último ejemplo:

```typescript
type Product = { name: string; unitPrice?: number };
```

2. Ahora asigne dos variables a este tipo `Product`:

```typescript
let table: Product = { name: “Table” }; let chair: Product = { name: “Chair”, unitPrice: 40 };
```

¡Eso es mucho más limpio!

3. Un alias de tipo puede extender otro objeto usando el símbolo `&`. Cree un segundo tipo para un producto con descuento agregando el siguiente alias de tipo:

```typescript
type DiscountedProduct = Product & { discount: number };
```

`DiscountedProduct` representa un objeto que contiene las propiedades `name`, `unitPrice` (opcional) y `discount`.

> [!NOTE]
> Un tipo que extiende a otro usando el símbolo `&` se conoce como un **tipo de intersección (intersection type)**.

4. Agregue la siguiente variable con el tipo `DiscountedProduct`:

```typescript
let chairOnSale: DiscountedProduct = { name: “Chair on Sale”, unitPrice: 30, discount: 5, };
```

5. Un alias de tipo también se puede utilizar para representar una función. Agregue el siguiente alias de tipo para representar una función:

```typescript
type Purchase = (quantity: number) => void; 
```

El tipo anterior representa una función que contiene un parámetro `number` y no devuelve nada.

> [!NOTE]
> El tipo `void` se utiliza para indicar que una función no devuelve un valor.

6. Utilice el tipo `Purchase` para crear una propiedad de función de compra en el tipo `Product`, de la siguiente manera:

```typescript
type Purchase = (quantity: number) => void; type Product = { name: string; unitPrice?: number; purchase: Purchase; };
```

Se generarán errores de tipo en las declaraciones de las variables `table`, `chair` y `chairOnSale` porque la propiedad de función `purchase` es requerida.

7. Agregue una propiedad de función `purchase` a las declaraciones de la variable `table`, de la siguiente manera:

```typescript
let table: Product = { name: “Table”, purchase: (quantity) => console.log(`Purchased ${quantity} tables`), }; table.purchase(4);
```

El error de tipo se resuelve en la declaración de la variable `table`.

Se podría agregar una propiedad `purchase` de manera similar a las declaraciones de las variables `chair` y `chairOnSale` para resolver sus errores de tipo. Sin embargo, ignore estos errores de tipo para esta exploración y pase al siguiente paso.

8. Haga clic en la opción **Run** para ejecutar el código que compra cuatro mesas. En la consola se muestra `"Purchased 4 tables"`.

En resumen, los alias de tipo permiten componer tipos existentes entre sí y mejoran la legibilidad y la reutilización de los tipos. Usaremos alias de tipo ampliamente en este libro.

A continuación, aprenderemos cómo crear un tipo para representar un rango de valores.

#### Crear tipos de unión

Un **tipo de unión (union type)** es la unión matemática de múltiples otros tipos para crear un nuevo tipo y puede representar un rango de valores. Los alias de tipo se pueden utilizar para crear tipos de unión.

Un ejemplo de un tipo de unión es el siguiente:

```typescript
type Level = “H” | “M” | “L”;
```

Una variable de tipo `Level` puede contener los valores `"H"`, `"M"` o `"L"`.

Borre cualquier código existente en TypeScript Playground y juguemos con los tipos de unión:

1. Comience creando un tipo para representar `"red"`, `"green"` o `"blue"`:

```typescript
type RGB = “red” | “green” | “blue”;
```

Tenga en cuenta que este tipo es una unión de cadenas, pero un tipo de unión puede constar de cualquier tipo, ¡incluso tipos mixtos!

2. Cree una variable con el tipo `RGB` y asigne un valor válido:

```typescript
let color: RGB = “red”;
```

3. Ahora intente asignar un valor fuera del tipo:

```typescript
color = “yellow”;
```

Ocurre un error de tipo, como se esperaba:

*Figura 2.20 – Error de tipo en el tipo de unión*

4. Ahora intente establecer `color` en `null`:

```typescript
color = null;
```

Como esperaríamos, esto todavía genera un error.

5. Los tipos de unión pueden hacer referencia a múltiples tipos. Agreguemos `null` a nuestro tipo `RGB`, de la siguiente manera:

```typescript
type RGB = “red” | “green” | “blue” | null;
```

La asignación de `color` a `null` ya no genera un error porque `null` está permitido dentro del tipo `RGB`.

Eso completa nuestra exploración de los tipos de unión. Los usaremos ampliamente a lo largo de este libro.

He aquí un resumen de lo que hemos aprendido sobre la creación de tipos:
- Los tipos de unión son una excelente manera de representar un conjunto específico de cadenas. También permiten que una variable contenga valores de múltiples tipos.
- Los alias de tipo permiten crear tipos nuevos y reutilizables, y se pueden utilizar para objetos, funciones y tipos de unión.
- Un alias de tipo existente se puede extender usando el símbolo `&`.
- El símbolo `?` puede especificar que una propiedad de un objeto o un parámetro de una función sea opcional.

Ahora que hemos cubierto los tipos, a continuación, aprenderemos sobre el compilador de TypeScript.

---

### Uso del compilador de TypeScript

En esta sección, aprenderemos a utilizar el compilador de TypeScript para verificar tipos en el código y transpilarlo a JavaScript. Primero, usaremos Visual Studio Code para crear un proyecto simple de TypeScript que contenga una función básica. Luego usaremos la terminal dentro de Visual Studio Code para interactuar con el compilador de TypeScript.

Abra Visual Studio Code en una carpeta vacía de su elección y lleve a cabo los siguientes pasos:

1. En el panel Explorador de Visual Studio Code, cree un archivo llamado `package.json` que contenga lo siguiente:

```json
{ “name”: “tsc-play”, “dependencies”: { “typescript”: “*” }, “scripts”: { “build”: “tsc src/welcome.ts” } }
```

El archivo define un nombre de proyecto de `tsc-play` y establece la última versión de TypeScript como la única dependencia. El archivo también define un script npm llamado `build`, que invocará al compilador de TypeScript (`tsc`), pasándole un archivo `welcome.ts` en la carpeta `src`. No se preocupe si `welcome.ts` no existe: lo crearemos en el paso 3.

2. En una terminal, navegue hasta la carpeta del proyecto e instale las dependencias mediante el siguiente comando:

```bash
npm i
```

Esto instalará todas las bibliotecas enumeradas en la sección de dependencias de `package.json`. Por lo tanto, esto instalará TypeScript.

3. Cree una carpeta llamada `src` y luego cree un archivo llamado `welcome.ts` dentro de ella.
4. Abra `welcome.ts` y agregue el siguiente contenido:

```typescript
function welcome(name: string | null) { if (name === null) { return `Welcome!`; } return `Welcome, ${name}!`; };
```

La función recibe un nombre y construye una cadena que da la bienvenida a ese nombre. Si no existe ningún nombre, se devuelve una bienvenida genérica.

5. Ingrese el siguiente comando en la terminal:

```bash
npm run build
```

Esto ejecutará el script de compilación de npm que definimos en el primer paso.

6. Una vez que finaliza el comando, observe que aparece un archivo `welcome.js` junto a `welcome.ts` en la carpeta `src`.
7. Abra el archivo `welcome.js` transpilado y lea el contenido. Se verá de la siguiente manera:

```javascript
function welcome(name) { if (name === null) { return “Welcome!”; } return “Welcome, “.concat(name, “!”); };
```

Observe que las anotaciones de tipo se han eliminado porque no son JavaScript válido. Observe también que se ha transpilado a JavaScript capaz de ejecutarse en navegadores muy antiguos.

La configuración predeterminada que utiliza el compilador de TypeScript no es la ideal. Por ejemplo, probablemente queramos el JavaScript transpilado en una carpeta completamente separada y es probable que queramos apuntar a navegadores más nuevos, lo que dará como resultado menos código JavaScript.

El compilador de TypeScript se puede configurar mediante un archivo llamado `tsconfig.json`. Agregue un archivo `tsconfig.json` en la raíz del proyecto, que contenga el siguiente código:

```json
{ “compilerOptions”: { “outDir”: “dist”, “target”: “esnext”, “module”: “esnext”, “lib”: [“DOM”, “esnext”], “strict”: true, “jsx”: “react”, “moduleResolution”: “node”, “noEmitOnError”: true }, “include”: [“src/**/*”], “exclude”: [“node_modules”, “build”] }
```

Este código se puede copiar desde: [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter02/using-the-typescript-compiler/tsconfig.json](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter02/using-the-typescript-compiler/tsconfig.json).

Aquí hay una explicación de cada ajuste en el campo `compilerOptions`:
- **outDir**: Esta es la carpeta en la que se coloca el JavaScript transpilado.
- **target**: Esta es la versión de JavaScript a la que queremos transpilar. El destino `esnext` significa la próxima versión. Esto significa que la transpilación utiliza características modernas de JavaScript en lugar de aplicar polyfills, reduciendo el tamaño del código generado.
- **module**: Este es el sistema de módulos dentro del código. El módulo `esnext` significa módulos estándar de JavaScript.
- **lib**: Proporciona los tipos de biblioteca estándar incluidos en el proceso de verificación de tipos. `DOM` proporciona los tipos de API DOM del navegador y `esnext` proporciona tipos para API en la próxima versión de JavaScript.
- **strict**: Cuando se establece en `true`, significa el nivel más estricto de verificación de tipos. Esto incluye la opción `strictNullChecks`, lo que significa que se requiere `null` en los tipos cuando sea necesario. Por ejemplo, la siguiente sentencia daría error cuando `strict` (y `strictNullChecks`) es `true`, pero no cuando es `false`:

```typescript
let firstName: string = null;
```

- **jsx**: Cuando se establece en `react`, permite que el compilador traspile el JSX de React.
- **moduleResolution**: Así es como se encuentran las dependencias. Queremos que TypeScript busque en la carpeta `node_modules`, por lo que hemos elegido `node`.
- **noEmitOnError**: Cuando se establece en `true`, significa que la transpilación no ocurrirá si se encuentra un error de tipo.

El campo `include` especifica los archivos de TypeScript que se van a compilar y el campo `exclude` especifica los archivos que se deben excluir.

> [!NOTE]
> Para obtener más información sobre las opciones del compilador de TypeScript, consulte el siguiente enlace: [https://www.typescriptlang.org/tsconfig](https://www.typescriptlang.org/tsconfig).

8. La configuración del compilador de TypeScript ahora especifica que se compilen todos los archivos en la carpeta `src`. Por lo tanto, elimine la ruta del archivo en el script `build` en `package.json`:

```json
{ ..., “scripts”: { “build”: “tsc” } }
```

9. Elimine el archivo `welcome.js` transpilado anteriormente en la carpeta `src`.
10. Vuelva a ejecutar el comando build en la terminal:

```bash
npm run build
```

Esta vez, el archivo transpilado se coloca en una carpeta `dist`; aquí se proporciona una copia de la función transpilada:

```javascript
function welcome(name) { if (name === null) { return `Welcome!`; } return `Welcome, ${name}!`; }
```

Notará que el JavaScript transpilado ahora usa comillas invertidas (*backticks*) para la cadena de bienvenida, lo cual es compatible con los navegadores modernos.

11. Lo último que vamos a probar es un error de tipo. Abra `welcome.ts` y agregue un tipo de retorno `number` a la función:

```typescript
function welcome(name: string | null): number {
```

Se generan inmediatamente dos errores de tipo en el editor.

12. Elimine la carpeta `dist` para eliminar el archivo JavaScript transpilado anteriormente.
13. Vuelva a ejecutar el comando build en la terminal:

```bash
npm run build
```

Los errores de tipo se informan en la terminal. Observe que el archivo JavaScript transpilado no se crea.

En resumen, TypeScript tiene un compilador, llamado `tsc`, que podemos utilizar para llevar a cabo la verificación de tipos y la transpilación como parte de un proceso de integración continua. El compilador es muy flexible y se puede configurar mediante un archivo llamado `tsconfig.json`. Vale la pena señalar que a menudo se usa Babel para transpilar TypeScript (así como React), dejando que TypeScript se concentre en la verificación de tipos.

A continuación, crearemos un componente de React que esté fuertemente tipado con TypeScript.

---

### Crear un componente con React y TypeScript

En el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*, construimos un componente alert usando React. En esta sección, usaremos TypeScript para hacer que el componente esté fuertemente tipado y experimentar los beneficios. Comenzamos agregando un tipo a las props del componente alert y luego experimentamos definiendo un tipo para su estado.

#### Crear un proyecto

Usaremos Vite para crear un proyecto como lo hicimos cuando construimos el componente alert por primera vez. Sin embargo, esta vez, elegiremos la plantilla de React y TypeScript. Lleve a cabo estos pasos:

1. En una terminal, en una carpeta de su elección, ejecute el siguiente comando para indicarle a Vite que cree un proyecto de React y TypeScript:

```bash
npm create vite@latest alert -- --template react-ts
```

En el fragmento de código anterior, hemos especificado el nombre del proyecto y la plantilla en el comando, por lo que no se nos solicitará esta información. Se ha elegido la plantilla `react-ts` para crear un proyecto de React y TypeScript.

2. El proyecto se crea. Ejecute los siguientes comandos en la terminal para cambiar el directorio de trabajo a la carpeta `alert`, instalar las dependencias del proyecto, abrir el proyecto en Visual Studio Code y ejecutar la aplicación en modo de desarrollo:

```bash
cd alert npm i code . npm run dev
```

Siéntase libre de agregar formateo automático de código. Cubrimos este tema con Prettier en el [Capítulo 1](https://subscription.packtpub.com/book/web-development/9781836643173/1), *Primeros pasos con React*.

3. Cree un nuevo archivo para el componente alert en la carpeta `src` llamado `Alert.tsx`.

> [!NOTE]
> Los componentes de React con TypeScript tienen una extensión de archivo `.tsx`.

4. Pegue en `Alert.tsx` la versión de JavaScript del componente alert, que se puede encontrar en GitHub en: [https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter01/using-events/src/Alert.jsx](https://github.com/PacktPublishing/Learn-React-with-TypeScript-Third-Edition/blob/main/Chapter01/using-events/src/Alert.jsx). Observe que se informan errores de tipo en algunas de las props porque solo se pueden inferir como si tuvieran el tipo `any`.

A continuación, agregaremos una anotación de tipo de TypeScript en las props del componente, lo que resolverá el error de tipo.

#### Añadir un tipo para las props

Anteriormente en el capítulo, aprendimos cómo agregar anotaciones de tipo a funciones. Usaremos este conocimiento para tipar fuertemente las props del componente alert:

1. Agregue el siguiente tipo justo encima del componente. Este será el tipo para las props del componente:

```typescript
type Props = { type?: string; heading: string; children: ReactNode; closable?: boolean; onClose?: () => void; };
```

Las props `heading` y `children` son obligatorias, pero el resto de las props son opcionales.

La prop `children` recibe un tipo especial de React llamado `ReactNode`. Esto le permite aceptar elementos JSX así como cadenas de texto. Por el momento, se produce un error de tipo en esto; lo resolveremos en el siguiente paso.

El nombre del tipo puede ser cualquiera, pero es una práctica común llamarlo `Props`.

2. Para resolver el error de tipo en la referencia `ReactNode`, actualice la declaración de importación de React de la siguiente manera:

```typescript
import { useState, type ReactNode } from ‘react’;
```

> [!NOTE]
> No es obligatorio incluir la palabra clave `type` antes de los tipos en las declaraciones de importación. Sin embargo, es una buena práctica porque ayuda al proceso de compilación de TypeScript a JavaScript a eliminar todos los rastros de información de tipos.

3. Ahora, asigne el tipo `Props` al componente alert después de los parámetros desestructurados:

```typescript
export function Alert({ type = “information”, heading, children, closable, onClose, }: Props) { ... }
```

Las props del alert ahora están fuertemente tipadas.

4. Abra `App.tsx` y reemplace el contenido con lo siguiente:

```tsx
import { Alert } from ‘./Alert’; import ‘./App.css’; function App() { return <Alert />; } export default App;
```

Hemos importado el componente `Alert` y lo hemos renderizado en el componente `App`. Se produce un error de tipo en `Alert` en el JSX porque no se han pasado las props que son obligatorias.

*Figura 2.21 – Error de tipo en el componente Alert*

5. Pase una prop `heading` a `Alert` y dele algo de contenido:

```tsx
<Alert heading=”Success”> Everything is really good! </Alert>
```

Los errores de tipo desaparecerán ahora que se han pasado las props obligatorias.

6. Inicie la aplicación ejecutando `npm run dev` en una terminal.
7. Si visita la aplicación en ejecución en un navegador, la aplicación React que contiene la alerta aparece en la página como se esperaba.

A continuación, aprenderemos cómo darle explícitamente un tipo al estado de React.

#### Añadir un tipo para el estado

Siga estos pasos para experimentar con el tipo de estado `visible` en el componente alert:

1. Abra `Alert.tsx` y pase el cursor sobre la variable de estado `visible` para determinar su tipo inferido. Se ha inferido que es `boolean` porque se ha inicializado con el valor `true`. El tipo `boolean` es precisamente lo que queremos.
2. Como experimento, elimine el valor inicial de `true` pasado a `useState`. Luego, pase el cursor sobre la variable de estado `visible` nuevamente. Se ha inferido que es `undefined` porque no se ha pasado ningún valor predeterminado a `useState`. Obviamente este no es el tipo que queremos.
3. A veces, el tipo de `useState` no se infiere como el tipo que queremos, como en el paso anterior. En estos casos, el tipo del estado se puede definir explícitamente utilizando un argumento genérico en `useState`. Asigne explícitamente al estado `visible` un tipo `boolean` agregando el siguiente argumento genérico:

```typescript
const [visible, setVisible] = useState<boolean>();
```

> [!NOTE]
> Un argumento genérico es como un argumento de función regular pero define un tipo para la función. Un argumento genérico se especifica utilizando corchetes angulares (`< >`) después del nombre de la función.

4. Restaure la sentencia `useState` a como estaba originalmente, inicializada en `true` y sin ningún tipo explícito:

```typescript
const [visible, setVisible] = useState(true);
```

5. Detenga la ejecución de la aplicación presionando `Ctrl + C`.

En resumen, verifique siempre el tipo de estado inferido de `useState` y use su argumento genérico para definir explícitamente el tipo si el tipo inferido no es el requerido.

Eso nos lleva al final del capítulo. A continuación, recapitularemos lo que hemos aprendido en este capítulo.

---

### Resumen

TypeScript complementa JavaScript con un sistema de tipos enriquecido, y en este capítulo, experimentamos la detección temprana de errores mediante la verificación de tipos de TypeScript.

También aprendimos que los tipos de JavaScript, como `number` y `string`, se pueden usar en TypeScript, así como tipos que solo existen en TypeScript, como `Date` y `unknown`.

Se pueden crear nuevos tipos utilizando alias de tipo. Aprendimos que los alias de tipo pueden basarse en objetos, funciones o incluso tipos mixtos usando un tipo de unión. Usamos un alias de tipo para tipar fuertemente las props en un componente alert de React.

Ahora sabemos que el símbolo `?` en una anotación de tipo hace que la propiedad de un objeto o el parámetro de una función sea opcional. Además, un tipo existente se puede extender usando el símbolo `&`.

Aprendimos que el compilador de TypeScript se puede invocar a través de una CLI, lo que permite integrarlo en un pipeline de integración continua. El compilador puede realizar la transpilación a JavaScript, así como la verificación de tipos, y se puede configurar con un archivo `tsconfig.json`.

Hasta ahora en este libro, solo hemos usado un Hook de React, `useState`. En el próximo capítulo, aprenderemos sobre muchos de los otros Hooks de React.

---

### Preguntas

Responda a las siguientes preguntas para comprobar lo que ha aprendido sobre TypeScript:

1. ¿Cuál sería el tipo inferido para la variable `flag` en el siguiente código?

```typescript
let flag = false;
```

2. ¿Cuál es la anotación de tipo para un array de fechas?

3. ¿Ocurrirá un error de tipo en el siguiente código?

```typescript
type Point = {x: number; y: number; z?: number}; const point: Point = { x: 24, y: 65 };
```

4. Utilice un alias de tipo para crear un número que solo pueda contener valores enteros entre 1 y 3 inclusive.

5. El siguiente código genera un error de tipo porque `lastSale` no puede aceptar valores `null`:

```typescript
type Product = { name: string; lastSale: Date; } const table: Product = {name: “Table”, lastSale: null}
```

¿Cómo se puede cambiar el tipo `Product` para permitir que `lastSale` acepte valores `null`?

---

### Respuestas

1. La variable `flag` se inferiría como de tipo `boolean`.

2. Un array de fechas se puede representar como `Date[]` o `Array<Date>`.

3. No se generará un error de tipo en la variable `point`. No necesita incluir la propiedad `z` porque es opcional.

4. Se puede crear un tipo para los números 1-3 de la siguiente manera:

```typescript
type OneToThree = 1 | 2 | 3;
```

5. Se puede utilizar un tipo de unión para la propiedad `lastSale` para permitirle aceptar valores `null`:

```typescript
type Product = { name: string; lastSale: Date | null; } const table: Product = {name: “Table”, lastSale: null}
```
