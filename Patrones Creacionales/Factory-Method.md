## Factory Method

**Patrón de Diseño Factory (Fábrica)** es un patrón creacional que proporciona una interfaz para crear objetos en una superclase, pero permite a las subclases alterar el tipo de objetos que se crearán. En esencia, abstrae el proceso de creación de objetos, permitiendo que el código cliente cree objetos sin necesidad de saber la clase exacta del objeto que se está creando. 

**Es muy útil cuando:**

- Una clase no puede prever la clase de objetos que debe crear.
- Una clase quiere que sus subclases especifiquen los objetos que crea.
- Quieres delegar la creación de objetos auxiliares a un objeto especializado.  

----

### **Ejemplo 1: JavaScript Clásico (Orientado a Clases)**

Supongamos que tienes diferentes tipos de vehículos y quieres una forma centralizada de crearlos.

```Javascript
// Definición de las "Clases de Producto" (Vehículos)

class Vehicle {
  constructor(model) {
    this.model = model;
  }
  displayInfo() {
    console.log(`Modelo: ${this.model}`);
  }
}

class Car extends Vehicle {
  constructor(model, doors) {
    super(model);
    this.doors = doors;
  }
  displayInfo() {
    super.displayInfo();
    console.log(`Tipo: Coche, Puertas: ${this.doors}`);
  }
}

class Motorcycle extends Vehicle {
  constructor(model, style) {
    super(model);
    this.style = style;
  }
  displayInfo() {
    super.displayInfo();
    console.log(`Tipo: Motocicleta, Estilo: ${this.style}`);
  }
}

// El "Factory" (Fábrica)

class VehicleFactory {
  createVehicle(type, options) {
    switch (type) {
      case 'car':
        return new Car(options.model, options.doors);
      case 'motorcycle':
        return new Motorcycle(options.model, options.style);
      default:
        throw new Error(`Tipo de vehículo desconocido: ${type}`);
    }
  }
}

// Uso del Factory

const factory = new VehicleFactory();

const myCar = factory.createVehicle('car', { model: 'Sedan', doors: 4 });
const myMotorcycle = factory.createVehicle('motorcycle', { model: 'Cruiser', style: 'Chopper' });

myCar.displayInfo(); // Salida: Modelo: Sedan, Tipo: Coche, Puertas: 4
myMotorcycle.displayInfo(); // Salida: Modelo: Cruiser, Tipo: Motocicleta, Estilo: Chopper

// Intentar crear un tipo desconocido
try {
  const unknown = factory.createVehicle('bike', { model: 'Mountain' });
} catch (error) {
  console.error(error.message); // Salida: Tipo de vehículo desconocido: bike
}
```
#### **Explicación:**

- Tenemos clases concretas (`Car`, `Motorcycle`) que representan los diferentes tipos de objetos que podemos crear.
- Tenemos una clase `VehicleFactory` con un método `createVehicle`. Este es el método de fábrica.
- El método `createVehicle` toma un parámetro (`type`) y, basándose en él, decide qué instancia de clase crear y devolver.
- El código cliente (`const myCar = factory.createVehicle(...)`) interactúa solo con el factory, sin necesidad de saber `new Car(...)` o `new Motorcycle(...)`. Si añadimos un nuevo tipo de vehículo (ej. `Truck`), solo necesitamos modificar el `VehicleFactory`, no el código cliente que ya usa el factory.

----

### Ejemplo 2: React (Orientado a Componentes Funcionales / JSX)

En React, el patrón Factory a menudo se manifiesta como una función que, basándose en ciertas props, devuelve un tipo diferente de componente o un elemento JSX configurado de manera particular. Esto es útil para renderizar diferentes variantes de UI dinámicamente.

Supongamos que necesitas renderizar diferentes tipos de botones (un botón simple, un botón con icono, un enlace que parece botón).

```JSX
// Definición de los "Productos" (Componentes React)

function SimpleButton({ label, onClick }) {
  return <button style={{ padding: '10px', backgroundColor: 'lightgray' }} onClick={onClick}>{label}</button>;
}

function IconButton({ label, icon, onClick }) {
  return (
    <button style={{ padding: '10px', backgroundColor: 'lightblue' }} onClick={onClick}>
      {icon} {label}
    </button>
  );
}

function LinkButton({ label, href }) {
  return <a href={href} style={{ display: 'inline-block', padding: '10px', backgroundColor: 'lightgreen', textDecoration: 'none', color: 'black' }}>{label}</a>;
}

// El "Factory" (Función que devuelve un Componente o Elemento)

function ButtonFactory({ type, ...props }) {
  switch (type) {
    case 'simple':
      return <SimpleButton {...props} />;
    case 'icon':
      // Aquí podrías pasar un elemento de icono como prop
      const { icon, ...restProps } = props;
      return <IconButton icon={icon} {...restProps} />;
    case 'link':
      // Asegurarnos de pasar 'href'
      const { href, ...linkProps } = props;
      return <LinkButton href={href} {...linkProps} />;
    default:
      // Opcional: devolver un fallback o null
      console.warn(`Tipo de botón desconocido: ${type}`);
      return null;
  }
}

// Uso del Factory en otro Componente React

function App() {
  const handleSimpleClick = () => alert('Simple Button Clicked!');
  const handleIconClick = () => alert('Icon Button Clicked!');

  // Ejemplo de un icono simple (podrías usar una librería de iconos)
  const mockIcon = <span>✨</span>;

  return (
    <div>
      <h1>Ejemplo de Button Factory</h1>
      <ButtonFactory type="simple" label="Haz clic" onClick={handleSimpleClick} />
      <br/><br/>
      <ButtonFactory type="icon" label="Con Icono" icon={mockIcon} onClick={handleIconClick} />
      <br/><br/>
      <ButtonFactory type="link" label="Ir a Google" href="https://www.google.com" />
      <br/><br/>
      <ButtonFactory type="unknown" label="Este no existe" /> {/* Ejemplo de tipo desconocido */}
    </div>
  );
}

// Para ver este ejemplo, necesitarías un setup básico de React
// import React from 'react';
// import ReactDOM from 'react-dom/client';
// const root = ReactDOM.createRoot(document.getElementById('root'));
// root.render(<App />);

```
#### **Explicación:**

- Tenemos componentes React funcionales (`SimpleButton`, `IconButton`, `LinkButton`) que son los diferentes "productos" visuales.
- Tenemos una función `ButtonFactory`. Esta función actúa como el factory.
Recibe props, incluyendo una prop `type`.
- Basándose en `type`, la función devuelve el elemento JSX del componente correspondiente, pasándole el resto de las props (`{...props}`).
- El componente `App` usa el `ButtonFactory` sin necesidad de importar directamente `SimpleButton`, `IconButton`, o `LinkButton` o decidir con un `if/else` qué componente renderizar. La lógica de selección está encapsulada en el factory.