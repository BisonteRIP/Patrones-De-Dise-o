### Patrón de Diseño Abstract Factory (Fábrica Abstracta)

Es un patrón creacional que va un paso más allá del Factory simple. Mientras que el Factory simple crea objetos de un solo tipo pero con diferentes variaciones (ej: diferentes tipos de vehículos: coche, moto), el Abstract Factory proporciona una interfaz para crear familias de objetos relacionados o dependientes sin especificar sus clases concretas.

Piensa en ello como tener diferentes "fábricas maestras", donde cada fábrica maestra puede producir un conjunto completo de productos que van juntos.
<br>

#### Concepto Clave:

- **Abstract Factory:** Una interfaz (o clase abstracta en lenguajes que la soportan) que declara un conjunto de métodos para crear cada uno de los productos de la familia. No implementa la creación, solo la define.
<br>
- **Concrete Factory:** Clases que implementan la interfaz Abstract Factory. Cada Concrete Factory es responsable de crear los objetos de una familia específica de productos.
<br>
- **Abstract Product:** Interfaces (o clases base) para cada tipo de producto en la familia. Por ejemplo, si la familia son elementos de UI, podrías tener Button, Checkbox.
<br>
- **Concrete Product:** Las implementaciones concretas de los Abstract Products para una familia particular. Por ejemplo, WindowsButton y WindowsCheckbox serían los productos concretos para la familia "Windows UI". MacButton y MacCheckbox serían para la familia "Mac UI".
<br>
- **Cliente:** Código que utiliza el Abstract Factory y los Abstract Products. El cliente interactúa solo con las interfaces (Abstract Factory, Abstract Products), sin conocer las clases concretas. Esto permite que el cliente funcione con cualquier familia de productos proporcionada por cualquier Concrete Factory.
----

#### Ejemplo en JavaScript (Orientado a Clases, Modelando UI para Diferentes OS)

Vamos a modelar la creación de elementos de UI (Botones y Checkboxes) para dos sistemas operativos diferentes: Windows y Mac.


```Javascript
// --- Productos Abstractos (Interfaces) ---
// Definimos qué métodos o propiedades deben tener los productos de cada tipo

class AbstractButton {
  render() { throw new Error('Implementar en subclase'); }
}

class AbstractCheckbox {
  paint() { throw new Error('Implementar en subclase'); }
}

// --- Productos Concretos (Familias de Implementación) ---
// Implementaciones específicas para cada familia (Windows y Mac)

// Familia Windows
class WindowsButton extends AbstractButton {
  render() {
    console.log('Renderizando Botón de Windows');
    // ... lógica de renderizado específica de Windows ...
  }
}

class WindowsCheckbox extends AbstractCheckbox {
  paint() {
    console.log('Pintando Checkbox de Windows');
    // ... lógica de pintado específica de Windows ...
  }
}

// Familia Mac
class MacButton extends AbstractButton {
  render() {
    console.log('Renderizando Botón de Mac');
    // ... lógica de renderizado específica de Mac ...
  }
}

class MacCheckbox extends AbstractCheckbox {
  paint() {
    console.log('Pintando Checkbox de Mac');
    // ... lógica de pintado específica de Mac ...
  }
}


// --- Fábrica Abstracta (Interfaz de Fábrica) ---
// Define los métodos para crear cada tipo de producto en la familia

class AbstractUIFactory {
  createButton() { throw new Error('Implementar en subclase'); }
  createCheckbox() { throw new Error('Implementar en subclase'); }
}

// --- Fábricas Concretas (Fábricas de Familias Específicas) ---
// Implementaciones específicas de la fábrica para cada familia

class WindowsUIFactory extends AbstractUIFactory {
  createButton() {
    return new WindowsButton(); // Crea un producto concreto de Windows
  }
  createCheckbox() {
    return new WindowsCheckbox(); // Crea un producto concreto de Windows
  }
}

class MacUIFactory extends AbstractUIFactory {
  createButton() {
    return new MacButton(); // Crea un producto concreto de Mac
  }
  createCheckbox() {
    return new MacCheckbox(); // Crea un producto concreto de Mac
  }
}

// --- Código Cliente ---
// El cliente trabaja con la Fábrica Abstracta y los Productos Abstractos
// Es independiente de la familia concreta de productos que se está usando

class Application {
  constructor(factory) {
    this.factory = factory;
    this.button = factory.createButton();     // El factory decide qué botón crear
    this.checkbox = factory.createCheckbox(); // El factory decide qué checkbox crear
  }

  paintUI() {
    console.log('--- Pintando UI ---');
    this.button.render();
    this.checkbox.paint();
    console.log('-----------------');
  }
}

// --- Uso del Abstract Factory ---

console.log('Usando la Fábrica de Windows:');
const windowsFactory = new WindowsUIFactory();
const windowsApp = new Application(windowsFactory);
windowsApp.paintUI();
/* Salida:
Usando la Fábrica de Windows:
--- Pintando UI ---
Renderizando Botón de Windows
Pintando Checkbox de Windows
-----------------
*/

console.log('\nUsando la Fábrica de Mac:');
const macFactory = new MacUIFactory();
const macApp = new Application(macFactory);
macApp.paintUI();
/* Salida:
Usando la Fábrica de Mac:
--- Pintando UI ---
Renderizando Botón de Mac
Pintando Checkbox de Mac
-----------------
*/

```
<br>

#### Explicación del Ejemplo:

- Tenemos dos familias de productos relacionados: UI de Windows y UI de Mac. Cada familia incluye al menos un Botón y un Checkbox.
  <br>
- Definimos las interfaces (`AbstractButton`, `AbstractCheckbox`) que todos los botones y checkboxes deben seguir, independientemente de su familia.
  <br>
- Creamos las clases concretas para cada producto dentro de cada familia (`WindowsButton`, `WindowsCheckbox`, `MacButton`, `MacCheckbox`).
  <br>
- Definimos la interfaz de la fábrica abstracta (`AbstractUIFactory`) con métodos para crear cada tipo de producto en la familia (`createButton`, `createCheckbox`).
  <br>
- Creamos las fábricas concretas (`WindowsUIFactory`, `MacUIFactory`), cada una implementando `AbstractUIFactory` y devolviendo las instancias de los productos de su propia familia.
  <br>
- El código cliente (`Application`) recibe una instancia de una fábrica (que se ajusta a `AbstractUIFactory`). Llama a los métodos de esa fábrica para obtener los productos (`createButton`, `createCheckbox`), y luego usa esos productos a través de sus interfaces abstractas (`render`, `paint`).
  <br>
- El cliente no sabe si recibió una `WindowsUIFactory` o una `MacUIFactory`, ni si está usando un `WindowsButton` o un `MacButton`. Su lógica es la misma, independientemente de la familia de UI.