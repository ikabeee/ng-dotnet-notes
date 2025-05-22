# Angular 19

## Sgnals

Las señales envuelven un valor que notifica a los clientes cuando un valor irá cambiando, estas pueden tener cualquier valor desde datos primitivos hasta datos complejos.

**Ejemplo de declaración de una señal**

```
const count = signal(0)
```

### Wiretable signals

Las writable signal proveen al API para actualizar sus valores directamente a través de los siguiente métodos.

```
.set() // Cambia el valor de la señal directamente
.update() // Cambia el valor de la señal desde un valor que ya existía
```

### Computed signals

Las señales computadas solamente son de lectura que derivan del valor de otras señales.

**Declaración de una señal computada**

```
const count: WritableSignal<number> = signal(0);
const doubleCount: Singla<number> = computed(() => count() * 2);
```

La señal computada *doubleCount,* no se ejecuta para calcular su valor hasta la primera vez que se lee doubleCount. El valor calculado se almacenará en el caché, en caso de leer doubleCount de nuevo, se devolverá el valor almacenado en caché sin volver a calcularlo.

Cabe recalcar que las señales computadas no podrán asignar valores ya que, el tipado de estas no son de WritableSignals

```
const showCount = signal(false);
const count = signal(0);
const conditionalCount = computed(() => {
  if (showCount()) {
    return `The count is ${count()}.`;
  } else {
    return 'Nothing to see here!';
  }
});
```

En este ejemplo cuando leemos conditionalCount, si showCount es falso se mostrará el mensaje *"Nothing to see here"* sin leer la señal count. Lo cual se traduce que si después actualizamos el valor de count no se volverá a ejecutar la señal computada de *conditionalCount.*

En caso que actualicemos *showCount* a true, y después se lee *conditionalCount* otra vez esto se volverá a ejecutar y tomará el camino donde *showCount()* sea verdadero cambiando el valor de la señal computada e invalidando el valor guardado en el caché

## Effects

Los efectos son operaciones que corren cuando una o más señales cambian de valor. Normalmente estas corren al menos una vez cuando esto sucede, se "persigue" el valor de las señales ya leídas.

```
effect(() => {
  console.log(`The current count is: ${count()}`);
});
```

## Resources

## Components

### Anatomía de los componentes

### Selectores

### Styling

### Inputs

### Outputs

### Lifeciycle

## Templates

### Event listeners

### Two-way biding

### Control flow

### Pipes

## Dependency Injection

## Routing

### Common routing tasks

### Router outlet

### Router links

## Forms

### Reactive forms

### Strictly typed reactive forms

### Validate form input

## Http Client

### Making requests

### Intercepting requests and responses

## Guards

## Interceptors

## Dev tools 

### Angular CLI
